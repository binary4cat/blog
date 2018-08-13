---
title: Go语言基础知识
date: 2017-08-11T21:53:08+08:00
categories: ["Golang"]
tags: ["Golang入门"]
permalink:
description:
photos: http://ww1.sinaimg.cn/large/c55a7aeely1ficnszlhh9j20go098jrl.jpg
---
<!-- TOC -->

- [1. 基础知识](#1-%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86)
- [2. 类型与变量](#2-%E7%B1%BB%E5%9E%8B%E4%B8%8E%E5%8F%98%E9%87%8F)
    - [2.1. 基本类型](#21-%E5%9F%BA%E6%9C%AC%E7%B1%BB%E5%9E%8B)
    - [2.2. 类型默认值](#22-%E7%B1%BB%E5%9E%8B%E9%BB%98%E8%AE%A4%E5%80%BC)
        - [2.2.1. 类型的别名](#221-%E7%B1%BB%E5%9E%8B%E7%9A%84%E5%88%AB%E5%90%8D)
    - [2.3. 变量的声明与赋值](#23-%E5%8F%98%E9%87%8F%E7%9A%84%E5%A3%B0%E6%98%8E%E4%B8%8E%E8%B5%8B%E5%80%BC)
    - [2.4. 变量的类型转换](#24-%E5%8F%98%E9%87%8F%E7%9A%84%E7%B1%BB%E5%9E%8B%E8%BD%AC%E6%8D%A2)
    - [2.5. 常量的定义](#25-%E5%B8%B8%E9%87%8F%E7%9A%84%E5%AE%9A%E4%B9%89)
    - [2.6. 运算符](#26-%E8%BF%90%E7%AE%97%E7%AC%A6)
    - [2.7. 控制语句](#27-%E6%8E%A7%E5%88%B6%E8%AF%AD%E5%8F%A5)
        - [2.7.1. 循环](#271-%E5%BE%AA%E7%8E%AF)
        - [2.7.2. switch](#272-switch)
    - [2.8. Array](#28-array)
    - [2.9. 切片Slice](#29-%E5%88%87%E7%89%87slice)
    - [2.10. map](#210-map)
    - [2.11. 函数](#211-%E5%87%BD%E6%95%B0)
        - [2.11.1. defer](#2111-defer)
    - [2.12. 结构struct](#212-%E7%BB%93%E6%9E%84struct)
    - [2.13. 方法method](#213-%E6%96%B9%E6%B3%95method)
    - [2.14. 接口interface](#214-%E6%8E%A5%E5%8F%A3interface)
    - [2.15. 反射reflection](#215-%E5%8F%8D%E5%B0%84reflection)
    - [2.16. 并发concurrency](#216-%E5%B9%B6%E5%8F%91concurrency)

<!-- /TOC -->

# 1. 基础知识

- 每个go程序都是由`package`定义的。`package`必须在源文件的第一行。
- 每个可执行程序必须有且只有一个main包，main包里面必须有main函数。

```golang
package main
// todo
func main(){
    //TODO
}
```

- import
    - 多个包可以一个一个import：
    ```golang
    import "fmt"
    import "os"
    import "log"
    ```
    
    - 也可以简化：
    ```golang
    import (
        "fmt"
        "os"
        "log"
        )
    //调用
    func main(){
        fmt.Println("hello world");
    }
    ```
    - 导入的包没有调用里面的函数等，就会导致编译失败。
    - 可以设置别名：
    ```golang
    import env "fmt"

    //调用
    func main(){
        env.Println("Hello world")
    }
    ```
    - 或者可以直接省略调用：使用“`.`”代替别名，调用包函数的时候就不需要使用包名了(但是不推荐这样做)
    ```golang
    import . "fmt"

    func main(){
    Println("hello world")
    }
    ```

    - 省略调用和别名不能同时使用
    - 可见性规则：使用大小写来决定是`private`或者`public`
        - 大写就是表示`public`，表示在包的外部可以调用
        - 小写就是表示`private`，表示只能在包内部被调用


# 2. 类型与变量

## 2.1. 基本类型

- 布尔类型：`bool`
    - 长度：1字节
    - 取值范围：true、false
    - 不能使用c#等其他语言中的0、1来代表
- 整型：`int`/`uint`
    - 根据程序运行平台分为32/64位
    - int类型必须是整数，带小数的必须申明为float类型
- 字节型：`byte`/`uint8`
- 复数：
- 值类型：
    - `array`、`struct`、`string`
- 引用类型：
    - `slice`(切片)、`map`(哈希)、`chan`(通道,并发编程必不可少)
- 接口类型：`interface`
- 函数类型：`func`

## 2.2. 类型默认值
- 值类型默认值为0
- bool默认为false
- string默认为空字符串

### 2.2.1. 类型的别名
```golang
package main
import (
    "fmt
)
//给类型起个别名
type(
    byte int8
    rune int32
    字符串 string
)
func main(){
    //使用别名“字符串”代替“string”申明变量
    var str 字符串
    str="我是文本字符串"
    fmt.Println(b);
}
```

## 2.3. 变量的声明与赋值

```golang
//申明一个int类型的变量a,未赋值，默认值为0
var a int 
//给变量a赋值  
a = 10        
//申明变量的同时赋值
var b int = 10    
//省略变量的类型，由编译器去根据值推断
var c = "hello"   
//申明赋值简写形式(方法体内使用)
d := 111
```

```golang
//申明多个相同类型的变量
var a,b,c int
//申明的同时赋值
var a,b,c int = 1,2,3
//省略类型
var a,b,c = 1,2,3
//简写形式
a,b,c := 1,2,3
```

```golang
//使用“_”忽略其中一个参数(忽略了2这个值)
var a,_,c = 1,2,3
// 用在返回多个值的时候，忽略其中一个参数值
```

## 2.4. 变量的类型转换
- Go语言中所有的类型转换必须是显示声明的强制转换
- 只能转换两种互相兼容的类型:例如int和float之间可以转换，但是int和bool之间转换会发生编译错误

```golang
var a float = 1.1
var b int = int(a)

//错误的转换
var a int = 0 
var b bool = int(a)
```

```golang
//int string
var a int = 65 
b := string(a)
fmt.Println(b)

//输出一个"A"，将int类型直接转string的时候，编译器会认为将要进行转换的是计算机存储的字符代码，数字65代表的是字母A，所以就会输出A
```

```golang
//可以使用一个内置包进行int、string的转换
package main
import(
    "fmt"
    "strconv"
)
func main(){
    var a int = 65 
    b := strconv.Itoa(a)  //int转string
    a = strconv.Atoi(b)   //string转int
    fmt.Println(b)
}

//输出的就是65
```

## 2.5. 常量的定义
- 常量的定义推荐使用全部大写字母声明
- 如果常量不想被外部调用，那就首字母需要是小写或者下划线
- 常量如果使用函数作为值，那么必须是一个内置函数
- 常量赋值必须是一个常量
- 同时声明多个常量，第一个常量必须赋默认值
- 申明多个常量，没有赋初始值的会默认取上一行的值


```golang
const (
    a = 1
    b
    c
)
// b和c就会默认取a的值，都等于1
```

- 可以使用常量实现枚举效果

```golang
const (
    a = "A"
    b
    c = iota
    d
)
// 上面的声明中，a和b的值都是“A”,遵循没有赋值取上一行的值
// c 使用了一个iota，iota表示常量的计数器，从0开始一次递增1，所以c的值就是3
```

```golang
const (
    a = iota
    b,
    c
)
//作为声明的第一个常量不能省略赋值(要么是其他值，要么就是iota)
```

## 2.6. 运算符


## 2.7. 控制语句

- 控制语句条件没有括号
```golang
if a > 1 {
    //TODO
}
```

- 可以在控制语句中声明变量并使用，但是作用域只在控制语句内
```golang
func main(){
    a := 10
    if a := 1; a > 0 {
        fmt.Println(a)    //输出的是1
    }
    fmt.Println(a)        //输出的是10
}
```

### 2.7.1. 循环
- Go语言中只有一个`for`循环关键字，像c#中除了`for`还有`foreach`、`while`关键字
- Go语言中的`for`支持3种形式
    - 无限循环
    ```golang
    func mian(){
        a := 1
        for {
            a++
            if a > 3 {
                break   //必须要有一个条件控制，否则就进入无限循环状态
            }
        }
    }
    ```

    - 自带条件表达式
    ```golang
    func main (){
        a := 1
        for a < 10 {
            //TODO
            a++
        }
    }
    ```

    - 正常循环语句
    ```golang
    func main(){
        for i := 0; i < 10; i++{
            //TODO
        }
    }
    ```

### 2.7.2. switch
- 不需要写关键字`break`,符合条件就会自动终止switch

```golang
func main(){
    a := 1
    switch a {
        case 0:
            fmt.Println("a=0")
        case 1:
            fmt.Println("a=1")
    }
}
```

- 如果符合后需要继续下一个`case`，那么就需要显示的使用`fallthrough`关键字
```golang
func main(){
    a := 1
    switch {
        case a > 0:
            fmt.Println("a>0")
            fallthrough
        case a < 0:
            fmt.Println("a<0")
    }
}
```

- 支持初始化一个表达式，这时表达式需要以封号结尾
```golang
func main (){
    switch a := 1;{
        case a > 0:
            fmt.Println("a>0")
            fallthrough
        case a < 0:
            fmt.Println("a<0")
    }
}
```

## 2.8. Array
- 数组的声明
```golang
func main(){
    a := [2]int  //数组必须要声明长度
    b := [1]int  //长度相同的两个数组，尽管元素类型一样，但是是属于两个不同的类型

    //给数组赋值，在下面有两个元素的int数组中，值赋值了一个1，那么就会认为1是第一个元素的值，那么第二个元素就是int类型的默认值0
    c := [2]int{1}     //output:[1,0]

    //如果想给指定的元素赋值，那么就要使用索引形式
    d := [5]int{4: 1}  //表示给索引为4也就是最后一个元素赋值1，其他的元素为int类型默认值
    //上面的语句输出[0,0,0,0,1]
}
```

- 可以不指定数组的长度
```golang
func main(){
    a := [...]int{1,2,3,4,5}  //使用3个点代替长度，编译器会自动帮我们计算

    //也可以实现上面例子中的指定索引赋值
    b := [...]int{19: 1}    //这个数组的长度就是20，最后一个元素值为1，其他元素为0
}
```

- 数组在Go语言中为值类型(c#中是作为引用类型的)
- 可以对单个元素的值进行操作
```golang
func main(){
    a := [10]int{}
    a[2]=2   //对数组索引为2(第三个元素)进行赋值操作
}
```

- 可以定义使用多维数组
```golang
func main(){
    a := [2][3]int {
        {2: 1},
        {1,2,3}
    }
}
```

- 冒泡排序
```golang
func main(){
    a := [...]int{3,6,1,2,8}

    num := len(a)  //获取数组长度
    for i := 0;i < num; i++ {
        for j := i + 1; j < num; j++ {
            if a[i] < a[j]{
                temp := a[i]
                a[i] = a[j]
                a[j] = temp
            }
        }
    }
}
```

## 2.9. 切片Slice
- Slice本身不是数组，它只是指向一个数组的底层地址
- 因为指向的是的内存地址，所以Slice是引用类型
- Slice可以从一个数组获取生成
```golang
func main(){
    a := [5]int{1,2,3,4,5}   
    //a1 ：= []int      //一个没有明确的长度的数组，就是Slice
    s1 := a[2]          //s1现在指向数组a的索引为2的元素值为3
    s2 := a[2:5]        //s2现在指向数组a中的索引2开始的元素，一直到第5个元素(注意这里冒号后面的5不再是索引，而是第几个元素)，所以值为：345
    s3 := a[2:]         //跟s2一样，冒号后面没有，表示取到最后。
    s4 := a[:3]         //表示从索引0开始，取到第三个元素
}
```

- Slice也可以使用函数`make([]T,len,cap)`进行创建
    - `[]T`：表示创建一个Slice(也就是不指定长度的数组)
    - `len`：长度，表示这个Slice里面包含多少个元素
    - `cap`：容量，因为Slice是长度可变的，为了不浪费空间，可以指定一个大致的容量，让内存取分配指定容量的连续内存空间。当实际存储的长度大于容量是，会自动的对容量进行乘2操作，并重新寻找一块新容量大小的连续内存空间。所以在开始应该对容量有一个大致的设定，避免频繁重新创建，带来的性能损耗。
    - `cap`可以省略不写，如果`cap`省略掉，那么默认就和`len`相同
```golang
func main(){
    //make函数的参数
    s := make([]int,5,10)
    fmt.Println(len(s),cap(s))   //输出5，10
}
```

- Slice指向的是一个数组的内存地址，并不是一个独立的数组
![](http://ww1.sinaimg.cn/large/c55a7aeely1figzlm0oafj20lm0d5abl.jpg)
    - 从图中可以看到，Slice_a 只包含三个元素，实际容量是包含了Array_ori这个数组的c元素开始到最后。
    - Slice_a根据内存寻址，可以找到从c开始到Array_ori这个数组结尾的所有元素，但是Slice_a本身只包含了c,d,e三个元素，这也说明了Slice指向的是一个底层数组的引用地址。
    - 例如:
    ```golang
    Slice_a[2]    //得到“e”
    Slice_a[4]    //得到“g”
    ```
- Reslice
    - 从一个Slice取一个新的Slice
    ```golang
    func main(){
        a := [] int{1,2,3,4,5}
        s1 := a[2:5]     //[3,4,5]
        //s2要取3,4元素
        s2 := a[2:4]     //可以从元素组中取
        //也可以从s1中取，因为s1已经包含3,4这两个元素了
        s2 := s1[0:2]    //取s1索引0开始到第二个元素就是3，4
    }
    ```

    - Reslice索引不可以超越被Slice的切片的容量`cap()`的值，超过之后会引发编译错误，上面的例子中`s2`取值如果这么写就是错误的：`s2 := s1[3:6]`，索引3已经超过`s1`的最大容量(`s1`的最大容量是a数组从3开始到结尾的大小，结合上面的说明理解)
- Append 
    - 可以在Slice的结尾追加元素
    - 可以把一个Slice追加到另一个Slice的尾部
    ```golang
    func main(){
        s := make([]int,3,5)   //长度为3，容量为5的slice
        s = append(s,1,2)      //把1，2两个元素追加到s的尾部，得到[0,0,0,1,2]

    }
    ```

    - 如果最终追加后的长度没有超过slice的容量，那么返回的还是原来的那个slice，如果超过了，那么就会创建一个新的容量的内存空间，把数值复制到新的地址，然后返回的就是新的slice。
        - 追加后的slice没有超过原slice的容量
        ```golang
        func main(){
            s := make([]int,5,10)
            s = append(s,1,2,3)        //追加3个元素，总元素为8，没有超过s的容量10，那么新赋值的s，指向的内存地址还是原先s的地址
        }
        ```

        - 追加后的slice超过了远slice的容量
        ```golang
        func main(){
            s := make([]int,5,10)
            s = append(s,1,2,3,4,5,6)    //追加了6个元素，超过了原来s的最大容量10，那么就会创建一个新内存空间(大小为容量乘2)，将新的s复制进去，所以现在这个s指向的其实是一个新的内存地址了
            s1 = s                      //s1指向的还是原先s的内存地址                 
        }
        ```

- Copy
    - 将一个slice拷贝到另一个slice中
        - `copy(拷贝到的slice,被拷贝的slice)`
        - 将长度大的slice拷贝到长度小的slice中
        ```golang
        func main(){
            s1 := []int{1,2,3,4,5,6}
            s2 := []int{7,8,9}
            copy(s2,s1)       //表示将s1拷贝到s2中，由于s1的长度为6，s2的长度为3，那么拷贝后s2就变成了[1,2,3]，因为s2的容量只有3，所以就把s1的前三个默认拷贝到s2中去了
        }
        ```

        - 将长度小的slice拷贝到长度大的slice中
        ```golang
        func main(){
            s1 := []int{1,2,3,4,5,6,}
            s2 := []int{7,8,9}
            copy(s1,s2)      //将s2的元素拷贝到s1当中去，拷贝后的s1变成[7,8,9,3,4,5]
        }
        ```

- 循环slice
```golang
func main(){
    //i取的是slice中元素的索引，v取的是slice中的每一个值
    for i,v:=range slice{
        //直接给v这样赋值，是没有办法改变slice中v的值的，因为这里v获取的只是一个引用地址
        v = "hello"
        //想要赋值，可以使用
        slice[i] = "hello"
    }
}
```

## 2.10. map

- `map`类似c#中的`Dictionary`，以`key-value`对的形式存储数据
```golang
func main(){
    var m map[int]string     //int表示key，string表示value
    m = map[int]string{}

    //或者使用make函数
    m = make(map[int]string)
    //或者最简单的
    ma := make(map[int]string)
}
```

```golang
func main(){
    m := make(map[int]string)
    m[1]="hello"         //赋值key为1的map，值为“hello”
    //使用
    a := m[1]            //使用key取值
    //删除
    delete(m,1)          //删除key为1的值
}
```

- map嵌套的时候，需要对每一级的map进行make操作
```golang
func main(){
    //声明一个key为int类型，value为map[int]string类型的map
    m := make(map[int]map[int]string)    
    //如果直接赋值会报错例如：
    m[1][1] = "hello"     //因为value也是一个map类型，也需要初始化的

    //正确方式
    m[1] = make(map[int]string)
    m[1][1] = "hello"
    a := m[1][1]      //a="hello"
}
```

- 判断map是否被初始化
```golang
func main(){
     m := make(map[int]map[int]string)   
     a, exist = m[2][1]             //使用多返回值，判断map是否存在(初始化)，返回一个bool类型的值
     if !exist {
         //如果不存在，就进行初始化一下
         m[2]=make(map[int]string)
     }
     a = m[2][1]
}
```

- 循环map
```golang
func main(){
    for k,v:=range map{
         //k，v在这里就是key、value
         //k,v在这里都是一个引用，直接赋值操作是不会改变map中元素的原来的值
         //赋值可以使用这种方式
         map[k] = "hello"
    }
}
```

```golang
func main(){
    //声明一个map类型的slice
    smap := make([]map[int]string,5)  
    for i,v:=range smap{
        v = make(map[int]string,2)
        v[1]="hello"
        fmt.Println(v)   //输出：map[1:hello]
    }
    fmt.Println(smap)    //这里还是一个空的未初始化的slice，因为在for循环中直接给v进行赋值操作，而此处的v只是一个引用，不会对引用本身(smap)进行修改
}
```

```golang
func main(){
    //声明一个map类型的slice
    smap := make([]map[int]string,5)  
    //这里我们要改变smap，就只需要索引就行了，把v隐藏掉
    for i,_:=range smap{
        smap[i] = make(map[int]string,2)  //这里改变的就是smap本身了
        smap[1]="hello"
        fmt.Println(smap[i])   //输出：map[1:hello]
    }
    fmt.Println(smap)      //输出：[map[1:hello],map[1:hello],map[1:hello],map[1:hello],map[1:hello]]
}
```

- map的间接排序
```golang
import (
    "fmt"
    "sort"
)
func main(){
    ma := map[int]string{1:"a",2:"b",3:"d",4:"e",5:"f"}
    //声明一个slice，长度是ma的长度
    sl := make([]int,len(ma)) 
    i := 0   //声明一个计数器
    for k,_:range ma{
        sl[i] = k
        i++
    }
    sort.Ints(s)  //使用sort包对sl进行升序排列,然后结合ma就可以对map进行简介的排序了
    fmt.Println(s)
}
```

## 2.11. 函数
- Go语言的函数不支持嵌套、重载和默认参数
```golang
//没有参数。没有返回值
func fa(){

}
//有参数，没有返回值
func fb(a int,b string){

}
//有参数，有一个返回值
func fc(a int,b string) int {

}
//有参数，有多个返回值
func fd(a int,b string) (int,string,byte){

}
//相同类型的参数、返回值可以简写
func ff(a,b,c int) (c,d,e int){
    //这种命名返回值参数，相当于已经创建了相应的参数，如果再次创建就会出错
    c := 10     //这是不行的，因为返回值是命名返回值
    return a, b, c
}
```

- 不定长变参
```golang
//不定长变参(其实就是相当于传入了一个slice类型)
func fa(a ...int){

}
//当有其他参数时
func fb(b string,a ...int){
    //不定长参数必须放在所有参数的最后
}

func main(){
    fa(1,2,3,4,5)
}
```

- int、string值类型参数传递
```golang
func main(){
    a,b := 1,2
    fa(a,b)
    fmt.Println(a,b)    //输出1，2 虽然这里对传进来的a,b进行了重新赋值，但是因为传递进来的是值的拷贝，并没有对a,b本身进行修改，所以还是原来的1，2
}

func fa(a ...int){
    a[0] = 3
    a[1] = 4
    fmt.Println(a)      //输出3，4  
}
```

- slice引用类型作为参数传递，传递的是内存地址的拷贝
```golang
func main(){
    s := []int{1,2}
    fa(s)
    fmt.Println(s)       //输出的是[3,4] ，因为slice传递的是内存地址的拷贝，改变的是slice本身
}
func fa(a []int){
    a[0] = 3
    a[1] = 4
    fmt.Println(a)     //输出的是[3,4]
}
```

- 传递引用类型，使用指针修改参数本身
```golang
func main(){
    a := 1
    fa(&a)      //传递变量a的内存地址
    fmt.Println(a)       //输出：2 ,因为传递的是a变量的内存地址，在fa函数中实际上就是对a本身进行操作了
}
//传递一个指针类型
func fa(a *int){
    *a = 2
    fmt.Println(*a)      //输出：2
}
```

- 函数可以作为类型使用
```golang
func main(){
    a := fa
    a()     //把函数fa当作类型参数赋值，然后调用
}
func fa(){
    fmt.Println("hello")
}
```

- 匿名函数的使用
```golang
func main(){
    a := func (){
        fmt.Println("hello")
    }
    a()    //直接把匿名函数赋值，然后调用
}
```

- 闭包
```golang
func main(){
    f := closure(10)   //将函数赋值给变量
    fmt.Println(f(1))  //输出11
    fmt.Println(f(2))  //输出12
}
//声明一个接受int类型参数，返回值是一个匿名函数的函数
func closure(x int) func(int) int{
    //closure函数的返回值是一个匿名函数，接受一个int参数，返回一个int值
    return func(y int) int{
        return x + y
    }
}
```

### 2.11.1. defer
- 类似于其他语言中的析构函数，在函数体执行结束后，按照调用顺序相反的顺序捉个执行一遍
```golang
func main(){
    for i := 0; i < 3; i++{
        defer fmt.Println(i)    //输出的结果是2，1，0，因为defer是按照调用相反的顺序执行的
    } 
}
```

- 即使函数发生严重的错误，defer也会正常执行
- 支持匿名函数调用
```golang
func main(){
    for i := 0; i < 3; i++{
       defer func(){
            fmt.Println(i)    //输出3,3,3 因为defer是按照相反的顺序执行的，正常结果应该就是3,2,输出3，3，3是因为在函数里面的i是值的引用，所以在调用的时候拿到的i都是最后一次调用过后的3
       }()     //最后的括号表示调用这个函数，defer可以用来调用函数，这里就相当于赋值变量后 fa() 这样
    } 
}
```

- Go语言没有异常处理机制，没有c#中`try cache`语句
- 在Go语言中可以使用`panic`/`recover`模式来处理错误
    - `panic`可以在任何地方引发错误抛出
    - `recover`只能在`defer`调用的函数中有效，因为使用`recover`的时候错误已经引发了，只能借助于`defer`进行一些“恢复”操作
    - `recover`可以返回`panic`信息
    ```golang
    func main(){

    }
    func fa(){
        fmt.Println("func fa")
    }
    func fb(){
        //defer如果放在panic 后面，那么就执行不了recover了，因为程序走到panic的时候就已经停止不会往下走了，所以需要在开始就注册recover，在有panic信息抛出，就执行recover
        defer func(){
            if err := recover(); err != nil{
                //获取到了panic信息
                fmt.Println("recover fb");
            }
        }
        panic("panic fb")   //引发错误
    }
    func fc(){
        fmt.Println("func fc")
    }

    //在没有写defer的时候最终输出
    /*
    func fa
    panic fb
    ...
    other err info
    */

    //在写有defer recover的时候会输出
    /*
    func fa
    recover fb
    func fc
    */
    ```

## 2.12. 结构struct

- Go语言中没有`class`的概念，而`struct`就相当于go语言中的类
- `struct`是一种类型(type)
```golang
//声明一个名称为person的struct
type person struct{
    Name string     //一个string类型的属性Name
    Age int         //一个int类型的属性Age
}

func main(){
    //声明一个person类型的变量
    a := person{}
    a.Name = "lisan"
    a.Age = 11
    fmt.Println(a)   //输出：{lisan 11}

    //常用的形式是，直接初始化
    b := person{
        Name : "lisan",
        Age : 11
    }
    fmt.Println(b)   //输出：{lisan 11}
}
```

- `struct`是一个值类型，传递的时候是值拷贝，在其他函数中修改改变不了值本身，希望做改变的时候，可以使用指针传递
```golang
type person struct{
    Name string
    Age int
}

func main(){
    //一般在初始化的时候，直接对struct进行取地址的操作，方便在之后调用的时候，可以拿到地址引用修改struct本身的数值
    a := &person{
        Name = "lisan",
        Age = 11
    }

    //调用改变person的Age值的函数
    fa(a)
}
//因为声明的a是person的内存地址，所以在传入参数的地方使用指针(*)，这样就可以使用内存地址对person进行实际的修改了
func fa(per *person){
    per.Age = 20
    fmt.Println(per)
}
```

-  匿名结构
```golang
func main(){
    a := struct{  //直接声明一个没有名称的struct
        Name string
        Age int
    }{  //声明完成后接着执行初始化操作
        Name : "lisan",
        Age : 11
    }

    //如果需要传递内存地址,写上&符号就可以了
    b := &struct{
        Name string
    }{
        Name = "lisan"
    }
}
```

- 嵌套struct
```golang
type person struct{
    Name string
    Age int
    Contact struct{   //person的Contact属性又是一个struct
        Phone, Address string 
    }
}

func main(){
    a := person{
        Name : "lisan",
        Age : 11
        //Contact是一个嵌套struct，不能再这么初始化了
    }
    a.Contact.Phone = "13233333"
    a.Contact.Address = "china"
}
```

- 匿名字段
```golang
type person struct{
    string     //没有字段名称，只有类型
    int
}
func main(){
    a := person{
        "lisan",
        11
    }
    fmt.Println(a)

    //下面这种初始化会发生错误
    b := person{
        11,      //因为这里使用的是匿名字段，初始化的时候只能根据
        "lisan"  //类型的顺序进行匹配，所以一定要严格按照声明匿名
    }            //struct的字段类型顺序进行初始化
}
```

- 相同类型的struct相互之间可以进行赋值
```golang
type person struct{
    Name string
    Age int
}
func main(){
    a := person{Name : "lisan",Age : 11}
    //声明一个person类型的变量b
    var b person
    //将a赋值给b的操作是允许的
    b = a
    fmt.Println(b)    //输出的跟a相同
}
```

- 两个名称不同的struct，尽管它们的字段一致，它们也是两个不同的类型，它们之间不能进行比较个赋值。
- 使用嵌入结构实现类似继承的能力
```golang
type person struct{
    Sex int
}

type teacher struct{
    person           //把一个struct当作字段放来
    Name string
    Age int
}
type student struct{
    person
    Name string
    Age int
}

func main(){
        //因为前面说过匿名字段，这里的person相当与一个匿名字段，
        //但是如果直接初始化会报错，这种组合关系，会给person类型一
        //认的名字，和类型相同，这样初始化操作就可以
    a := teacher{Name : "zhangsan",Age : 29,person : person{Sex : 1}}
    b := student{Name : "lisi",Age : 11,person : person{Sex : 0}}

    //调用的时候可以直接使用
    a.Sex = 1
    b.Sex = 0
    //也可以这样调用
    a.person.Sex = 1
    b.person.Sex = 0
}
```

- 嵌入结构，同名字段的处理
    - 单层嵌套
    ```golang
    type sa struct{
        sb
        Name string
    }
    type sb struct{
        Name string
    }
    func main(){
        a := sa{ Name : "sa",sb : sb{Name : "sb"}}
        //a.Name就会找sa中的Namw字段
        //a.sb.Name就会找sb中的Name字段
        fmt.Println(a.Name,a.sb.Name)
    }
    ```

    - 多层嵌入
    ```golang
    type sa struct{
        sb
        sc
    }
    type sb struct{
        Name string
    }
    type sc struct{
        Name string
    }
    func main(){
        a := sa{sb : {Name : "sb"},sc : {Name : "sc"}}
        fmt.Println(a.Name) //这里会报错，因为sb、sc里面都有Name字段，没有办法判断该调用哪个,可以使用a.sb.Name 、a.sc.Name
    }
    ```

## 2.13. 方法method

- 相当于是c#中的扩展方法，给某一个类型(type)编写额外的扩展方法，供类型(type)调用
```golang
type sa struct{
    Name string
}
type sb strut{
    Name string
}
func main(){
    a := sa{}
    a.PrintName()   //因为是给类型sa绑定的方法，所以可以像c#中那样调用

    b := sb{}
    b.PrintName()
}

//method
//虽然两个方法都是Print，但是因为传入的参数不同，所以是两个不同的方法
func (a sa) PrintName(){
    fmt.Println("sa")
}
func (a sb) PrintName(){
    fmt.Println("sb")
}
```

- 方法同样可以传入指针，对参数本身进行修改
```golang
type sa struct{
    Name string
}
func main(){
    a := sa{Name:"zhangsan"}
    fmt.Println(a.Name)    //zhangsan
    a.EditName()           //因为调用这个方法传入的是指针，所以修改了Name本身的值
    fmt.Println(a.Name)    //lisi
}
func (a *sa) EditName() {
    a.Name = "lisi"
    fmt.Println(a.Name)    //lisi
}
```

- 使用底层类型方法
```golang
type IN int  //声明一个底层为int类型的type

func (i *IN) Add(num int){
    *i += IN(num)  //i是IN类型的，num是int类型的，虽然IN底层是int，但是不同的两个类型，所以要进行强制转换
}
func main(){
    var a IN
    a.Add(100)
    fmt.Println(a)
}
```

## 2.14. 接口interface

- 接口是一个或多个方法签名的集合(类似c#中的接口，只有声明，没有具体的实现)
- 只要某个类型拥有某个接口的所有的方法签名，那么就算是这个类型实现了该接口，而无需显示的声明实现了哪个接口(在c#中实现一个接口是在类的名字后面写上冒号，然后写接口名，go语言中无需这样做)
```golang
//声明一个接口
type USB interface{
    Name() string    //一个返回string类型的方法Name
    Connect()        //一个没有返回值的方法Connect
}
type PhoneConnect struct{
    name string
}
//结构PhoneConnect实现了接口USB的所有方法，所以就说明结构PhoneConnect实现了接口USB
func (ph PhoneConnect) Name() string{
    return ph.Name    
}
func (ph PhoneConnect) Connect(){
    fmt.Println("Connected:",pc.Name)    
}

func main(){
    var a USB
    //a虽然是USB类型的参数，但是由于PhoneConnect实现了USB接口，所以可以给a赋值一个PhoneConnect类型
    a = PhoneConnect{name:"PhoneConnect"}
    a.Connect()       //输出“Connected:PhoneConnect”
}
```

- 可以嵌入接口
```golang
//声明一个接口
type USB interface{
    Name() string    
    Connecter    //这里把一个接口嵌入进来
}
type Connecter interface{
    Connect()
}
type PhoneConnect struct{
    name string
}

func (ph PhoneConnect) Name() string{
    return ph.Name    
}
//Connect方法是嵌入进接口USB的，也是USB接口的声明，需要实现它
func (ph PhoneConnect) Connect(){
    fmt.Println("Connected:",pc.Name)    
}

func main(){
    var a USB
    a = PhoneConnect{name:"PhoneConnect"}
    a.Connect()       //输出“Connected:PhoneConnect”
}
```

- 接口调用
```golang
//编写一个判断传入对象的方法
func Disconnect(usb USB){
    //判断传入这个方法的是不是“PhoneConnect”类型，如果是就执行一些操作
    if ph,ok := usb.(PhoneConnect); ok{
         //输出：“Disconnected：PhoneConnect”
        fmt.Println("Disconnected:",ph.name)  
        return
    }
    fmt.Println("No PhoneConnect")
}
```

- 所有的类型的父接口，就是一个空的接口(相当于就是c#中的`System.Object`)，理论上来说所有的类型都实现了一个空接口
- 简单工厂
```golang
//方法接受一个空接口，也就是可以传入进来任何值
func Disconnect(usb interface{}){
    //根据传入的值的类型，做出不同的操作
    switch v := usb.(type){   //这里的type就是让系统判断传入的类型
        case PhoneConnect:
            fmt.Println("Disconnected:",v.name)
        default:
            fmt.Println("No PhoneConnect")
    }
}
```

- 接口之间可以互相转换：按照上面的例子，因为接口`USB`包含了接口`Connecter`,所以接口`USB`可以转换成`Connecter`,但是`Connecter`不能转换乘`USB`,这里有一个包含和被包含的关系
```golang
func main(){
    //a是接口USB变量
    a := PhoneConnect{"PhoneConnect"}  
    var b Connecter
    b = Connecter(a)    //Connecter可以转换为USB去执行
    a.Connect()    //输出“Connected:PhoneConnect”
    //但是b中没有USB的Name方法，因为Connecter是被USB包含的，拿不到上一级的成员
}
```

## 2.15. 反射reflection
- 反射使用`TypeOf`和`ValueOf`函数从接口中获取目标对象信息。
```golang
package main
import (
    "fmt"
    "reflect"    //反射包
)
type User struct{
    Id int
    Name string
    Age int
}
func (u User) Hello(){
    fmt.Println("hello")
}

func main(){
    u := User{1,"OK",12}
    //传入结构类型的值u
    Info(o)
}

//传入一个空接口(即可以传入任何类型的参数)
func Info(o interface{}){
    //反射获取参数o的类型信息
    t := reflect.TypeOf(o)
    fmt.Println("Type:",t.Name())

    v := reflect.ValueOf(o)
    fmt.Println("Fields:")

    //获取值信息需要使用循环(因为有多个字段)
    //NumField函数可以获取类型中字段的个数
    for i := 0;i < t.NumField(); i++{
        f := t.Field(i)    //根据字段的索引，获取字段的信息
        val := v.Field(i).Interface()  //取出字段对应的值
        fmt.Printf("%6s: %v = %v\n",f.Name,f.Type,val)
    }
    //获取参数o的方法信息
    for i := 0; i < t.NumMethod(); i++{
        m := t.Method(i)
        fmt.Printf("%6s : %v\n",m.Name,m.Type)
    }
}
```

输出结果
```Shell
Type: User
Fields: 
   Id: int = 1
 Name: string = OK
  Age: int = 12
Hello: func(main.User)
```

- 如果传入反射的参数是一个地址引用，那么使用上面的方法就会出错，可以使用if判断一下

```golang
func main(){
    u := User{1,"OK",12}
    Info(&o)
}
func Info(o Interface{}){
    if k := t.Kind(); k != reflect.Struct{
        fmt.Println("传入的参数不是Sstruct")
        return
    }
}
```

- 反射匿名、嵌入字段
```golang
type User struct{
    Id int
    Name string
    Age int
}
type Manager struct{
    User    //匿名字段(结构类型)
    title string 
}
func main(){
    m := Manager{User:User{1,"OK",12},title:"haha"}
    t := reflect.TypeOf(m)

    //输出1
    fmt.Printf("%#v\n",t.Field(0))
    //输出2
    fmt.Printf("%#v\n",t.Field(1))

    //上面两个取的都是Magager的两个字段，但是匿名字段User中的怎么取呢
    //输出3
    fmt.Printf("%#v\n",t.FieldByIndex([]int{0,0}))
     //输出4
    fmt.Printf("%#v\n",t.FieldByIndex([]int{0,1}))
}
```

输出：
```Shell
//输出1：可以看到根据索引，输出了第一个字段(匿名字段)的类型信息最后一个参数很特殊：Anonymous，值为true的时候表示这个字段是匿名字段，反之就不是匿名字段
reflect.StructField{Name:"User", PkgPath:"", Type:(*reflect.rtype)(0x4a4000), Tag:"", Offset:0x0, Index:[]int{0}, Anonymous:true}

输出2：这个也输出了第二个字段的信息，最后一个参数Anonymous值为false，说明这个字段不是匿名字段
reflect.StructField{Name:"title", PkgPath:"main", Type:(*reflect.rtype)(0x4988e0), Tag:"", Offset:0x20, Index:[]int{1}, Anonymous:false}

//输出3：可以看出来这里取到了User中的Id字段的信息
reflect.StructField{Name:"Id", PkgPath:"", Type:(*reflect.rtype)(0x4982e0), Tag:"", Offset:0x0, Index:[]int{0}, Anonymous:false}

//输出4：
reflect.StructField{Name:"Name", PkgPath:"", Type:(*reflect.rtype)(0x498860), Tag:"", Offset:0x8, Index:[]int{1}, Anonymous:false}
```

- 反射修改字段的值
```golang
func main(){
    i := 111
    v := reflect.ValueOf(&i)    //传入内存地址，获取int类型i的值
    //通过Elem()方法取到实际的对象，然后使用SetInt方法给该变量赋值
    v.Elem().SetInt(222)  

    fmt.Println(i)    //输出222
}
```

- 反射修改struct的字段值
```golang
type User struct{
    Id int
    Name string
    Age int
}

func main(){
    u := User{1,"haha",12}
    Set(&u)   //传入指针地址
    fmt.Println(u)   //输出:{1 heiheihei 12}
}
func Set(o interface{}){
    v := reflect.ValueOf(o)

    //判断反射的value是不是一个引用地址，并且是不是可以被修改的
    if v.Kind() == reflect.Ptr && !v.Elem().CanSet() {
        fmt.Println("reflect error")
        return 
    }else{
        v = v.Elem()   //v定义的是反射出来的value，所以能够赋值一个本身真实的对象
    }

    //修改Name字段的值，如果这里的字段名写错了，那么就无法进行修改
    if f := v.FieldByName("Name"); f.Kind() == reflect.String{
        f.SetString("heiheihei")
    }

    //也可以进行一个字段是否存在的判断(防止字段名错误)
    f := v.FieldByName("Name1")
    if !f.IsValid(){
        fmt.Println("没有这个字段啦")
        return
    }
    //判断字段类型是否是string
    if f.Kind() == reflect.String{
        f.SetString("yoyoyo")
    }
}
```

- 反射调用方法
```golang
type User struct{
    Id int
    Name string
    Age int
}
func (u User) Hello(name string){
    fmt.Println("Hello",name," my name is",u.Name)
}

func main(){
    u := User{1,"OK",12}
    v := reflect.ValueOf(u)
    //根据方法名获取方法对象(方法写错则会抛出异常)
    mv := v.MethodByName("Hello")
    //组织参数(参数必须是一个slice类型)
    args := []reflect.Value{reflect.ValueOf("hai")}
    //调用方法
    mv.Call(args)

    //最终输出：Hello hai my name is OK
}
```

## 2.16. 并发concurrency  

- goroutine是由官方实现的超级“线程池”，每个实例只有4-5KB的栈内存占用，由于实现机制而大幅减少的创建和销毁“线程”的开销，是go语言可以实现高并发的根本原因。
- 并发不是并行：
    - 并行是直接利用多核处理器实现多个核心同时运行多线程的能力，并行是由处理器的核心数决定的，例如单核处理器就实现不了并行。
    - 并发是由切换时间片来实现的，每个线程执行一段时间，然后来回切换。
    - Go语言可以设置使用并发的核数，可以最大发挥多核处理器的能力。
    - 所以Go语言的并发比并行更加高效
- goroutine奉行通过通信来共享内存，而不是共享内存来通信
    - 简单理解：goroutine之间是使用channel进行通信的，而不是使用锁机制，所以不需要对两个goroutine之间的切换加锁，只需要使用go语言实现的channel进行通信即可实现共享内存的切换。
- 使用`go`关键字使用：
```golang
import(
    "fmt"
)
func main(){
    go Go()    //使用gorountine调用Go函数
}
func Go(){
    fmt.Println("hahaha")
}
```

上面的代码运行结果并没有跟想象的那样输出一句"hahaha"，而是什么都没有输出，然后就退出了，原因是：我们在main函数中启动了一个goroutine执行Go函数，这时候就由一个线程去执行Go函数了，然后main没什么影响，继续往下执行，下面什么都没有，所以就直接退出了。
```golang
//为了正确输出，我们使用线程休眠的方法，实现一下
import(
    "fmt"
    "time"
)
func main(){
    go Go()
    time.Sleep(2*time.Second)   //让线程睡两秒钟，大概可以等到Go函数运行结束，然后再退出main函数
}
func Go(){
    fmt.Prinltn("hahaha")
}
```

- 在实际的代码中，我们一般使用channel进行通信，通知其他函数，这个goroutine执行完毕了，然后可以操作其他的了
    - channel是goroutine之间通信的桥梁，大都是阻塞同步的
    - channel存取值操作：
        - 存值使用操作符：`channel<-` (存值时操作符在channel变量的右边)
        - 取值使用操作符：`<-channel` (取值时操作符在channel变量的左边)
    ```golang
    func main(){
        c := make(chan bool)   //使用make函数创建一个channel，存储bool 类型的值
        //定义一个goroutine匿名函数
        go func(){
            fmt.Println("hahaha")
            c <- true     //给channel存一个值
        }()
        //在main函数的最后，从channel中取值，所以这里就会等待goroutine中对channel进行存值，然后这里取出来了，才会结束main函数，这里相当一个阻塞线程的操作
        <- c
    }
    ```
    
    - channel是通过`make()`创建，`close()`进行关闭的
    - channel是引用类型
    - 可以使用`for range`来不断的迭代操作channel，进行取值操作
    ```golang
    func main(){
        c := make(chan bool)
        go func (){
            fmt.Println("hahaha")
            c <- true
            Close(s)  //因为main函数最后有一个for循环在一直迭代取值channel存入的值，所以这里如果不进行Close的话，那么就会发生一个死锁，而抛出异常
        }()
        //这里会等待上面的匿名函数给channel存入一个值，然后打印出来，接着匿名函数调用了Close函数，通知for循环channel关闭，停止迭代，然后main函数继续往下执行，直至退出
        for _,v := range c{
            fmt.Println(v)
        }
    }
    ```

    - 可以设置双向(读写)和单向通道(只读、只写)
    - 可以设置缓存(channel存储数据量)的大小，在没有到最大值的时候，不会发生线程阻塞
    ```golang
    func main (){
        c := make(chan bool,3)   //创建一个channel，可以存储3个长度的数据
        go func(){
            fmt.Println("123")
            c <- true
        }
        <- c   //取出channel中的数据
    }
    ```

> 1. 有缓存的channel相当于是非阻塞的，而有进程的是同步阻塞的。
> 2. channel相当于是queue的功能，先进先出。  
> 3. 如果创建了一个带缓存的channel，那么在channel还没有存储满的时候，取channel数据这个操作不会阻塞程序的运行。反之就会等待channel中有值可以取出时，才会继续下面的其他操作。
> 4. 看到网上一个解释：把channel比作邮递员，有缓存时，相当于你家门外面有一个信箱，只要没装满，邮递员就直接把信放进去就走，不用管你取不取。 没缓存就是邮递员必须把信交到你手里才能走。
