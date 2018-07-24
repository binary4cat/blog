---
title: Go标准库笔记Ⅰ：IO
date: 2017-10-14T16:56:24+08:00
categories: ["Golang"]
tags: ["Go标准库"]
permalink: GO标准库笔记Ⅰ-IO
description: GO标准库笔记Ⅰ：IO
photos: http://ww1.sinaimg.cn/large/c55a7aeely1ficnszlhh9j20go098jrl.jpg
---
## `io`：基本的I/O接口
### `Reader`接口
- 定义：
    ```Golang
    type Reader interface{
        Read(p []byte)(n int,err error)
    }
    ```
- `Read`方法将`len(p)`个字节读取到p中，返回值是读取的字节数和任何遇到的错误，这个错误可能是读取中遇到的意外或者EOF(end-of-file)。
- 当`Read`方法返回错误时，不代表没有读取到任何数据。**调用者应该处理返回的任何数据，之后才处理可能的错误**。
- 即使`Read`函数返回的的字节数小于`p`的长度，函数也会返回完整的p；如果读取的数据不到`p`的最大长度，`Read`方法会返回当前读取到的可用数据，不会继续等待读取。
- 因为所有实现了`Read`方法的类型都相当于实现了`io.Reader`接口，也就是说，在所有需要`io.Reader`的地方，可以传递实现了`Read()`方法的类型的实例。
<!--more-->
- Example：
    ```golang
    func ReadFrom(reader io.Reader,num int)([]byte,error){
        p := make([]byte,num)    //创建长度为num的byte数组
        n, err := reader.Read(p)    //将reader中的数据读到p里面
        if n > 0{
            return p[:n],nil
        }
        return p ,err
    }

    // 从标准输入读取
    data, err = ReadFrom(os.Stdin, 11)

    // 从普通文件读取，其中 file 是 os.File 的实例
    data, err = ReadFrom(file, 9)

    // 从字符串读取
    data, err = ReadFrom(strings.NewReader("from string"), 12)
    ```
### `Writer`接口
- 定义：
    ```golang
    type Write interface{
        Write(p []byte)(n ,int,err error)
    }
    ```
- 所有实现了`Write`方法的类型都实现了`io.Write`接口。
- `Write`方法将`len(p)`个字节写入基本数据流(也就是传递数据的io实例，例如`os.Stdout`、`http.ResponseWriter`)，返回写入的字节数以及遇到的任何错误，不同的是，`Write`返回的字节数小于`p`的长度时，必须返回一个非nil的错误。
- 参考文章：[《以io.Writer为例看go中的interface{}》](http://blog.studygolang.com/2013/02/%E4%BB%A5io-writer%E4%B8%BA%E4%BE%8B%E7%9C%8Bgo%E4%B8%AD%E7%9A%84interface/)

### 举例实现了`io.Reader`接口或`io.Writer`接口的标准库类型
- 一个能查看接口所有的实现类型的官网镜像：[http://docs.studygolang.com](http://docs.studygolang.com./)
- 举例一些标准库类型
    - `os.File`同时实现了`io.Reader`和`io.Writer`接口
    - `strings.Reader`
    - `bufio.Reader/Writer`
    - `bytes.Buffer`同时实现了`io.Reader`和`io.Writer`
    - `bytes.Reader`
    - `compress/gzip.Reader/Writer`
    - `crypto/cipher.StreamReader/StreamWriter`
    - `crypto/tls.Conn`同时实现了`io.Reader`和`io.Writer`
    - `encoding/csv.Reader/Writer`
    - `mime/multipart.Part` 实现了`io.Reader`
    - `net/conn`分别实现了`io.Reader`和`io.Writer`(`Conn`接口定义了`Read/Write`)

### `ReaderAt`和`WriterAt`接口
- 定义：
    ```golang
    type ReaderAt interface{
        ReadAt(p []byte, off int64) (n int, err error)
    }
    type WriterAt interface {
        WriteAt(p []byte, off int64) (n int, err error)
    } 
    ```
- `ReadAt()`和`WriteAt()`方法多了一个参数`off int64`，这个参数表示偏移量。实现的功能是从该制定的偏移量处读取/写入。
- `ReadAt()`方法比`Read()`方法严格一些，当返回的字节数小于`p`的长度时，必须要返回一个非nil的错误，通知调用方。
- Example:
    ```golang
    reader := strings.NewReader("Go标准库")
    p := make([]byte,9)
    n, err := reader.ReadAt(p,2)
    if err != nil{
        panic(err)
    }
    fmt.Printf("%s,%d\n",p,n)
    ```
    **输出：**
    ```shell
    标准库,9
    ```
    **注意**：需要知道的是，如果上面代码中改成`p := make([]byte,10)`将会触发`panic`，输出`panic: EOF`，这就可以看出`ReadAt`比Read严格的错误机制了。
- `WriteAt`方法基本一致，但是在写入的时候，会将从offset处开始到写入内容长度相同的这一段内容覆盖掉，举例:
    ```golang
    // file是一个.txt文件，里面的内容是“Golang中文社区——这里是多余的”
    n,err := file.WriteAt([]byte("Go语言中文网"),24)
    //最终file中的内容变成了“Golang中文社区——Go语言中文网”，也就是从offset=24处写入的内容，将之前的内容覆盖掉了
    ```
### `ReaderFrom`和`WriterTo`接口
- 定义：
    ```golang
    type ReadFrom interface{
        ReadFrom(r Reader)(n int64,err error)
    }
    ```
- `ReadFrom`方法从一个reader中读取数据，读取到文件末尾(EOF)或者发生错误是进行返回，返回读取的字节数，还有发生的错误，或者`io.EOF`。
- 
