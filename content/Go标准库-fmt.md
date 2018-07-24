---
title: Go语言标准库-fmt
date: 2017-08-23T20:56:34+08:00
categories: ["Golang"]
tags: ["Golang入门"]
permalink:
description: Go标准库-fmt
photos: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1503503176164&di=3178bb0b334068f911022455b9f48b80&imgtype=0&src=http%3A%2F%2F7viirv.com1.z0.glb.clouddn.com%2Fgolang.jpg
---
## fmt包
- fmt包实现了类似c语言的`printf`和`scanf`函数，实现格式化I/O函数。

### printf函数
**示例类型和变量**
```
type Human struct{
    Name string
}

func main(){
    var people = Human{Name:"zhangsan"}
}
```
<!--more-->
**1. 普通占位符**  

|占位符|说明|举例|输出| 
|:---|:---|:---|:---|
|`%v`|相应值的默认格式|`Printf("%v",people)`|`{zhangsan}`|
|`%+v`|打印结构体时，会添加字段名显示|`Printf("%+v",people)`|`{Name:zhangsan}`|
|`%#v`|相应值的Go语法表示|`Printf("%#v",people)`|`main.Human{Name:zhangsan}`|
|`%T`|相应值*类型*的Go语法表示|`Printf("%T",people)`|`main.Human`|
|`%%`|表示输出一个%(类似转义符)|`Printf("%%")`|`%`|

**2. 布尔占位符**

|占位符|说明|举例|输出| 
|:---|:---|:---|:---|
|`%t`|输出`true`或`false`|`Printf("%t",true)`|`true`|

**3. 整数占位符**

|占位符|说明|举例|输出| 
|:---|:---|:---|:---|
|`%b`|二进制表示|`Printf("%b",5)`|`101`|
|`%d`|十进制表示|`Printf("%d",0x12)`|`18`|
|`%o`|八进制表示|`Printf("%o",10)`|`12`|
|`%x`|小写的十六进制表示|`Printf("%x",13)`|`d`|
|`%X`|大写的十六进制表示|`Printf("%X",13)`|`D`|


**4. 浮点数和复数的组成部分（实部和虚部）**

|占位符|说明|举例|输出| 
|:---|:---|:---|:---|
|`%b`|打印整型的二进制|`Printf("%b",10)`|`1010`|
|`%e`|科学计数法，例如：`-1234.456e+78`|`Printf("%e",11.909)`|`1.190900e+01`|
|`%E`|科学计数法，例如：`-1234.456E+78`|`Printf("%e",11.909)`|`1.190900E+01`|
|`%f`|有小数点后面补足0|`Printf("%f",10.2)`|`10.200000`|
|`%g`|还原`%e`或`%E`输出的科学计数法类型的内容|`Printf("%g",1.190900E+01)`|`11.909`|

**5. 字符串与字节切片**

|占位符|说明|举例|输出| 
|:---|:---|:---|:---|
|`%U`|Unicode格式：`U+1234`，等同于`U+%04X`|`Printf("%U",0x4E2D)`|`U+4E2D`|
|`%c`|相应Unicode码所表示的字符|`Printf("%c",0x4E2D)`|`中`|
|`%s`|输出字符串表示(接受`string`、`[]byte`类型)|`Printf("%s",[]byte("Go语言"))`|`Go语言`|
|`%q`|单引号包括的字符字面值，由Go语法安全转义(参数为Unicode编码时)|`Printf("%q",0x4E2D)`|`'中'`|
|`%q`|双引号包括的字符字面值，由Go语法安全转义(参数为string)|`Printf("%q","hehe")`|`hehe`|
|`%x`|值为字符串时，输出字符的编码(小写)|`Printf("%x","Go语言")`|`476fe8afade8a880`|
|`%X`|值为字符串时，输出字符的编码(大写)|`Printf("%X","Go语言")`|`476FE8AFADE8A880`|

**6. 指针**  

|占位符|说明|举例|输出| 
|:---|:---|:---|:---|
|`%p`|输出带0x的指针|`Printf("%p",&people)`|`0xc042040400`|
|`%#p`|输出不带0x的指针|`Printf("%#p",&people)`|`c042040400`|

- 转载[Go语言中文网](http://books.studygolang.com/The-Golang-Standard-Library-by-Example/chapter01/01.3.html)，重新编辑
