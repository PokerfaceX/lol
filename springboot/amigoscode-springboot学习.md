<img src="https://user-images.githubusercontent.com/84719774/129191963-2cdfd48f-6056-4bbf-b5d5-bb35ba7e6fc8.png" alt="img" style="zoom:150%;" />

#### 这张图，代表了**N-tier architecture**

这张图非常重要，可以帮助我们建立一个整体的框架



#### 1. api layer/ controller layer/ rest layer(控制层)

第一层是api layer，或者说是controller layer，当http请求被发送到后端的时候，这里会先收到http请求，然后给调用相应地service层的逻辑



#### 2. Service Layer(服务层)

第二层就是service layer，是后端里面处理业务逻辑的层，通常包含多个服务接口，每个接口对应一个业务操作，不会直接写sql语句来和数据库进行交互，而是通过调用DAO层来完成这个需求



#### 3. Data Access Layer(数据访问层)

第三层就是data access layer，在后端里面和数据库进行打交道的层，只有这里才会涉及到sql语句，需要和最底层的数据库进行接触，这里主要的作用是将应用程序的增删改查操作转换为数据库操作



| - 标记                | -作用                                                        | 层次      |
| --------------------- | ------------------------------------------------------------ | --------- |
| RestController        | 用来创建一个restful web服务器的控制器，可以接受客户端的http请求 | api layer |
| SpringBootApplication | 暂时理解为，来标注一整个应用程序是springboot来写出来的       |           |



---

### <u>Dependency Injection</u>

<img src="/Users/jasonjin/Library/Application Support/typora-user-images/image-20230413234850058.png" alt="image-20230413234850058" style="zoom:50%;" />

#### 依赖注入(dependency injection)是什么?

我们的customer controller api需要调用customer service里面的方法，所以通过new这个关键词，我们创建了service类并且进行了赋值，但是这么写其实很不好

- 在写test的时候，需要手动创建和管理类与类之间的关系，非常麻烦
- 耦合性(coupling)很高，当他们的关系出现变化的时候，就要进行一大堆更改



所以我们使用**@Autowired**与**@Component**

<img src="/Users/jasonjin/Library/Application Support/typora-user-images/image-20230413235414359.png" alt="image-20230413235414359" style="zoom:50%;" />

<img src="/Users/jasonjin/Library/Application Support/typora-user-images/image-20230413235624587.png" alt="image-20230413235624587" style="zoom:50%;" />

在这个例子中，使用了@Component就把CustomerService类标记成了spring容器里的一个组件(bean)，spring会管理这个对象，在其他地方使用autowired可以注入这个相同的对象



改成上面这样就可以了，原理就是(目前有一点不理解也无所谓)

1. 在启动时，Spring容器会扫描所有标记了@Service、@Component、@Repository或@Controller注解的类，并实例化这些类。在实例化过程中，如果这些类中存在@Autowired注解，则Spring容器会检测这些注解，并将它们标记为待注入的依赖项。
2. 当Spring容器需要创建CustomerController对象时，它会检查OrderService类中存在的@Autowired注解，发现CustomerService是一个待注入的依赖项。
3. Spring容器会查找所有标记了@Service、@Component、@Repository或@Controller注解的类，并选择一个最合适的实现类进行注入。
4. Spring容器会自动创建这个对象，并将其注入到customerController对象中的customerService变量中，使得customerController对象可以访问customerService对象。

在创建对象时，Spring容器会检查类的构造函数、setter方法和成员变量，找到所有使用@Autowired注解标注的依赖项，并自动创建这些依赖项的实例。在注入依赖项时，Spring容器会查找所有符合条件的Bean对象，并选择最合适的Bean对象进行注入。



#### spring容器是啥？

目前可以理解为spring容器就是一个自动创建和管理对象的工厂，当我们在spring容器中定义一个Bean的时候，这个工厂就会负责创建这个bean并且负责他的初始化和销毁。这个工厂也可以自己处理类与类之间的依赖关系



#### java bean是啥？

一个java对象如果

- 只有一个default constructor
- 所有field都是private
- 是serilizable，意思就是说这个java object可以被转换成字节流，用来存放到比如磁盘，也可以把这个字节流读取了，重新转换成java对象

那就是java bean类型了，通常用来表示业务对象，数据传输对象，可以用@component @service等标记，这样spring容器就会知道他们是bean对象，然后可以通过@autowired来完成注入



#### 多个实现类

当一个interface需要被@autowired注入的时候，他有可能有多个实现类，这时候就通过来指定使用哪个，需要注意的是，字段注入和构造函数注入不能被同时执行，因为对于一个构造器而言，就算不写autowired，spring容器也会自动查找相应的对象，然后注入进来

