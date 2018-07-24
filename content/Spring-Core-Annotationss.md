---
title: Spring Core Annotations
date: 2018-06-12T10:15:12+08:00
categories: ["Java"]
tags: ["Spring Annotations"]
permalink: Spring-Core-Annotations-1
description: Spring 核心注解
photos: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1528780019766&di=68e5f2f2eef5b87bf5b5d54455fe994f&imgtype=jpg&src=http%3A%2F%2Fimg1.imgtn.bdimg.com%2Fit%2Fu%3D2161947716%2C2940818680%26fm%3D214%26gp%3D0.jpg
---
> 原文地址：[http://www.baeldung.com/spring-core-annotations](http://www.baeldung.com/spring-core-annotations)
> 此篇为译文，由于英语水平有限，可能对原文的理解有误，欢迎指正，不甚感激！

<!-- TOC -->

- [1. 概览](#1-%E6%A6%82%E8%A7%88)
- [2. DI有关的注解](#2-di%E6%9C%89%E5%85%B3%E7%9A%84%E6%B3%A8%E8%A7%A3)
    - [2.1. `@Autowired`](#21-autowired)
    - [2.2. `@Bean`](#22-bean)
    - [2.3. `@Qualifier`](#23-qualifier)
    - [2.4. `@Required`](#24-required)
    - [2.5. `@Value`](#25-value)
    - [2.6. `@DependsOn`](#26-dependson)
    - [2.7. `@Lazy`](#27-lazy)
    - [2.8. `@Lookup`](#28-lookup)
    - [2.9. `@Primary`](#29-primary)
    - [2.10. `@Scope`](#210-scope)
- [3. 上下文配置注解](#3-%E4%B8%8A%E4%B8%8B%E6%96%87%E9%85%8D%E7%BD%AE%E6%B3%A8%E8%A7%A3)
    - [3.1. `@Profile`](#31-profile)
    - [3.2. `@Import`](#32-import)
    - [3.3. `@ImportResource`](#33-importresource)
    - [3.4. `@PropertySource`](#34-propertysource)
    - [3.5. `@PropertySources`](#35-propertysources)
- [4. 总结](#4-%E6%80%BB%E7%BB%93)

<!-- /TOC -->

# 1. 概览  

我们可以利用`org.springframework.beans.factory.annotation`和`org.springframework.context.annotation`这两个包提供的注解功能使用Spring的依赖注入。
我们经常称它为"Spring core annotations"，我将在本教程对此进行回顾。

# 2. DI有关的注解

## 2.1. `@Autowired`
我们可以使用`@Autowired`注解标记需要Spring去解析和注入的依赖项。<!--more-->

构造函数注入：
```Java
class Car{
    Engine engine;

    @Autowired
    Car(Engine engine){
        this.engine=engine;
    }
}
```

Setter注入：
```Java
class Car{
    Engine engine;

    @Autowired
    void setEngine(Engine engine){
        this.engine=engine;
    }
}
```

字段注入：
```Java
class Car{
    @Autowired
    Engine engine;
}
```

`@Autowired`注解有一个布尔类型的名为`required`的参数，默认值为`true`。它可以调整Spring在没有找到所需的bean时的行为，当值为`true`时，会抛出异常，值为`false`时，则什么都不会发生，只不过在调用“注入”的变量时，可能会得到一个`null`引用。
Note:如果使用的时构造函数注入，那么所有构造函数的参数都是必须的。
从Spring4.3版本开始，我们可以直接省略`@Autowired`注解，就可以实现依赖的注入，除非我们声明了两个或以上的构造函数，这时才需要指定一个构造函数用`@Autowired`修饰。
更多的信息可以阅读我们的其他文章[@Autowired](http://www.baeldung.com/spring-autowire)和[constructor injection](http://www.baeldung.com/constructor-injection-in-spring)

## 2.2. `@Bean`
`@Bean`标记一个实例化Spring bean的工厂方法：
```Java
@Bean
Engine engine(){
    return new Engine();
}
```
当需要返回类型的新实例时，Spring会调用这些方法。
返回的bean的name和工厂方法的名称一致，如果想要不同的名称，可以在`@Bean`注解中传入一个`String`类型的值，这个值会作为返回的bean的name：
```Java
@Bean("engine")
Engine getEngine(){
    return new Engine();
}
```
Note:用`@Bean`注解标记的所有方法，必须在`@Configuration`标记的类中。

## 2.3. `@Qualifier`
我们使用`@Qualifier`与`@Autowired`注解，在一些使用不明确的情况下，提供所需的`bean name`或者`bean id`。
举个例子，下面的两个bean实现了同一个接口：
```Java
class Bike implements Vehicle{}

class Car implements Vehicle{}
```

如果Spring需要注入一个`Vehicle` bean，但是`Vehicle`有多个定义(实现)，在这个案例中，我们可以使用`@Qualifier`注解显示的提供一个`bean name`。

使用构造函数注入：
```Java
@Autowired
Biker(@Qualifier("bike") Vehicle vehicle){
    this.vehicle = vehicle;
}
```

使用setter注入：
```Java
@Autowired
void setVihicle(@Qualifier("bike") Vehicle vehicle){
    this.vehicle = vehicle;
}
```
或者：
```Java
@Autowired
@Qualifier
void setVihicle(Vehicle vehicle){
    this.vehicle = vehicle;
}
```

使用字段注入：
```Java
@Autowired
@Qualifier("bike")
Vehicle vehicle;
```

更多的信息，请阅读[这篇文章](http://www.baeldung.com/spring-autowire)

## 2.4. `@Required`
`@Required`注解用来在`setter`方法上标记我们想通过XML填入的依赖：
```Java
@Required
void setColor(String color){
    this.color = color;
}
```
```xml
<bean class="com.baeldung.annotations.Bike">
    <property name="color" value="green" />
</bean>
```
否则，会抛出`BeanInitializationException`异常。

## 2.5. `@Value`
我们可以使用`@Value`注解将属性值注入到bean中，`@Value`注解兼容构造函数注入、setter注入、字段注入。

构造函数注入：
```Java
Engine(@Value("8") int cyLinderCount){
    this.cyLinderCount = cyLinderCount;
}
```

setter注入：
```Java
@Autowired
void setCyLinderCount(@Value("8") int cyLinderCount){
    this.cyLinderCount = cyLinderCount; 
}
```

或者：
```Java
@Value("8")
void setCyLinderCount(int cyLinderCount){
    this.cyLinderCount = cyLinderCount; 
}
```

字段注入：
```Java
@Value("8")
int cylinderCount;
```

当然了，注入静态值是没有用的，不过我们可以在`@Value`中使用“占位字符串”的方式，写入一个外部定义的源数据。例如，在`.properties`或者`.yaml`文件。
假设有以下的`.properties`文件：
```xml
engine.fuelType=petrol
```
我们可以将engine.fuelType的值注入：
```Java
@Value("${engine.fuelType}")
String fuelType;
```

我们还可以使用`@Value`的`SpEL`表达式，关于更多的高级用法，可以阅读我们[关于@Value的文章](http://www.baeldung.com/spring-value-annotation)。

## 2.6. `@DependsOn`
我们可以使用`@DependsOn`注解标注Spring在初始化当前(被@DependsOn标记)的bean之前，先初始化其他bean。通常情况下，这个依赖注入的关系是自动完成的，Spring会查找bean之间的显式的依赖关系自动初始化。
我们仅仅需要在初始化隐式依赖关系bean的时候使用`@DependsOn`注解，例如JDBC驱动加载或者静态变量初始化(静态变量会先于类加载，所以需要提前初始化其他的bean，博主注)。
我们可以使用`@DependsOn`指定一个当前bean依赖的另一个bean的名字，`@DependsOn`注解的参数需要一个String类型的数组(或者String字符串)：
```Java
@DependsOn("engine")
class Car implements Vehicle {}

@DependsOn({"engine"})
class Car implements Vehicle {}
```

另外的，如果我们使用`@Bean`注解定义一个bean，那么这个工厂方法应该使用`@DependsOn`注解：
```Java
@Bean
@DependsOn("fuel")
Engine engine() {
    return new Engine();
}
```

## 2.7. `@Lazy`
我们使用`@Lazy`注解标记我们想要延迟初始化的bean，默认情况，Spring会在`application context` startup/bootstrapping时第一时间创建单例bean。然而，有时我们不需要在`application` startup时创建bean。
`@Lazy`注解的行为有些不同，取决与我们在什么位置使用它，我们可以在下列情况使用：
- 用在`@Bean`标记的工厂方法上，会延迟该方法的调用(对应的，工厂方法中的bean也会延迟创建)。
- `@Configuration`标记的类和`@Bean`标记的方法都会收到影响。
- 用在`@Component`标记的类上，如果它不是一个`@Configuration`类，那么就会被延迟初始化。
- 用在`@Autowired`标记的构造函数、setter方法或者字段上面，可以延迟加载这个依赖。

`@Lazy`注解有一个bool类型的参数，默认值为true，这个参数会改变`@Lazy`注解的行为。
例如，在一个全局设置为懒加载时，第一时间加载；或者配置具体的`@Bean`工厂方法先于`@Configuration`类加载：
```Java
@Configuration
@Lazy
class VehicleFactoryConfig {
 
    @Bean
    @Lazy(false)
    Engine engine() {
        return new Engine();
    }
}
```
更近一步的阅读，请查阅[this article](http://www.baeldung.com/spring-lazy-annotation)。

## 2.8. `@Lookup`
用`@Lookup`注解的方法告诉Spring在我们调用它时返回方法返回类型的一个实例。
更多的信息请查看关于`Lookup`注解的文章[can be found in this article](http://www.baeldung.com/spring-lookup)

## 2.9. `@Primary`
有时我们需要定义同类型的多个bean，这时Spring的依赖注入时不会成功的，因为Spring并不知道我们需要注入哪一个bean。
我们可能已经知道该问题的解决选项：使用`@Qualifier`标记所有的连接点，并且指定bean的name。
然而大多数时候我们只需要一个具体的bean，并不需要其他的bean，这时我们可以使用`@Primary`注解简化这个案例：我们可以用`@Primary`标注那个最常使用的bean，当注入点没有使用`@Qualifier`指定注入bean名称时，默认选择这个bean：
```Java
@Component
@Primary
class Car implements Vehicle {}
 
@Component
class Bike implements Vehicle {}
 
@Component
class Driver {
    @Autowired
    Vehicle vehicle; // 默认会注入@Primary标记的Car bean
}
 
@Component
class Biker {
    @Autowired
    @Qualifier("bike")
    Vehicle vehicle;
}
```
在上面的例子中，`Car`是被`@Primary`标记的，因此在`Driver`类中，Spring会默认注入`Car` bean。当然了在`Biker` bean中`vehicle`变量会赋值一个`Bike`对象，因为它是被`@Qualifier`明确指定为`bike` name的bean。

## 2.10. `@Scope`
我们可以使用`@Scope`注解定义`@Component`或者`Bean`的[作用域](http://www.baeldung.com/spring-bean-scopes)。作用域可以是`singleton`、`prototype`、`request`、`session`、`globalSession `中的一个，或者自定义的作用域。
举例：
```Java
@Component
@Scope("prototype")
class Engine {}
```

# 3. 上下文配置注解
我们可以使用本节描述的内容来对应用上下文进行配置。

## 3.1. `@Profile`
如果我们希望Spring仅在特定配置文件处于活动状态时才使用`@Component`类或`@Bean`方法，我们可以用`@Profile`来标记它，我们可以在`@Profile`注解的参数中传入配置文件的名称。
```java
@Component
@Profile("sportDay")
class Bike implements Vehicle {}
```

你可以从[这篇文章](http://www.baeldung.com/spring-profiles)中获取更多关于`@Profile`的信息。

## 3.2. `@Import`
使用`@Import`注解可以使用具体的`@Configuration`类而不是使用组件扫描，我们可以提供一个`@Configuration`类给`@Import`作为参数：
```java
@Import(VehiclePartSupplier.class)
class VehicleFactoryConfig {}
```

博主注：`@Import`注解其实就是把多个分散的`@Configuration`配置类给整合到一起，使用的时候用这个一个类就可以了，举个例子：
```java
@Configuration
public class JavaConfigService {
    @Bean(name="userService")
    public Car getUerService(){
        return new UserService();
    }
}

@Configuration
public class JavaConfigModel {
    @Bean(name="user")
    public Car getUser(){
        return new User();
    }
}

//将两个配置合并起来
@Configuration
@Import({JavaConfigService.class,JavaConfigModel.class})
public class GlobalConfig {
    // 其他的bean的定义
}

public static void main (String args[]){
    // 在spring中就可以使用合并配置，而不用一个一个去实例化配置了
    AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(GlobalConfig.class);
    User user = (User)context.getBean("user");
    UserService us = (UserService)context.getBean("userService");
    context.close();
}
```

## 3.3. `@ImportResource`
我们可以使用该注解导入指定的XML配置，可以使用具体的XML文件的本地路径、或者别名作为该注解的参数：
```java
@Configuration
@ImportResource("classpath:/annotations.xml")
class VehicleFactoryConfig {}
```

## 3.4. `@PropertySource`
通过这个注解，我们可以为应用程序定义属性文件：
```java
@Configuration
@PropertySource("classpath:/annotations.properties")
class VehicleFactoryConfig {}
```

`@PropertySource`利用Java 8的repeating annotations功能，这意味着我们可以使用多种手段标记一个类：
```java
@Configuration
@PropertySource("classpath:/annotations.properties")
@PropertySource("classpath:/vehicle-factory.properties")
class VehicleFactoryConfig {}
```

## 3.5. `@PropertySources`
我们可以使用这个注解来指定多个`@PropertySource`配置：
```java
@Configuration
@PropertySources({ 
    @PropertySource("classpath:/annotations.properties"),
    @PropertySource("classpath:/vehicle-factory.properties")
})
class VehicleFactoryConfig {}
```
注意，从Java 8开始，我们可以通过repeating annotations功能实现上述功能。

# 4. 总结
在本文中，我们看到了最常见的Spring核心注解概述。 我们看到了如何配置bean注入和应用程序上下文，以及如何为组件扫描标记类。这些例子都可以在我们的[GitHub](https://github.com/eugenp/tutorials/tree/master/spring-mvc-java)上获得。
