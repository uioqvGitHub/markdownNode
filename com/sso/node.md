[TOC]

### sso  构成

1. `redis`中的key   存储一个  `List\<SecurityTokenVO>`

2. token -----> `SecurityTokenVO`     中   `sid`属性   

3. store中    根据token生成的key    存储信息    context

4. context 中应该  包含  数据的存活时间    `maxAge   `， `userId`， `domain`

   若其中没有包含  `maxAge`   或者格式无法转成int   默认存活时间为  86400 * 7

###SsoStoreManage中常用 api

```java
//根据token获取设置刷新 登录信息,   map中需要包含userId, domain
	@MethodDesc("设置一个登录信息")
    void set(String var1, Map<String, String> var2);
//		String userId = context.get("userId");
//		String domain = context.get("domain");

    @MethodDesc("获取一个登录信息, 同时会touch")
    Map<String, String> get(String var1);
    @MethodDesc("刷新一个缓存的有效时间")
    void touch(String var1, int var2);
    @MethodDesc("删除一个登录信息")
    void remove(String var1);
//根据userId
    @MethodDesc("删除用户的所有token")
    void removeUserTokens(String var1);
    @MethodDesc("根据UserId刷新一个")
    void touchByUserId(String var1, int var2);
    @MethodDesc("获取一个用户的所有token")
    List<SecurityTokenVO> getTokenByUserId(String var1);
	
```

> 登录成功加token，

