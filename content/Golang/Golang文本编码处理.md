---
title: Golang文本编码处理
date: 2017-09-05T22:43:02+08:00
categories: ["Golang"]
tags: ["Golang入门"]
permalink:
description:
photos: http://ww1.sinaimg.cn/large/c55a7aeely1ficnszlhh9j20go098jrl.jpg
---

# 1. strings库常见例子

## 1.1. `strings.Contains()`包含

```Golang
import(
    "fmt"
    "strings"
)
func main(){
    str := "hello world"
    fmt.Println(strings.Contains(str,"hello"))      //输出 true
    fmt.Println(strings.Contains(str,"?"))      //输出  false
}
```

## 1.2. `strings.Index()`索引

```Golang
import(
    "fmt"
    "strings"
)
func main(){
    str := "hello world"
    fmt.Println(strings.Index(str,"w"))      //输出 6
}
```

## 1.3. `strings.Split()`切割

```Golang
import(
    "fmt"
    "strings"
)
func main(){
    str := "hello,world"
    fmt.Println(strings.Split(str,","))      //输出 [hello world]
}
```

## 1.4. `strings.Join()`合并

```Golang
import(
    "fmt"
    "strings"
)
func main(){
    str := []string{"hello","world"}
    fmt.Println(strings.Join(str,","))      //输出 hello,world
}
```

## 1.5. `strings.HasPrefix() strings.HasSuffix()`是否包含前、后缀

```Golang
import(
    "fmt"
    "strings"
)
func main(){
    str := "hello,world"
    fmt.Println(strings.HasPrefix(str,"he"))      //输出 true
    fmt.Println(strings.HasSuffix(str,"ld"))      //输出 true
}
```

# 2. `strconv`库常用操作

## 2.1. `Itoa`数字转字符串

```Golang
import(
    "fmt"
    "strconv"
)
func main(){
    fmt.Println(strconv.Itoa(10))  //输出 10
}
```

- Itoa的内部实现:  
可以看到是直接调用`FormatInt`函函数进行格式化的

```Golang
// Itoa is shorthand for FormatInt(int64(i), 10).
func Itoa(i int) string {
    return FormatInt(int64(i), 10)
}
```

## 2.2. `Atoi`字符串转数字

```Golang
import(
    "fmt"
    "strconv"
)
func main(){
    fmt.Println(strconv.Atoi("10"))  //输出 10 <nil>
}
```

- Atoi的内部实现:  
内部调用了`ParseInt`函数进行转换，失败会将异常返回，所以传入的字符串必须是符合转换要求的

```Golang
// Atoi returns the result of ParseInt(s, 10, 0) converted to type int.
func Atoi(s string) (int, error) {
    const fnAtoi = "Atoi"
    i64, err := ParseInt(s, 10, 0)
    if nerr, ok := err.(*NumError); ok {
        nerr.Func = fnAtoi
    }
    return int(i64), err
}
```

## 2.3. `ParseBool`字符串转bool类型

```Golang
import(
    "fmt"
    "strconv"
)
func main(){
    fmt.Println(strconv.ParseBool("true"))  //输出 true <nil>
}
```

- `ParseBool`的内部实现：   
从内部实现可以看到这个函数需要的参数要求

```Golang
// ParseBool returns the boolean value represented by the string.
// It accepts 1, t, T, TRUE, true, True, 0, f, F, FALSE, false, False.
// Any other value returns an error.
func ParseBool(str string) (bool, error) {
    switch str {
    case "1", "t", "T", "true", "TRUE", "True":
        return true, nil
    case "0", "f", "F", "false", "FALSE", "False":
        return false, nil
    }
    return false, syntaxError("ParseBool", str)
}
```

## 2.4. `ParseFloat`字符串转浮点数

```Golang
import(
    "fmt"
    "strconv"
)
func main(){
    fmt.Println(strconv.ParseFloat("1.11",64))  //输出 1.11 <nil>
    fmt.Println(strconv.ParseFloat("1.11",32))  //输出 1.1100000143051147 <nil>
}
```

