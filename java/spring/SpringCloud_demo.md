[TOC]

## pom.xml

### cloud pom.xml

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Finchley.M9</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-config</artifactId>
    </dependency>
</dependencies>
```

###zookeeper pom.xml

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
    </dependency>
    <!-- 热部署工具 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
    </dependency>
</dependencies>
```

### user-service pom.xml

```xml
<dependencies>
    <dependency>
        <groupId>com.uioqv.cloud</groupId>
        <artifactId>zookeeper</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
</dependencies>
```

### book-service pom.xml

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-ribbon</artifactId>
    </dependency>

    <dependency>
        <groupId>com.uioqv.cloud</groupId>
        <artifactId>zookeeper</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
</dependencies>
```

## user-service

### application.yml 配置

```yaml
server:
  port: 8001
spring:
  application:
    name: userService
  cloud:
    zookeeper:
      connectString: localhost:2181
      discovery:
        instanceHost: localhost
        instancePort: ${server.port}
```

## book-service

### application.yml 配置

```yaml
server:
  port: 8002
spring:
  application:
    name: bookService
  cloud:
    zookeeper:
      connectString: 192.168.16.150:2181
      discovery:
        register: false
#ribbon:
#  ConnectTimeout: 5000
#  ReadTimeout: 5000

```



