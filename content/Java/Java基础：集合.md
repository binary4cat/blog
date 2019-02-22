---
title: Java基础：集合
date: 2017-06-01T20:28:49+08:00
categories: ["Java"]
tags: ["Java基础"]
permalink:
description:
photos: http://img.mp.sohu.com/upload/20170809/7f6678264b154d028f0e36e9159c8e9a.png
---

# 1. 集合概述 
- 集合，集合是java中提供的一种容器，可以用来存储多个数据
    - 数组的长度是固定的。集合的长度是可变的
    - 集合中存储的元素必须是引用类型数据(基本类型可以使用包装类，Java内部自动帮我们做了拆装箱操作)

<!--more-->
# 2. 集合继承关系图
![](http://ww1.sinaimg.cn/large/c55a7aeely1fmeyzdwjidj20nc08wmxf.jpg)

- ArrayList的继承关系:
    - ArrayList类继承了抽象类AbstractList同时实现接口List，而List接口又继承了Collection接口
    - Collection接口为集合的最顶层接口


```java
interface List extends Collection {
}
public class ArrayList extends AbstractList implements List{
}
```


- 集合继承体系
    - 我们在使用`ArrayList`类时，该类已经把所有抽象方法进行了重写。
    - 实现`Collection`接口的所有子类都会进行方法重写。
    - `Collecton`接口常用的子接口有：`List`接口、`Set`接口
    - `List`接口常用的子类有：`ArrayList`类、`LinkedList`类
    - `Set`接口常用的子类有：`HashSet`类、`LinkedHashSet`类

   

# 3. 集合Collection的方法
- 集合Collection的方法,是集合中所有实现类必须拥有的方法
- `Object[] toArray()`  
    - 集合中的元素,转成一个数组中的元素, 集合转成数组   


```Java 
public static void function() {
    Collection<String> cet = new ArrayList<String>();
    cet.add("hai");
    cet.add("123");
    cet.add("456");
    Object[] objs = coll.toArray();
    for(int i = 0 ; i < objs.length ; i++){
        System.out.println(objs[i]);
    }
}
```


- `public abstract int size();` 
    - 返回泛型集合中有多少个元素

```Java
Collection<String> collection=new ArrayList<String>();
collection.add("hai");
collection.add("jd");
System.out.println(collection.size()); // 输出：2
```

> java中的三种获取长度的方式：
    `length`属性：针对数组的长度获取，可以获取一个数组的长度。
    `length()`方法：针对字符串`String`的，获取字符串中含有多少个字符。
    `size()`方法：针对泛型集合，获取泛型集合中有多少个元素。

- `boolean contains(Object o)`
    - 判断对象是否存在于集合中,对象存在返回true

```Java
private static void function() {
    Collection<String> coll = new ArrayList<String>();
    coll.add("hai");
    coll.add("jd");
    boolean b = coll.contains("jd");
    System.out.println(b);   //输出：true
}
```

- `void clear()`
    - 清空集合中的所有元素,集合容器本身依然存在

```java
public static void function(){
    Collection<String> coll = new ArrayList<String>();
    coll.add("hai");
    coll.add("jd");
    System.out.println(coll); //输出：[hai, jd]
    coll.clear();
    System.out.println(coll); //输出：[]
}
```

# 4. 集合Collection的remove方法
- `boolean remove(Object o)`   ：移除集合中指定的元素
- `boolean removeAll(Collection<?> c)` ：删除有元素，集合容器本身还在

```java
public static void function(){
    Collection<String> coll = new ArrayList<String>();
    coll.add("hai");
    coll.add("jd");
    System.out.println(coll);  //输出：[hai, jd]
    boolean b = coll.remove("jd");
    System.out.println(b);     //输出：true
    System.out.println(coll);  //输出：[hai]
    coll.add("jd");
	System.out.println(coll);  //输出：[hai, jd]
	boolean b1 =coll.removeAll(coll);  
	System.out.println(b1);    //输出：true
	System.out.println(coll);  //输出：[]
}
```
    
