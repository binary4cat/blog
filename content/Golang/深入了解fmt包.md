---
title: "深入了解fmt包"
date: 2019-01-12T20:39:10+08:00
tags: ["fmt"]
categories: ["Golang"]
description : "深入了解fmt包"
draft: false
---

`fmt`包是我们最常使用的package，我们使用它打印输出一些内容，也可以当作字符串formatter工具。在这篇内容中我们再更进一步了解fmt的使用技巧。

# Formatting Output

Go `fmt`的方法支持多个“描述符”，一些常见的“描述符”例如：`%s`表示格式化输出为字符串，`%d`表示格式化输出为整数型，`%f`表示格式化输出为浮点类型。除了这些常见的，这里介绍一些其他的`fmt`包的“描述符”：

## `%v`&`%T`

`%v`将会格式化输出任意的变量的值，`%T`则会格式化输出任意变量的类型（type）

```golang
var e interface{} = 2.7182
fmt.Printf("e = %v (%T)\n", e, e)
```

输出：

```shell
e = 2.7182 (float64)
```

## 宽度

可以在格式化输出的时候，指定输出内容的宽度，例如：

```golang
fmt.Println("[%10d]\n", 353)
```

输出：

```shell
[       353]
```

可以看到输出的内容在353前面多了7个空格(我真的数了)，加上“353”正好是10个字符，这就是“宽度”的意思，把变量格式化为指定的宽度输出。当然指定的宽度如果小于要输出的内容，那么就不会生效了。

这个宽度可以用在其他的“描述符”中，`%s`、`%f`、`%T`等等，对于`%T`这种使用宽度时，会给类型描述设置宽度，这么描述可能不清楚，可以看看代码：

```golang
s := "123"
fmt.Printf("[%10T]\n", s)  // 输出：[    string]
```

也可以在参数中指定宽度的数值，使用`*`来指代宽度数值参数，例如：

```golang
fmt.Printf("%*d", 10, 353)
```

输出：

```shell
[       353]
```

例如在打印一组数字集合时，宽度可以用来使输出内容对其，这样更方便对比查看：

```golang
package main

import (
    "fmt"
    "math"
)

func main() {
    nums := []int{12, 237, 3878, 3}
    size := alignSize(nums)
    for i, n := range nums {
        fmt.Printf("%02d %*d\n", i, size, n)
    }
}

// 计算数组中数字的最大宽度(返回的最大宽度最终+1)
func alignSize(nums []int) int {
    // 宽度的初始值为0
    size := 0
    // 循环数组，找出最大的宽度，并且赋值给size
    for _, n := range nums {
        // log10的对数，可以近似为数字的个数
        if s := int(math.Log10(float64(n))) + 1; s > size {
            size = s
        }
    }
    return size
}
```

输出：

```golang
00   12
01  237
02 3878
03    3
```

## 参数位置索引

如果你在一个格式化输出中，想要在多处““描述符””中引用一个变量，那么可以使用参数位置索引，使用`%[n]d`这种语法就能做到，其中`n`代表参数的位置，位置从1开始（如果写了参数索引为0，就会出现错误`BADINDEX`）。

```golang
fmt.Printf("您购买了%[1]s 消费 $%[2]d元. 合计$%[2]d元!\n", "机械键盘", 2300)
```

输出：

```shell
您购买了机械键盘 消费 $2300元. 合计$2300元!
```

## `%v`

`%v`可以格式化输出一个值，在输出一个struct类型的值时，可以使用`+`前缀同时输出字段的名字，也可以使用`#`前缀同时输出字段的名字和struct的类型：

```golang
func main() {
    p := person{"hai", 26}
    s := "123"
    fmt.Printf("%v %+v %#v\n", p, p, p)
    fmt.Printf("%v %+v %#v\n", &p, &p, &p)
    fmt.Printf("%v %+v %#v\n", s, s, s)
    fmt.Printf("%v %+v %#v\n", &s, &s, &s)
}
```

输出：

```shell
{hai 26} {name:hai age:26} main.person{name:"hai", age:26}
&{hai 26} &{name:hai age:26} &main.person{name:"hai", age:26}
123 123 "123"
0xc0420461c0 0xc0420461c0 (*string)(0xc0420461c0)
```

# fmt.Stringer & fmt.Formatter

有些情况下你也许需要精细的控制要格式化输出的内容，举个例子，你可能想把一些错误信息分成两种格式化输出：用户端显示简化的错误信息，日志内输出详细的错误内容。

