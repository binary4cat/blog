---
title: Spring Web Annotations
date: 2018-06-13T10:09:46+08:00
categories: ["Java"]
tags: ["Spring Annotations"]
permalink: Spring-Web-Annotations-1
description: Spring MVC注解入门
photos: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1528780019766&di=68e5f2f2eef5b87bf5b5d54455fe994f&imgtype=jpg&src=http%3A%2F%2Fimg1.imgtn.bdimg.com%2Fit%2Fu%3D2161947716%2C2940818680%26fm%3D214%26gp%3D0.jpg
---
> 原文地址：[http://www.baeldung.com/spring-mvc-annotations](http://www.baeldung.com/spring-mvc-annotations)
> 此篇为译文，由于英语水平有限，可能对原文的理解有误，欢迎指正，不甚感激！
<!-- TOC -->

- [1. 概览](#1-%E6%A6%82%E8%A7%88)
- [2. `@RequestMapping`](#2-requestmapping)
- [3. `@RequestBody`](#3-requestbody)
- [4. `@PathVariable`](#4-pathvariable)
- [5. `@RequestParam`](#5-requestparam)
- [6. 响应相关的注解](#6-%E5%93%8D%E5%BA%94%E7%9B%B8%E5%85%B3%E7%9A%84%E6%B3%A8%E8%A7%A3)
    - [6.1. `@ResponseBody`](#61-responsebody)
    - [6.2. `@ExceptionHandler`](#62-exceptionhandler)
    - [6.3. `@ResponseStatus`](#63-responsestatus)
- [7. 其他Web相关注解](#7-%E5%85%B6%E4%BB%96web%E7%9B%B8%E5%85%B3%E6%B3%A8%E8%A7%A3)
    - [7.1. `@RestController`](#71-restcontroller)
    - [7.2. `@ModelAttribute`](#72-modelattribute)
    - [7.3. `@CrossOrigin`](#73-crossorigin)
- [8. 总结](#8-%E6%80%BB%E7%BB%93)

<!-- /TOC -->

# 1. 概览
在本篇教程中，我们将要探讨Spring web开发有关的注解(Spring MVC注解)，这些注解存在于`org.springframework.web.bind.annotation`包中。

# 2. `@RequestMapping`
简而言之，`@RequestMapping`标记`@Controller`类中的请求处理器(request handler)方法。可用的配置有：
- `path`：或者别名`name`、`value`都可以作为参数名，它指定了哪个URL映射到当前的方法。
- `method`：协调处理HTTP请求的方法。
- `params`：根据HTTP请求参数的存在、不存在或者值来过滤请求。
- `headers`：根据HTTP请求的`headers`的存在、不存在或者值来过滤请求。
- `consumes`：限制哪些HTTP请求主体中的媒体类型可以被该方法使用。
- `produces`：限制改方法可以响应哪些HTTP媒体类型。   


<!--more-->


来看一个简单的例子：

```Java
@Controller
class VehicleController {
 
    @RequestMapping(value = "/vehicles/home", method = RequestMethod.GET)
    String home() {
        return "home";
    }
}
```

如果我们在类级别上应用此注释，我们可以为`@Controller`类中的所有处理程序方法(request handler)提供默认设置。唯一的区别就是Spring在寻找URL映射的时候，不仅仅只看方法或者类上面的配置，而是会结合起来：

```Java
@Controller
@RequestMapping(value = "/vehicles", method = RequestMethod.GET)
class VehicleController {
 
    @RequestMapping("/home")
    String home() {
        return "home";
    }
}
```

此外，可以使用`@GetMapping`、`@PostMapping`、`@PutMapping`、`@DeleteMapping`以及`@PatchMapping`这些不同的`@RequestMapping`变种，可以单独的设置`GET`、`POST`、`PUT`、`DELETE`、`PATCH `请求。

这些是在Spring4.3版本以后可用的。

# 3. `@RequestBody`
使用`@RequestBody`注解可以将HTTP请求body体映射到一个对象：

```Java
@PostMapping("/save")
void saveVehicle(@RequestBody Vehicle vehicle) {
    // ...
}
```

反序列化是自动的，当然着取决于请求的content type的类型。

# 4. `@PathVariable`
接下来，我们来谈谈`@PathVariable`。

此注解表示将方法参数绑定到URI模板变量。我们可以使用`@RequestMapping`注解指定URI模板变量，然后使用`@PathVariable`指定需要绑定的方法参数。

我们可以使用`name`或者别名绑定，有`value`参数的例子：

```Java
@RequestMapping("/{id}")
Vehicle getVehicle(@PathVariable("id") long id) {
    // ...
}
```

如果方法的参数名和URI模板变量的名称一致，那么我们就不需要在`@PathVariable`注解中指定了：

```Java
@RequestMapping("/{id}")
Vehicle getVehicle(@PathVariable long id) {
    // ...
}
```

此外，我们还可以标记这个URI模板变量是不是必需的：

```Java
@RequestMapping("/{id}")
Vehicle getVehicle(@PathVariable(required = false) long id) {
    // ...
}
```

# 5. `@RequestParam`
我们可以使用`@RequestParam`注解访问HTTP请求参数：

```Java
@RequestMapping
Vehicle getVehicleByParam(@RequestParam("id") long id) {
    // ...
}
```

`@RequestParam`注解的配置选项和`PathVariable`注解一致。

除了一致的配置以外，当Spring从绑定的HTTP请求参数中获得了一个空值，或者根本没找到这个参数的时候，`@RequestParam`还可以为方法参数设置一个默认值，为了实现这一点，我们需要使用`defaultValue`这个参数：

提供默认的值的情况下，`required`参数会被设置为`false`(博主注：也就是无论如何都赋一个值)：

```Java
@RequestMapping("/buy")
Car buyCar(@RequestParam(defaultValue = "5") int seatCount){
    // ...
}
```

除了访问HTTP请求的参数，像HTTP请求中的其他数据也可以访问到：`cookies`和`headers`，访问它们可以使用另外两个注解`@CookieValue`和`@RequestHeader`。可以像使用`@RequestParam`一样来配置这两个注解。

# 6. 响应相关的注解

## 6.1. `@ResponseBody`
如果我们对一个请求处理器(request handler)方法使用`@ResponseBody`注解，那么Spring会把这个方法的返回值当作响应本身(博主注：也就是不会当作视图名称返回，当作rest API使用可以返回json数据)：

```Java
@ResponseBody
@RequestMapping("/hello")
String hello() {
    return "Hello World!";
}
```

如果我们在`@Controller`类上使用`@ResponseBody`注解，那么其内部所有的请求处理器(request handler)方法都会生效。

## 6.2. `@ExceptionHandler`
使用这个注解，我们可以声明一个自定义的错误处理器(error handler)方法。当请求处理器(request handler)方法抛出任何指定的异常时，Spring会调用此自定义方法。

被捕获的异常可以作为参数传递给方法：

```Java
@ExceptionHandler(IllegalArgumentException.class)
void onIllegalArgumentException(IllegalArgumentException exception) {
    // ...
}
```

博主注：上面的方法定义了一个处理`IllegalArgumentException`类型异常的方法，当任何的请求处理器(request handler)方法抛出这个异常，就会自动执行该方法。

## 6.3. `@ResponseStatus`
如果我们使用`@ResponseStatus`注解标注一个请求处理器(request handler)方法，则可以自定义一个期望返回的HTTP状态码。我们可以使用`code`参数声明状态码，或者别名也可以。

另外，我们也可以使用`reason`参数提供一个“reason”。

可以在`@ExceptionHandler`方法上使用`@ResponseStatus`：

```Java
@ExceptionHandler(IllegalArgumentException.class)
@ResponseStatus(value = HttpStatus.BAD_REQUEST,reason = "用户名和密码不匹配!")
void onIllegalArgumentException(IllegalArgumentException exception) {
    // ...
}
```

博主注：reason会在返回的响应体中出现。

# 7. 其他Web相关注解
一些注解并不能直接的管理HTTP请求或者响应，在这一节中，我们将介绍这种最常见的注解。

## 7.1. `@RestController`
`@RestController`注解合并了`@Controller`和`@ResponseBody`这两个注解的功能。(博主注：也就是实现rest API的关键)

因此，下面这两组代码实现的功能是相同的：

```Java
@Controller
@ResponseBody
class VehicleRestController {
    // ...
}
```

```Java
@RestController
class VehicleRestController {
    // ...
}
```

## 7.2. `@ModelAttribute`
通过这个注解我们可以通过设置的数据模型(model)`key`访问已经在MVC控制器中的数据模型(model)：

```Java
@PostMapping("/assemble")
void assembleVehicle(@ModelAttribute("vehicle") Vehicle vehicleInModel) {
    // ...
}
```

类似`@PathVariable`和`@RequestParam`注解，我们如果方法参数名和数据模型(model)`key`一致，就可省略：

```Java
@PostMapping("/assemble")
void assembleVehicle(@ModelAttribute Vehicle vehicle) {
    // ...
}
```

除了这些，`@ModelAttribute`还有另外一个地方会用到：如果我们在一个方法上标注，Spring会自动将方法的返回值添加到这个数据模型(model)中：

```Java
@ModelAttribute("vehicle")
Vehicle getVehicle() {
    // ...
}
```

类似的，如果方法名和数据模型(model)`key`一致，那么也可以省略这个`key`：

```Java
@ModelAttribute
Vehicle vehicle() {
    // ...
}
```

Spring在调用请求处理器(request handler)方法之前，他会先调用该类中所有的`@ModelAttribute`注解方法。

更多关于`@ModelAttribute`注解的信息，可以阅读[这篇文章](http://www.baeldung.com/spring-mvc-and-the-modelattribute-annotation)。

## 7.3. `@CrossOrigin`
`@CrossOrigin`注解可以在请求处理器(request handler)方法上启用允许跨域请求：

```Java
@CrossOrigin
@RequestMapping("/hello")
String hello() {
    return "Hello World!";
}
```

如果我们标记在一个类上，那么该类下面的所有请求处理器(request handler)方法都会允许跨域请求。

我们可以用这个注解的参数来微调CORS行为。

更多的信息，请查阅[这篇文章](http://www.baeldung.com/spring-cors)。

# 8. 总结
在这片文章中，我们展现了怎么在Spring MVC中处理HTTP请求和响应，同样，文章中的示例代码可以在我们的[GitHub](https://github.com/eugenp/tutorials/tree/master/spring-mvc-java)上获得。
