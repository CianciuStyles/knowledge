# Spring \(framework\)

## Notes - Mastering Spring Framework Fundamentals

### Chapter 1: Getting Started

* The smallest addition to a Maven `pom.xml` file to include Spring is the following:

```markup
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-context</artifactId>
  <version>${VERSION}</version>
</dependency>
```

* The **Bill of Materials \(BOM\)** is a particular `pom.xml` file that only defines a set of dependencies. Including it in a Spring project assures that all the different Spring modules will be from the same version:

```markup
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-framework-bom</artifactId>
      <version>${VERSION}</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

### Chapter 2: Creating the Spring Container with Application Contexts

* The main interface for the Spring framework is called an `ApplicationContext`, and its three main implementations are:
  * `AnnotationConfigApplicationContext`: it will scan some packages of the project and find components by looking at annotations;
  * `FileSystemXmlApplicationContext`: it loads the configuration file that describes the beans from the file system; 
  * `ClassPathXmlApplicationContext`: it loads the configuration file that describes the beans from the classpath.
* A **profile** is an environment-specific configuration on how you want the Spring application to run.

### Chapter 3: Creating, Retrieving, and Using Spring-Managed Beans

* Declaring a bean via XML:

```markup
<bean id="service" class="com.mypackage.myapp.MyService" />
```

* Retrieving a bean from the file-based `ApplicationContext`:

```java
ApplicationContext ctx = new FileSystemXmlApplicationContext("application-context.xml");
MyService bean = ctx.getBean(MyService.class);
bean.doSomething();
```

* By default, Spring creates beans as singletons:

```java
ApplicationContext ctx = new ClassPathXmlApplicationContext("application-context.xml");
MyService bean1 = ctx.getBean(MyService.class); // bean1: MyService@1506
MyService bean2 = ctx.getBean(MyService.class); // bean2: MyService@1506
MyService bean3 = ctx.getBean(MyService.class); // bean3: MyService@1506
```

* To create different instances every time, we can change the `scope` to `"prototype"`:

```markup
<bean id="service" class="com.mypackage.myapp.MyService" scope="prototype" />
```

```java
ApplicationContext ctx = new ClassPathXmlApplicationContext("application-context.xml");
MyService bean1 = ctx.getBean(MyService.class); // bean1: MyService@1500
MyService bean2 = ctx.getBean(MyService.class); // bean2: MyService@1502
MyService bean3 = ctx.getBean(MyService.class); // bean3: MyService@1504
```

### Chapter 4: Understanding Dependency Injection and Inversion of Control

* By default, beans are initialized using the zero-arguments constructor. To inject dependencies through the constructor:

```java
public class MyRepository {
    public void doQuery() {
        System.out.println("Performing a query...");
    }
}
```

```java
public class MyService {
    private MyRepository repository;
    
    public MyService(MyRepository repository) {
        this.repository = repository;
    }
    
    public void doBusinessLogic() {
        System.out.println("Performing business logic...");
        repository.doQuery();
    }
}
```

```markup
<beans>
    <bean id="service" class="com.mypackage.myapp.MyService">
        <constructor-arg ref="repository" />
    </bean>
    
    <bean id="repository" class="com.mypackage.myapp.MyRepository" />
</beans>
```

* Hollywood principle: don't call us, we'll call you
* An alternative approach to the constructor-based depedency injection is the property-based dependency injection, where the dependencies are injected through setter methods:

```markup
<beans>
    <bean id="service" class="com.mypackage.myapp.MyService">
        <property name="repository" ref="repository" />
    </bean>
    
    <bean id="repository" class="com.mypackage.myapp.MyRepository" />
</beans>
```

```java
public class MyService {
    private MyRepository repository;
    
    public void doBusinessLogic() {
        System.out.println("Performing business logic...");
    }
    
    public void setRepository(MyRepository repository) {
        this.repository = repository;
    }
}
```

### Chapter 5: Auto-wiring Dependencies in the Spring Container

* An alternative to specifying beans and their dependencies manually within an XML file is to use **annotations**. In order to do so, we define in the XML file a `context:component-scan` tag, where we specify the package name that Spring will introspect and retrieve the beans from:

```markup
<context:component-scan base-package="com.mypackage.myapp" />
```

* After that, we can use the `@Component` annotation on all the classes that Spring should consider beans, and the `@Autowired` annotation on the dependencies to be injected:

```java
@Component
public class MyService {
    @Autowired
    private MyRepository repository;
    
    public void doBusinessLogic() {
        System.out.println("Performing business logic...");
        repository.doQuery();
    }
}
```

* Component is the most general annotation that we can use to annotate beans; more specific annotations include:
  * Controller: the class is a web controller in a Spring MVC application
  * Service
  * Repository
  * Configuration
* The `@Autowired` annotation can be used on member fields for setter-based dependency injection, or on the constructor for constructor-based dependency injection.
* To completely get rid of the XML context file, we can use the `AnnotationConfigApplicationContext` class, referencing the base package to scan for components:

```java
ApplicationContext ctx = new AnnotationConfigApplicationContext("com.mypackage.myapp");

