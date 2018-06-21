### AppLoginServiceImpl

```java
/**
     * 登录
     *
     * @param account    可以是账号/手机号
     * @param password   密码不解释
     * @param appCode    租户编码
     * @param userDomain 域
     * @return
     */
String login(String account, String password, String appCode, String userDomain);

/**
     * 根据id获取一个用户token
     *
     * @param userId
     * @param appCode
     * @return
     */
String getTokenById(Long userId, String appCode);

/**
     * 重新加载用户信息
     *
     * @param userId
     * @param appCode
     */
void reloadToken(Long userId, String appCode);
```

#### login

1. 判断参数 不合法  throw   OptimusParamMissException   msg->缺少参数

2. 调AppUserSPI->AppUserService的checkLogin方法校验密码

   1. 失败throw OptimsParamErrorException msg->校验密码出错

      > 打warn日志  msg->用户登录失败

3. 调AppUserMaager的getAppUserLoginContext方法获取用户登录信息

   1. 失败return null

4. 生成token, 调SsoStoreSPI->SsoStoreManager的setContext->set方法设置token

   > 打info日志  msg->用户登录成功获取到,account:{}token:{}

5. 返回token

#### getTokenById

1. 判断参数 不合法  throw   OptimusParamMissException   msg->缺少参数

2. 调AppUserMaager的getAppUserLoginById方法获取用户登录信息

   1. 失败return null

3. 生成token, 调SsoStoreSPI->SsoStoreManager的setContext->set方法设置token

   > 打info日志  msg->用户登录成功获取到,account:{}token:{}

4. 返回token

#### reloadToken

1. 判断参数 不合法  throw   OptimusParamMissException   msg->缺少参数
2. 调AppUserMaager的getAppUserLoginById方法获取用户登录信息
3.  调SsoStoreSPI->SsoStoreManager的reloadContext->getTokenByUserId,get方法生新加载用户信息   注：reloadContext是通过获取到token的appCode

### LoginServiceImpl

#### login

####getToken

### HttpApi

#### getAuthZ

根据token获取登录信息

### LoginAction

#### login

登录接口



```java
/**
     * 登录
     *
     * @param account    可以是账号/手机号/专营店code
     * @param password   密码不解释
     * @param userDomain 域
     * @return
     */
String login(String account, String password, String userDomain);

/**
     * 只有一个用户id获取用户的登录信息
     *
     * @param uid
     * @return
     */
String getToken(Long uid, String appCode);
```

