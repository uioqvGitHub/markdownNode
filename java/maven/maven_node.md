# Maven 笔记

[TOC]

### 修改jdk版本

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.6.0</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
                <encoding>${project.build.sourceEncoding}</encoding>
                <compilerArguments>
                    <verbose />
                 	<bootclasspath>
                         ${java.home}/lib/rt.jar;${java.home}/lib/jce.jar
                    </bootclasspath>
                </compilerArguments>
            </configuration>
        </plugin>
    </plugins>
</build>
```



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

