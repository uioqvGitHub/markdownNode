---
typora-root-url: ../../
---

[TOC]



SpringBoot Note 2018-03-19T08.40.20
========================





### 简介

主要用于快速开发 Spring应用程序。

	-	SpringBoot对Spring框架做了封装
	-	SpringBoot内置Tomcat服务器
	-	SpringBoot具有自动配置功能， 去除XML配置， 安全采用Java(注解)配置
	-   SpringBoot内置自动创建很多对象
	-   SpringBoot提供了一系列的工具集合

包|包名
--- |	---
基础包|spring-boot-starter-parent
ioc包|spring-boot-starter
MVC|spring-boot-starter-web
DAO|spring-boot-starter-jdbc
AOP|spring-boot-starter-aop
mybatis|mybatis-spring-boot-starter

### springboot parent pom.xml

	<parent>
	  <groupId>org.springframework.boot</groupId>
	  <artifactId>spring-boot-starter-parent</artifactId>
	  <version>1.5.10.RELEASE</version>
	</parent>

### @SpringBootApplication 注解 

@SpringBootApplication是一个标记集合，其包含

1. @SpringBootConfiguration -> Configuration -> @ Component
2. @EnableAutoConfiguration
3. @ComponentScan

### @EnableAutoConfiguration 注解

SpringBoot提供了大量组件， 组件采用**@Configuration+@Bean**标记。自动配置组件在**spring-boot-autoconfigure-1.4.7.RELEASE.jar的META-INF/spring.factories**定义， SpringBoot启动时会自动加载定义的@Configuration组件，在Spring容器中产生组件对象

### 如何使用DataSource连接池

1. 添加连接池开发包、驱动包、 SpringBoot基础包

		<parent>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-starter-parent</artifactId>
				<version>1.5.10.RELEASE</version>
		</parent>
		<dependencies>
			<dependency>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-starter</artifactId>
			</dependency>
			<!-- dataSource(tomcat), jdbcTemplate -->
			<dependency>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-starter-jdbc</artifactId>
			</dependency>
			<dependency>
				<groupId>com.oracle</groupId>
				<artifactId>ojdbc</artifactId>
				<version>6</version>
			</dependency>
		</dependencies>
	- 遇到maven添加ojdbc依赖问题，使用如下命令将ojdbc6.js添加到maven的本地仓库

				mvn install:install-file 
					-DgroupId=com.oracle
					-DartifactId=ojdbc
					-Dversion=6
				 	-Dpackaging=jar
					-Dfile=ojdbc6.jar
2. 在application.yml中配置连接池参数

		spring:
		 datasource:
		  username: scott
		  password: tiger
		  url: jdbc:oracle:thin:@localhost:1521:XE 
		  driverClassName:oracle.jdbc.driver.OracleDriver
3. 开启@SpringBootApplication标记创建Spring容器,获取DataSource对象(id为dataSource)

		@SpringBootApplication
		public class MyBootApplication {

		}

#### SpringBoot默认连接池原理
SpringBoot利用DataSourceAutoConfiguration创建出id=dataSource的连接池对象。内部创建优先级为：

- 首先会尝试创建tomcat连接池(tomcat-jdbc包)

		spring-boot-start-jdbc引入后会自动包含tomcat-jdbc包

- 如果创建tomcat-jdbc失败，会创建hiKariCP连接池(hikaricp包)
- 如果创建HikariCP失败，会创建dbcp连接池（commons-dbcp包)
- 如果创建dbcp失败,会创建dbcp2连接池(commons-dbcp2包)

如果需要创建指定类型的连接池，可以采用下面方法：

- 引入选用 的连接池的jar包

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jdbc</artifactId>
			<!--  -->
			<exclusions>
				<exclusion>
					<groupId>org.apache.tomcat</groupId>
					<artifactId>tomcat-jdbc</artifactId>
				</exclusion>
			</exclusions>
					
		</dependency>
		<dependency>
			<groupId>commons-dbcp</groupId>
			<artifactId>commons-dbcp</artifactId>
		</dependency>

- 在application.yml 添加指定类型连接池(指定连接池类型或者将spring-boot-starter-jdbc中tomcat-jdbc排除)
	
		spring.datasource.type: org.apache.commons.dbcp.BasicDataSource

