[TOC]



##ssh整合

**四、项目源码**

###1、db.properties

```properties
db.username=root
db.password=root
db.driverClass=com.mysql.jdbc.Driver
db.jdbcUrl=jdbc:mysql://localhost:3306/ssh_01
db.initialPoolSize=2
db.maxPoolSize=20
```

###2、log4j.properties

```properties
# Global logging configuration
log4j.rootLogger=ERROR, stdout
# MyBatis logging configuration...
log4j.logger.org.mybatis.example.BlogMapper=TRACE
# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
# Start logging
log4j.logger.cn.com.zfc.model.UserMapper=TRACE
```

###3、applicationContext.xml（可以把通用的配置抽取出来，便于管理）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd">

    <!-- 注册数据库链接文件 -->
    <context:property-placeholder location="classpath:db.properties" />

    <!-- 注册C3P0 数据源 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="user" value="${db.username}"></property>
        <property name="password" value="${db.password}"></property>
        <property name="driverClass" value="${db.driverClass}"></property>
        <property name="jdbcUrl" value="${db.jdbcUrl}"></property>
        <property name="initialPoolSize" value="${db.initialPoolSize}"></property>
        <property name="maxPoolSize" value="${db.maxPoolSize}"></property>
    </bean>

    <!-- 注册 LocalSessionFactoryBean,这种配置可以省略 Hibernate 的主配置文件 -->
    <bean id="sessionFactory"
        class="org.springframework.orm.hibernate4.LocalSessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
        <property name="hibernateProperties">
            <props>
                <prop key="hibernate.dialect">org.hibernate.dialect.MySQL5InnoDBDialect</prop>
                <prop key="hibernate.show_sql">true</prop>
                <prop key="hibernate.format_sql">true</prop>
                <prop key="hibernate.hbm2ddl.auto">update</prop>
            </props>
        </property>
        <!-- 配置 hibernate 映射文件的位置及名称,可以直接写目录 -->
        <property name="mappingDirectoryLocations">
            <list>
                <value>classpath:cn/com/zfc/ssh/entity</value>
            </list>
        </property>
    </bean>

    <!-- 注册事务管理器 -->
    <bean id="transactionManager"
        class="org.springframework.orm.hibernate4.HibernateTransactionManager">
        <property name="sessionFactory" ref="sessionFactory"></property>
    </bean>

    <!-- 注册通知,是对 Service 层的方法起作用 -->
    <tx:advice transaction-manager="transactionManager" id="txadvice">
        <tx:attributes>
            <tx:method name="add*" propagation="REQUIRED" />
            <tx:method name="remove*" propagation="REQUIRED" />
            <tx:method name="modify*" propagation="REQUIRED" />
            <tx:method name="find*" read-only="true" />
            <tx:method name="*" read-only="true" />
        </tx:attributes>
    </tx:advice>

    <!-- 注册切面 -->
    <aop:config>
        <!-- Service 层 -->
        <aop:pointcut expression="execution(* *..service.*.*(..))"
            id="txPoint" />
        <aop:advisor advice-ref="txadvice" pointcut-ref="txPoint" />
    </aop:config>

    <!-- 注册 User -->
    <!-- Dao -->
    <bean id="userDao" class="cn.com.zfc.ssh.dao.impl.UserDaoImpl">
        <property name="sessionFactory" ref="sessionFactory"></property>
    </bean>

    <!-- Service -->
    <bean id="userService" class="cn.com.zfc.ssh.service.impl.UserServiceImpl">
        <property name="userDao" ref="userDao"></property>
    </bean>

    <!-- Action -->
    <!-- 跳转到 User 添加的视图的Action -->
    <bean id="userAddViewAction" class="cn.com.zfc.ssh.action.UserAddViewAction"></bean>

    <!-- User 添加的 Action -->
    <bean id="userAddAction" class="cn.com.zfc.ssh.action.UserAddAction">
        <property name="userService" ref="userService"></property>
    </bean>

    <!-- User 查询全部的 Action -->
    <bean id="usersFindAction" class="cn.com.zfc.ssh.action.UsersFindAction">
        <property name="userService" ref="userService"></property>
    </bean>

    <!-- User 删除的 Action -->
    <bean id="userRemoveAction" class="cn.com.zfc.ssh.action.UserRemoveAction">
        <property name="userService" ref="userService"></property>
    </bean>


    <!-- 跳转到 User 修改视图的 Action -->
    <bean id="userModifyViewAction" class="cn.com.zfc.ssh.action.UserModifyViewAction">
        <property name="userService" ref="userService"></property>
    </bean>

    <!-- User 修改的 Action -->
    <bean id="userModifyAction" class="cn.com.zfc.ssh.action.UserModifyAction">
        <property name="userService" ref="userService"></property>
    </bean>
