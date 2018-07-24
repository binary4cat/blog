---
title: RabbitMQ快速上手
date: 2017-04-11T20:18:59+08:00
categories: ["Message queue"]
tags: ["RabbitMQ"]
permalink: RabbitMQ_Quick_Start_Guide
description: RabbitMQ快速上手
photos: http://ww1.sinaimg.cn/large/c55a7aeely1fej0bq1oryj206404hmwy.jpg
---
>　　这是一篇简单的快速上手文章，不对RabbitMQ做过多的研究，只对安装使用的要点做一些提示，详细的使用心得，有时间记录。

## 安装
　　演示一下windows版本的安装：由于RabbitMQ使用Erlang编写的，需要运行在Erlang运行时环境上，首先需要安装**Erlang**语言，然后安装RabbitMQ的windows版本服务端。**[Erlang下载](http://www.erlang.org/download.html)**  **[RabbitMQ下载](http://www.rabbitmq.com/download.html)**
### 以windows service的方式启动运行
　　以windows服务的形式启动RabbitMQ,可以在后台自动运行；我们进入RabbitMQ的安装目录，进入安装目录种的**sbin**目录下，以**管理员权限**打开cmd命令行工具，分别执行下列三条命令：
<!--more-->
```
rabbitmq-service install
rabbitmq-service enable
rabbitmq-service start
```
### 允许RabbitMQ与Erlang交互
　　进入`C:\Windows`目录下，将`.erlang.cookie`文件复制到`C:\Users\UserName`文件夹下(也就是你自己的用户文件夹下面)。
### 为RabbitMQ创建默认管理员用户并设置密码
　　执行下面命令创建新管理员用户名密码，授予所有权限，并且设置为管理员用户：
```
rabbitmqctl  add_user  username  password
rabbitmqctl  set_permissions  username  ".*"  ".*"  ".*"
rabbitmqctl  set_user_tags username administrator
```
　　删除默认的guest用户:
```
rabbitmqctl delete_user guest
```
　　RabbitMQ提供了一个web管理界面，在服务启动后，我们访问[http://localhost:15672/](http://localhost:15672/)输入设置得用户名密码登陆，就可以看到RabbitMQ的所有执行状态了。

## .Net(Core)中使用RabbitMQ
### 安装.Net(Core)客户端
　　RabbitMQ的.Net客户端支持.Net Core，所以使用.Net Core开发也是可以的。打开NuGet安装工具，搜索**RabbitMQ.Client**安装，或者在程序包管理控制台中输入命令安装：
```
Install-Package RabbitMQ.Client
```
### Publish:
　　首先我们需要编写发布(推送)消息到RabbitMQ的消息队列中的代码：
```
/// <summary>
/// 发送消息
/// </summary>
/// <typeparam name="T">发送的数据类型</typeparam>
/// <param name="t">要发送的数据</param>
public static void Send<T>(T t)
{
    const string exchangeName = "exchangeName";  //交换器名称
    const string queueName = "TestQueue";  //队列名称,区分队列
    const string hostName = "localhost";   //可设置远程地址
    const string userName = "username";    //RabbitMQ的用户名
    const string password = "password";    //RabbitMQ的密码
    //创建连接
    var factory = new ConnectionFactory() { HostName = hostName, UserName = userName, Password = password };
    using (var connection = factory.CreateConnection())
    {
          using (var channel = connection.CreateModel())
          {
                //声明消息队列(持久化到磁盘)
                channel.ExchangeDeclare(exchangeName, "direct", durable: true, autoDelete: false, arguments: null);
                channel.QueueDeclare(queueName, durable: true, exclusive: false, autoDelete: false, arguments: null);
                //绑定队列
                hannel.QueueBind(queueName, exchangeName, routingKey: queueName);
                //使用protobuf序列化对象为二进制(推荐使用protobuf序列化，使用方法在后面)
                byte[] result;
                using (MemoryStream ms = new MemoryStream())
                {
                     Serializer.Serialize(ms, t);
                     result = new byte[ms.Length];
                     ms.Position = 0;
                     ms.Read(result, 0, result.Length);
                }
                //设置消息为可持久化的
                var props = channel.CreateBasicProperties();
                props.Persistent = true;

                //发布
                channel.BasicPublish(exchangeName, routingKey: queueName, basicProperties: props, body: result);
           }
      }

}
```
　　以上就是简单的推送消息的代码，其中使用了谷歌的为进制序列化器**protobuf**对类进行序列化，当然推送的数据除了二进制，也可以是字符串。

### Consumer:
　　接下来我们编写消息队列的接收端代码，接收端代码在windows环境下，一般编写为windows服务启动运行。
```
public static async void Consumer()
{
      const string exchangeName = "exchangeName";  //交换器名称
      const string queueName = "TestQueue";  //队列名称,区分队列
      const string hostName = "localhost";   //可设置远程地址
      const string userName = "username";    //RabbitMQ的用户名
      const string password = "password";    //RabbitMQ的密码

      using (var connection = factory.CreateConnection())
      using (var channel = connection.CreateModel())
      {
            channel.ExchangeDeclare(exchangeName, "direct", durable: true, autoDelete: false, arguments: null);
            channel.QueueDeclare(queueName, durable: true, exclusive: false, autoDelete: false, arguments: null);
            channel.QueueBind(queueName, exchangeName, routingKey: queueName);

            //创建消息接受者
            var consumer = new QueueingBasicConsumer(channel);
            //把接收者添加到channel中
            channel.BasicConsume(queueName, false, consumer);
            //轮询接收消息
            while (true)
            {
                 var res = consumer.Queue.Dequeue();
                 //protobuf反序列化
                 T t;
                 using (MemoryStream ms = new MemoryStream())
                 {
                     ms.Write(res.Body, 0, res.Body.Length);
                     ms.Position = 0;
                     t = Serializer.Deserialize<T>(ms);
                 }
                 //执行操作
                 var flag = false;
                 //可以将要执行的操作写在其他放法中，执行成后返回一个bool值，在最后的代码中判断是否清除该条消息，配合持久化，可以做到消息不丢失
                 flag = DoSomething(t);

                 //重要！ 控制当前是否释放队列中的数据。BasicConsume方法中的noAck必须为false。
                 //如果忘记这个方法，将会无法释放队列中处理完的数据，造成内存泄露
                 if (flag)
                 {
                     channel.BasicAck(res.DeliveryTag, false);
                 }
           }
      }
}
```
### 扩展
　　一般情况下，消息的接收端是寄宿在windows服务中的，我们在windows服务中编写的时候，会在`OnStart`方法中开启消息的监听方法，也就是调用锁编写的`Consumer`方法，这样写会比较方便一些，如果我们有多个接收端需要同时监听处理消息，那么可以使用`Task`进行多线程处理：
```
protected override void OnStart(string[] args)
{
      // TODO: 在此处添加代码以执行启动服务所需的关闭操作。

      //调用消费者处理方法
      Consumer();

      //假如有多个队列同时启动监听处理，那么就使用下面的多线程方式
      //RabbitMQ应该提供了自身多队列同步处理的方法，知道的同学麻烦指导一下，谢谢。
      var task = new Task(()=>{
          Consumer();
      });
      var task1 = new Task(()=>{
          Consumer1();
      });
      task.Start();
      task1.Start();
}
```

## protobuf的使用
### 安装
　　protobuf同样支持.Net Core，我们可以使用NuGet进行安装，在NuGet管理界面输入**protobuf-net**即可找到该组件，点击安装即可；或者也可以使用程序包管理控制台，输入命令安装：
```
Install-Package protobuf-net
```
### 使用
```
[ProtoContract]
public class Person
{
    [ProtoMember(1)]
    public string Name { get; set; }
    [ProtoMember(2)]
    public int Age { get; set; }
    [ProtoMember(3)]
    public string Sex { get; set; }
}
```
PS:类上面使用特性标签`[ProtoContract]`标注，表示这个类可以被protobuf序列化，成员被特性标签`[ProtoMember(int tag)]`标注，表示是可以被序列化的成员，int参数的作用相当于下标(序列化顺序)不可省略。

## 总结
　　这篇文章简单的介绍了RabbitMQ的使用，是我在使用中查阅官方资料、google搜索以及探索过程中的总结，在这里感谢他们的分享。之后有时间的话，会继续深入研究消息队列，希望能带来有干货的文章，最后贴上一本电子书，大家一起看看学习一下：[RabbitMQ实战-高效部署分布式消息队列.pdf](https://share.weiyun.com/54592504fbcd60010497ada1c55a2c48)
