# Maven 笔记

[TOC]

### 将其他Maven项目引入当前项目

​	被引入的项目只能是jar或pom。 

1. 被引入项目定义

   ```xml
   <modelVersion>4.0.0</modelVersion>
   <groupId>cn.xdl</groupId>
   <artifactId>ovls_common_service</artifactId>
   <version>0.0.1-SNAPSHOT</version>
   ```

2. 引入定义

   ```xml
   <dependencies>
       <!-- 引入ovls_common_service的jar包定义 -->
       <dependency>
           <groupId>cn.xdl</groupId>
           <artifactId>ovls_common_service</artifactId>
           <version>0.0.1-SNAPSHOT</version>
       </dependency>
   </dependencies>
   ```

   ​

