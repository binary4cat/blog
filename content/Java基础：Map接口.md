---
title: Java基础：Map接口
date: 2017-06-20T22:13:42+08:00
categories: Java
tags: Java基础
permalink: 
description: Java中的Map接口使用概述
photos: http://ww1.sinaimg.cn/large/c55a7aeely1fmbwbz5s71j20et08cjra.jpg
---
<!-- TOC -->

- [Map接口](#map接口)
- [Map接口中的常用方法](#map接口中的常用方法)
- [Map集合遍历(keySet)](#map集合遍历keyset)
- [Map集合Entry对象](#map集合entry对象)
- [Map集合遍历(entrySet方法)](#map集合遍历entryset方法)
- [HashMap集合存储自定义对象](#hashmap集合存储自定义对象)
- [LinkedHashMap](#linkedhashmap)

<!-- /TOC -->

### Map接口
- Map接口概述:
    - Map中存储元素是成对存在的，每一个元素都包含键和值(key、value)。
    - Map集合不能批量添加，只能执行单个添加操作。
    - Map集合中的`key`是不能重复的，但是`value`可以重复，每个键只能对应一个值。
    - Map下的主要实现类：`HashMap`、`LinkedHashMap`

<!--more-->
### Map接口中的常用方法
- `public V put(K key, V value)` ：存储键值对
- `public void putAll(Map<? extends K, ? extends V> m)` :添加一个相同类型的Map(注意限定符约束)
- 查看源码可以知道，无论是put还是putAll最终调用的都是`putVal`这个方法(putAll也是循环调用的该方法)
```java
//可以看到第一个参数是一个hash
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,boolean evict){}
//从这里可以看出来，HashMap在存储的时候会对key进行hash操作，在putVal方法中对hash进行判断，用来判重
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
```
- `public V get(Object key)` ：根据key获取value
- `public V getOrDefault(Object key, V defaultValue)` ：根据key获取value，并且传递一个默认的value，当该key的时候，就返回这个传过来的默认的value，这是JDK8中新扩展的方法。
```java
public V get(Object key) {
        Node<K,V> e;
        return (e = getNode(hash(key), key)) == null ? null : e.value;
    }
// Overrides of JDK8 Map extension methods
    @Override
    public V getOrDefault(Object key, V defaultValue) {
        Node<K,V> e;
        return (e = getNode(hash(key), key)) == null ? defaultValue : e.value;
    }
```
- `public V remove(Object key)` ：根据key删除集合中的键值对
- `public boolean remove(Object key, Object value)` :根据key、value的值删除集合中的键值对，键和值都匹配上才删除。

```java
public static void function() {
    Map<String, Integer> map=new HashMap<String,Integer>();
    map.put("a", 1);
    map.put("b", 2);
    System.out.println(map);  // {a=1, b=2}
    Integer integer=map.get("a");  
    System.out.println(integer);   //1
    Integer value=map.remove("a");
    System.out.println(value);     //1
    boolean success=map.remove("b", 2);
    System.out.println(success);  //true
}
```


### Map集合遍历(keySet)
- 首先获取Map集合中所有的键，`keySet()`方法返回一个Set集合存储所有的键
- 遍历返回的Set，调用`get(key)`方法获取对应的值

```java
public static void function() {
    Map<String, Integer> map=new HashMap<String,Integer>();
    map.put("a", 1);
    map.put("b", 2);
    Set<String> keys= map.keySet();
    for (String key : keys) {
		Integer value=map.get(key);
		System.out.println(value);   //1 2
	}

	//Iterator迭代器方式
	Iterator<String> iterator=keys.iterator();
    while (iterator.hasNext()) {
		String key=iterator.next();
		Integer value=map.get(key);
		System.out.println(value);
	}
}
```


### Map集合Entry对象
- Map接口中有一个嵌套的接口`Entry<K,V>`，它将键值对的对应关系封装成了键值对对象

```java
interface Entry<K,V> {
	K getKey();
	V getValue();
	V setValue(V value);
	boolean equals(Object o);
	int hashCode();
}
```
- 在Map接口中有一个方法`Set<Map.Entry<K, V>> entrySet();` `HashMap`重写了这个方法，返回Map集合中所有的Entry(键值对)对象。
```java
public Set<Map.Entry<K,V>> entrySet() {
    Set<Map.Entry<K,V>> es;
    return (es = entrySet) == null ? (entrySet = new EntrySet()) : es;
}
```


### Map集合遍历(entrySet方法)
```java
public static void function() {
    Map<String, Integer> map=new HashMap<String,Integer>();
    map.put("a", 1);
    map.put("b", 2);
    //获取map的键值对Set集合
    Set<Map.Entry<String,Integer>> set=map.entrySet();
	//获取该Set的迭代器对象
    Iterator<Map.Entry<String, Integer>> iterator=set.iterator();
    while (iterator.hasNext()) {
		Map.Entry<String, Integer> entry=iterator.next();
		//调用Entry对象的方法获取键和值
		String key=entry.getKey();
		Integer value=entry.getValue();
		System.out.println("Key:"+key+"---------"+"Value:"+value);  
		// 输出：Key:a---------Value:1  Key:b---------Value:2
	}

	//foreach
	for (Map.Entry<String, Integer> entry : set) {
        System.out.println("Key:"+entry.getKey()+"---------"+"Value:"+entry.getValue());
		// 输出：Key:a---------Value:1  Key:b---------Value:2
	}
}
```


### HashMap集合存储自定义对象
- `HashMap`集合的key、value值都可以是Object类型的，也就是可以存储任意类型的对象。
- key为自定义对象时：
	- 因为Map类型的数据在存储时，会对key进行哈希，如果是自定义对象还会调用`Object`对象的`equals`方法比较key是否重复。
	- 默认的`Object`对象的`equals`方法其实调用的是`==`比较的，也就是比较的对象的地址。所以当自定义对象作为Map的键时，应该重写Object的equals方法，例如比较对象中属性值的不同。
- value为自定义对象时：
	- 因为Map存储时判断的时键是否重复，所以value为自定义对象时，可以没有特殊的处理。

### LinkedHashMap
- `LinkedHashMap`和`HashMap`基本一致，`LinkedHashMap`的特定就是可以保证迭代的顺序和插入的顺序一致。
```java
public static void function() {
    LinkedHashMap<String, Integer> map=new LinkedHashMap<String,Integer>();
    map.put("a", 1);
    map.put("b", 2);
    System.out.println(map);  //输出：{a=1, b=2}
    Set<String> set=map.keySet();
    for (String key : set) {
		System.out.println("Value:"+map.get(key));  //输出：Value:1   Value:2
	}
}
```