</beans>
```

###4、struts.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
    "http://struts.apache.org/dtds/struts-2.3.dtd">

<struts>
    <!-- 声明对象是由 Spring 创建的 -->
    <constant name="struts.objectFactory" value="spring"></constant>
    
    <!-- 定义包,有关 User 的配置 -->
    <package name="userPackage" extends="struts-default" namespace="/">
        <!-- User 添加 -->
        <action name="userAddViewAction" class="userAddViewAction"
            method="userAddView">
            <result>/user_add.jsp</result>
        </action>

        <!-- 添加页面 -->
        <action name="userAddAction" class="userAddAction" method="addUser">
            <!-- 直接跳转到查询全部 User 的Action -->
            <result type="redirectAction">/usersFindAction</result>
        </action>

        <!-- Users 查询 -->
        <action name="usersFindAction" class="usersFindAction" method="findUsers">
            <result>/users_query.jsp</result>
        </action>

        <!-- User 删除 -->
        <action name="userRemoveAction" class="userRemoveAction"
            method="removeUser">
            <result type="redirectAction">/usersFindAction</result>
        </action>
        <!-- User 修改页面 -->
        <action name="userModifyViewAction" class="userModifyViewAction"
            method="modifyUserView">
            <result>/user-modify.jsp</result>
        </action>

        <!-- User 修改 -->
        <action name="userModifyAction" class="userModifyAction"
            method="modifyUser">
            <result type="redirectAction">/usersFindAction</result>
        </action>

    </package>
</struts>
```

###5、web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://xmlns.jcp.org/xml/ns/javaee" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd" id="WebApp_ID" version="3.1">
  <display-name>SSH</display-name>
  <welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
  </welcome-file-list>
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
  </context-param>
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
  <filter>
    <filter-name>encodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <async-supported>true</async-supported>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>encodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
  <filter>
    <filter-name>struts2</filter-name>
    <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>struts2</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
</web-app>
```

6、User.java

```java
package cn.com.zfc.ssh.entity;

public class User {
    private Integer id;
    private String userName;
    private int userAge;

    public User() {
        super();
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public int getUserAge() {
        return userAge;
    }

    public void setUserAge(int userAge) {
        this.userAge = userAge;
    }

    @Override
    public String toString() {
        return "User [id=" + id + ", userName=" + userName + ", userAge=" + userAge + "]";
    }

}

```

###7、User.hbm.xml

```xml
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">
<!-- Generated 2017-6-9 23:41:28 by Hibernate Tools 3.5.0.Final -->
<hibernate-mapping package="cn.com.zfc.ssh.entity">
    <class name="User" table="USER_INFO">
        <id name="id" type="java.lang.Integer">
            <column name="ID" />
            <!-- 主键生成策略 -->
            <generator class="native" />
        </id>
        <property name="userName" type="java.lang.String">
            <column name="USER_NAME" />
        </property>
        <property name="userAge" type="int">
            <column name="USER_AGE" />
        </property>
    </class>
</hibernate-mapping>
```

8、UserDao.java

```java
package cn.com.zfc.ssh.dao;

import java.util.List;
import cn.com.zfc.ssh.entity.User;
public interface UserDao {
    void insertUser(User user);
    void deleteUser(int id);
    void updateUser(User user);
    User queryUserById(int id);
    List<User> queryUsers();
}
```

9、UserDaoImpl.java

```java
package cn.com.zfc.ssh.dao.impl;

import java.util.List;

import org.hibernate.SessionFactory;

import cn.com.zfc.ssh.dao.UserDao;
import cn.com.zfc.ssh.entity.User;
/**
 * 
 * @title UserDaoImpl
 * @describe User 相关的 Dao 层操作,Session 要与当前线程绑定
 * @author zfc
 * @date 2017年6月10日下午3:31:49
 */
public class UserDaoImpl implements UserDao {

    private SessionFactory sessionFactory;

    public void setSessionFactory(SessionFactory sessionFactory) {
        this.sessionFactory = sessionFactory;
    }

    @Override
    public void insertUser(User user) {
        sessionFactory.getCurrentSession().save(user);
    }

    @Override
    public void deleteUser(int id) {
        User user = (User) sessionFactory.getCurrentSession().get(User.class, id);
        sessionFactory.getCurrentSession().delete(user);

    }

    @Override
    public void updateUser(User user) {
        sessionFactory.getCurrentSession().update(user);

    }

    @Override
    public List<User> queryUsers() {
        String hql = "From User";
        return sessionFactory.getCurrentSession().createQuery(hql).list();
    }

