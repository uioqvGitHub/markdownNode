[TOC]

### spring core配置文件 


```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:task="http://www.springframework.org/schema/task"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
						http://www.springframework.org/schema/beans/spring-beans-3.0.xsd 
						http://www.springframework.org/schema/tx 
						http://www.springframework.org/schema/tx/spring-tx-3.0.xsd 
						http://www.springframework.org/schema/context 
						http://www.springframework.org/schema/context/spring-context-3.0.xsd
						http://www.springframework.org/schema/mvc
						http://www.springframework.org/schema/mvc/spring-mvc-3.0.xsd
						http://www.springframework.org/schema/task  
						http://www.springframework.org/schema/task/spring-task-3.0.xsd
						http://www.springframework.org/schema/aop  
           				http://www.springframework.org/schema/aop/spring-aop-3.0.xsd">
	<!-- 引入jdbc配置文件 -->
	<bean id="propertyConfigurer"
		class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
		<property name="locations">
			<list>
				<value>classpath:config.properties</value>
				<value>classpath:jdbc.properties</value>
			</list>
		</property>
	</bean>

	<context:component-scan base-package="com.hjcrm">
		<!--将Controller的注解排除掉 -->
		<context:exclude-filter type="annotation"
			expression="org.springframework.stereotype.Controller" />
	</context:component-scan>


	<task:annotation-driven />

	<!--创建jdbc数据源 -->
	<bean id="dataSource"
		class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
		<property name="driverClassName" value="${driver}" />
		<property name="url" value="${url}" />
		<property name="username" value="${username}" />
		<property name="password" value="${password}" />
		<!-- 连接池启动时的初始值 -->
		<property name="initialSize" value="30" />
		<!-- 连接池的最大值 -->
		<property name="maxActive" value="1000" />
		<!-- 最大空闲值.当经过一个高峰时间后，连接池可以慢慢将已经用不到的连接慢慢释放一部分，一直减少到maxIdle为止 -->
		<property name="maxIdle" value="50" />
		<!-- 最小空闲值.当空闲的连接数少于阀值时，连接池就会预申请去一些连接，以免洪峰来时来不及申请 -->
		<property name="minIdle" value="30" />
		<!--#给出一条简单的sql语句进行验证 -->
		<property name="validationQuery" value="select 1" />
		<!--#在取出连接时进行有效验证 -->
		<property name="testOnBorrow" value="true" />
		<property name="removeAbandonedTimeout" value="120" />
		<property name="removeAbandoned" value="true" />
		<!-- #运行判断连接超时任务的时间间隔，单位为毫秒，默认为-1，即不执行任务。 -->
		<property name="timeBetweenEvictionRunsMillis"
			value="3600000" />
		<!-- #连接的超时时间，默认为半小时。 -->
		<property name="minEvictableIdleTimeMillis" value="3600000" />
	</bean>

	<!-- (事务管理)transaction manager, use JtaTransactionManager for global tx -->
	<bean id="transactionManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource" />
	</bean>

	<tx:annotation-driven
		transaction-manager="transactionManager" />

	<!-- 以下两个类为配置表服务. -->
	<bean id="cfgSqlSessionFactory"
		class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="configLocation"
			value="classpath:mybatis-config.xml" />
		<property name="dataSource" ref="dataSource" />
	</bean>

	<bean id="cfgDataAccess"
		class="com.hjcrm.publics.dao.DataAccessImpl">

		<constructor-arg index="0" ref="cfgSqlSessionFactory" />
	</bean>

	<bean id="springUtil" class="com.hjcrm.publics.util.ContextUtil"
		depends-on="cfgDataAccess">
		<property name="configPath"
			value="config.properties,jdbc.properties" />
	</bean>

	<!-- <import resource="classpath:applicationContext-ehcache.xml"/> <import 
		resource="classpath:applicationContext-quartz.xml"/> -->

</beans>  
```

