[TOC]



## 如何生成安全的密码 Hash：MD5, SHA, PBKDF2, BCrypt

### 一、前言

密码 Hash 值的产生是将用户所提供的密码通过使用一定的算法计算后得到的加密字符序列。在 Java 中提供很多被证明能有效保证密码安全的 Hash 算法实现，我将在这篇文章中讨论其中的部分算法。

需要注意的是，一旦生成密码的 Hash 值并存储在数据库中后，你将不可能再把它转换回密码明文。只能每次用户在登录到应用程序时，须重新生成 Hash 值与数据库中的 Hash 值匹配来完成密码的校验。

### 二、简单的密码安全实现使用 MD5 算法

MD5消息摘要算法(MD5 Message-Digest Algorithm)是一种广泛使用的加密 Hash 函数，主要用于生成一个128bit(16byte) Hash 值。它的实现思路非常简单且易懂，其最基本的思路是将可变长度的数据集映射为固定长度的数据集。为了做到这一点，它将输入消息分割成 512-bit 的数据块。通过填补消息到末尾以确保其长度能除以512。现在这些块将通过 MD5 算法处理，其结果将是一个128位的散列值。使用MD5后，生成的散列值通常是32位16进制的数字。

在该文中，对于被加密的密码明文被称为“消息(message)”，其加密后所生成的 Hash 值称为 “消息摘要(message digest)” 或简称 "摘要(digest)"。以下是 MD5 产生 Hash 值的代码示例：

```java
public class SimpleMD5Example 
{
    public static void main(String[] args) 
    {
        String passwordToHash = "password";
        String generatedPassword = null;
        try {
            // Create MessageDigest instance for MD5
            MessageDigest md = MessageDigest.getInstance("MD5");
            //Add password bytes to digest
            md.update(passwordToHash.getBytes());
            //Get the hash's bytes 
            byte[] bytes = md.digest();
            //This bytes[] has bytes in decimal format;
            //Convert it to hexadecimal format
            StringBuilder sb = new StringBuilder();
            for(int i=0; i< bytes.length ;i++)
            {
                sb.append(Integer.toString((bytes[i] & 0xff) + 0x100, 16).substring(1));
            }
            //Get complete hashed password in hex format
            generatedPassword = sb.toString();
        } 
        catch (NoSuchAlgorithmException e) 
        {
            e.printStackTrace();
        }
        System.out.println(generatedPassword);
    }
}
```

虽然 MD5 是一个广为流传的 Hash 算法, 但它并不安全且所生成的 Hash 值也是相当的薄弱。它主要的优点在于生成速度快且易于实现。但是，这也意味着它是容易被暴力攻击和字典攻击。例如使用明文和 Hash 生成的彩虹表可以快速地搜索已知 Hash 对应的原数据。

此外，MD5 并没有避免 Hash 碰撞：这意味不同的密码会导致生成相同的 Hash 值。

不过，如果你仍然需要使用 MD5，可以考虑为其加 salt 来进一步保证它的安全性。
使用 salt 让生成的 MD5 更加安全

这里需要注意的是，加 salt 並不是 MD5 所特有的, 你同样可以把它应用在其它算法中。所以，在这里你只需关注它是如何应用而不是它与 MD5 的联系。

在 Wikipedia 上对 salt 的定义是通过一个单向函数获取随机数据来为密码或口令添加一些额外的数据。更简单的说法则是通过生成一些随机的文本将其附加到密码上来生成 Hash。

为 Hash 加 salt 的主要目的是用来防止预先被计算好的彩虹表攻击。现在加 slat 好处是将原本一次比较变为多次比较从而减慢对密码 Hash 值的猜测，否则对 Hash 密码库的破解效率将是非常之高。

重要的是：在 Java 中，我们总是需要使用 SecureRandom 来生成一个好的 salt 值，因此可以利用 SecureRandom 类所提供 “SHA1PRNG” 算法来生成伪随机数。代码如下所示：