```java
@Autowired
@Qualifier("real")
private CustomerRepo customerRepo;
```

上面是通过@qualifier来指定具体某一个，但是如果没有@qualifier标签的话，就在其中一个实现类写上@Primary，那就会被设置成默认实现类



#### @service和@repository

其实这两个标签都算是@component一个变种，他们三个互相换着用都能达成完全相同的功能，对代码没什么实质性的影响。但是这两个annotation极大的增强了代码的可读性，如果一个类完成的功能是在service layer，我们用@service，如果是dao层，就用@repository



#### @bean

这个标签有将返回的对象注册为spring容器中的bean的作用，可以通过任何一个返回的方法来标记为@bean对象，但是这个类必须是@configuration标注的，不然spring容器不会注意的到



#### jackson和json

```java
public String getPassword() {
    return password;
}
```

- 在上述代码中，有一个Customer类，我们把这个类返回给了rest controller并且最后通过json形式显示在了屏幕上，这个的实现原理就是jackson library，有几个get方法，就会返回哪几个field

- 在springboot应用程序返回一个java对象的时候，springboot会自动使用jackson库并将java对象转换为json格式，因为在springboot程序中，默认配置了jackson library，我们不用自己在手动加入到配置中

```java
@JsonIgnore
public String getPassword() {
    return password;
}

@JsonProperty("customer_id")
public Long getId() {
    return id;
}
```

有些时候，我们不想返回某些json属性，所以我们就用@JsonIgnore来忽略它，如果是想让这个field以不同的名字返回到json字符串，那么就用@JsonProperty就可以了

记得和@JsonProperty(access = JsonProperty.Access.WRITE_ONLY)来搭配使用，这样的话可以通过json字符串写入，但是不会读写



#### @PostMapping， @PutMapping，@DeleteMapping

```java
@PostMapping
public void createNewCustomer(@RequestBody Customer customer) {}

@PutMapping
public void updateCustomer(@RequestBody Customer customer) {}

@DeleteMapping(path="{customerId}")
public void deleteCustomer(@PathVariable("customerId") Long id)
```

通过这三个标签来接受http的post，put和delete请求，通过@RequestBody可以自动把http request body中提取json 字符串，然后转换成一个java对象，但是要注意的是jackson library在转换时，要转换的对象必须要getter和setter方法，否则方法会失效。



#### api路径管理

在上面的例子中，我们直接把api暴露在了localhost下，这是不对的，永远不能这么做。任何api都应该放在一个更有意义的路径下，比如/api，/v1

- 可以提高api的可读性，比如把和user相关的操作放在/user里面，order相关放在/order里面

- 方便管理，通常api可能会有很多不同的版本，所以可以分成/v1，/v2，这样可以让客户端明确使用的api版本从而达到向前或者向后兼容的效果

  

```java
@RequestMapping(path = "api/v1/customer")
@RestController
public class CustomerController {
    @Autowired
    private CustomerService customerService;

    @GetMapping(value = "all")
    public List<Customer> getCustomers() {
        return this.customerService.getCustomers();
    }
}
```

在类的上面我们使用了@RequestMapping来管理一个总路径，在下面使用了@GetMapping来管理响应路径。简单来说就是，当我们试图访问getCustomers这个方法的时候，我们request mapping里面的东西会成为前缀，所以总的来说是http://localhost:8080/api/v1/customer/all



#### Springboot Validation

当我们接收了json字符串的时候，我们可能想提前做一些检查，比如看一些field是不是在这个json字符串里面

```java
public class Customer {
    private Long id;

    @NotBlank
    private String name;

    @NotBlank
    @JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    private String password;
  
  	@Email
  	private String email;
}

@PostMapping
public void createNewCustomer(@Valid @RequestBody Customer customer) {
    System.out.println("POST REQUEST");
    System.out.println(customer);
}
```

通过@Valid来激活检查，@Valid才能让springboot对这个传进来的http request body里面的json字符串进行检查



```java
server.error.include-message=always
server.error.include-binding-errors=always
server.error.include-stacktrace=ON_PARAM

```

上面的三段句子可以放在resources/application.properties文件里面，这样的话我们在postman收到http response的时候，就可以让信息变得更多的健全，比如显示stack trace，显示exception的message



#### Springboot exception handling

