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

## Resources

### Websites

* [Official documentation](https://docs.spring.io/spring-framework/docs/current/reference/html/)