```java
private static String getSalt() throws NoSuchAlgorithmException
{
    //Always use a SecureRandom generator
    SecureRandom sr = SecureRandom.getInstance("SHA1PRNG");
    //Create array for salt
    byte[] salt = new byte[16];
    //Get a random salt
    sr.nextBytes(salt);
    //return salt
    return salt.toString();
}
```

SHA1PRNG 算法是基于 SHA-1 算法实现且保密性较强的伪随机数生成器。要注意的是，如果不为它提供随机数种子，它将会通过真随机数来生成一个种子（TRNG）。 译注：关于 TRGN 和 PRGN。

接下来，看看为 **MD5 Hash 加 slat** 的代码示例：

```java
public class SaltedMD5Example
{
    public static void main(String[] args) throws NoSuchAlgorithmException, NoSuchProviderException
    {
        String passwordToHash = "password";
        String salt = getSalt();
        String securePassword = getSecurePassword(passwordToHash, salt);
        System.out.println(securePassword); //Prints 83ee5baeea20b6c21635e4ea67847f66
        String regeneratedPassowrdToVerify = getSecurePassword(passwordToHash, salt);
        System.out.println(regeneratedPassowrdToVerify); //Prints 83ee5baeea20b6c21635e4ea67847f66
    }
    private static String getSecurePassword(String passwordToHash, String salt)
    {
        String generatedPassword = null;
        try {
            // Create MessageDigest instance for MD5
            MessageDigest md = MessageDigest.getInstance("MD5");
            //Add password bytes to digest
            md.update(salt.getBytes());
            //Get the hash's bytes
            byte[] bytes = md.digest(passwordToHash.getBytes());
            //This bytes[] has bytes in decimal format;
            //Convert it to hexadecimal format
            StringBuilder sb = new StringBuilder();
            for(int i=0; i< bytes.length ;i++)
            {
                sb.append(Integer.toString((bytes[i] & 0xff) + 0x100, 16).substring(1));
            }
            //Get complete hashed password in hex format
            generatedPassword = sb.toString();
        }
        catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        }
        return generatedPassword;
    }
    //Add salt
    private static String getSalt() throws NoSuchAlgorithmException, NoSuchProviderException
    {
        //Always use a SecureRandom generator
        SecureRandom sr = SecureRandom.getInstance("SHA1PRNG", "SUN");
        //Create array for salt
        byte[] salt = new byte[16];
        //Get a random salt
        sr.nextBytes(salt);
        //return salt
        return salt.toString();
    }
}
```

重要提示：请注意，现在你必须每一个密码 Hash 存储它 slat 值。因为当用户登录系统，你必须使用最初生成的 slat 来再次生成 Hash 与存储的 Hash 进行匹配。如果使用不同的 slat（生成随机 slat）那么生成的 Hash 将不同。

此外，你可能听说过多次 Hash 和加 salt。通常就是指创建自定义的组合，如：

**salt+password+salt => hash**

实际上你没必要这样去做，因为这并不能帮助你进一步巩固 Hash 的安全性。如果你需要更高的安全性，正确的做法应该去选择一个更好的算法。
中等的密码安全实现使用SHA算法

SHA（安全散列算法）同样是作为加密 Hash 函数家族中的一员。除了它生成的 Hash 安全性比 MD5 更强之外其它都与 MD5 非常类似。然而，它们生成的 Hash 并不总是唯一的，这意味着输入两个不同的值所获的 Hash 却是相同的。通常这种情况的发生我们称之为“碰撞”。 不过，SHA 碰撞的几率小于 MD5。你甚至无需担心碰撞的发生，因为这种情况非常罕见。

在 Java 中有提供有4种 SHA 算法的实现，相对 MD5(128 bit hash) 它提供了以下长度的 Hash：

