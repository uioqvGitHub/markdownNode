[TOC]

##SSM整合

​       下面主要介绍三大框架的整合，至于环境的搭建以及项目的创建，参看上面的博文。这次整合我分了 2 个配置文件 ，分别是 spring-mybatis.xml ，包含 spring 和 mybatis 的配置文件，还有个是 spring-mvc 的配置文件，此外有 2 个资源文件 ： jdbc.propertis 和 log4j.properties 。

使用框架都是较新的版本 ：

Spring 4.0.2 RELEASE

Spring MVC 4.0.2 RELEASE

MyBatis 3.2.6

4.2、Spring与MyBatis的整合

所有需要的 JAR 包都引入以后，首先进行 Spring 与 MyBatis 的整合，然后再进行 JUnit 测试

###4.2.1、建立JDBC属性文件

jdbc.properties （文件编码修改为 utf-8 ）

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://IP:8080/dbNAME
username=demao
password=demao
#定义初始连接数
initialSize=0
#定义最大连接数
maxActive=20
#定义最大空闲
maxIdle=20
#定义最小空闲
minIdle=1
#定义最长等待时间
maxWait=60000
```

###4.2.2、建立spring-mybatis.xml配置文件

这个文件就是用来完成spring和mybatis的整合的。这里面也没多少行配置，主要的就是 自动扫描，自动注入，配置数据库 。注释也很详细，大家看看就明白了。

spring-mybatis.xml

```xml
<?xml version="1.0"encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"xmlns:p="http://www.springframework.org/schema/p"
  xmlns:context="http://www.springframework.org/schema/context"
  xmlns:mvc="http://www.springframework.org/schema/mvc"
  xsi:schemaLocation="http://www.springframework.org/schema/beans 
 http://www.springframework.org/schema/beans/spring-beans-3.1.xsd 
 http://www.springframework.org/schema/context 
 http://www.springframework.org/schema/context/spring-context-3.1.xsd 
 http://www.springframework.org/schema/mvc 
 http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd">
  <!-- 自动扫描 -->
  <context:component-scan base-package="com.cn.hnust" />
  <!-- 引入配置文件 -->
  <bean id="propertyConfigurer"    class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    <property name="location"value="classpath:jdbc.properties" />
  </bean>
  <bean id="dataSource"class="org.apache.commons.dbcp.BasicDataSource"
    destroy-method="close">
    <property name="driverClassName"value="${driver}" />
    <property name="url"value="${url}" />
    <property name="username"value="${username}" />
    <property name="password"value="${password}" />
    <!-- 初始化连接大小 -->
    <property name="initialSize"value="${initialSize}"></property>
    <!-- 连接池最大数量 -->
    <property name="maxActive"value="${maxActive}"></property>
    <!-- 连接池最大空闲 -->
    <property name="maxIdle"value="${maxIdle}"></property>
    <!-- 连接池最小空闲 -->
    <property name="minIdle"value="${minIdle}"></property>
    <!-- 获取连接最大等待时间 -->
    <property name="maxWait"value="${maxWait}"></property>
  </bean>
  <!-- spring和MyBatis完美整合，不需要mybatis的配置映射文件 -->
  <bean id="sqlSessionFactory"class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource"ref="dataSource" />
    <!-- 自动扫描mapping.xml文件 -->
    <property name="mapperLocations"value="classpath:com/cn/test/mapping/*.xml"></property>
  </bean>
  <!-- DAO接口所在包名，Spring会自动查找其下的类 -->
  <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage"value="com.cn.test.dao" />
    <property name="sqlSessionFactoryBeanName"value="sqlSessionFactory"></property>
  </bean>
  <!-- (事务管理)transaction manager, use JtaTransactionManager for global tx -->
  <bean id="transactionManager"    class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource"ref="dataSource" />
  </bean>
