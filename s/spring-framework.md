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



## Resources

### Websites

* [Official documentation](https://docs.spring.io/spring-framework/docs/current/reference/html/)

