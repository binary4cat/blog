---
title: Java基础：System类
date: 2017-05-28T21:48:41+08:00
categories: ["Java"]
tags: ["Java基础"]
permalink:
description: System类
photos: http://ww1.sinaimg.cn/large/c55a7aeely1fmbwbz5s71j20et08cjra.jpg
---

# 1. System类方法currentTimeMillis
- System类方法`currentTimeMillis()`:获取系统当前的毫秒值

```Java
/*
*  获取系统当前毫秒值
*  static long currentTimeMillis()
*  对程序执行时间测试
*/
public static void function(){
    long start = System.currentTimeMillis();//当前时间x-1970年1月1日零时零分零秒
    for(int i = 0 ; i < 10000; i++){
      	System.out.println(i);
    }
    long end = System.currentTimeMillis();//当前时间y-1970年1月1日零时零分零秒
    System.out.println(end - start);//当前时间y-当前时间x 
}
```


<!--more-->
# 2. System类方法exit 
- System类方法`exit()`方法
	- 退出虚拟机，所有程序全停止

```Java
/*
*  退出虚拟机,所有程序全停止
*  static void exit(0)
*/
public static void function(){
	while(true){
		System.out.println("hello");
		System.exit(0); //0表示正常终止，其他的代表异常终止。
	}
}
```


# 3. System类方法gc 
- System类方法gc
	- 调用垃圾回收，直接回收所有对象。

```Java
public class Test {
	//此方法会在对象被回收时执行一次
    public void finalize(){
        System.out.println("垃圾收取了");
     }
}
/*
*  JVM在内存中,收取对象的垃圾
*  当没有更多引用指向该对象时,会自动调用垃圾回收机制回收堆中的对象
*  同时调用回收对象所属类的finalize方法()
*  static void gc()
*/
public static void function(){
    new Test();
    new Test();
    new Test();
    new Test();
    new Test();
    new Test();
    new Test();
    new Test();
    System.gc(); //调用垃圾回收，回收对象。
}
```


# 4. System类方法getProperties 
- System类方法getProperties
	- 获取当前操作系统的相关属性数据

```Java
/*
*  获取当前操作系统的属性:例如操作系统名称,
*  static Properties getProperties() 
*/
public static void function(){
    System.out.println(System.getProperties());
}
```


![](http://ww1.sinaimg.cn/large/c55a7aeely1fmeb40nxj7j20fo0hrgm8.jpg)   
# 5. System类方法arraycopy
- System类方法arraycopy：
	- 用来实现将源数组部分元素复制到目标数组的指定位置
    
```Java
/*
* System类方法,复制数组
* arraycopy(Object src, int srcPos, Object dest, int destPos, int length)
* Object src, 要复制的源数组
* int srcPos, 数组源的起始索引
* Object dest,复制后的目标数组
* int destPos,目标数组起始索引 
* int length, 复制几个
*/
public static void function(){
	String[] olds= {"j","d"};
	String[] news= {"h","a","i","",""};  //因为下面的拷贝中，我们从索引3的位置拷贝了两个元素，所以总共有5个元素，要注意拷贝后的数组的长度，否则会发生数组越界的异常
	System.arraycopy(olds, 0, news, 3, 2);
	for (String n : news) {
		System.out.println(n);  //输出：h a i j d
	}
}
```