```java
@ControllerAdvice
public class ApiExceptionHandler {

    @ExceptionHandler(value = ApiRequestException.class)
    public ResponseEntity<Object> handleApiRequestException(ApiRequestException e) {
        ApiException apiException = new ApiException(
                e.getMessage(),
                e,
                HttpStatus.BAD_REQUEST,
                ZonedDateTime.now()
        );
        return new ResponseEntity<>(apiException, HttpStatus.BAD_REQUEST);
    }

    @ExceptionHandler(value = NotFoundException.class)
    public ResponseEntity<Object> handleApiRequestException(NotFoundException e) {
        ApiException apiException = new ApiException(
                e.getMessage(),
                e,
                HttpStatus.NOT_FOUND,
                ZonedDateTime.now()
        );
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_JSON);
        return new ResponseEntity<>(apiException, headers,HttpStatus.NOT_FOUND);
    }
}
```

上面的代码中@controlleradvice是用来标记这个类是用来处理异常的，对于每一个异常处理的方法，我们都用@exceptionhandler标记，当某个特定的异常被抛出的时候，@exceptionhandler被标记的方法会被执行。而response entity是用来表示一个http响应，可以包含状态码，头部数据，响应体。



#### jpa简单入门

之前学的都是只是用arraylist来模拟一个数据库，现在的话来正儿八经的链接一个数据库。这里使用了h2 database，这也是一个relational database，只不过是超级轻量级，而且主要是设置成了在memory中，所以程序关了就没了，但是用来练习足够了，sql script和properties文件全部传到了github，就不说了

```java
@Repository
public interface CustomerRepository
        extends JpaRepository<Customer, Long> {

}

@Service
public class CustomerService {
    @Autowired
    private CustomerRepository customerRepository;

    public List<Customer> getCustomers() {
        return customerRepository.findAll();
    }

    public Customer getCustomerById(Long id) {
        return this.customerRepository.findById(id)
                .orElseThrow(() -> new NotFoundException("customer with id-" + id + " is not found"));
    }
}
```

在上面的例子中，我们让接口继承JpaRepository,提供了常用的访问操作，比如简单的增删改查。更关键的是，对于JpaRepository而言，我们不需要自己定义一个实体类，springboot会自动定义一个实现类，然后我们可以直接使用@autowired来注入。所以直接调用这个接口的方法，而不用担心null pointer exception



**TODO**: 这个涉及到了代理设计模式，以及spring的底层原理，到时候在看看



#### Feign client

看起来很懵逼，但是其实和公司代码里的HAPI FHIR Client类似，就是一个可以让我们用java代码声明一个http客户端从而发送http请求的工具类，从属于spring cloud

```java
@FeignClient(
        value = "jsonplaceholder",
        url = "https://jsonplaceholder.typicode.com/"
)
public interface JSONPlaceHolderClient {
    @GetMapping("posts")
    public List<Post> getPosts();

    @GetMapping("posts/{postId}")
    public Post getPostById(@PathVariable("postId") Integer postId);
}

@Configuration
public class JSONPlaceHolderConfiguration {
    @Bean("jsonplaceholder")
    public CommandLineRunner runner(JSONPlaceHolderClient jsonPlaceHolderClient) {
        return args-> {
            int a = jsonPlaceHolderClient.getPosts().size();
            System.out.println(a);
            System.out.println(jsonPlaceHolderClient.getPostById(1));
        };
    }
}

在该类的方法中，你已经通过参数传入了 `JSONPlaceHolderClient` 对象，Spring 容器在创建 `CommandLineRunner` bean 的时候，会自动注入 `JSONPlaceHolderClient` bean，这是 Spring 的依赖注入机制自动完成的。所以即使你没有显式地使用 `@Autowired` 或者 `@Resource` 注解，Spring 容器仍然可以在创建 `CommandLineRunner` bean 的时候正确地解析 `JSONPlaceHolderClient` 的依赖关系。(以后学习spring原理的回来看)
```

url就是请求的base address，springboot会自动搞出一个实现类，之后我们这里简单的通过@configuration来让springboot启动的时候加载，然后commandlinerunner的方法会在类加载时自动执行



#### Logging快速入门

```java
public class CustomerService {

    private final static Logger LOGGER = LoggerFactory.getLogger(CustomerService.class);
    
    public List<Customer> getCustomers() {
        LOGGER.info("getCustomers was called");
        return customerRepository.findAll();
    }
  }
```

通过logger，我们可以来更好的对程序进行维护以及管理，日后在出问题的时候，也能快速知道问题出在了哪里，这里就简单入了一下门



#### Springboot actuator

这里也是简单的入门了一下springboot的actuator，这是一个springboot提供的用于monitoring和checking的一个集合，提供了很多有用的endpoint，比如/health, /info, /metrics，可以通过http请求来访问，可以显示很多信息，点击prometheus甚至会显示jvm内存