- `ParseFloat`的内部实现：
可以看到内部最终是把转换后的浮点数放到float64类型中返回的

```Golang
// ParseFloat converts the string s to a floating-point number
// with the precision specified by bitSize: 32 for float32, or 64 for float64.
// When bitSize=32, the result still has type float64, but it will be
// convertible to float32 without changing its value.
//
// If s is well-formed and near a valid floating point number,
// ParseFloat returns the nearest floating point number rounded
// using IEEE754 unbiased rounding.
//
// The errors that ParseFloat returns have concrete type *NumError
// and include err.Num = s.
//
// If s is not syntactically well-formed, ParseFloat returns err.Err = ErrSyntax.
//
// If s is syntactically well-formed but is more than 1/2 ULP
// away from the largest floating point number of the given size,
// ParseFloat returns f = ±Inf, err.Err = ErrRange.
func ParseFloat(s string, bitSize int) (float64, error) {
    if bitSize == 32 {
        f, err := atof32(s)
        return float64(f), err
    }
    return atof64(s)
}
```

## 2.5. `Format...`格式化

```Golang
import(
    "fmt"
    "strconv"
)
func main(){
    fmt.Println(strconv.FormatBool(true)) //输出 true(字符串)
    fmt.Println(strconv.FormatFloat(1.123, 'e', 2, 32))  //输出 1.12e+00(字符串)
    fmt.Println(strconv.FormatInt(222,2))   //输出 11011110(字符串)
    fmt.Println(strconv.FormatUint(123,10))  //输出 123(字符串)
}
```

- `FormatBool`内部实现：   
内部实现比较简单，直接if判断返回相应的字符串

```Golang
// FormatBool returns "true" or "false" according to the value of b
func FormatBool(b bool) string {
    if b {
        return "true"
    }
    return "false"
}
```

- `FormatFloat`内部实现： 
可以看到源码注释很详细：需要4个参数分别是：
    - f : 要转换的浮点数
    - fmt : 格式标记(b、e、E、f、g、G)
    - prec : 精度(数字部分的长度，不包括指数部分)
    - bitSize : 指定浮点类型(32:float32、64:float64)

```Golang
// FormatFloat converts the floating-point number f to a string,
// according to the format fmt and precision prec. It rounds the
// result assuming that the original was obtained from a floating-point
// value of bitSize bits (32 for float32, 64 for float64).
//
// The format fmt is one of (格式标记)
// 'b' (-ddddp±ddd, a binary exponent),
// 'e' (-d.dddde±dd, a decimal exponent),
// 'E' (-d.ddddE±dd, a decimal exponent),
// 'f' (-ddd.dddd, no exponent),
// 'g' ('e' for large exponents, 'f' otherwise), or
// 'G' ('E' for large exponents, 'f' otherwise).
//
// The precision prec controls the number of digits
// (excluding the exponent) printed by the 'e', 'E', 'f', 'g', and 'G' formats.
// For 'e', 'E', and 'f' it is the number of digits after the decimal point.
// For 'g' and 'G' it is the total number of digits.
// The special precision -1 uses the smallest number of digits
// necessary such that ParseFloat will return f exactly.
func FormatFloat(f float64, fmt byte, prec, bitSize int) string {
	return string(genericFtoa(make([]byte, 0, max(prec+4, 24)), f, fmt, prec, bitSize))
}
```

- `FormatInt`内部实现:    
可以看到第二个参数`base`表示进制，调用了`formatBits`这个函数对int进行了转换

```Golang
// FormatInt returns the string representation of i in the given base,
// for 2 <= base <= 36. The result uses the lower-case letters 'a' to 'z'
// for digit values >= 10.
func FormatInt(i int64, base int) string {
    _, s := formatBits(nil, uint64(i), base, i < 0, false)
    return s
}
```