方法二： 利用@Configuration+@Bean自定义组件

	package cn.xdl.config;
	
	import javax.sql.DataSource;
	import org.apache.commons.dbcp.BasicDataSource;
	import org.springframework.context.annotation.Bean;
	import org.springframework.context.annotation.Configuration;
	
	@Configuration
	public class MyDataSourceConfig {
	
	    @Bean
	    public DataSource myDataSource(){
	        BasicDataSource ds = new BasicDataSource();
	        ds.setUsername("SCOTT");
	        ds.setPassword("TIGER");
	        ds.setUrl("jdbc:oracle:thin:@localhost:1521:XE");
	        ds.setDriverClassName("oracle.jdbc.OracleDriver");
	        return ds;
	    }
	
	}

> 如果指定了自定义的连接池，系统自动配置将失败


### 配置参数注入

在SpringBoot中自动配置组件ConfigurationPropertiesAutoConfiguration,可以将Properties文件中的配置参数注入到Bean对象属性中。

- application.yml
		
		spring.name=SCOTT
		spring.password=tiger

- 利用@ConfigurationPreperties注入参数
	
		@Configuration
		@ConfigurationProperties(prefix="spring")
		public class MyDataSourceConfig {
			private String name;
			private String password;
		}
	
> 如果参数名和属性不一致，可以使用@Value("${xxx}")表达式指定注入

- 也可以将@ConfigurationProperties用在@Bean方法上，表示给@Bean对象属性注入参数
	
		@Bean
		@ConfigurationProperties(prefix="spring")
		public DataSource myDataSource() {
			System.out.println("-----" + name);
			System.out.println("-------" + password);
			...
			return ds;
		}

### SpringBoot中使用JdbcTemplate
JdbcTemplate也是JdbcTemplateAutoConfiguration自动配置组件创建。在应用中，只需要提供连接参数、jar包,然后直接编写实体类、DAO，注入JdbcTemplate使用

1.	配置DataSource连接池
	-	在pom.xml引入jar包定义
			在application.yml定义数据库链接参数
			编写实体类

		Dept.java,属性名和类型与数据表保持一致

			编写DAO接口和实现类

		扫描DAO实现类，并且注入JdbcTemplate使用。

### SpringBoot中使用mybatis
1. 搭建SpringBoot+MyBatis环境

	- 在pom.xml中添加mybatis-spring-boot-start定义和DataSource
	
			<dependency>
				<groupId>org.mybatis.spring.boot</groupId>
				<artifactId>mybatis-spring-boot-starter</artifactId>
				<version>1.3.2</version>
			</dependency>

2. 编写实体类

3. sql定义文件
	
		<?xml version="1.0" encoding="UTF-8" ?>  
		<!DOCTYPE mapper PUBLIC "-//ibatis.apache.org//DTD Mapper 3.0//EN"      
		 "http://ibatis.apache.org/dtd/ibatis-3-mapper.dtd">
		 
		<mapper namespace="com.uioqv.mapper.EmpMapper">
			<!-- 定义SQL语句 -->	
		    <select id="findAll" 
		      resultType="com.uioqv.entity.Emp">
		         select * from dept where deptno = #{no}
		    </select>
		</mapper>

4. 编写Mapper映射接口

		package com.uioqv.mapper;
		
		import java.util.List;
		
		import com.uioqv.entity.Emp;
		
		public interface EmpMapper {
			List<Emp> findAll();
		}


5. 启动类添加@MapperScan

		@MapperScan(basePackages="com.uioqv.mapper")