</beans>
```





###4.2.3、Log4j的配置

​     为了方便调试，一般都会使用日志来输出信息， Log4j是 **Apache** 的一个开放源代码项目，通过使用Log4j，我们可以控制 **日志** 信息输送的目的地是 **控制台** 、文件、**GUI** 组件，甚至是套接口服务器、 **NT** 的事件记录器、 **UNIX**   **Syslog** **守护进程** 等；我们也可以控制每一条日志的输出格式；通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程。

​     Log4j 的配置很简单，而且也是 通用的 ，下面 给出一个基本的配置 ，换到其他项目中也无需做多大的调整 

 

log4j.properties
```properties
#定义LOG输出级别
log4j.rootLogger=INFO,Console,File
#定义日志输出目的地为控制台
log4j.appender.Console=org.apache.log4j.ConsoleAppender
log4j.appender.Console.Target=System.out
#可以灵活地指定日志输出格式，下面一行是指定具体的格式
log4j.appender.Console.layout = org.apache.log4j.PatternLayout
log4j.appender.Console.layout.ConversionPattern=[%c] - %m%n
#文件大小到达指定尺寸的时候产生一个新的文件
log4j.appender.File =org.apache.log4j.RollingFileAppender
#指定输出目录
log4j.appender.File.File = logs/ssm.log
#定义文件最大大小
log4j.appender.File.MaxFileSize = 10MB
# 输出所以日志，如果换成DEBUG表示输出DEBUG以上级别日志
log4j.appender.File.Threshold = ALL
log4j.appender.File.layout =org.apache.log4j.PatternLayout
log4j.appender.File.layout.ConversionPattern =[%p] [%d{yyyy-MM-ddHH\:mm\:ss}][%c]%m%n
```
4.2.4、JUnit测试

​     经过以上步骤（到4.2.2，log4j不配也没影响），我们 已经完成了Spring和mybatis的整合 ，这样我们就可以编写一段测试代码来试试是否成功了。

**4.2.4.1、创建测试用表**

既然我们需要测试，那么我们就需要建立在数据库中建立一个测试表，这个表建的很简单， SQL 语句为：

```sql
DROP TABLE IF EXISTS user_t;
CREATE TABLE user_t (

  id int(11) NOT NULL AUTO_INCREMENT,

  user_name varchar(40) NOT NULL,

  password varchar(255) NOT NULL,

  age int(4) NOT NULL,

  PRIMARY KEY(id)

) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;

insert  into user_t(id,user_name,password,age) values (1,'测试','sfasgfaf',24);
```

**4.2.4.2、利用MyBatisGenerator自动创建代码**

 逆向工程

​      这个可根据表自动创建实体类、 MyBatis 映射文件以及 DAO 接口，当然，我 习惯将生成的接口名改为 IUserDao ，而不是直接用它生成的 UserMapper 。如果不想麻烦就 可以不改 。完成后将文件复制到工程中。

**4.2.4.3、建立Service接口和实现类**

下面给出具体的内容：

IUserService.java

```java
package com.cn.test.service;
import com.cn.test.pojo.User;
public interface IUserService {
  publicUser getUserById(int userId);
}
```
UserServiceImpl.java
```java
package com.cn.test.service.impl;

import javax.annotation.Resource;
import org.springframework.stereotype.Service;
import com.cn.test.dao.IUserDao;
import com.cn.test.pojo.User;
import com.cn.test.service.IUserService;

@Service("userService")
public class UserServiceImpl implements IUserService {
  @Resource
  privateIUserDao userDao;
  @Override
  publicUser getUserById(int userId) {
    // TODO Auto-generated method stub
    return this.userDao.selectByPrimaryKey(userId);
  }
}
```



**4.2.4.4、建立测试类**

​      测试类 在src/test/java中建立 ，下面测试类中 注释掉的部分是不使用 Spring 时，一般情况下的一种测试方法 ；如果使用了 Spring 那么就可以使用注解的方式来引入配置文件和类，然后再将 service 接口对象注入，就可以进行测试了。

​      如果测试成功，表示 Spring 和 Mybatis 已经整合成功了 。输出信息使用的是 Log4j 打印到控制台。

```java
package org.zsl.testmybatis; 

import javax.annotation.Resource;
import org.apache.log4j.Logger;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import com.alibaba.fastjson.JSON;
import com.cn.test.pojo.User;
import com.cn.test.service.IUserService; 

@RunWith(SpringJUnit4ClassRunner.class)         
//表示继承了SpringJUnit4ClassRunner类
@ContextConfiguration(locations = {"classpath:spring-mybatis.xml"})
public class TestMyBatis {
  private staticLogger logger = Logger.getLogger(TestMyBatis.class);
//       privateApplicationContext ac = null;
  @Resource
  privateIUserService userService = null;
//       @Before
//       public voidbefore() {
//               ac= new ClassPathXmlApplicationContext("applicationContext.xml");
//               userService= (IUserService) ac.getBean("userService");
//       }

  @Test
  public voidtest1() {
    User user =userService.getUserById(1);
    //System.out.println(user.getUserName());
    // logger.info("值："+user.getUserName());
   	logger.info(JSON.toJSONString(user));
  }
}