要自定义控制对象的格式化输出，你需要实现[`fmt.Formatter`](https://golang.org/pkg/fmt/#Formatter)接口和可选的[`fmt.Stringer`](https://golang.org/pkg/fmt/#Stringer)接口。一个使用`fmt.Formatter`接口最优秀的例子是[`github.com/pkg/errors`](https://github.com/pkg/errors)项目。

假如你的项目中有一个加载配置文件的错误发生，你可以给用户展示简洁的错误，在日志中输出详细的错误信息。

```golang
cfg, err := loadConfig("/no/such/config.toml")
if err != nil {
    fmt.Printf("error: %s\n", err)
    log.Printf("can't load config\n%+v", err)
}
```

展示给用户的错误信息：

```shell
error: can't open config file: open /no/such/file.toml: no such file or directory
```

输出到日志文件的错误信息：

```shell
2018/11/28 10:43:00 can't load config
open /no/such/file.toml: no such file or directory
can't open config file
main.loadConfig
    /home/miki/Projects/gopheracademy-web/content/advent-2018/fmt.go:101
main.main
    /home/miki/Projects/gopheracademy-web/content/advent-2018/fmt.go:135
runtime.main
    /usr/lib/go/src/runtime/proc.go:201
runtime.goexit
    /usr/lib/go/src/runtime/asm_amd64.s:1333
```

接下来说明一下怎么使用`fmt.Formatter`和`fmt.Stringer`来精细化控制格式化信息。

假如有一个存储用户认证信息的struct `AthInfo`：

```golang
type AuthInfo struct {
    Login  string
    ACL    uint
    APIKey string
}
```

在格式化输出(error或者information ..)的时候，应该不能直接把`APIKey`的值展示出来，最好的做法是使用掩码`******`替代。所以可以定义一个常量：

```golang
const (
    keyMask = "******"
)
```

首先实现`fmt.Stringer`接口，以用来使用掩码隐藏`APIKey`：

```golang
// 实现https://golang.org/pkg/fmt/#Stringer接口
func (ai *AuthInfo) String() string {
    key := ai.APIKey
    if key != "" {
        key = keyMask
    }
    // 返回使用掩码格式化后的内容
    return fmt.Sprintf("Login:%s, ACL:%08b, APIKey:%s", ai.Login, ai.ACL, key)
}
```

接下来实现`fmt.Formatter`接口，该接口只有一个方法`Format(f State, c rune)`，这个方法可以实现对格式化输出的自定义操作，其中第一个参数是提供给自定义`Formatter`的一个“printer”的状态数据(struct)，其中包含了当前格式化输出的一些数据，例如当前正在格式化的“描述符”。

当我们的struct实现了`Format`方法，在使用`Printf`格式化方法时，会自动调用自定义的`Format`方法，从而实现对格式化输出的精细化控制。

`Format`函数会在`fmt\print.go`文件中的`handleMethods`方法中被调用，我们可以看看这个方法：

```golang
func (p *pp) handleMethods(verb rune) (handled bool) {
    if p.erroring {
        return
    }
    // p是当前操作的printer，这是一个使用sync.pool生成的对象，因为所有的格式化输出都需要用到它，
    // 所以借助池，减少创建对象时的开销。
    // p.arg 就是当前正在格式化的参数(因为一个格式化输出操作，
    // 可能包含多个“描述符”和参数，所以必不可少的使用循环，来一对一[verb <=> p.arg]操作)
    // 因为我们的参数(对象)有可能实现了Format方法，所以如果断言它是Formatter接口类型，
    // 那么就可以调用自定义的Format方法了
    if formatter, ok := p.arg.(Formatter); ok {
        handled = true
        defer p.catchPanic(p.arg, verb)
        // 调用自定义的Format函数，传入printer和描述符
        formatter.Format(p, verb)
        return
    }

    ...
    return false
}
```

解释完了`Formatter`接口，为了接下来的例子，先实现一个存储需要格式化输出struct的所有字段名字的数组，可以在`init`函数中获取，获取也很简单，直接使用`reflect`反射操作：

```golang
var authInfoFields []string

func init() {
    typ := reflect.TypeOf(AuthInfo{})
    authInfoFields = make([]string, typ.NumField())
    for i := 0; i < typ.NumField(); i++ {
        authInfoFields[i] = typ.Field(i).Name
    }
    sort.Strings(authInfoFields) 
}
```

最后一步，实现我们的自定义`Format`方法：

```golang
func (ai *AuthInfo) Format(state fmt.State, verb rune) {
    switch verb {
    case 's', 'q':
        val := ai.String()
        if verb == 'q' {
            val = fmt.Sprintf("%q", val)
        }
        fmt.Fprint(state, val)
    case 'v':
        // 如果描述符标志为“#”号，就先将格式化参数的类型写入“state”
        if state.Flag('#') {
            fmt.Fprintf(state, "%T", ai)
        }
        // 做拼接操作
        fmt.Fprint(state, "{")
        val := reflect.ValueOf(*ai)
        for i, name := range authInfoFields {
            if state.Flag('#') || state.Flag('+') {
                fmt.Fprintf(state, "%s:", name)
            }
            fld := val.FieldByName(name)
            // 字段名称为`APIKey`并且值的长度大于0，就需要使用掩码替代
            if name == "APIKey" && fld.Len() > 0 {
                fmt.Fprint(state, keyMask)
            } else {
                fmt.Fprint(state, fld)
            }

            if i < len(authInfoFields)-1 {
                fmt.Fprint(state, " ")
            }
        }
        fmt.Fprint(state, "}")
    }
}
```

最后测试一个格式化输出结果：

```golang
func main() {
    ai := &AuthInfo{
        Login:  "daffy",
        ACL:    1,
        APIKey: "dck season",
    }
    fmt.Println(ai.String())
    fmt.Printf("ai %%s 描述符输出: %s\n", ai)
    fmt.Printf("ai %%q 描述符输出: %q\n", ai)
    fmt.Printf("ai %%v 描述符输出: %v\n", ai)
    fmt.Printf("ai %%+v 描述符输出: %+v\n", ai)
    fmt.Printf("ai %%#v 描述符输出: %#v\n", ai)
}
```

输出结果：

```shell
Login:daffy, ACL:00000001, APIKey:******
ai %s 描述符输出: Login:daffy, ACL:00000001, APIKey:******
ai %q 描述符输出: "Login:daffy, ACL:00000001, APIKey:******"
ai %v 描述符输出: {1 ****** daffy}
ai %+v 描述符输出: {ACL:1 APIKey:****** Login:daffy}
ai %#v 描述符输出: *main.AuthInfo{ACL:1 APIKey:****** Login:daffy}
```

# 参考资料

本文参照翻译[https://blog.gopheracademy.com/advent-2018/fmt/](https://blog.gopheracademy.com/advent-2018/fmt/) 并且修改增加了部分内容。