6. application中添加mybatis.mapper-locations定义

		mybatis.mapper-locations: classpath:sql/*.xml

### SpringBoot MVC应用
#### 开发Web服务（Restful服务）


1.	在pom.xml添加spring-boot-starter-web定义

		<dependency>
		    <groupId>org.springframework.boot</groupId>
		    <artifactId>spring-boot-starter-web</artifactId>
		</dependency>


2.	编写DeptController，利用@RestController、@RequestMapping标记

		@RestController//@Controller+@ResponseBody
		public class DeptController {
		
		    @Autowired
		    private DeptDao deptDao;
		
		    @RequestMapping(value="/dept",method=RequestMethod.GET)
		    public List<Dept> loadAll(){
		        return deptDao.loadAll();
		    }
		
		    @RequestMapping(value="/dept/{id}",method=RequestMethod.GET)
		    public MyResult loadDept(@PathVariable("id")int no){
		        MyResult result = new MyResult();
		        Dept dept = deptDao.loadById(no);
		        if(dept == null){
		            result.setStatus(0);
		            result.setMsg("未找到符合条件记录");
		        }else{
		            result.setStatus(1);
		            result.setMsg("查询成功");
		            result.setData(dept);
		        } 
		        return result;
		    }
		
		}

	.	定义共同的返回对象MyResult.java

		public class MyResult implements Serializable{
		
		    private int status;//处理结果状态
		    private String msg;//提示信息
		    private Object data;//返回的数据
		
		    //省略了set和get方法
		}

#### 开发JSP应用（PC网站应用）

/dept/list.do-->DispatcherServlet-->HandlerMapping-->ListController-->DeptDao -->ModelAndView-->ViewResolver-->/list.jsp

1.	编写ListController

	    @Controller
	    public class ListController {
	
	        @Autowired
	        private DeptDao deptDao;
	    
	        @RequestMapping("/dept/list.do")
	        public ModelAndView list(){
	            List<Dept> list = deptDao.loadAll();
	            ModelAndView mav = new ModelAndView();
	            mav.setViewName("list");
	            mav.getModel().put("depts", list);
	            return mav;
	        }
	    
	    }

	.	在application.properties添加view参数配置

	    spring.mvc.view.prefix=/
	    spring.mvc.view.suffix=.jsp

	.	在pom.xml中追加jstl和tomcat-embed-jasper引擎

	    <dependency>
	      <groupId>jstl</groupId>
	      <artifactId>jstl</artifactId>
	      <version>1.2</version>
	    </dependency>
	    
	    <dependency>
	      <groupId>org.apache.tomcat.embed</groupId>
	      <artifactId>tomcat-embed-jasper</artifactId>
	    </dependency>

	： 在配置时出现No Java compiler available异常，	原因是tomcat-embed-jasper所依赖的ecj版本不正确导致，
将ecj修改为最高版本问题解决。

			<dependency>
				<groupId>org.apache.tomcat.embed</groupId>
				<artifactId>tomcat-embed-jasper</artifactId>
				<exclusions>
					<exclusion>
						<groupId>org.eclipse.jdt</groupId>
						<artifactId>ecj</artifactId>
					</exclusion>
				</exclusions>
			</dependency>
			<dependency>
				<groupId>org.eclipse.jdt.core.compiler</groupId>
				<artifactId>ecj</artifactId>
				<version>4.6.1</version>
			</dependency>

	.	编写list.jsp，使用JSTL和EL表达式显示

		<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
		<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
		<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
		<html>
		<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<title>Insert title here</title>
		</head>
		<body>
		    <h1>部门列表</h1>
		    <table>
		        <tr>
		            <td>编号</td>
		            <td>部门名</td>
		            <td>地址</td>
		        </tr>
		        <c:forEach items="${depts}" var="dept">
		        <tr>
		            <td>${dept.deptno}</td>
		            <td>${dept.dname}</td>
		            <td>${dept.loc}</td>
		        </tr>
		        </c:forEach>
		    </table>
		</body>

#### 开发thymeleaf模板应用（PC网站应用）

模板技术：velocity、freemarker、thymeleaf等。

JSP--》转译成Servlet--》编译Servlet--》运行--》生成HTML响应输出

模板文件+模板表达式（提取模型数据）--》生成HTML响应输出

-	velocity技术： *.vm+VTL表达式

		freemarker技术： *.ftl+FTL表达式

		thymeleaf技术： *.html+TH表达式

/template/list.do-->DispatcherServlet-->HandlerMapping-->TemplateController-->DeptDao-->返回ModelAndView-->thymeleaf模板文件 （src\main\resources\templates）

1.	在pom.xml追加thymeleaf定义

	    <dependency>
	        <groupId>org.springframework.boot</groupId>
	        <artifactId>spring-boot-starter-thymeleaf</artifactId>
	    </dependency>
	
	    编写Controller（与JSP响应Controller相同）
	    
	    @Controller
	    public class TemplateController {
	    
	        @Autowired
	        private DeptDao deptDao;
	    
	        @RequestMapping("/template/list.do")
	        public ModelAndView list(){
	            List<Dept> list = deptDao.loadAll();
	            ModelAndView mav = new ModelAndView();
	            mav.setViewName("list");
	            mav.getModel().put("depts", list);
	            return mav;
	        }
	    
	    }

	.	在src/main/resources/templates添加模板文件

	    注意：html模板文件，开始和结束标记必须匹配；给< html>元素添加xmlns:th="http://www.thymeleaf.org"定义。
	    
	    <!DOCTYPE html>
	    <html xmlns:th="http://www.thymeleaf.org">
	    <head>
	    <meta charset="UTF-8"/>
	    <title>Insert title here</title>
	    </head>
	    <body>
	        <h1>部门列表(thymeleaf模板)</h1>
	        <table>
	            <tr>
	                <td>编号</td>
	                <td>名称</td>
	                <td>地址</td>
	            </tr>
	            <tr th:each="dept:${depts}">
	                <td th:text="${dept.deptno}"></td>
	                <td th:text="${dept.dname}"></td>
	                <td th:text="${dept.loc}"></td>
	            </tr>
	        </table>
	    </body>
	    </html>



### springBootMvc 异常处理
springboot有一个ErrorMvcAutoConfiguration自动配置组件， 默认加载BasicErrorController, Controller中定义了丙个/error处理。 （一个html响应、 一个json响应）
当mvc程序底层发生异常，会自动转向/error请求处理,显示错误界面。
#### 自定义ErrorController （全局处理）
自定义ErrorController组件，需要实现ErrorController接口或继承AbstractErrorController都可以 。

		@Controller
		public class MyErrorController implements ErrorController{
		
		    //自定义/error请求处理逻辑
		    @RequestMapping("/error")
		    public ModelAndView error(){
		        ModelAndView mav = new ModelAndView();
		        mav.setViewName("error");//error.html
		        return mav;
		    }
		
		    @Override
		    public String getErrorPath() {
		        return "/error";
		    }
		
		}

#### 使用@ExceptionHandler
处理某个Controller异常， 应用方法就是在Controller添加带Exception参数的方法，然后使用@ExceptionHander标记。

		@ExceptionHandler
		@ResponseBody
		public MyResult handlerException(Exception e){
		    MyResult result = new MyResult();
		    result.setStatus(0);
		    result.setMsg("参数有错");
		    result.setData(e.getMessage());
		    return result;
		}

### SpringBootMVC 拦截器

1. 编写拦截器组件，实现HandlerInterceptor接口

				@Component//扫描
				public class SomeInterceptor implements HandlerInterceptor{
				
				    @Override
				    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
				            throws Exception {
				        System.out.println("开始执行Controller处理");
				        return true;
				    }
				
				    @Override
				    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
				            ModelAndView modelAndView) throws Exception {
				        // TODO Auto-generated method stub
				        System.out.println("Controller执行完毕");
				    }
				
				    @Override
				    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
				            throws Exception {
				        // TODO Auto-generated method stub
				        String param = request.getQueryString();
				        System.out.println(request.getRequestURI()+"?"+param+"请求处理完毕");
				    }
				
				}

2. 配置拦截器

				@Configuration
				public class InterceptorConfiguration extends WebMvcConfigurerAdapter{
				
				    @Autowired
				    private SomeInterceptor some;
				
				    @Override
				    public void addInterceptors(InterceptorRegistry registry) {
				        registry.addInterceptor(some).addPathPatterns("/compute.do");
				    }
				
				}

### SpringBootAOP
AOP关键概念：切面、切入点、通知。

案例：记录每个Controller方法执行的时间

- 切面：计算方法执行时间
- 切入点：所有Controller方法 within(cn.xdl.controller.*)
- 通知：环绕通知 @Around


1. 在pom.xml追加aop定义

			<dependency>
			    <groupId>org.springframework.boot</groupId>
			    <artifactId>spring-boot-starter-aop</artifactId>
			</dependency>

2. 编写切面组件，使用@Aspect、@Around等标记

			@Component//ioc
			@Aspect//定义为切面
			public class WatchBean {
			
			    @Around("within(cn.xdl.controller.*)")
			    public Object execute(ProceedingJoinPoint pjp) throws Throwable{
			        //开始计时
			        StopWatch watch = new StopWatch();
			        watch.start();
			        Object obj = pjp.proceed();//执行controller方法
			        //结束计时
			        watch.stop();
			        long time = watch.getTotalTimeMillis();//执行时长
			        String targetClass = pjp.getTarget().getClass().getName();//组件名
			        String methodName = pjp.getSignature().getName();//方法名
			        System.out.println("组件："+targetClass+"方法:"+methodName+"执行时长为:"+time+"ms");
			        return obj;
			    }
			
			}

### SpringBoot静态资源管理
在SpringBoot工程中，默认静态资源目录如下：

		src/main/resources/public (最低)
		src/main/resources/statis
		src/main/resources/resources
		src/main/resources/META-INF/resources (最高)

资源目录杜撰优先级， 从高到低查找
资源目录杜撰优先级， 从高到低查找

如果需要自定义静态资源存储路径，可以采用下面配置类

		@Configuration
		public class MyResourceConfiguration extends WebMvcConfigurerAdapter{
		
		    @Override
		    public void addResourceHandlers(ResourceHandlerRegistry registry) {
		        registry.addResourceHandler("/mystatic/**")
		            .addResourceLocations("classpath:/mystatic/");
		    }
		
		}
	
	-	提示：不要使用/** 映射，会破坏原有默认静态资源访问。

### SpringBoot对JavaWeb集成

1. Servlet
	-	编写Servlet组件， 继承HttpServlet
			利用@WebServlet注解配置
			在启动类中，添加@ServletComponentScan

			@WebServlet(name="helloservlet",urlPatterns="/hello.do")
			public class HelloServlet extends HttpServlet{
			
			    public void service(
			        HttpServletRequest request,HttpServletResponse response) throws IOException{
			        response.setContentType("text/html;charset=UTF-8");
			        PrintWriter out = response.getWriter();
			        out.println("Hello Servlet");
			        out.close();
			
			    }
			
			}
	
			Filter
			编写Filter组件，实现Filter接口
			利用@WebFilter注解配置
			在启动类中， 添加@ServletComponentScan
	
			@WebFilter(urlPatterns="/hello.do",filterName="somefilter")
			public class SomeFilter implements Filter{
			
			    @Override
			    public void init(FilterConfig filterConfig) throws ServletException {
			        // TODO Auto-generated method stub
			
			    }
			
			    @Override
			    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			            throws IOException, ServletException {
			        System.out.println("-----doFilter------");
			        chain.doFilter(request, response);//调用后续servlet\jsp等
			
			    }
			
			    @Override
			    public void destroy() {
			        // TODO Auto-generated method stub
			
			    }
			
			}
	
			Listener
			编写Listener组件
			利用@WebListener注解配置
			在启动类中， 添加@ServletComponentScan
	
			启用druid连接池的监控功能
			配置启用StatViewServlet组件
	
			@WebServlet(urlPatterns="/druid/*",initParams={
			    @WebInitParam(name="loginUsername",value="xdl"),
			    @WebInitParam(name="loginPassword",value="123")
			})
			public class DruidStatServlet extends StatViewServlet{
			
			}
	
			配置启用WebStatFilter组件 
	
			@WebFilter(urlPatterns="/*",initParams={
				@WebInitParam(name="exclusions",value="*.js,*.jpg,*.css,/druid/*")
			})
			public class DruidStatFilter extends WebStatFilter{
			
			}

### SpringBoot 任务调用
#### 服务器启动时自动执行某个任务
SpringBoot提供了两种方法，编写一个组件实现ApplicationRunner或CommandLineRunner。

1.	ApplicationRunner

	@Component
	@Order(2)
	public class MyTask1 implements ApplicationRunner{
	
	    @Override
	    public void run(ApplicationArguments args) throws Exception {
	        System.out.println("自动执行任务1处理");
	    }
	
	}
	.	CommandLineRunner

	@Component
	@Order(1)
	public class MyTask2 implements CommandLineRunner{
	
	    @Override
	    public void run(String... args) throws Exception {
	        System.out.println("自动执行任务2处理");
	    }
	
	}
提示：可以利用@Order指定任务触发顺序，1、2、3..

#### 服务器启动后定时自动执行某个任务
案例：每隔5S调用任务打印输出当前时间。

在启动类前添加@EnableScheduling，开启Schedul任务调度模块
编写任务组件，使用@@Scheduled

	@Component
	public class MyTask3 {
	
	//  @Scheduled(fixedRate=5000,initialDelay=2000)
	    @Scheduled(cron="0/5 * * ? * *")
	    public void execute(){
	        System.out.println("自动执行任务3,时间:"+new Date());
	    }
	
	}

通过corn表达式，方便定时，具体语法参考下面资料：

![Scheduled_cron](/.images/1267492514.png)

### SpringBootTest
spring提供了一套test测试框架，与junit结合应用，利用junit启动。

#### 测试Spring容器中对象方法
将DeptDao对象注入到Test类测试。

	    @RunWith(SpringRunner.class)
	    @SpringBootTest(classes={MyBootApplication.class})
	    public class TestDeptDao {
	
	        @Autowired
	        private DeptDao deptDao;
	
	        @Test
	        public void test1(){
	            List<Dept> list = deptDao.loadAll();
	            for(Dept dept:list){
	                System.out.println(dept.getDeptno()+" "+dept.getDname());
	            }
	        }
	
	    }
#### 测试SpringMVC处理流程
将Controller注入，然后利用MockMVC、MockResult等API测试MVC流程。

	    @RunWith(SpringRunner.class)
	    @SpringBootTest(classes={MyBootApplication.class})
	    public class TestRestful {
	
	        @Autowired
	        private DeptController controller;
	
	        @Test
	        public void test1() throws Exception{
	            //发送http请求调用resuful服务
	            MockMvc mock = MockMvcBuilders.standaloneSetup(controller).build();
	            //创建一个/dept/10 GET类型请求
	            RequestBuilder getRequest = MockMvcRequestBuilders.get("/dept/10");
	            //发送请求,获取返回结果信息
	            MvcResult result = mock.perform(getRequest).andReturn();
	            String content = result.getResponse().getContentAsString();
	            System.out.println(content);
	        }
	
	    }

### Redis
### Redis

#### Redis简介
Redis是完全基于内存的存储（内存数据库），存储结构为key-value键值对模式，value可以是字符串、列表、集合、有序集合、哈希类型。

Redis是属于NoSQL数据库(非关系型数据库)之一，NoSQL包含： 

Redis优点：

-	存取速度异常快速
		支持持久化（RDB和AOF机制）
		支持多种存储类型,字符产、集合、列表等
		适合做应用程序的缓存

#### Redis常用命令
1.	字符串操作

		set key value //存
		get key //取
		strlen key //取value字符长度
		incr key //value值加1
		incrby key n //value值加n
		decr key //value值减1
		decrby key n //value值减n
		append key str //将str拼接到原来value后面
		key操作
		
		del key //删除key
		keys pattern //查看有哪些key
		expire key 秒 //指定失效时长,单位秒
		pexpire key 毫秒 //指定失效时长,单位毫秒
		type key //查看value类型

	.	哈希操作(Java map, Redis hash)

		hset key 字段名 字段值
		hmset key 字段名1 字段值1 字段名2 字段值2
		hget key 字段名
		hmget key 字段名1 字段名2
		hlen key //返回字段数量
		hkeys key //返回字段名
		hdel key 字段名 //删除字段名

3. 列表操作(Java List, Redis list)

		lpush key value //从头部添加
		rpush key value //从结尾添加
		lrange key begin end //获取指定区间的元素
		llen key //返回集合元素数
		lpop key //将头部元素弹出
		rpop key //将尾部元素弹出
3. 集合操作(Java Set, Redis set)

		sadd key value //存值
		smembers key //取值
		srem key value //删除元素
		scard key //获取元素数量
		srandmember key n //获取n个随机元素
		spop key //弹出一个随机元素
4. 有序集合操作(Java TreeSet, Redis zet)

		zadd key score value //存值
		zrange key begin end //获取元素（由小到大）
		zrevrange key begin end //获取元素（由大到小）
		zcard key //获取元素个数
		zcount key min max //统计分数区间个数


#### Java访问Redis
##### 原始API Jedis对象操作

1.	测试连接

		@Test
		public void test1(){
		    Jedis jedis = new Jedis("localhost",6379);
		    System.out.println(jedis.ping());
		    jedis.close();
		}
2. 查看所有key

		@Test
		public void test2(){
		    Jedis jedis = new Jedis("localhost",6379);
		
		    Set<String> keys = jedis.keys("*");//keys *
		    for(String s:keys){
		        System.out.println(s);
		    }
		
		    jedis.close();
		}
3. 字符串存取

		@Test
		public void test3(){
		    Jedis jedis = new Jedis("localhost",6379);
		    jedis.set("java", "hello redis");
		    String name = jedis.get("name");
		    System.out.println(name);
		    jedis.close();
		}
4. 哈希操作

		@Test
		public void test4(){
		    Jedis jedis = new Jedis("localhost",6379);
		    String name = jedis.hget("myhash1", "name");
		    System.out.println("name:"+name);
		    String no = jedis.hget("myhash1", "no");
		    System.out.println("no:"+no);
		    jedis.close();
		}

#### 封装API RedisTemplate（spring-data）
1. 在pom.xml添加spring-boot-starter-redis定义

		<dependency>
		    <groupId>org.springframework.boot</groupId>
		    <artifactId>spring-boot-starter-redis</artifactId>
		</dependency>
2. 在application.properties添加redis连接参数

		spring.redis.host=localhost
		spring.redis.port=6379
3. 获取Spring容器中RedisTemplate使用

	-	写入Java对象

			@Test
			public void test2(){
			    ApplicationContext ac = 
			        SpringApplication.run(MyBootApplication.class);
			    RedisTemplate template = 
			        ac.getBean("redisTemplate",RedisTemplate.class);
			
			    Dept dept = new Dept();
			    dept.setDeptno(1);
			    dept.setDname("SpringBoot");
			
			    template.opsForValue().set("spring", dept);
			
			}
			读取Java对象
	
			@Test
			public void test3(){
			    ApplicationContext ac = 
			        SpringApplication.run(MyBootApplication.class);
			    RedisTemplate template = 
			        ac.getBean("redisTemplate",RedisTemplate.class);
			    Dept dept = (Dept)template.opsForValue().get("spring");
			    System.out.println(dept.getDeptno()+" "+dept.getDname());
			}


#### Redis缓存
Redis作为系统的缓存，介于业务和关系型数据库之间。



-	案例：利用Redis优化部门列表显示

	1.	在pom.xml定义boot-redis

			<dependency>
			    <groupId>org.springframework.boot</groupId>
			    <artifactId>spring-boot-starter-redis</artifactId>
			</dependency>

		.	在application.properties定义redis连接参数

			spring.redis.host=localhost
			spring.redis.port=6379
		.	注入RedisTemplate<Object,Object>使用

			@Controller
			public class TemplateController {
			
			    @Autowired
			    private DeptDao deptDao;
			
			    @Autowired
			    private RedisTemplate<Object, Object> redis;
			
			    @RequestMapping("/template/list.do")
			    public ModelAndView list(){
			        List<Dept> list = null;
			        //访问redis,加载缓存数据
			        list = (List)redis.opsForValue().get("depts");
			        //如果缓存没有，去数据库加载，并且将返回结果放入redis缓存
			        if(list == null){
			            list = deptDao.loadAll();
			            redis.opsForValue().set("depts", list);
			            System.out.println("从数据库缓存获取数据");
			        }else{
			            System.out.println("从Redis缓存获取数据");
			        }
			        ModelAndView mav = new ModelAndView();
			        mav.setViewName("list");
			        mav.getModel().put("depts", list);
			        return mav;
			    }
			}
		.	利用自动启动调用任务提前向缓存加载数据

			@Component
			@Order(1)
			public class MyTask1 implements ApplicationRunner{
			
			    @Autowired
			    private DeptDao deptDao;
			
			    @Autowired
			    private RedisTemplate<Object, Object> redis;
			
			    @Override
			    public void run(ApplicationArguments args) throws Exception {
			        System.out.println("自动执行任务1处理,将部门列表数据加载到缓存");
			        List<Dept> list = deptDao.loadAll();
			        redis.opsForValue().set("depts", list);
			        //将单个dept缓存
			        for(Dept dept:list){
			            redis.opsForValue().set("dept:"+dept.getDeptno(), dept);
			        }
			    }
			
			}

#### Redis持久化机制
Redis内部提供了RDB和AOF两种持久化机制。

-	RDB模式

	快照机制，快速、但是两次快照之间数据会丢失。默认间隔为1分钟、5分钟、15分钟。（会存在1分钟数据丢失风险）

		AOF模式

	日志模式，将每一个命令都采用日志记录下来。日志文件会很大，恢复时慢。 默认关闭，打开方式将appendonly设置为yes，然后appendfsync参数指定日志记录频率，有always、everysec、no。

提示：两种可以都开启，但是恢复时优先使用日志模式。


### Mybatis分页处理

1.	添加pom.xml定义

		<dependency>
		  <groupId>com.github.pagehelper</groupId>
		  <artifactId>pagehelper-spring-boot-starter</artifactId>
		  <version>1.2.3</version>
		</dependency>
	.	代码

		PageHelper.startPage(2,5);
		List<Dept> list = deptDao.loadAll();
		springboot test测试

