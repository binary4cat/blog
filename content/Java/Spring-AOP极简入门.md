---
title: Spring-AOP极简入门
date: 2018-05-30T22:36:50+08:00
categories: ["Java"]
tags: ["Spring"]
permalink: Spring-AOP-Minimalist-Getting-Started
description: SpringAop入门教程
photos: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1527701268185&di=7ca52773e094b8ee3c40a78ccdf701d9&imgtype=jpg&src=http%3A%2F%2Fimg3.imgtn.bdimg.com%2Fit%2Fu%3D4172169013%2C464131932%26fm%3D214%26gp%3D0.jpg
---

# 1. APO介绍

## 1.1. 简介

按照惯例先来一份[维基百科的解释](https://zh.wikipedia.org/wiki/面向侧面的程序设计)：

> 面向侧面的程序设计（aspect-oriented programming，AOP，又译作面向方面的程序设计、观点导向编程、剖面导向程序设计）是计算机科学中的一个术语，指一种程序设计范型。该范型以一种称为侧面（aspect，又译作方面）的语言构造为基础，侧面是一种新的模块化机制，用来描述分散在对象、类或函数中的横切关注点（crosscutting concern）。<!--more-->

一句话解释AOP就是：AOP可以在不修改源码的情况下对程序进行增强。

举个栗子：![举个栗子](/image/face/举个栗子.jpg)

　　用这个简图示范一下AOP的具体使用，可能会更容易理解AOP是什么。

　　假设我们已经完成了所有的Web、Service、Dao层的代码，但是现在程序出了Bug，这时你想到在程序中加入日志，每当程序出现异常，就记录下来异常的信息，方便排查，这样做很对，但是代码已经很多了，不可能在每个Method中都加入一个记录日志的代码，这样做太麻烦了，而且很不优雅。

　　这时候，我们的AOP就上场了，不用改动任何的现有代码，只需加入记录日志操作的代码，并且配置每当程序抛出未捕获(或者任何异常)异常时就记录日志。

　　又或者是，在Service调用Dao层的数据库操作代码前后，加入事务。

　　这里的*日志记录问题*和*事务记录问题*就是**横切关注点**。

　　不破坏现有的业务代码对程序增加功能，这就是AOP存在的意义。

![AOP演示](/image/Java/111111.png)

## 1.2. AOP名词

- `Joinpoint`(连接点)：目标对象中所有可以被增强的方法。
- `Pointcut`(切入点)：目标对象中已经被增强的方法。
- `Advice`(通知)：用来增强的代码。
- `Target`(目标对象)：被代理的对象。
- `Weaving`(织入)：将通知(Advice)应用到切入点(Pointcut)的过程。
- `Proxy`(代理)：将通知织入到目标对象之后形成代理对象。
- `Aspect`(切面)：切入点(Pointcut)**+**(Advice)通知。

# 2. Java动态代理介绍

## 2.1. 代理模式

　　介绍Java的动态代理之前，先提一下代理模式，代理模式就是为其他对象提供一种代理，以控制对这个对象的访问。这么说可能不好理解，举个例子：我们现在要租房子，一般都是通过中介公司找房子，而不是直接找房东，在这个过程中中介公司就相当于一个代理(Proxy)，我们(client)并不能直接和房东(bean)联系，而是通过中介的联系，这样的好处就是，房东不用到处找客户，只需要把房子交给中介，自己该干嘛干嘛，而我们只需要找到靠谱的中介，就免去了和房东的一些理不清的纠纷。

　　而程序中的代理，就是为了给我们已经写好的代码增强功能，也就是AOP的具体实现模式。

## 2.2. Java中的动态代理

　　动态类型语言入python、ruby等，可以在运行时动态的对类进行修改，而在Java中这是不被允许的，因为Java属于静态类型语言，运行时类已经被编译成字节码，无法对其进行修改(并不绝对，我们接下来介绍的CGLib就是个例外)。

　　为了实现在运行时动态的修改类型定义，Java5引入了一个新特性就是动态代理，而这个特性并不是特别的优雅，Java规定了需要动态代理的类，必须实现接口才行，这样被代理类和动态创建的代理类都会继承该接口，从而实现类的代理。

## 2.3. 代码示例:

接口`IHello`：

```java
package Proxy_1;

public interface IHello {
    public void sayHello();
}
```

被代理对象`Hello`：

```java
package Proxy_1;

public class Hello implements IHello {

    public void sayHello() {
        System.out.print("Hello World!");
    }
}
```

代理实现类`ProxyHandler`：

Java规定，代理实现类必须继承`InvocationHandler`接口，并且实现其`invoke`方法。

```java
package Proxy_1;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class ProxyHandler implements InvocationHandler {

    private Object target;
    // 绑定被代理对象，返回代理
    public Object bind(Object target){
        this.target=target;
        // 返回生成的代理
        return Proxy.newProxyInstance(target.getClass().getClassLoader(),target.getClass().getInterfaces(),this);
    }
    // 这里就是写扩展代码的地方
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        Object result;

        /*目标方法调用之前*/
        System.out.print("Before sayHello\n");
        result=method.invoke(target,args);
        /*目标方法调用之后*/
        System.out.print("\nAfter sayHello");

        return result;
    }
}
```

测试类`TestProxy`：

```java
import Proxy_1.Hello;
import Proxy_1.IHello;
import Proxy_1.ProxyHandler;

public class TestProxy {
    public static void main(String args[]){
        ProxyHandler proxy=new ProxyHandler();

        IHello hello=(IHello)proxy.bind(new Hello());
        hello.sayHello();
    }
}

```

输出：

```shell
Before sayHello
Hello World!
After sayHello
```

# 3. CGLib代理

## 3.1. cglib介绍

　　[cglib](https://github.com/cglib/cglib)是一个开源的Java类库，提供了比Java动态代理更加强大的代理功能，cglib不需要动态代理的对象实现任何接口，比起Java自身的动态代理着实好用了许多。

## 3.2. cglib与JDK动态代理

cglib和Java动态代理的主要区别：

- Java动态代理是利用反射机制生成一个实现代理接口的匿名类，在调用目标方法前调用`InvokeHandler`来执行一些自定义的处理。
- cglib底层使用了一个`ASM`开源包，直接对代理对象编译后的class文件进行修改，修改其字节码，给代理对象生成一个子类，由子类实现代理功能。
- 实现方式上，Java动态代理使用的是反射查找指定接口的实现类；cglib是给代理对象生成子类，从而继承其能力，实现代理功能。
- 如果目标对象被`final`修饰，那么该类就无法被cglib代理了。

两种方式对比，cglib比较方便灵活一些，在spring-aop中两种代理方式都使用到了，默认情况下使用的是Java动态代理，但是如果目标对象没有实现接口，那么必须强制使用cglib库进行代理。

强制使用cglib需要在spring配置中加入：`<aop:aspectj-autoproxy proxy-target-class="true"/>`

## 3.3. cglib库实现代理示例：

代理对象`Hello`：

使用cglib不需要实现任何接口。

```java
package dome_1;

public class Hello {
    public void sayHello(){
        System.out.print("Hello World!");
    }
}
```

Cglib代理类`CglibProxyHandler`:

需要继承cglib库的`MethodInterceptor`的`intercept`方法，该方法类似于JDK代理的`invoke`方法。

```java
package dome_1;

import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

public class CglibProxyHandler implements MethodInterceptor {

    // 获得一个代理对象实例
    public Object getProxy(Class c){
        Enhancer enhancer=new Enhancer();
        enhancer.setSuperclass(c);
        enhancer.setCallback(this);
        return enhancer.create();
    }

    @Override
    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        /*目标方法调用之前*/
        System.out.print("Before sayHello\n");
        Object returnValue = methodProxy.invokeSuper(o, objects);
        /*目标方法调用之后*/
        System.out.print("\nAfter sayHello");

        return returnValue;
    }
}
```

测试类`TestProxy`:

```java
import dome_1.CglibProxyHandler;
import dome_1.Hello;

public class TestProxy {
    public static void main(String args[]){
        CglibProxyHandler proxy=new CglibProxyHandler();
        // 获得一个代理对象
        Hello hello=(Hello) proxy.getProxy(Hello.class);
        hello.sayHello();
    }
}
```

执行结果：

```shell
Before sayHello
Hello World!
After sayHello
```

# 4. Spring AOP的实现

## 4.1. 使用IDEA创建一个Maven项目，添加下列依赖：

```java
<dependencies>
        <dependency>
            <groupId>cglib</groupId>
            <artifactId>cglib</artifactId>
            <version>3.2.6</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>4.3.17.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>4.3.17.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>4.3.17.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjrt</artifactId>
            <version>1.9.1</version>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjtools</artifactId>
            <version>1.9.1</version>
        </dependency>
    </dependencies>
```

## 4.2. 创建代理类`Hello`：

```java
package spring_aop;

public class Hello {
    public void listen() {
        System.out.println("Listen Hello World!");
    }

    public void speak() {
        System.out.println("Speak Hello World!");
    }

    public void read() {
        System.out.println("Read Hello World!");
    }

    public void write() {
        System.out.println("Write Hello World!");
    }
}
```

## 4.3. 创建一个增强代码类`MyAdvice`：

AOP的五种通知类型：

**前置通知**(`before`) ：在目标方法执行之前执行。
**后置通知**(`after-returning`) ：在目标方法执行之后执行,出现异常时不会执行。
**环绕通知**(`around`) ：在目标方法执行前和执行后执行。
**异常抛出通知**(`after-throwing`)：在目标方法执行出现异常的时候执行。
**最终通知**(`after`) ：无论目标方法是否出现异常最终通知都会执行。

```java
package spring_aop;

import org.aspectj.lang.ProceedingJoinPoint;

public class MyAdvice {
    public void before() {
        System.out.println("我是前置通知");
    }

    public void afterReturning() {
        System.out.println("我是后置通知(出现异常不会调用我)");
    }

    public Object around(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("我是环绕在切入点之前的通知");
        Object proceed = pjp.proceed();   // 调用目标方法
        System.out.println("我是环绕在切入点之后的通知");
        return proceed;
    }

    // 程序抛出异常的时候，会调用该方法，可以在此收集日志。
    public void afterException() {
        System.out.println("出现异常啦！！！");
    }
    public void after() {
        System.out.println("我是后置通知(出现异常也会调用我)");
    }
}
```

## 4.4. 接下来创建一个spring的配置文件`spring.xml`

切点表达式简介(针对下面的表达式解释，具体请查阅网络更详细的解释)：

| 符号             | 含义                                           |
| :--------------- | :--------------------------------------------- |
| `execution()`    | 表示切点表达式的主体                           |
| 第一个`*`        | 表示返回值，`*`表示任意返回值                  |
| spring_aop.Hello | 切点所在的类名，需要完全限定名                 |
| 包名后的`.`      | 表示当前类(包)，`..`两个点就表示当前的包和子包 |
| 第二个`*`        | 表示该类下所有的方法                           |
| `(..)`           | 表示方法可以有任意参数                         |

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">

    <bean name="hello" class="spring_aop.Hello"></bean>
    <bean name="myAdvice" class="spring_aop.MyAdvice"></bean>

    <aop:config>
        <aop:pointcut id="muSay" expression="execution(* spring_aop.Hello.*(..))"/>
        <aop:aspect ref="myAdvice">
            <aop:before method="before" pointcut-ref="muSay"/>
            <aop:after-returning method="afterReturning" pointcut-ref="muSay"/>
            <aop:around method="around" pointcut-ref="muSay"/>
            <aop:after-throwing method="afterException" pointcut-ref="muSay"/>
            <aop:after method="after" pointcut-ref="muSay"/>
        </aop:aspect>
    </aop:config>
</beans>
```

## 4.5. 测试类`TestAop`:

```java
import org.springframework.context.support.ClassPathXmlApplicationContext;
import spring_aop.Hello;

public class TestAop {
    public static void main(String args[]){
        //1 创建容器对象
        ClassPathXmlApplicationContext ac;
        ac = new ClassPathXmlApplicationContext("spring.xml");

        // 从容器中获取bean
        Hello hello=(Hello)ac.getBean("hello");
        hello.listen();
        System.out.println("*****************");
        hello.speak();
    }
}
```

## 4.6. 输出：

```shell
我是前置通知
我是环绕在切入点之前的通知
Listen Hello World!
我是后置通知(出现异常也会调用我)
我是环绕在切入点之后的通知
我是后置通知(出现异常不会调用我)
*****************
我是前置通知
我是环绕在切入点之前的通知
Speak Hello World!
我是后置通知(出现异常也会调用我)
我是环绕在切入点之后的通知
我是后置通知(出现异常不会调用我)
```

# 5. 使用注解实现Spring AOP

使用注解则省去了spring中的`aop:config`配置，只需要增加一个使用注解的配置`<aop:aspectj-autoproxy></aop:aspectj-autoproxy>`即可：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    <!-- 配置通知对象 -->
    <bean name="hello" class="spring_aop.Hello"></bean>
    <!-- 配置通知对象 -->
    <bean name="myAdvice" class="spring_aop.MyAdvice"></bean>
    <!-- 开启使用注解 -->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
```

在`MyAdvice`类中使用注解标记各个通知即可：

- `@Aspect`表示这个类是一个通知类。
- `@Pointcut`可以标记一个空方法，当通知类里面的切点表达式相同时，可以定义一个公用的切点表达式方法，在其他注解就可以直接使用该方法的完全限定名了。

```java
package spring_aop;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;

@Aspect
public class MyAdvice {
    // 定义一个公用的切点表达式
    @Pointcut("execution(* spring_aop.Hello.*(..))")
    public void expression(){}


    @Before("spring_aop.MyAdvice.expression()")
    public void before() {
        System.out.println("我是前置通知");
    }
    @AfterReturning("spring_aop.MyAdvice.expression()")
    public void afterReturning() {
        System.out.println("我是后置通知(出现异常不会调用我)");
    }
    @Around("execution(* spring_aop.Hello.*(..))")
    public Object around(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("我是环绕在切入点之前的通知");
        Object proceed = pjp.proceed();   // 调用目标方法
        System.out.println("我是环绕在切入点之后的通知");
        return proceed;
    }
    @AfterThrowing("execution(* spring_aop.Hello.*(..))")
    public void afterException() {
        System.out.println("出现异常啦！！！");
    }
    @After("execution(* spring_aop.Hello.*(..))")
    public void after() {
        System.out.println("我是后置通知(出现异常也会调用我)");
    }
}
```

# 6. 参考资料

[https://zh.wikipedia.org/wiki/面向侧面的程序设计](https://zh.wikipedia.org/wiki/面向侧面的程序设计)
[https://baike.baidu.com/item/Aspectj](https://baike.baidu.com/item/Aspectj)
[https://blog.csdn.net/ABCD898989/article/details/50809321](https://blog.csdn.net/ABCD898989/article/details/50809321)
《Spring实战第四版》