    @Override
    public User queryUserById(int id) {
        return (User) sessionFactory.getCurrentSession().get(User.class, id);
    }
}
```

10、UserService.java

```java
package cn.com.zfc.ssh.service;

import java.util.List;

import cn.com.zfc.ssh.entity.User;

public interface UserService {
    void addUser(User user);

    void removeUser(int id);

    void modifyUser(User user);

    List<User> findUsers();

    User findUserById(int id);
}
```

11、UserServiceImpl.java

```java
package cn.com.zfc.ssh.service.impl;

import java.util.List;

import cn.com.zfc.ssh.dao.UserDao;
import cn.com.zfc.ssh.entity.User;
import cn.com.zfc.ssh.service.UserService;
/**
 * 
 * @title UserServiceImpl
 * @describe 业务逻辑实现
 * @author zfc
 * @date 2017年6月11日下午8:16:50
 */
public class UserServiceImpl implements UserService {

    private UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    @Override
    public void addUser(User user) {
        userDao.insertUser(user);

    }

    @Override
    public void removeUser(int id) {
        userDao.deleteUser(id);

    }

    @Override
    public void modifyUser(User user) {
        userDao.updateUser(user);

    }

    @Override
    public List<User> findUsers() {
        return userDao.queryUsers();
    }

    @Override
    public User findUserById(int id) {
        return userDao.queryUserById(id);
    }

}
```

12、UserAddViewAction.java

```java
package cn.com.zfc.ssh.action;
/**
 * 
 * @title UserAddViewAction
 * @describe User 添加的视图的 Action
 * @author zfc
 * @date 2017年6月10日下午3:29:15
 */
public class UserAddViewAction {

    public String userAddView() {
        return "success";
    }
}
```

13、UserAddAction.java

```java
package cn.com.zfc.ssh.action;

import cn.com.zfc.ssh.entity.User;
import cn.com.zfc.ssh.service.UserService;
/**
 * 
 * @title UserAddAction
 * @describe User 添加的Action
 * @author zfc
 * @date 2017年6月10日下午3:28:57
 */
public class UserAddAction {

    private UserService userService;
    private User user;

    public User getUser() {
        return user;
    }

    public void setUser(User user) {
        this.user = user;
    }

    public void setUserService(UserService userService) {
        this.userService = userService;
    }