### spring ehcache配置文件 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:oxm="http://www.springframework.org/schema/oxm"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:cache="http://www.springframework.org/schema/cache"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc   
        http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd    
        http://www.springframework.org/schema/beans   
        http://www.springframework.org/schema/beans/spring-beans-3.2.xsd    
        http://www.springframework.org/schema/context   
        http://www.springframework.org/schema/context/spring-context-3.2.xsd  
        http://www.springframework.org/schema/cache   
        http://www.springframework.org/schema/cache/spring-cache.xsd">



	<!-- 缓存 属性 -->
	<bean id="cacheManagerFactory"
		class="org.springframework.cache.ehcache.EhCacheManagerFactoryBean">
		<property name="configLocation" value="classpath:ehcache.xml" />
	</bean>

	<!-- 支持缓存注解 -->
	<cache:annotation-driven
		cache-manager="cacheManager" />

	<!-- 默认是cacheManager -->
	<bean id="cacheManager"
		class="org.springframework.cache.ehcache.EhCacheCacheManager">
		<property name="cacheManager" ref="cacheManagerFactory" />
	</bean>
</beans>   
```

### spring quartz配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
	http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
        
        <!-- 定时  -->
        <bean id="nextVisitMessageJob" class="com.hjcrm.publics.task.NextVisitMessageJob"></bean>
        
        <bean id="nextVisitMessageTask" class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
            <property name="targetObject">
                <ref bean="nextVisitMessageJob"/>
            </property>
            <property name="targetMethod">
                <value>nextVisitMessage</value>
            </property>
        </bean>
        <bean id="nextVisitMessageJobTrigger" class="org.springframework.scheduling.quartz.CronTriggerBean">
            <property name="jobDetail">
                <ref bean="nextVisitMessageTask"/>
            </property>
            <property name="cronExpression">
                <value>0/1 * * * * ?</value>
            </property>
        </bean>
        
        
          <!-- 定时  -->
        <bean id="nextVisitMessageJob1" class="com.hjcrm.publics.task.NextVisitMessageJob1"></bean>
        
        <bean id="nextVisitMessageTask1" class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
            <property name="targetObject">
                <ref bean="nextVisitMessageJob1"/>
            </property>
            <property name="targetMethod">
                <value>demo09Job</value>
            </property>
            
        </bean>
        <bean id="nextVisitMessageJobTrigger1" class="org.springframework.scheduling.quartz.CronTriggerBean">
            <property name="jobDetail">
                <ref bean="nextVisitMessageTask1"/>
            </property>
            <property name="cronExpression">
                <value>0/1 * * * * ?</value>
            </property>
        </bean>
        
        
        
        <!-- 总管理类 如果将lazy-init='false'那么容器启动就会执行调度程序  -->
        <bean class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
            <property name="triggers">
                <list>
                    <!-- <ref bean="nextVisitMessageJobTrigger"/>  -->
                     <ref bean="nextVisitMessageJobTrigger1"/> 
                </list>
            </property>
        </bean>
<!-- 
关于cronExpression的介绍:
s m h d m w(?) y(?) ,分别对应: 秒>分>小时>日>月>周>年
字段 允许值 允许的特殊字符
秒 	  	0-59 	  	, - * /
分 	  	0-59 	  	, - * /
小时 	  	0-23 	  	, - * /
日期 	  	1-31 	  	, - * ? / L W C
月份 	  	1-12 或者 JAN-DEC 	  	, - * /
星期 	  	1-7 或者 SUN-SAT 	  	, - * ? / L C #
年（可选） 	  	留空, 1970-2099 	  	, - * /

表达式
 意义
"0 0/1 * * * ?"     每1分钟触发
"0 0 12 * * ?" 	  	每天中午12点触发
"0 15 10 ? * *" 	  	每天上午10:15触发
"0 15 10 * * ?" 	  	每天上午10:15触发
"0 15 10 * * ? *" 	  	每天上午10:15触发
"0 15 10 * * ? 2005" 	  	2005年的每天上午10:15触发
"0 * 14 * * ?" 	  	在每天下午2点到下午2:59期间的每1分钟触发
"0 0/5 14 * * ?" 	  	在每天下午2点到下午2:55期间的每5分钟触发
"0 0/5 14,18 * * ?" 	  	在每天下午2点到2:55期间和下午6点到6:55期间的每5分钟触发
"0 0-5 14 * * ?" 	  	在每天下午2点到下午2:05期间的每1分钟触发
"0 10,44 14 ? 3 WED" 	  	每年三月的星期三的下午2:10和2:44触发
"0 15 10 ? * MON-FRI" 	  	周一至周五的上午10:15触发
"0 15 10 15 * ?" 	  	每月15日上午10:15触发
"0 15 10 L * ?" 	  	每月最后一日的上午10:15触发
"0 15 10 ? * 6L" 	  	每月的最后一个星期五上午10:15触发 
"0 15 10 ? * 6L 2002-2005" 	  	2002年至2005年的每月的最后一个星期五上午10:15触发
"0 15 10 ? * 6#3" 	  	每月的第三个星期五上午10:15触发
每天早上6点

0 6 * * * 

每两个小时

0 */2 * * *
晚上11点到早上8点之间每两个小时，早上八点

0 23-7/2，8 * * *

每个月的4号和每个礼拜的礼拜一到礼拜三的早上11点

0 11 4 * 1-3
1月1日早上4点

0 4 1 1 *
 -->
</beans>
```