- SHA-1 (简单实现 – 160 bits Hash)
- SHA-256 (强于 SHA-1 – 256 bits Hash)
- SHA-384 (强于 SHA-256 – 384 bits Hash)
- SHA-512 (强于 SHA-384 – 512 bits Hash)

通常越长的 Hash 越难破解，这是核心思想。

要获取相应算法实现，可通过参数的方式传给 MessageDigest 来获得实例。如下所示：

```java
MessageDigest md = MessageDigest.getInstance("SHA-1");
//OR
MessageDigest md = MessageDigest.getInstance("SHA-256");
```

下面通过测试程序来看 SHA 的应用：

```java
package com.howtodoinjava.hashing.password.demo.sha;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
import java.security.SecureRandom;
public class SHAExample {
    public static void main(String[] args) throws NoSuchAlgorithmException {
        String passwordToHash = "password";
        String salt = getSalt();
        String securePassword = get_SHA_1_SecurePassword(passwordToHash, salt);
        System.out.println(securePassword);
        securePassword = get_SHA_256_SecurePassword(passwordToHash, salt);
        System.out.println(securePassword);
        securePassword = get_SHA_384_SecurePassword(passwordToHash, salt);
        System.out.println(securePassword);
        securePassword = get_SHA_512_SecurePassword(passwordToHash, salt);
        System.out.println(securePassword);
    }
    private static String get_SHA_1_SecurePassword(String passwordToHash, String salt)
    {
        String generatedPassword = null;
        try {
            MessageDigest md = MessageDigest.getInstance("SHA-1");
            md.update(salt.getBytes());
            byte[] bytes = md.digest(passwordToHash.getBytes());
            StringBuilder sb = new StringBuilder();
            for(int i=0; i< bytes.length ;i++)
            {
                sb.append(Integer.toString((bytes[i] & 0xff) + 0x100, 16).substring(1));
            }
            generatedPassword = sb.toString();
        } 
        catch (NoSuchAlgorithmException e) 
        {
            e.printStackTrace();
        }
        return generatedPassword;
    }
    private static String get_SHA_256_SecurePassword(String passwordToHash, String salt)
    {
        //Use MessageDigest md = MessageDigest.getInstance("SHA-256");
    }
    private static String get_SHA_384_SecurePassword(String passwordToHash, String salt)
    {
        //Use MessageDigest md = MessageDigest.getInstance("SHA-384");
    }
    private static String get_SHA_512_SecurePassword(String passwordToHash, String salt)
    {
        //Use MessageDigest md = MessageDigest.getInstance("SHA-512");
    }
    //Add salt
    private static String getSalt() throws NoSuchAlgorithmException
    {
        SecureRandom sr = SecureRandom.getInstance("SHA1PRNG");
        byte[] salt = new byte[16];
        sr.nextBytes(salt);
        return salt.toString();
    }
}
```

如上代码所示，在使用 SHA 的同时同样可以为其加 salt 来加强它的安全性。
较高密码安全实现使用 PBKDF2WithHmacSHA1 算法

到目前为止，我们已经了解如何为密码生成安全的 Hash 值以及通过利用 salt 来加强它的安全性。但今天的问题是，硬件的速度已经远远超过任何使用字典或彩虹表进行的暴力攻击，并且任何密码都能被破解，只是使用时间多少的问题。

为了解决这个问题，主要想法是尽可能降低暴力攻击速度来保证最小化的损失。我们下一个算法同样是基于这个概念。目标是使 Hash 函数足够慢以妨碍攻击，并对用户来说仍然非常快且不会感到有明显的延时。

要达到这个目的通常是使用某些 CPU 密集型算法来实现，比如 PBKDF2, Bcrypt 或 Scrypt 。这些算法采用 work factor(也称之为 security factor)或迭代次数作为参数来确定 Hash 函数将变的有多慢，并且随着日后计算能力的提高，可以逐步增大 work factor 来使之与计算能力达到平衡。

Java 中可通过 "PBKDF2WithHmacSHA1" 来实现"PBKDF2"算法，下面代码是使用示例：