    public String addUser() {
        // System.out.println("user:" + user);
        userService.addUser(user);
        return "success";
    }

}
```

14、UsersFindAction.java

```java
 1 package cn.com.zfc.ssh.action;
 2 
 3 import java.util.List;
 4 import java.util.Map;
 5 
 6 import org.apache.struts2.interceptor.RequestAware;
 7 
 8 import cn.com.zfc.ssh.entity.User;
 9 import cn.com.zfc.ssh.service.UserService;
10 /**
11  * 
12  * @title UsersFindAction
13  * @describe User 查询全部的 Action
14  * @author zfc
15  * @date 2017年6月10日下午3:31:07
16  */
17 public class UsersFindAction implements RequestAware {
18 
19     private UserService userService;
20     private Map<String, Object> request;
21 
22     public void setUserService(UserService userService) {
23         this.userService = userService;
24     }
25 
26     @Override
27     public void setRequest(Map<String, Object> request) {
28         this.request = request;
29     }
30 
31     public String findUsers() {
32         // System.out.println("查询全部Users Action");
33         List<User> users = userService.findUsers();
34         request.put("users", users);
35         // System.out.println(userService.findUsers());
36         return "success";
37     }
38 
39 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

15、UserRemoveAction.java

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```java
 1 package cn.com.zfc.ssh.action;
 2 
 3 import cn.com.zfc.ssh.service.UserService;
 4 /**
 5  * 
 6  * @title UserRemoveAction
 7  * @describe User 删除的 Action
 8  * @author zfc
 9  * @date 2017年6月10日下午3:30:50
10  */
11 public class UserRemoveAction {
12     private Integer id;
13 
14     private UserService userService;
15 
16     public UserService getUserService() {
17         return userService;
18     }
19 
20     public void setUserService(UserService userService) {
21         this.userService = userService;
22     }
23 
24     public Integer getId() {
25         return id;
26     }
27 
28     public void setId(Integer id) {
29         this.id = id;
30     }
31 
32     public String removeUser() {
33         userService.removeUser(id);
34         return "success";
35     }
36 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

16、UserModifyViewAction.java

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```java
 1 package cn.com.zfc.ssh.action;
 2 
 3 import java.util.Map;
 4 
 5 import org.apache.struts2.interceptor.RequestAware;
 6 
 7 import cn.com.zfc.ssh.entity.User;
 8 import cn.com.zfc.ssh.service.UserService;
 9 
10 /**
11  * 
12  * @title UserModifyViewAction
13  * @describe 跳转 User 修改视图的 Action
14  * @author zfc
15  * @date 2017年6月10日下午3:29:56
16  */
17 public class UserModifyViewAction implements RequestAware {
18 
19     private Map<String, Object> request;
20     private UserService userService;
21     private Integer id;
22 
23     public void setId(Integer id) {
24         this.id = id;
25     }
26 
27     public void setUserService(UserService userService) {
28         this.userService = userService;
29     }
30 
31     @Override
32     public void setRequest(Map<String, Object> request) {
33         this.request = request;
34     }
35 
36     public String modifyUserView() {
37         User user = userService.findUserById(id);
38         // System.out.println(user);
39         request.put("user", user);
40         return "success";
41     }
42 
43 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

17、UserModifyAction.java

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```java
 1 package cn.com.zfc.ssh.action;
 2 import cn.com.zfc.ssh.entity.User;
 3 import cn.com.zfc.ssh.service.UserService;
 4 /**
 5  * 
 6  * @title UserModifyAction
 7  * @describe User 修改的Action
 8  * @author zfc
 9  * @date 2017年6月10日下午3:29:40
10  */
11 public class UserModifyAction {
12 
13     private User user;
14     private UserService userService;
15 
16     public User getUser() {
17         return user;
18     }
19 
20     public void setUser(User user) {
21         this.user = user;
22     }
23 
24     public UserService getUserService() {
25         return userService;
26     }
27 
28     public void setUserService(UserService userService) {
29         this.userService = userService;
30     }
31 
32     public String modifyUser() {
33         System.out.println(user);
34         userService.modifyUser(user);
35         return "success";
36     }
37 
38 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

18、index.jsp

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```jsp
 1 <%@ page language="java" contentType="text/html; charset=UTF-8"
 2     pageEncoding="UTF-8"%>
 3 <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
 4 <html>
 5 <head>
 6 <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
 7 <title>SSH_01</title>
 8 </head>
 9 <body>
10     <a href="usersFindAction">Users Query</a>
11     <hr />
12     <a href="userAddViewAction">User Add</a>
13 </body>
14 </html>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

19、user_add.jsp

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```jsp
 1 <%@ page language="java" contentType="text/html; charset=UTF-8"
 2     pageEncoding="UTF-8"%>
 3 <%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
 4 <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
 5 <html>
 6 <head>
 7 <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
 8 <title>用户添加</title>
 9 </head>
10 <body>
11     <form action="userAddAction" method="post">
12         UserName:<input type="text" name="user.userName">
13         <hr />
14         UserAge:<input type="text" name="user.userAge">
15         <hr />
16         <input type="submit" value="ADD" />
17     </form>
18     <hr />
19     <a href="usersFindAction">Users Query</a>
20 </body>
21 </html>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

20、users_query.jsp

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```jsp
 1 <%@ page language="java" contentType="text/html; charset=UTF-8"
 2     pageEncoding="UTF-8"%>
 3 <%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
 4 <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
 5 <html>
 6 <head>
 7 <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
 8 <title>用户查询</title>
 9 </head>
10 <body>
11     <table cellspacing="0" cellpadding="0" border="1" width="50%">
12         <thead>
13             <tr>
14                 <th>ID</th>
15                 <th>UserName</th>
16                 <th>UserAge</th>
17                 <th>Operate</th>
18             </tr>
19         </thead>
20         <tbody>
21             <c:forEach items="${users}" var="user">
22                 <tr>
23                     <td>${user.id}</td>
24                     <td>${user.userName}</td>
25                     <td>${user.userAge}</td>
26                     <td><a href="userModifyViewAction?id=${user.id}">Update</a>&amp;<a
27                         href="userRemoveAction?id=${user.id}">Remove</a></td>
28                 </tr>
29             </c:forEach>
30         </tbody>
31     </table>
32     <hr />
33     <a href="userAddViewAction">User Add</a>
34 </body>
35 </html>
```

21、user_modify.jsp

```jsp
 1 <%@ page language="java" contentType="text/html; charset=UTF-8"
 2     pageEncoding="UTF-8"%>
 3 <%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
 4 <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
 5 <html>
 6 <head>
 7 <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
 8 <title>Insert title here</title>
 9 </head>
10 <body>
11     <form action="userModifyAction" method="post">
12         ${user}
13         <!-- 隐藏域,用来保存当前 User 的 id -->
14         <input type="hidden" name="user.id" value="${user.id}" />
15         <hr />
16         UserName:<input type="text" name="user.userName" value="${user.userName}">
17         <hr />
18         UserAge:<input type="text" name="user.userAge" value="${user.userAge}">
19         <hr />
20         <input type="submit" value="Modify" />
21     </form>
22     <hr />
23     <a href="usersFindAction">Users Query</a>
24 </body>
25 </html>
```