```



至此， 完成Spring和mybatis这两大框架的整合 ，下面在继续进行SpringMVC的整合。

4.3、整合SpringMVC

上面已经完成了2大框架的整合，SpringMVC的配置文件单独放，然后在web.xml中配置整合。

###4.3.1、配置spring-mvc.xml

配置里面的注释也很详细，在此就不说了，主要是 自动扫描控制器，视图模式，注解的启动 这三个。

```xml
<?xml version="1.0"encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"xmlns:p="http://www.springframework.org/schema/p"
  xmlns:context="http://www.springframework.org/schema/context"
  xmlns:mvc="http://www.springframework.org/schema/mvc"
  xsi:schemaLocation="http://www.springframework.org/schema/beans 
 http://www.springframework.org/schema/beans/spring-beans-3.1.xsd 
 http://www.springframework.org/schema/context 
 http://www.springframework.org/schema/context/spring-context-3.1.xsd 
 http://www.springframework.org/schema/mvc 
 http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd">
  <!-- 自动扫描该包，使SpringMVC认为包下用了@controller注解的类是控制器 -->
  <context:component-scan base-package="com.cn.test.controller" />
  <!--避免IE执行AJAX时，返回JSON出现下载文件 -->
  <bean id="mappingJacksonHttpMessageConverter"
    class="org.springframework.http.converter.json.MappingJacksonHttpMessageConverter">
    <property name="supportedMediaTypes">
      <list>
        <value>text/html;charset=UTF-8</value>
      </list>
    </property>
  </bean>
  <!-- 启动SpringMVC的注解功能，完成请求和注解POJO的映射 -->
  <bean   class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter">
    <property name="messageConverters">
      <list>
        <ref bean="mappingJacksonHttpMessageConverter" />     <!-- JSON转换器 -->
      </list>
    </property>
  </bean>
  <!-- 定义跳转的文件的前后缀，视图模式配置-->
  <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <!-- 这里的配置我的理解是自动给后面action的方法return的字符串加上前缀和后缀，变成一个可用的url地址 -->
    <property name="prefix"value="/WEB-INF/jsp/" />
    <property name="suffix"value=".jsp" />
  </bean>
  <!-- 配置文件上传，如果没有使用文件上传可以不用配置，当然如果不配，那么配置文件中也不必引入上传组件包 -->
  <bean id="multipartResolver" 
        class="org.springframework.web.multipart.commons.CommonsMultipartResolver"> 
        <!-- 默认编码 -->
        <property name="defaultEncoding"value="utf-8" /> 
        <!-- 文件大小最大值 -->
        <property name="maxUploadSize"value="10485760000" /> 
        <!-- 内存中的最大值 -->
        <property name="maxInMemorySize"value="40960" /> 
    </bean>
</beans>

```



###4.3.2、配置web.xml文件

这里面对spring-mybatis.xml的引入以及配置的spring-mvc的Servlet就是为了完成SSM整合，之前2框架整合不需要在此处进行任何配置。配置一样有详细注释，不多解释了。

web.xml

```xml
<?xml version="1.0"encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://java.sun.com/xml/ns/javaee"
  xsi:schemaLocation="http://java.sun.com/xml/ns/javaeehttp://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
  version="3.0">
  <display-name>Archetype Created Web Application</display-name>
  <!-- Spring和mybatis的配置文件 -->
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring-mybatis.xml</param-value>
  </context-param>
  <!-- 编码过滤器 -->
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
  <!-- Spring监听器 -->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
  <!-- 防止Spring内存溢出监听器 -->
  <listener>
    <listener-class>org.springframework.web.util.IntrospectorCleanupListener</listener-class>
  </listener> 
  <!-- Spring MVC servlet -->
  <servlet>
    <servlet-name>SpringMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:spring-mvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
    <async-supported>true</async-supported>
  </servlet>
  <servlet-mapping>
    <servlet-name>SpringMVC</servlet-name>
    <!-- 此处可以可以配置成\*.do，对应struts的后缀习惯 -->
    <url-pattern>/</url-pattern>
  </servlet-mapping>
  <welcome-file-list>
    <welcome-file>/index.jsp</welcome-file>
  </welcome-file-list>
</web-app>
```

4.3.3、测试

至此已经完成了SSM三大框架的整合了，接下来测试一下，如果成功了，那么恭喜你，如果失败了，继续调试吧， 作为程序员就是不停的与BUG做斗争 ！

**4.3.3.1、新建jsp页面**

showUser.jsp  此页面仅输出一下用户名，完成一个 完整的简单流程 。

```html
<%@ page language="java" import="java.util.*" pageEncoding="utf-8"%>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01Transitional//EN">
<html>
  <head>
      <title>测试</title>
  </head>
  <body>
   ${user.userName}
  </body>
</html>
```

**4.3.3.2、建立UserController类**

UserController.java  控制器

```java
package com.cn.test.controller; 

import javax.annotation.Resource;
import javax.servlet.http.HttpServletRequest;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import com.cn.test.pojo.User;
import com.cn.test.service.IUserService; 

@Controller
@RequestMapping("/user")
public class UserController {
  @Resource
  privateIUserService userService;

  @RequestMapping("/showUser")
  publicString toIndex(HttpServletRequest request,Model model){
    intuserId = Integer.parseInt(request.getParameter("id"));
    User user = this.userService.getUserById(userId);
   model.addAttribute("user", user);
    return "showUser";
  }
}
```

**4.3.3.3、部署项目**

输入地址： localhost:8080/ 项目名称 /user/showUser?id=1

至此，SSM三大框架的整合就完成了，在此基础上可再添加其他功能。