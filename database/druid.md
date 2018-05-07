[TOC]

## github wiki

https://github.com/alibaba/druid/wiki

# 配置_Druid和Spring关联监控配置

Druid提供了Spring和Jdbc的关联监控。

# 配置spring

com.alibaba.druid.support.spring.stat.DruidStatInterceptor是一个标准的Spring MethodInterceptor。可以灵活进行AOP配置。

Spring AOP的配置文档： <http://static.springsource.org/spring/docs/current/spring-framework-reference/html/aop-api.html>

## 按类型拦截配置

```
  <bean id="druid-stat-interceptor"
  	class="com.alibaba.druid.support.spring.stat.DruidStatInterceptor">
  </bean>
  
  <bean id="druid-type-proxyCreator" class="com.alibaba.druid.support.spring.stat.BeanTypeAutoProxyCreator">
  	<!-- 所有ABCInterface的派生类被拦截监控  -->
  	<property name="targetBeanType" value="xxxx.ABCInterface" />
  	<property name="interceptorNames">
  		<list>
  			<value>druid-stat-interceptor</value>
  		</list>
  	</property>
  </bean>
```

## 方法名正则匹配拦截配置

```
  <bean id="druid-stat-interceptor"
  	class="com.alibaba.druid.support.spring.stat.DruidStatInterceptor">
  </bean>

<bean id="druid-stat-pointcut" class="org.springframework.aop.support.JdkRegexpMethodPointcut"
	scope="prototype">
	<property name="patterns">
		<list>
			<value>com.mycompany.service.*</value>
			<value>com.mycompany.dao.*</value>
		</list>
	</property>
</bean>

<aop:config>
	<aop:advisor advice-ref="druid-stat-interceptor"
		pointcut-ref="druid-stat-pointcut" />
</aop:config>
```

有些情况下，可能你需要配置proxy-target-class，例如：

```
<aop:config proxy-target-class="true">
	<aop:advisor advice-ref="druid-stat-interceptor"
		pointcut-ref="druid-stat-pointcut" />
</aop:config>
```

\##按照BeanId来拦截配置

```
  <bean id="druid-stat-interceptor"
  	class="com.alibaba.druid.support.spring.stat.DruidStatInterceptor">
  </bean>

<bean
	class="org.springframework.aop.framework.autoproxy.BeanNameAutoProxyCreator">
	<property name="proxyTargetClass" value="true" />
	<property name="beanNames">
		<list>
			<!-- 这里配置需要拦截的bean id列表 -->
			<value>xxx-dao</value>
			<value>xxx-service</value>
		</list>
	</property>
	<property name="interceptorNames">
		<list>
			<value>druid-stat-interceptor</value>
		</list>
	</property>
</bean>
```