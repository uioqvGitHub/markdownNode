[TOC]

# SpringCloud

## 特性

Spring Cloud专注于提供良好的开箱即用经验的典型用例和可扩展性机制覆盖。

- 分布式/版本化配置
- 服务注册和发现
- 路由
- service - to - service调用
- 负载均衡
- 断路器
- 分布式消息传递

## Spring Cloud Commons：普通抽象

诸如服务发现，负载平衡和断路器之类的模式适用于所有Spring Cloud客户端可以独立于实现（例如通过Eureka或Consul发现）的消耗的共同抽象层。

### @EnableDiscoveryClient

Commons提供`@EnableDiscoveryClient`注释。这通过`META-INF/spring.factories`查找`DiscoveryClient`接口的实现。Discovery Client的实现将在`org.springframework.cloud.client.discovery.EnableDiscoveryClient`键下的`spring.factories`中添加一个配置类。`DiscoveryClient`实现的示例是[Spring Cloud Netflix Eureka](http://cloud.spring.io/spring-cloud-netflix/)，[Spring Cloud Consul发现](http://cloud.spring.io/spring-cloud-consul/)和[Spring Cloud Zookeeper发现](http://cloud.spring.io/spring-cloud-zookeeper/)。

默认情况下，`DiscoveryClient`的实现将使用远程发现服务器自动注册本地Spring Boot服务器。可以通过在`@EnableDiscoveryClient`中设置`autoRegister=false`来禁用此功能。

### ServiceRegistry

Commons现在提供了一个`ServiceRegistry`接口，它提供了诸如`register(Registration)`和`deregister(Registration)`之类的方法，允许您提供定制的注册服务。`Registration`是一个标记界面。

```java
@Configuration
@EnableDiscoveryClient(autoRegister=false)
public class MyConfiguration {
    private ServiceRegistry registry;

    public MyConfiguration(ServiceRegistry registry) {
        this.registry = registry;
    }

    // called via some external process, such as an event or a custom actuator endpoint
    public void register() {
        Registration registration = constructRegistration();
        this.registry.register(registration);
    }
}
```

每个`ServiceRegistry`实现都有自己的`Registry`实现。

#### 服务自动注册

默认情况下，`ServiceRegistry`实现将自动注册正在运行的服务。要禁用该行为，有两种方法。您可以设置`@EnableDiscoveryClient(autoRegister=false)`永久禁用自动注册。您还可以设置`spring.cloud.service-registry.auto-registration.enabled=false`以通过配置禁用该行为。

### Spring RestTemplate作为负载平衡器客户端

`RestTemplate`可以自动配置为使用功能区。要创建负载平衡`RestTemplate`创建`RestTemplate` `@Bean`并使用`@LoadBalanced`限定符。

>  警告 | 自动配置不创建`RestTemplate` bean。它必须由单个应用程序创建。 

> URI需要使用虚拟主机名（即服务名称，而不是主机名）。

```
@Configuration
public class MyConfiguration {

    @LoadBalanced
    @Bean
    RestTemplate restTemplate() {
        return new RestTemplate();
    }
}

public class MyClass {
    @Autowired
    private RestTemplate restTemplate;

    public String doOtherStuff() {
        String results = restTemplate.getForObject("http://stores/stores", String.class);
        return results;
    }
}
```

