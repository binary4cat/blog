---
title: Java基础：List接口
date: 2017-06-14T22:00:58+08:00
categories: ["Java"]
tags: ["Java基础"]
permalink: 
description: Java中的List接口
photos: http://img.mp.sohu.com/upload/20170809/7f6678264b154d028f0e36e9159c8e9a.png
---
<!-- TOC -->

- [List接口](#list%E6%8E%A5%E5%8F%A3)
- [List接口的方法](#list%E6%8E%A5%E5%8F%A3%E7%9A%84%E6%96%B9%E6%B3%95)
- [ArrayList集合](#arraylist%E9%9B%86%E5%90%88)
- [LinkedList集合的自身特点](#linkedlist%E9%9B%86%E5%90%88%E7%9A%84%E8%87%AA%E8%BA%AB%E7%89%B9%E7%82%B9)
- [LinkedList集合特有方法](#linkedlist%E9%9B%86%E5%90%88%E7%89%B9%E6%9C%89%E6%96%B9%E6%B3%95)
- [Vector类](#vector%E7%B1%BB)

<!-- /TOC -->
### List接口
- List接口的特点:
    - 它是一个元素存取有序的集合
    - 它是一个带有索引的集合，通过索引就可以精确的操作集合中的元素。
    - 集合中可以有重复的元素。

- List接口子类有：
    - `ArrayList`集合
    - `LinkedList`集合

### List接口的方法
- `boolean add(E e);` :将指定的元素添加到集合的末尾，返回是否添加成功
- `void add(int index, E element);`  :将指定元素添加到指定的索引处

```java
public static void function(){
    List<String> list = new ArrayList<String>();
    list.add("a1");
    list.add("a2");
    list.add("a3");
    list.add("a4");
    System.out.println(list);  //输出：[a1, a2, a3, a4]
    list.add(1, "aa");
    System.out.println(list);  //输出：[a1, aa, a2, a3, a4]
}

```

<!--more-->
- `boolean remove(Object o);`  ：从集合中删除指定元素，返回值为是否删除成功
- `E remove(int index);` ：删除集合中指定位置的元素，返回值为被删除的元素

```java
public static void function(){
    List<String> list = new ArrayList<String>();
    list.add("a1");
    list.add("a2");
    list.add("a3");
    list.add("a4");
    String d1=list.remove(1);
	System.out.println(d1);       //输出：a2
	boolean d2=list.remove("a1");
	System.out.println(d2);       //输出：true
}

```
- `E set(int index, E element);`：将指定索引处的元素替换成指定的元素，返回值为替换前的元素

```java
public static void function(){
    List<String> list = new ArrayList<String>();
    list.add("a1");
    list.add("a2");
    list.add("a3");
    list.add("a4");

    list.set(2, "aa");
	System.out.println(list);  //输出：[a1, a2, aa, a4]
}

```

- ` E get(int index);` ：获取指定索引处的元素

```java
public static void function(){
    List<String> list = new ArrayList<String>();
    list.add("a1");
    list.add("a2");
    list.add("a3");
    list.add("a4");

    String str=list.get(2);
	System.out.println(str);   //输出：a3
}

```

### ArrayList集合
- ArrayList集合底层采用的是数组结构
    - 当`new ArrayList()`的时候，底层创建了一个长度为10的Object类型的数组
    
```java
    /**
     * Shared empty array instance used for default sized empty instances. We
     * distinguish this from EMPTY_ELEMENTDATA to know how much to inflate when
     * first element is added.
     */
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

    /**
     * Constructs an empty list with an initial capacity of ten.
     */
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
``` 
  - 通过阅读源码可以知道，默认的数组长度是10，当`add`操作超出了默认的长度的时候，就会进行数组的扩容，数组的扩容其实就是将数组重新复制一份，保存到一个新的数组中，新数组的长度是旧数组长度乘1.5取整。
    
```java
    /**
     * Increases the capacity to ensure that it can hold at least the
     * number of elements specified by the minimum capacity argument.
     *
     * @param minCapacity the desired minimum capacity
     */
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);    //这里的位移操作相当于就是给旧数组乘1.5取整
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);     //这里判断的是数组是否超过了规定的数组最大容量，超过的话就返回int数值的最大值
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
    /**
     * The maximum size of array to allocate.
     * Some VMs reserve some header words in an array.
     * Attempts to allocate larger arrays may result in
     * OutOfMemoryError: Requested array size exceeds VM limit
     */
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;  //数组默认的最大容量为int类型的最大值减8
    
```


### LinkedList集合的自身特点
- LinkedList集合底层采用链表结构,每次查询都要从链头或链尾找起,查询相比数组较慢，但是增删直接修改元素记录的地址值即可,不要大量移动元素，相比数组较快。
- LinkedList根据索引使用二分法查找需要的元素数据

### LinkedList集合特有方法
- `E removeFirst()` :移除链表的开头，并返回移除的元素
- `E removeLast()` ：移除链表的结尾，并返回移除的元素

```java
/**
* Unlinks non-null first node f.
*/
private E unlinkFirst(Node<E> f) {
    // assert f == first && f != null;
    final E element = f.item;
    final Node<E> next = f.next;
    f.item = null;
    f.next = null; // help GC  将原来的链表头对象指向空，让GC释放
    first = next;
    if (next == null)
        last = null;
    else
        next.prev = null;  //释放之前的第二个链表指向前一个对象，因为这个已经成为first了
    size--;
    modCount++;            //操作记录，在迭代的时候判断错误检查用的，可以看迭代的那篇博客
    return element;        //返回原先的(移除)节点数据
}
private E unlinkLast(Node<E> l)  //类似unlinkFirst

```

- `E getFirst()` : 获取链表的开头
- `E getLast()`  : 获取链表的结尾

```java
/**
* Returns the first element in this list.
*
* @return the first element in this list
* @throws NoSuchElementException if this list is empty
*/
public E getFirst() {
    final Node<E> f = first;
    if (f == null)
        throw new NoSuchElementException();
    return f.item;    //直接返回头结点的数据
}

```
- `void addFirst(E)` : 添加到链表的开头
- `void addLast(E)` : 添加到链表的结尾

```java
/**
* Links e as first element.
*/
private void linkFirst(E e) {
    final Node<E> f = first;
    //创建一个新节点，构造函数的参数分别是：前一个节点(没有)，当前的节点(新插入的)，后一个节点(之前的头结点)
    final Node<E> newNode = new Node<>(null, e, f);  
    first = newNode;     //头节点现在让位啦
    if (f == null)
        last = newNode;
    else
        f.prev = newNode;  //f现在成第二个节点，所以把f的前一个节点设置成新生成的节点啦
    size++;
    modCount++;
}

```

### Vector类
- `Vector`类的特点
    - `Vector`集合数据存储的结构是数组结构，为JDK中最早提供的集合,它是线程同步的
    - `Vector`中提供了一个独特的取出方式，就是枚举`Enumeration`，它其实就是早期的迭代器。
    - 此接口`Enumeration`的功能与`Iterator`接口的功能是类似的。
    - `Vector`集合已被`ArrayList`替代。枚举`Enumeration`已被迭代器`Iterator`替代。
