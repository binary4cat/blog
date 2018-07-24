---
title: Java基础：Iterator迭代器
date: 2017-06-03T19:43:42+08:00
categories: ["Java"]
tags: ["Java基础"]
permalink: 
description: Iterator迭代器概述和应用
photos: http://img.mp.sohu.com/upload/20170809/7f6678264b154d028f0e36e9159c8e9a.png
---
<!-- TOC -->

- [迭代器的概述](#迭代器的概述)
- [迭代器的实现原理](#迭代器的实现原理)
- [迭代器代码实现](#迭代器代码实现)
- [迭代器的执行过程](#迭代器的执行过程)
- [集合迭代中的转型](#集合迭代中的转型)
- [foreach遍历数组](#foreach遍历数组)

<!-- /TOC -->
### 迭代器的概述
- 迭代器概述:
    - Java中有很多种集合，这些集合在存储元素的时候，存储的方式都是不尽相同的，那么对应的我们在取出元素的时候也不相同，为每一种集合设计一个取出方式显然是不合理的，所以就出现了一种屏蔽集合间差异的读取元素的方式，就是迭代器。
   
    - Collection集合元素的通用获取方式：在取元素之前先要判断集合中有没有元素，如果有，就把这个元素取出来，然后继续判断，如果有就继续取。一直把集合中的所有元素全部取出。这种取出方式专业术语称为迭代。
   
    - 每种集合的底层的数据结构不同,例如ArrayList是数组,LinkedList底层是链表,但是无论使用那种集合,我们都会有判断是否有元素
     以及取出里面的元素的动作,那么Java为我们提供一个迭代器定义了统一的判断元素和取元素的方法 。
<!--more-->
### 迭代器的实现原理
- 迭代器的实现原理:Collection接口定义了一个方法`Iterator  iterator()`,实现类重写这个方法就可以返回一个实现`Iterator`接口的对象，该对象就可以对集合中的元素进行迭代。
    - 接口`Iterator` : 两个抽象方法
        - `boolean hasNext()` 判断集合中还有没有可以被取出的元素,如果有返回true
        - `next()` 取出集合中的下一个元素

### 迭代器代码实现
```java
public static void main(String[] args) {
    Collection<String> coll = new ArrayList<String>();
    coll.add("123");
    coll.add("456");
    coll.add("789");
    coll.add("0");
    //调用集合的方法iterator()获取一个实现Iterator接口的对象，用来执行集合迭代
    Iterator<String> it = coll.iterator();
    //使用循环判断集合中是否有元素，如果有就取出
    while(it.hasNext()){
        String s = it.next();
        System.out.println(s);
    }
    //for (Iterator<String> it = coll.iterator(); it.hasNext();  ) {
    //     System.out.println(it.next());
    //}    
}
```


### 迭代器的执行过程
```java
/**
* An optimized version of AbstractList.Itr
*/
private class Itr implements Iterator<E> {
    int cursor;       // index of next element to return
    int lastRet = -1; // index of last element returned; -1 if no such
    int expectedModCount = modCount;

    public boolean hasNext() {
        return cursor != size;
    }

    @SuppressWarnings("unchecked")
    public E next() {
        checkForComodification();
        int i = cursor;
        if (i >= size)
            throw new NoSuchElementException();
        Object[] elementData = ArrayList.this.elementData;
        if (i >= elementData.length)
            throw new ConcurrentModificationException();
        cursor = i + 1;
        return (E) elementData[lastRet = i];
    }

    public void remove() {
        if (lastRet < 0)
            throw new IllegalStateException();
        checkForComodification();

        try {
            ArrayList.this.remove(lastRet);
            cursor = lastRet;
            lastRet = -1;
            expectedModCount = modCount;
        } catch (IndexOutOfBoundsException ex) {
            throw new ConcurrentModificationException();
        }
    }

    @Override
    @SuppressWarnings("unchecked")
    public void forEachRemaining(Consumer<? super E> consumer) {
        Objects.requireNonNull(consumer);
        final int size = ArrayList.this.size;
        int i = cursor;
        if (i >= size) {
            return;
        }
        final Object[] elementData = ArrayList.this.elementData;
        if (i >= elementData.length) {
            throw new ConcurrentModificationException();
        }
        while (i != size && modCount == expectedModCount) {
            consumer.accept((E) elementData[i++]);
        }
        // update once at end of iteration to reduce heap write traffic
        cursor = i;
        lastRet = i - 1;
        checkForComodification();
    }

    final void checkForComodification() {
        if (modCount != expectedModCount)
            throw new ConcurrentModificationException();
    }
}
```

- 可以看到初始化的时候，`lastRet`的值为-1，说明迭代开始前，指针在第一个元素前一位。
- `hasNext`方法
    - `cursor`游标，记录当前指向的索引值(位置)，`cursor`初始化值为0。这里判断是游标是否走到了最大位置。
    - `size`为集合初始化时的长度属性。
- `next`方法
    - 首先判断了当前的游标位置是否超出了集合的最大位置，超出了就抛出对应的异常。
    - 在下面首先将游标的值“++”操作，指向了下一个位置，并且返回了当前索引指向的元素
- `int expectedModCount = modCount;`
    - 由于`ArrayList`是线程不安全的，也就是在迭代的时候，还能对集合进行增删操作，这行代码实现了一个叫做“Fail-Fast(快速失败)机制”，在迭代的过程中有其他的线程修改了集合，就会抛出异常，快速失败。
    - `modCount`是ArrayList中的一个属性，对集合中的增删操作都会进行`modCount++`操作，它保存了对这个集合操作的次数。
    - `checkForComodification()`函数中对这个这个值进行了判断，看看是否在迭代的时候，对该集合进行了增删操作，所以如果两个值不一样，就会抛出异常。可以看到在`next`和`remove`的时候，都执行了该方法，就是对集合正确性的判断。
    - 比如下面的代码中，在迭代的时候，对集合进行了增加操作，就会抛出异常
    ```java
    while(it.hasNext()){
        String s = it.next();
        System.out.println(s);
        coll.add("haha");
    }
    ```
![](http://ww1.sinaimg.cn/large/c55a7aeely1fmf7hlgxx2j20r709owek.jpg)


### 集合迭代中的转型 
- 在使用集合时，我们需要注意以下几点：
    - 集合中存储其实都是对象的地址。
    - jdk1.5版本以后因为出现了基本类型包装类，它提供了自动装箱操作，这样，集合中的元素就是基本数值的包装类对象。
    
- 集合存储时默认使用了Object作为储存元素的类型。也就是说集合中可以存储任何类型的数据
    - 下面的代码中，存储了String和int类型，然后在迭代的时候，进行强转的时候就会发生错误。
```java
Collection coll = new ArrayList();
coll.add("123");
coll.add(123);
Iterator it = coll.iterator();
while (it.hasNext()) {
    //由于元素被存放进集合后全部被提升为Object类型
    //当需要使用子类对象特有方法时，需要向下转型
    String str = (String) it.next();
    System.out.println(str.length());
}
```

- 要灵活使用，就需要用到泛型
    - 泛型限制了集合中存储的对象的类型，在编译期就会检测出集合元素的类型，避免发生异常
```java
Collection<String> coll = new ArrayList<String>();
coll.add("123");
coll.add("456");
Iterator it = coll.iterator();
while (it.hasNext()) {
    String str = it.next();
    System.out.println(str.length());
}
```


### foreach遍历数组
- JDK1.5版本后,出现新的接口`java.lang.Iterable`，对于任何实现`Iterator`接口的对象都可以使用`foreach`遍历。
- 格式：
```java
public static void function(){
    //遍历数组
    String[] str = {"123","456","789"};
    for(String s : str){
        System.out.println(s.length());
    }
    //遍历集合
    ArrayList<Student> array = new ArrayList<Student>();
    array.add(new Student("zhangsan",22));
    array.add(new Student("lisi",11));
    for(Person stu : array){
        System.out.println(stu.name + "-----" + stu.age);
    }
}
```
