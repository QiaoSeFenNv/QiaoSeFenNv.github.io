---
title: "Spring Boot Framework Documentation"
description: ""
summary: ""
date: 2023-09-07T16:13:18+02:00
lastmod: 2023-09-07T16:13:18+02:00
draft: false
images: []
menu:
  docs:
    parent: ""
    identifier: "spring-ee514306871548sd6e68dea3359133ad"
weight: 900
toc: true
---




## 程序启动后的初始化

### ApplcationRun接口

`ApplicationRunner`是一个函数式接口，用于在Spring Boot应用程序启动后执行一些任务。



```java
@Bean
    public ApplicationRunner runner(KafkaTemplate<String, String> template) {
        return args -> {
            template.send("topic1", "test");
        };
    }
```

```java
import org.springframework.boot.ApplicationRunner;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Component;

@Component
public class KafkaMessageSender implements ApplicationRunner {

    private final KafkaTemplate<String, String> kafkaTemplate;

    public KafkaMessageSender(KafkaTemplate<String, String> kafkaTemplate) {
        this.kafkaTemplate = kafkaTemplate;
    }

    @Override
    public void run(ApplicationArguments args) {
        kafkaTemplate.send("topic1", "test");
    }
}

```

>***Tip***
>
>创建一个`ApplicationRunner`的Spring Bean，或者实现该接。再应用程序启动之后`ApplicationRunner`接口的`run`方法将被自动调用
>
>因此，如果未来我们想实现再程序启动之后就执行的功能，可以使用次方法。





## Bean 注入

### 构造注入

构造函数注入是通过将依赖作为构造函数的参数传递给目标Bean来实现的

- 不可变性：一旦Bean被创建，其依赖关系就不可变，这有助于保持Bean的一致性和线程安全性。

```java
public class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    // ...
}
```

>***Tip***
>
>`Spring Boot`中，如果目标Bean的构造函数只有一个，而且该构造函数没有任何其它注解（例如`@Autowired`、`@Qualifier`等），Spring Boot会自动将构造函数参数作为依赖进行注入，而不需要显式使用`@Autowired`注解。



## Spring Boot 路径匹配策略

在Spring Boot中，你可以使用不同的路径匹配策略来定义URL路径的匹配规则。`ant_path_matcher`是一种广泛使用的策略，它支持通配符和模式匹配，例如`*`和`?`等通配符字符。

```yml
spring:
  mvc:
    pathmatch:
      matching-strategy: ant_path_matcher
```

通过将`ant_path_matcher`作为路径匹配策略，你可以在处理URL路径时使用通配符和模式匹配，以简化URL匹配逻辑。这对于定义RESTful API的路由、拦截URL请求等方面非常有用。

>举一个例子，我们配置`swagger-ui`时，不配置上面的路径匹配规则。那么再多模块项目中，将获取不到`api`接口的信息
>
>```java
>    @Bean
>    public Docket createRestApi() {
>        return new Docket(DocumentationType.SWAGGER_2)
>                .apiInfo(apiInfo())
>                .groupName("qiaosefennv")
>                .select()// 选择那些路径和api会生成document
>                .apis(RequestHandlerSelectors.basePackage("com.qiaose"))// 对指定目录下文件进行监控
>                .paths(PathSelectors.any())// 对根下所有路径进行监控
>                .build();
>    }
>```

