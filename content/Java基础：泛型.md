---
title: Java基础：泛型
date: 2017-06-08T22:37:01+08:00
categories: ["Java"]
tags: ["Java基础"]
permalink:
description: Java泛型学习
photos: http://img.mp.sohu.com/upload/20170809/7f6678264b154d028f0e36e9159c8e9a.png
---
<!-- TOC -->

- [泛型概述](#泛型概述)
- [Java中的伪泛型](#java中的伪泛型)
- [泛型类](#泛型类)
- [泛型的方法](#泛型的方法)
- [泛型的接口](#泛型的接口)
- [泛型的通配符](#泛型的通配符)
- [泛型的限定](#泛型的限定)

<!-- /TOC -->
### 泛型概述
- Java 泛型（generics）是 JDK 5 中引入的一个新特性, 泛型提供了编译时类型安全检测机制，该机制允许程序员在编译时检测到非法的类型。
- 泛型的本质就是参数化类型，也就是说把数据类型作为一个参数传递。例如：`private class Itr implements Iterator<E>{...}`

### Java中的伪泛型
- 泛型只在编译时存在，编译之后的class文件中并不存在泛型。
    - 例如:ArrayList<String> al=new ArrayList<String>();
    - 编译后:ArrayList al = new ArrayList();
- 泛型只是在编译期进行的类型安全检查机制，避免程序可能发生的错误。
<!--more-->
### 泛型类
- 定义格式：
```java
class ArrayList<E>{ 
    public boolean add(E e){ }
    public E get(int index){  }
}
```

- 使用格式：
    - 创建对象时，确定泛型的类型
```java
ArrayList<String> list = new ArrayList<String>();
//此时，变量E的值就是String类型
class ArrayList<String>{ 
    public boolean add(String e){ }
    public String get(int index){  }
}

ArrayList<Integer> list = new ArrayList<Integer>();
//此时，变量E的值就是Integer类型
class ArrayList<Integer>{ 
    public boolean add(Integer e){ }
    public Integer get(int index){  }
}
```


### 泛型的方法
- 定义格式：
```java
public <T> T[] toArray(T[] a){  } 
```

- 使用格式：调用方法时，确定泛型的类型
```java
//变量T的值就是String类型
public <String> String[] toArray(String[] a){ 
    //TODO
} 
//变量T的值就是Integer类型
public <Integer> Integer[] toArray(Integer[] a){ 
    //TODO
} 
```

### 泛型的接口 
- 带有泛型的接口
```java
public interface List <E>{
    abstract boolean add(E e);
}

public class ArrayList<E> implements List<E>{
}

public static void function(){
    List<String> al = new ArrayList<String>();  //指定了数据类型
}
```


### 泛型的通配符   
- 泛型的通配符就相当于给泛型穿了一个Object类型，也就是可以给泛型传递任何类型的参数。
- 用在集合中，那么就相当于之前的没有使用泛型的集合，因为没有限制了。
```java
public class Test {
    public static void main(String[] args) {
        ArrayList<String> al = new ArrayList<String>();
        HashSet<Integer> hs = new HashSet<Integer>();
        al.add("123");
        hs.add(123);
      
        iterator(array);
        iterator(set);
    }
    
    public static void iterator(Collection<?> coll){
        Iterator<?> it = coll.iterator();
        while(it.hasNext()){
            System.out.println(it.next());
        }
    }
}
```

### 泛型的限定
- 由于通配符太灵活了，容易出错，所以引入了限制条件，这就是限定通配符
```java
class Parent  {}
class Subclass extends Parent {}
class Sub extends Parent{}

public class Test {
    public static void main(String[] args) {       
        Collection<Sub> subs = new ArrayList<Sub>();
        subs.add(new Sub());
        Collection<Subclass> scs = new ArrayList<Subclass>();
        scs.add(new Subclass());
        //可以直接调用function方法，因为function方法的限定符指定了只要继承Parent类就可以
        extFunc(subs);
        extFunc(scs);

        Collection<Parent> pars = new ArrayList<Parent>();
        //必须传递父类
        supFunc(pars);
    }
    //限制父类，必须继承这个父类才能传递
    public static void extFunc(Collection<? extends Parent>){
        //TODO

    }  
    //限制子类，必须传递它的父类才可以
    public static void supFunc(Collection<? super Subclass>){
        //TODO

    } 

}
```

- `? extends className` 限制的是父类, 上限限定, 可以传递它的子类对象
- `? super   className` 限制的是子类, 下限限定, 可以传递它的父类对象
