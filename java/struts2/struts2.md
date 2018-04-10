[TOC]

##struts2 笔记



```flow
st=>start: Start
op1=>operation: /hello.action
op2=>operation: filter 配置
op3=>operation: HelloActtion
op4=>operation: Result 配置
op5=>operation: /hello.jsp
e=>end
st->op1->op2->op3->op4->op5->e

```

### 基本使用

1. filter

   > `web.xml`中配置
   >
   > `<filter-class> `在旧版本为`org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter`

   ```xml
   <filter>
   	<filter-name>strutsmvc</filter-name>
       <filter-class>
           org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter
       </filter-class>
   </filter>
   <filter-mapping>
   	<filter-name>strutsmvc</filter-name>
       <url-pattern>*.action</url-pattern>
   </filter-mapping>
   ```

2. HelloAction

   > 默认方法名为`execute`, 无参
   >
   > 返回的`String` 与`Result`配置对应

   ```java
   package com.uioqv.struts2.action;
   public class HelloAction {
       public String execute() {
           System.out.println("进入HelloAction");
           return "success";
       }
   }
   ```

3. Result

   > `struts.xml`中配置
   >
   > 继承`struts-default`
   >
   > `<action>`定义请求和`action`对应
   >
   > > `name`属性指定请求名
   > >
   > > `class`指定`Action` 类
   > >
   > > `method`指定`Action`中方法名， 默认`execute`
   >
   > `<result>`根据 `Action` 返回的String值调用 `result`
   >
   > > `name`与`Action`返回值对应
   > >
   > > `type`指定 `Result`类型

   ```xml
   <struts>
   	<package name="demo1" extends="struts-default">
       	<action name="hello" class="com.uioqv.struts2.action.HelloAction">
           	<result name="success" type="dispatcher">
               	/WEB-INF/hello.jsp
               </result>
           </action>
       </package>

   </struts>
   ```

4. hello.jsp

   ```jsp
   <h1>
       Struts2入门示例
   </h1>
   ```

### struts.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.1.7//EN"
    "http://struts.apache.org/dtds/struts-2.1.7.dtd">

<struts>

</struts>
```

####`<constant>`

1. `struts.action.extension`属性

   指定图的扩展名

```xml
<constant name="struts.action.extension" value="action,do,,"></constant>
```

#### `<package>`

1. `namespace`属性 

   视图的命名空间（视图前面的路径名）

   ```xml
   <package namespace="/com/uioqv"></package>
   ```

##### `<result-types>`



######`<result-type>`



##### `<interceptors>`

```java
package com.uioqv.struts2.interceptor;

import com.opensymphony.xwork2.ActionInvocation;
import com.opensymphony.xwork2.interceptor.Interceptor;

public class Demo1Interceptor implements Interceptor {

	/**
	 * 
	 */
	private static final long serialVersionUID = 1L;

	@Override
	public void destroy() {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void init() {
		// TODO Auto-generated method stub
		
	}

	@Override
	public String intercept(ActionInvocation actionInvocation) throws Exception {
		System.out.println("进入拦截器");		
		actionInvocation.invoke();
		System.out.println("拦截器");
		return null;
	}

}

```

> status.xml

```xml
<interceptors>
	<interceptor name="inte1" class="com.uioqv.struts2.interceptor.Demo1Interceptor" />
	<interceptor name="inte2" class="com.uioqv.struts2.interceptor.Demo2Interceptor" />
</interceptors>
```


###### `<interceptor>`




##### `<default-interceptor-ref>`

##### `<global-results>`

配置全局的result

```xml
<global-results>
	<result name="nologin">/WEB-INF/login.jsp</result>
</global-results>
```

######`<result>`


##### `<action>`

1. `class`属性默认为`ActionSupport`

   在`struts-default`中有定义
```xml
<action name="hello" class="com.uioqv.struts2.action.HelloAction">
    <interceptor-ref name="inte2" />
    <result name="success" type="dispatcher">
        /WEB-INF/hello.jsp
    </result>
</action>
```

###### `<interceptor-ref>`

###### `<result>`

1. `name`属性默认为`success`
2. `type`属性默认为`dispatcher`









默认的拦截器， 当指定拦截器时默认的不会调用







### request, session, appliction的使用

#### 通用方式

1. `Map`类型

   首先得到`ActionContext`

   `ActionContext context = ActionContext.getContext();`

   - `request `

     `context.get("request");`

   - `session`

     `context.getSession()`

   - `application`

     `context.getApplication()`

2. `Servlet`类型

   - `HttpServletRequest`

     `ServletActionContext.getRequest();`

   - `HttpSession`

     通过`HttpServletRequest`获取`HttpSession`

   - `ServletContext`

     `ServletActionContext.getServletContext();`

   - `HttpServletResponse`

     `ServletActionContext.getResponse()`

#### Action内专用方式

1. `Map`类型

   - `request` 

     实现`RequestAware`

   - `session`

      实现`SessionAware`

   - `application`

      实现`ApplicationAware`

2. `Servlet`类型

   - `HttpServletRequest` 

     实现`ServletRequestAware`

   - `HttpSession`

      通过`HttpServletRequest`获取`HttpSession`

   - `ServletContext`

      实现`ServletContextAware`

   - `HttpServletResponse`

     实现`ServletResponseAware`


### 常用Result组件

1. jsp响应

   - dispatcher

     ```xml
     <result name="" type="dispatcher">
     	/hello.jsp
     </result>
     ```

   - redirect

     ```xml
     <result name="" type="redirect">
     	/hello.do
     </result>
     ```

2. 调下一个Action

   - redirectAction（重定向方式)

     ```xml
     <result name="" type="redirectAction">
     	<param name="actionName"></param>
         <param name="namespace"></param>
     </result>
     ```

   - chain(转发方式)

     ```xml
     <result name="" type="chain">
     	<param name="actionName"></param>
         <param name="namespace"></param>
     </result>
     ```

3. json 响应

   不加param节点会将action内全部属性写到前台

   加param-root 配置显示单个属性

   加param-includeProperties 配置显示多个属性

   > 需要引入jar包

   ```xml
   <dependency>
       <groupId>org.apache.struts</groupId>
       <artifactId>struts2-json-plugin</artifactId>
       <version>2.5.16</version>
   </dependency>
   ```

   > `<package> `需要设置 `extends="json-default"`

   ```xml
   <result name="" type="json">
       <param name="root">属性名</param>
       <param name="includeProperties">属性名,属性名</param>
   </result>
   ```

### 注解配置

> Action组件放在action包名中， 类命名带有Action.

```xml
<dependency>
    <groupId>org.apache.struts</groupId>
    <artifactId>struts2-convention-plugin</artifactId>
    <version>2.5.16</version>
</dependency>
```

#### @Action

####@Result

#### @ParentPackage

#### @Namespace

####@Interceptor