### spring mvc 配置文件 

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:aop="http://www.springframework.org/schema/aop" 
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
	http://www.springframework.org/schema/beans/spring-beans-3.0.xsd 
	http://www.springframework.org/schema/context 
	http://www.springframework.org/schema/context/spring-context-3.0.xsd 
	http://www.springframework.org/schema/mvc 
	http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd
	http://www.springframework.org/schema/aop  
    http://www.springframework.org/schema/aop/spring-aop-3.0.xsd">

	<!-- 使用注解的包，包括子集 -->
    <context:component-scan base-package="com.hjcrm.**.controller" >  
		<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Service" />  
	</context:component-scan>
	
  <!-- 	<bean id="logService" class="com.hjcrm.publics.ipAndOperation.controller.LogService"></bean> 
	<aop:aspectj-autoproxy proxy-target-class="true"/>  -->
	
	<!-- 访问国际化信息的MessageSource接口 -->
	<bean id="messageSource"
		class="org.springframework.context.support.ResourceBundleMessageSource">
		<property name="basename" value="messages" />
	</bean>

	<!-- 视图解析器 -->
	<bean id="viewResolver"
		class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="viewClass"
			value="org.springframework.web.servlet.view.JstlView" />
		<property name="prefix" value="/WEB-INF/jsp/" />
		<property name="suffix" value=".jsp"></property>
	</bean>

	<!-- MultipartFile来进行文件上传 -->
	<bean id="multipartResolver"
		class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
		<property name="maxUploadSize" value="10240000000" />
		<property name="defaultEncoding" value="UTF-8"></property>  
	</bean>
	
	<!-- <bean id="logService" class="com.hjcrm.publics.logs.controller.LogController"></bean> 
	<aop:aspectj-autoproxy proxy-target-class="true"/>  -->

	<mvc:interceptors>
		<mvc:interceptor>
			<mvc:mapping path="/**/*.do" />
			<bean class="com.hjcrm.publics.interceptorfiles.BackgroundLoginInterceptor"></bean>
		</mvc:interceptor>
		<!-- <mvc:interceptor>
			<mvc:mapping path="/auth/**/*.htm"/>
			<bean class="com.hjcrm.publics.interceptorfiles.FrontLoginInterceptor"></bean>
		</mvc:interceptor> -->
	</mvc:interceptors>

	<!-- 处理请求映射,启用注解驱动 -->
	<mvc:annotation-driven>
		<mvc:message-converters register-defaults="true">
			<bean class="org.springframework.http.converter.StringHttpMessageConverter">
				<property name="supportedMediaTypes">
					<list>
						<value>text/plain;charset=UTF-8</value>
						<value>text/html;charset=UTF-8</value>
						<value>application/json;charset=UTF-8</value>
					</list>
				</property>
			</bean>
		</mvc:message-converters>
	</mvc:annotation-driven>

</beans>  
```