```java
public static void main(String[] args) throws NoSuchAlgorithmException, InvalidKeySpecException 
{
    String  originalPassword = "password";
    String generatedSecuredPasswordHash = generateStorngPasswordHash(originalPassword);
    System.out.println(generatedSecuredPasswordHash);
}
private static String generateStorngPasswordHash(String password) throws NoSuchAlgorithmException, InvalidKeySpecException
{
    int iterations = 1000;
    char[] chars = password.toCharArray();
    byte[] salt = getSalt().getBytes();
    PBEKeySpec spec = new PBEKeySpec(chars, salt, iterations, 64 * 8);
    SecretKeyFactory skf = SecretKeyFactory.getInstance("PBKDF2WithHmacSHA1");
    byte[] hash = skf.generateSecret(spec).getEncoded();
    return iterations + ":" + toHex(salt) + ":" + toHex(hash);
}
private static String getSalt() throws NoSuchAlgorithmException
{
    SecureRandom sr = SecureRandom.getInstance("SHA1PRNG");
    byte[] salt = new byte[16];
    sr.nextBytes(salt);
    return salt.toString();
}
private static String toHex(byte[] array) throws NoSuchAlgorithmException
{
    BigInteger bi = new BigInteger(1, array);
    String hex = bi.toString(16);
    int paddingLength = (array.length * 2) - hex.length();
    if(paddingLength > 0)
    {
        return String.format("%0"  +paddingLength + "d", 0) + hex;
    }else{
        return hex;
    }
}
```

接下来的是当重新回来时，需要提供登录密码验证的方法实现：

```java
public static void main(String[] args) throws NoSuchAlgorithmException, InvalidKeySpecException 
    {
        String  originalPassword = "password";
        String generatedSecuredPasswordHash = generateStorngPasswordHash(originalPassword);
        System.out.println(generatedSecuredPasswordHash);
        boolean matched = validatePassword("password", generatedSecuredPasswordHash);
        System.out.println(matched);
        matched = validatePassword("password1", generatedSecuredPasswordHash);
        System.out.println(matched);
    }
    private static boolean validatePassword(String originalPassword, String storedPassword) throws NoSuchAlgorithmException, InvalidKeySpecException
    {
        String[] parts = storedPassword.split(":");
        int iterations = Integer.parseInt(parts[0]);
        byte[] salt = fromHex(parts[1]);
        byte[] hash = fromHex(parts[2]);
        PBEKeySpec spec = new PBEKeySpec(originalPassword.toCharArray(), salt, iterations, hash.length * 8);
        SecretKeyFactory skf = SecretKeyFactory.getInstance("PBKDF2WithHmacSHA1");
        byte[] testHash = skf.generateSecret(spec).getEncoded();
        int diff = hash.length ^ testHash.length;
        for(int i = 0; i < hash.length && i < testHash.length; i++)
        {
            diff |= hash[i] ^ testHash[i];
        }
        return diff == 0;
    }
    private static byte[] fromHex(String hex) throws NoSuchAlgorithmException
    {
        byte[] bytes = new byte[hex.length() / 2];
        for(int i = 0; i<bytes.length ;i++)
        {
            bytes[i] = (byte)Integer.parseInt(hex.substring(2 * i, 2 * i + 2), 16);
        }
        return bytes;
    }
```

参考以上代码时需注意，如果发现任何问题请下载本文章结尾处附件中的代码。

### 三、更加安全的密码实现使用 bcrypt 和 scrypt 算法

bcrypt 的背后的思想与 PBKDF2 类似。只是 Java 中并没有内置支持使攻击者变慢的 bcrypt 算法实现，但你仍然可以找到并下载它的源码。

下以是 bcrypt 的使用代码示例（其中 Bcrypt.java 已提供在源码中）：