MyService service = ctx.getBean(MyService.class);
service.doBusinessLogic();
```

* Alternatively, we can create a class annotated with the @Configuration annotation, and reference that in the AnnotationConfigApplicationContext constructor:

```java
ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);

MyService service = ctx.getBean(MyService.class);
service.doBusinessLogic();
```

```java
@Configuration
@ComponentScan(basePackages = "com.mypackage.myapp")
class AppConfig {}
```

* When multiple beans could be eligible to be injected \(e.g., two concrete implementations of a common interface\), we need to use the `@Qualifier` annotation to help Spring decide which one to use:

```java
public interface MyRepository {
    public void doQuery();
}
```

```java
public class MyRepositoryImpl implements MyRepository {
    @Overide
    public void doQuery() {
        System.out.println("Performing a query...");
    }
}
```

```java
public class AnotherRepositoryImpl implements MyRepository {
    @Overide
    public void doQuery() {
        System.out.println("Performing a query slightly differently...");
    }
}
```

```java
public interface MyService {
    public void doBusinessLogic();
}
```

```java
@Service
public class MyServiceImpl implements MyService {
    private MyRepository repository;
    
    public MyServiceImpl(@Qualifier("myRepositoryImpl") Repository repository) {
        this.repository = repository;
    }
    
    public void doBusinessLogic() {
        System.out.println("Performing business logic...");
        repository.doQuery();
    }
}        
```

```java
@Service
public class AnotherServiceImpl implements MyService {
    public void doBusinessLogic() {
        System.out.println("Performing business logic slightly differently...");
    }
}        
```

```java
ApplicationContext ctx = new AnnotationConfigApplicationContext("com.mypackage.myapp");

MyService service1 = ctx.getBean(MyService.class); // NoUniqueBeanDefinitionException
MyService service2 = ctx.getBean(AnotherServiceImpl.class); // OK
```

### Chapter 6: Using Properties and Profiles in Spring Projects

* It is possible to define properties for the application in a property file, then add a `@PropertySource` annotation on the `@Configuration` component, and then inject the value in fields using the `@Value` annotation:

{% code title="application.properties" %}
```java
my.name=Matt
```
{% endcode %}

{% code title="AppConfig.java" %}
```java
@Configuration
@ComponentScan("com.mypackage.myapp")
@PropertySource("classpath:application.properties")
public class AppConfig {}
```
{% endcode %}

{% code title="MyService.java" %}
```java
@Service
public class MyService {
    @Value("${my.name}")
    private String name;
    
    @Autowired
    private MyRepository repository;
    
    public void doBusinessLogic() {
        System.out.println("Performing business logic for " + name);
        repository.doQuery();
    }
}
```
{% endcode %}

* Other ways to inject values is to provide them through environment variables or command line arguments \(with the `-D` switch\).
* Profiles are useful to inject different values for the same variables \(e.g., an application deployed locally and on a production instance\). The main two ways to set a profile is either to pass a `-Dspring.profiles.active=<profile>` command line argument, or call `System.setProperty("spring.profiles.active", "<profile>")` from the code.
* We can annotate classes with the `@Profile` annotation, which means those classes will only be instantiated if the profile within the annotation matches the active profile.

{% code title="DevelopmentConfig.java" %}
```java
@Profile("local")
@Configuration
@ComponentScan("com.mypackage.myapp")
@PropertySource("classpath:application-local.properties")
public class DevelopmentConfig {}
```
{% endcode %}

{% code title="ProductionConfig.java" %}
```java
@Profile("prod")
@Configuration
@ComponentScan("com.mypackage.myapp")
@PropertySource("classpath:application-prod.properties")
public class ProductionConfig {}
```
{% endcode %}

### Chapter 7: Getting to Know the Spring Container

* It is possible to make a bean aware of two lifecycle methods \(one when the bean is created, and one when it is destroyed\) by making the bean implement two interfaces:

```java
@Service
public class EmployeeService implements InitializingBean, DisposableBean {

    @Autowired
    private EmployeeRepository repository;
    
    public void afterPropertiesSet() throws Exception {
        // called after the bean is created and all properties are injected
    }
    
    public void destroy() {
        // called when the context is closed
    }
}
```

* We can achieve the same result by using the JDK lifecycle annotations instead:

```java
@Service
public class EmployeeService {
    
    @Autowired
    private EmployeeRepository repository;
    
    @PostConstruct
    public void init() {
        repository.loadStaticData();
    }
    
    @PreDestroy
    public void dispose() {
        System.out.println("Destroying service bean.");
    }
}
```

* The general bean lifecycle consists of:
  * the constructor being called
  * setter methods are called
  * bean post-processor is called
  * post-constructor method is called
  * destroy method is called
  * finalize method is called

### Chapter 8: Accessing Databases with Spring

* The `javax.sql.DataSource` class is essential to interface Spring with a database, as it will return connections \(new or reused\) to the database:

```java
@Configuration
public class AppConfig {
    @Bean
    public DataSource dataSource() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName("org.h2.Driver");
        dataSource.setUrl("jdbc:h2:mem:mydb");
        dataSource.setUsername("sa");
        dataSource.setPassword("");
        
