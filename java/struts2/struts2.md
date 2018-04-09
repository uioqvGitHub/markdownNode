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

##### `<action>`

1. `class`属性默认为`ActionSupport`

   在`struts-default`中有定义

###### `<result>`

1. `name`属性默认为`success`
2. `type`属性默认为`dispatcher`

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

