# thymeleaf笔记

[TOC]



###取消thymeleaf中html5的严格校验

```xml
<!-- pom.xml -->
<dependency>
    <groupId>net.sourceforge.nekohtml</groupId>
    <artifactId>nekohtml</artifactId>
</dependency>
```

```properties
#application.yml
spring:
 thymeleaf:
  mode: LEGACYHTML5
```

