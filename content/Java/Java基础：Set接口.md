---
title: Java基础：Set接口
date: 2017-06-16T15:39:46.000Z
categories: ["Java"]
tags: ["Java基础"]
permalink: null
description: Java种的Set接口
photos: 'http://img.mp.sohu.com/upload/20170809/7f6678264b154d028f0e36e9159c8e9a.png'
---

# 1. Set接口

- Set接口的特点
  - Set接口是不能包含重复元素的集合。
  - Set集合取出元素的方式可以采用：迭代器、foreach。

# 2. 实现类HashSet的存储和迭代

- HashSet (哈希表)无序集合,存储和取出的顺序不同,没有索引,不存储重复元素
- 内部使用的`map`存储的，添加操作在内部实现是当作map的key存储的，所以不会出现重复元素。 <!-- more -->

  ```java
  public static void main(String[] args){
    Set<String> set=new HashSet<String>();
    set.add("a1");
    set.add("a2");
    set.add("a3");
    set.add("a3");   //相同数据会存储失败
    Iterator<String> iterator=set.iterator();
    while (iterator.hasNext()) {
        System.out.println(iterator.next());  //输出：a1 a2 a3
    }
    for (String str : set) {
        System.out.println(str);              //输出：a1 a2 a3
    }
  }
  ```

# 3. 字符串对象的哈希值

- 计算对象的散列值，String类重写了父类Object的hashCode方法

  ```java
  /**
     * Returns a hash code for this string. The hash code for a
     * {@code String} object is computed as
     * <blockquote><pre>
     * s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]
     * </pre></blockquote>
     * using {@code int} arithmetic, where {@code s[i]} is the
     * <i>i</i>th character of the string, {@code n} is the length of
     * the string, and {@code ^} indicates exponentiation.
     * (The hash value of the empty string is zero.)
     *
     * @return  a hash code value for this object.
     */
    public int hashCode() {
        int h = hash;
        if (h == 0 && value.length > 0) {
            char val[] = value;

            for (int i = 0; i < value.length; i++) {
                //val字节数组在这里记录了字符串的所有字符，在计算的时候转成了ASCII码参与运算。
                h = 31 * h + val[i];  
            }
            hash = h;
        }
        return h;
    }
  ```

- String类重写的hashCode返回一串数字

```java
 String s1 = new String("abc");
 String s2 = new String("abc");
 System.out.println(s1.hashCode());
 //输出：96354 System.out.println(s2.hashCode());
 //输出：96354 System.out.println("哈哈".hashCode());
 //输出：694528 System.out.println("呵呵".hashCode());
 //输出：691872
```

## 3.1. HashSet的存储过程

1. 首先调用当前数据类型本类的hashCode()方法算出哈希值(自定义类型调用的是Object的方法，也可以自己重写hashCode方法) 
2. 在map容器中找是否与新元素哈希值相同的已有元素
  - 如果没有直接存入
  - 如果有转到第三步 
3. 新元素会与该索引位置下的已有元素利用equals方法一一对比
  - 新元素.equals(已有元素)返回true，则停止对比，说明重复，不再存入
  - 如果与该索引位置下的已有元素都通过equals方法对比返回false,说明没有重复，存入容器

## 3.2. LinkedHashSet集合

- LinkedHashSet是Set的一个实现，LinkedHashSet继承于HashSet，并且其内部是通过LinkedHashMap来实现的。
- LinkedHashSet具有顺序，也就是怎么存进去的怎么拿出来，顺序不会乱。
- LinkedHashSet是线程不安全的，也就是说会有两个线程操作同一个LinkedHashSet集合的情况发生。

```java
  public static void function() {
    LinkedHashSet<String> lsh=new LinkedHashSet<String>();
    lsh.add("a1");
    lsh.add("a2");
    lsh.add("a3");
    for (String string : lsh) {
            System.out.println(string);  //输出：a1 a2 a3
        }
  }
```

# 4. ArrayList,HashSet判断对象是否重复

- ArrayList的contains方法底层依赖于equals方法

  - ArrayList的contains方法在执行时，会使用调用方法时传入的元素(类型)的equals方法依次与集合中的已有元素进行比较，根据返回的Boolean值判断是否有重复元素。
  - 当ArrayList存放自定义类型(对象)时，如果自定义类型没有重写equals方法，判断存入的对象是否重复的依据是对象在堆中的地址值，所以如果想根据对象的属性等内容判断是否为重复元素，就应该重写元素的equals方法。

- HashSet的`add()`方法和`contains方法()`底层都依赖`hashCode()`方法与`equals()`方法

  - HashSet集合由于是无序的，其判断唯一的依据是元素类型的`hashCode`与`equals`方法的返回结果:

    - 先判断新元素与集合内已有元素的`HashCode`值
    - 如果不同，说明是不同元素，添加到集合。
    - 如果相同，再判断equals比较结果。返回true则相同元素；返回false则不同元素，添加到集合。

  - 使用HashSet存储自定义类型，如果没有重写该类的hashCode与equals方法，则判断重复时，使用的是地址值，如果想通过内容比较元素是否相同，需要重写该元素类的`hashcode`与`equals`方法。

# 5. hashCode和equals方法

> [推荐阅读](http://www.cnblogs.com/Qian123/p/5703507.html)
