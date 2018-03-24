thymeleaf-spring配置 Note 2018-03-19T14.57.40
========================
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
		xmlns:jdbc="http://www.springframework.org/schema/jdbc" xmlns:jee="http://www.springframework.org/schema/jee"
		xmlns:tx="http://www.springframework.org/schema/tx" xmlns:aop="http://www.springframework.org/schema/aop"
		xmlns:mvc="http://www.springframework.org/schema/mvc" xmlns:util="http://www.springframework.org/schema/util"
		xmlns:jpa="http://www.springframework.org/schema/data/jpa"
		xsi:schemaLocation="
			http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.1.xsd
			http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.1.xsd
			http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc-4.1.xsd
			http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-4.1.xsd
			http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.1.xsd
			http://www.springframework.org/schema/data/jpa http://www.springframework.org/schema/data/jpa/spring-jpa-1.3.xsd
			http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.1.xsd
			http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.1.xsd
			http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.1.xsd">
		<!-- spring mvc -->
		<mvc:annotation-driven />
		<mvc:default-servlet-handler />
		<!-- viewResolver -->
		<bean id="viewResolver" class="org.thymeleaf.spring4.view.ThymeleafViewResolver">
			<property name="contentType" value="text/html;charset=utf-8" />
			<property name="templateEngine" ref="templateEngine" />
		</bean>
		<bean id="templateEngine" class="org.thymeleaf.spring4.SpringTemplateEngine">
			<property name="enableSpringELCompiler" value="true" />
			<property name="templateResolver" ref="templateResolver"></property>
		</bean>
		<bean id="templateResolver"
			class="org.thymeleaf.spring4.templateresolver.SpringResourceTemplateResolver">
			<property name="prefix" value="/WEB-INF/view/" />
			<property name="suffix" value=".html" />
			<property name="templateMode" value="HTML" />
			<property name="characterEncoding" value="utf-8" />
		</bean>
	</beans>
