---
title: Spring Boot Annotations
date: 2018-06-14T11:00:01+08:00
categories: Java
tags: Spring Annotations
permalink: Spring-Boot-Annotations-1
description: Spring Boot注解概览、入门
photos: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1528780019766&di=68e5f2f2eef5b87bf5b5d54455fe994f&imgtype=jpg&src=http%3A%2F%2Fimg1.imgtn.bdimg.com%2Fit%2Fu%3D2161947716%2C2940818680%26fm%3D214%26gp%3D0.jpg
---
> 原文地址：[http://www.baeldung.com/spring-boot-annotations](http://www.baeldung.com/spring-boot-annotations)
> 此篇为译文，由于英语水平有限，可能对原文的理解有误，欢迎指正，不甚感激！
<!-- TOC -->

- [1. 概览](#1-%E6%A6%82%E8%A7%88)
- [2. `@SpringBootApplication`](#2-springbootapplication)
- [3. `@EnableAutoConfiguration`](#3-enableautoconfiguration)
- [4. Auto-Configuration条件注解](#4-auto-configuration%E6%9D%A1%E4%BB%B6%E6%B3%A8%E8%A7%A3)
    - [4.1. `@ConditionalOnClass`与`@ConditionalOnMissingClass`](#41-conditionalonclass%E4%B8%8Econditionalonmissingclass)
    - [4.2. `@ConditionalOnBean`与`@ConditionalOnMissingBean`](#42-conditionalonbean%E4%B8%8Econditionalonmissingbean)
    - [4.3. `@ConditionalOnProperty`](#43-conditionalonproperty)
    - [4.4. `@ConditionalOnResource`](#44-conditionalonresource)
    - [4.5. `@ConditionalOnWebApplication`与`@ConditionalOnNotWebApplication`](#45-conditionalonwebapplication%E4%B8%8Econditionalonnotwebapplication)
    - [4.6. `@ConditionalExpression`](#46-conditionalexpression)
    - [4.7. `@Conditional`](#47-conditional)
- [5. 总结](#5-%E6%80%BB%E7%BB%93)

<!-- /TOC -->

# 1. 概览
Spring Boot的自动配置功能使创建Spring配置变得非常容易。

在这个入门教程，我们将要探讨Spring Boot相关的注解，其注解来自于两个包：`org.springframework.boot.autoconfigure `和`org.springframework.boot.autoconfigure.condition`。
<!--more-->
# 2. `@SpringBootApplication`
我们使用这个注解在Spring Boot应用中创建一个入口类(main class)：
```Java
@SpringBootApplication
class VehicleFactoryApplication{
    public static void main(String[] args){
        SpringApplication.run(VehicleFactoryApplication.class, args);
    }
}
```

`@SpringBootApplication`注解封装了`@Configuration`、`@EnableAutoConfiguration`和`@ComponentScan`这三个注解的功能和默认属性。

# 3. `@EnableAutoConfiguration`
`@EnableAutoConfiguration`注解就像它的名字一样：启用自动注解。这意味着Spring Boot会在其类路径中查找并且自动配置bean，然后自动应用它们。

Note：我们必须同时使用`@Configuration`注解：
```Java
@Configuration
@EnableAutoConfiguration
class VehicleFactoryConfig{}
```

# 4. Auto-Configuration条件注解
通常情况下，当我们编写自定义自动配置(auto-configurations)的时候，我们想要Spring有条件的使用它们，我们可以使用本节中介绍的注解来实现这一点。

我们可以将本节中介绍的注释放置在`@Configuration`类或`@Bean`方法上。

在接下来的部分中，我将仅仅介绍基础的相关概念，需要更进一步的了解，请查阅[这篇文章](http://www.baeldung.com/spring-boot-custom-auto-configuration)。

## 4.1. `@ConditionalOnClass`与`@ConditionalOnMissingClass`
使用这个条件注解，Spring会根据注解参数中传入的类存在与否，来判断是否使用这个`@Configuration`类。(博主注：`@ConditionalOnClass({Source.class, Data.class})`表示当`Source`和`Data`这两个类存在的时候，才使用被标记的类，`@ConditionalOnMissingClass`则正好相反。)
```Java
@Configuration
@ConditionalOnClass(DataSource.class)
class MySQLAutoconfiguration {
    //...
}
```

## 4.2. `@ConditionalOnBean`与`@ConditionalOnMissingBean`
当我们想使用一个具体的bean是否存在作为特定条件的时候，就可以使用这两个注解：
```Java
@Bean
@ConditionalOnBean(name = "dataSource")
LocalContainerEntityManagerFactoryBean entityManagerFactory() {
    // ...
}
```

## 4.3. `@ConditionalOnProperty`
使用该注解，我们可以为属性(properties)的值设置一个特定条件：
```Java
@Bean
@ConditionalOnProperty(
    name = "usemysql", 
    havingValue = "local"
)
DataSource dataSource() {
    // ...
}
```

## 4.4. `@ConditionalOnResource`
我们可以使用这个注解，让Spring在指定的资源存在的时候才使用被标注的定义：
```Java
@ConditionalOnResource(resources = "classpath:mysql.properties")
Properties additionalProperties() {
    // ...
}
```

## 4.5. `@ConditionalOnWebApplication`与`@ConditionalOnNotWebApplication`
通过这个注解，我们可以设置一个条件，当前的应用是否是一个web应用：
```Java
@ConditionalOnWebApplication
HealthCheckController healthCheckController() {
    // ...
}
```

## 4.6. `@ConditionalExpression`
在复杂的情况下我们可以使用该注解，当SpEL表达式的结果为`true`的时候，Spring才会使用被标注的定义：
```Java
@Bean
@ConditionalOnExpression("${usemysql} && ${mysqlserver == 'local'}")
DataSource dataSource() {
    // ...
}
```

## 4.7. `@Conditional`
甚至在更复杂的条件下，我们可以创建一个自定义的条件注解，`@Conditional`会告诉Spring使用这个自定义的条件：
```Java
@Conditional(HibernateCondition.class)
Properties additionalProperties() {
    //...
}
```

# 5. 总结
在本篇中，我们展示了一个概览，如何在微调自动配置，对自动配置设置一个条件。

同样的，本片中的示例代码可以在我们的[GitHub](https://github.com/eugenp/tutorials/tree/master/spring-mvc-java)上获取。