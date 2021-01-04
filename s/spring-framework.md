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

## Resources

### Websites

* [Official documentation](https://docs.spring.io/spring-framework/docs/current/reference/html/)