```java
public class BcryptHashingExample 
{
    public static void main(String[] args) throws NoSuchAlgorithmException 
    {
        String  originalPassword = "password";
        String generatedSecuredPasswordHash = BCrypt.hashpw(originalPassword, BCrypt.gensalt(12));
        System.out.println(generatedSecuredPasswordHash);
        boolean matched = BCrypt.checkpw(originalPassword, generatedSecuredPasswordHash);
        System.out.println(matched);
    }
}
```

与 bcrypt 算法类似，我已经从 github 下载了 scrypt 算法的源代码并添加到在最后一节中的源码下载中。看看它是如何使用：

```java
public class ScryptPasswordHashingDemo 
{
    public static void main(String[] args) {
        String originalPassword = "password";
        String generatedSecuredPasswordHash = SCryptUtil.scrypt(originalPassword, 16, 16, 16);
        System.out.println(generatedSecuredPasswordHash);
        boolean matched = SCryptUtil.check("password", generatedSecuredPasswordHash);
        System.out.println(matched);
        matched = SCryptUtil.check("passwordno", generatedSecuredPasswordHash);
        System.out.println(matched);
    }
}
```
### xdlMD5

```java
package com.hjcrm.publics.util;

import java.security.MessageDigest;

/**
 * MD5技术加密解密
 * @author likang
 * @date 2016-10-11 下午5:14:12
 */
public class MD5Tools {

	/**
	 * 
	 * @param src
	 * @return
	 * @author likang 
	 * @date 2016-10-11 下午5:22:44
	 */
	public static String bytesToHexString(byte[] src) {
		StringBuilder stringBuilder = new StringBuilder("");
		if (src == null || src.length <= 0) {
			return null;
		}
		for (int i = 0; i < src.length; i++) {
			int v = src[i] & 0xFF;
			String hv = Integer.toHexString(v);
			if (hv.length() < 2) {
				stringBuilder.append(0);
			}
			stringBuilder.append(hv);
		}
		return stringBuilder.toString();
	}

	/**
	 * 解析
	 * @param hexString
	 * @return
	 * @author likang 
	 * @date 2016-10-11 下午5:15:46
	 */
	public static byte[] hexStringToBytes(String hexString) {
		if (hexString == null || hexString.equals("")) {
			return null;
		}
		hexString = hexString.toUpperCase();
		int length = hexString.length() / 2;
		char[] hexChars = hexString.toCharArray();
		byte[] d = new byte[length];
		for (int i = 0; i < length; i++) {
			int pos = i * 2;
			d[i] = (byte) (charToByte(hexChars[pos]) << 4 | charToByte(hexChars[pos + 1]));
		}
		return d;
	}

	/**
	 * 将指定byte数组以16进制的形式打印到控制台
	 * @param b
	 * @author likang 
	 * @date 2016-10-11 下午5:15:57
	 */
	public static void printHexString(byte[] b) {
		for (int i = 0; i < b.length; i++) {
			String hex = Integer.toHexString(b[i] & 0xFF);
			if (hex.length() == 1) {
				hex = '0' + hex;
			}
			System.out.print(hex.toUpperCase());
		}

	}

	/**
	 * Convert char to byte
	 * @param c char
	 * @return byte
	 */
	private static byte charToByte(char c) {
		return (byte) "0123456789abcdef".indexOf(c);
	}

	/**
	 * 加密
	 * @param str
	 * @return
	 * @author likang 
	 * @date 2016-10-11 下午5:16:12
	 */
	public static String encode(String str) {
		String strDigest = "";
		try {
			// 此 MessageDigest 类为应用程序提供信息摘要算法的功能，必须用try,catch捕获
			MessageDigest md5 = MessageDigest.getInstance("MD5");

			byte[] data;
			data = md5.digest(str.getBytes("utf-8"));// 转换为MD5码
			strDigest = bytesToHexString(data);
		} catch (Exception ex) {
			throw new RuntimeException(ex);
		}
		return strDigest;
	}

}

```