        return dataSource;
    }
}
```

* Other useful types of DataSource implementations are:
  * SingleConnectionDataSource
  * HikariDataSource
  * JndiDataSourceLookup
* `@Repository` is the correct annotation to use with DAO \(data access objects\):

```java
@Repository
public class MyDao {
    private DataSource dataSource;
    private JdbcTemplate jdbcTemplate;
    
    @Autowired
    public MyDao(DataSource dataSource, JdbcTemplate jdbcTemplate) {
        this.dataSource = dataSource;
        this.jdbcTemplate = jdbcTemplate;
    }
    
    @PostConstruct
    public void doQuery() {
        jdbcTemplate.execute("select 1 from dual");
    }
}
```

* There is an abstract class called JdbcDaoSupport which removes a lot of the boilerplate needed to configure a DAO:

```java
@Repository
public class MyDao extends JdbcDaoSupport {
    @Autowired
    public MyDao(DataSource dataSource) {
        setDataSource(dataSource);
    }
    
    @PostConstruct
    public void doQuery() {
        String result = getJdbcTemplate().queryForObject("select 1 from dual", String.class);
        System.out.println(result);
    }
}
```

### Chapter 9: Aspect-oriented Programming

* Aspect-oriented Programming \(AOP\) allows you to define aspects, which are methods that are invoked before or after some events are fired in the application.
* In order to use AOP in Spring, we need to include `spring-aspects` in the `pom.xml` file:

```markup
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-aspects</artifactId>
  <version>${VERSION}</version>
</dependency>
```

* In order to make AOP working in the Spring application, we need to annotate the `@Configuration` class with the `@EnableAspectJAutoProxy` annotation:

```java
@Configuration
@ComponentScan("com.mypackage.myapp")
@EnableAspectJAutoProxy
public class AppConfig {}
```

* There are three types of Advice in Spring:
  * `@Before`: the aspect will run before the original method
  * `@After`: the aspect will wun after the original method
  * `@Around`: the aspect wraps the original method \(which can be invoked via the `ProceedingJoinPoint.proceed()` method\)

{% code title="MyAspect.java" %}
```java
@Aspect
@Component
public class MyAspect {
    @Before("execution(void MyService.doSomething(..))")
    public void intercept() {
        System.out.println("Intercepted!")
    }
}
```
{% endcode %}

{% code title="MyService.java" %}
```java
@Service
public class MyService {
    public void doSomething() {
        System.out.println("Doing business method...");
    }
}
```
{% endcode %}

* A **pointcut** is the string within the Advice annotation, and it allows to specify on what event\(s\) the aspect should run - e.g., execution of a method, accessing a variable, execution of any method in a class, etc.
* A **joinpoint** is created in Spring anytime an event that can be matched against a pointcut is fired, and if any pointcuts match, the corresponding methods will be invoked. It is possible to access the joinpoint from the aspect method, in order to retrieve information about the original method invocation:

```java
@Aspect
@Component
public class MyAspect {
    @Before("execution(void MyService.doSomething(..))")
    public void intercept(JoinPoint joinPoint) {
        System.out.println("Intercepted! Args: " + Arrays.toString(joinPoints.getArgs()));
    }
}
```

### Chapter 10: Web Application Development in Spring

* The Spring MVC component allows us to use the popular Model-View-Controller pattern to create applications that handle web requests and produce responses. In order to use it, we need to add `spring-webmvc` as a dependency in the `pom.xml` file.
* The `web.xml` file \(in the webapp/WEB-INF folder\) is a deployment descriptor, which tells the essential configuration the app needs to run to its web container \(JBoss, Tomcat, etc.\)

```markup
<web-app>
    <servlet>
        <servlet-name>myapp</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    
    <servlet-mapping>
        <servlet-name>myapp</servlet-name>
        <url-pattern>/myapp/*</url-pattern>
    </servlet-mapping>
</web-app>
```

* The `@Controller` annotation tells Spring MVC that the annotated class is a Controller.

```java
@Controller
public class MyController {
    @GetMapping(path="/greeting")
    public String greet() {
        return "greet"; // this is the name of the view that Spring MVC will try to resolve
    }
}
```

* We can also access path variables and request parameters as arguments to the method:

```java
@Controller
public class MyController {
    @GetMapping(path="/greeting/{userName}")
    public ModelAndView greet(
        @PathVariable("userName") String user,
        @RequestParam String mood) {
        
        Map<String, String> values = new HashMap<>();
        values.put("name", user);
        values.put("mood", mood);
        
        ModelAndView view = new ModelAndView("greet", values);
        return view;
    }
}
```

## Resources

### Courses

* [Mastering Spring Framework Fundamentals](https://www.packtpub.com/product/mastering-spring-framework-fundamentals-video/9781801079525) - Matthew Speake

### Websites

* [Official documentation](https://docs.spring.io/spring-framework/docs/current/reference/html/)