```json
{
   "_links":{
      "self":{
         "href":"http://localhost:8080/actuator",
         "templated":false
      },
      "health":{
         "href":"http://localhost:8080/actuator/health",
         "templated":false
      },
      "health-path":{
         "href":"http://localhost:8080/actuator/health/{*path}",
         "templated":true
      },
      "info":{
         "href":"http://localhost:8080/actuator/info",
         "templated":false
      },
      "prometheus":{
         "href":"http://localhost:8080/actuator/prometheus",
         "templated":false
      }
   }
}
```

```properties
management.info.env.enabled=true
management.endpoints.web.exposure.include=info,health,health-path, prometheus
#management.endpoints.web.exposure.include=*
info.app.name=Spring Boot Master Class Course
info.app.description=Master Spring Boot
info.app.version=1.0.0
info.company.name=Amigoscode

management.endpoint.health.show-details=always
```

上面的就是我当时写这个demo的时候用到的properties文件



#### @Value和properties的加载

我们很多时候其实都想加载一些文本信息，比如我们在上面的properties文件里面写的company name，app name啥的，这时候我们可以通过两个方法来实现

```java
@Configuration
public class CustomerConfig {
    @Value("${info.company.name}")
    private String companyName;

    @Autowired
    private Environment environment;

    @Bean
    CommandLineRunner commandLineRunner() {
        return args -> {
            System.out.println("company name is " + companyName);
            System.out.println("using springboot environment property " + environment.getProperty("info.app.name"));
        };
    }
}
```

通过@configuration，我们声明了当前是个配置类，我们保证了springboot容器会自动扫描并且加载这个类，之后我们可以通过@value就可以直接注入到这个bean中，因为配置类在springboot眼里就是个bean

关于commandlinerrunner，这里是解释，可以偶尔看看

This class also implements **Spring's CommandLineRunner interface**. *CommandLineRunner* is a simple Spring Boot interface with a *run* method. Spring Boot will automatically call the *run* method of all beans implementing this interface after the application context has been loaded

通过Environment类，可以直接接触到当前的配置文件，这里是官方文档https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/env/Environment.html



#### @ConfigurationProperties

有些时候，我们想把applicaiton.properties里面的各种在java程序中显示出来，那我们只需要加上@ConfigurationProperties就可以了

```java
@Configuration
@ConfigurationProperties(prefix = "info.app")
public class InfoApp {
    private String name;
    private String description;
    private String version;
}
/**
info.app.name=Spring Boot Master Class Course
info.app.description=Master Spring Boot
info.app.version=1.0.0
info.company.name=Amigoscode
*/
```

这上面的info.app就是我们在application properties文件里面定义的一个key，他会被自动生成一个类，然后加载到这三个field里面去，因为我们在properties文件里面定义过这些属性



#### 多个properties文件

有些时候，我们的properties可能只适用于local dev environment，就比如我们只链接了本地的数据库，但是很多时候我们也想测试下连接到aws的数据库会有什么效果，那就需要为aws dev environment，在写一个properties文件，这里我们就直接在src/main/resources里面创建多个文件就好了，比如```application.properties, application-dev.properties, application-prod.properties```等等，在intellij里面可以通过edit configuration然后把program argument改为```--spring.profiles.active=dev```就行



#### yaml简单使用

对于配置文件，我们除了用properties为尾缀的格式以外，还可以用.yaml/.yml，这种文件被称为yaml文件，和json很相似，但是更加的便于阅读，一般都是用来写配置文件的

```yaml
info:
    app:
        description: Master Spring Boot
        name: Spring Boot Master Class Course
        version: 1.0.0
    company:
        name: Amigoscode
management:
    endpoint:
        health:
            show-details: always
    endpoints:
        web:
            exposure:
                include: info,health,health-path, prometheus
    info:
        env:
            enabled: true
server:
    error:
        include-binding-errors: always
        include-message: always
        include-stacktrace: ON_PARAM
spring:
    datasource:
        driver-class-name: org.h2.Driver
        initialization-mode: always
        password: hello
        url: jdbc:h2:mem:testdb-dev-environment
        username: hello
    h2:
        console:
            enabled: true
            path: /h2-dev
    jpa:
        database-platform: org.hibernate.dialect.H2Dialect
        show-sql: true
    main:
        banner-mode: 'off'
    output:
        ansi:
            enabled: ALWAYS
    sql:
        init:
            mode: always
            platform: h2

```

https://www.redhat.com/en/topics/automation/what-is-yaml 简易入门



