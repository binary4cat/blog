---
title: 适配器模式(Adapter)
date: 2016-01-12T20:52:41+08:00
tags: 设计模式学习笔记
categories: 设计模式
permalink: Design-patterns-Adapter
description: 适配器模式(Adapter)
---
>将一个类的接口转换成客户希望的另一个接口。Adapter模式是将原本由于接口不兼容而不能工作在一起的那些类可以在一起工作。       ————GoF  

　　在软件系统中，由于应用环境的变化，常常需要将一些老的系统放在新的系统中使用，但是新的系统要求的接口和老的系统不一致，这就需要一个中间的适配器去将新老系统转换连接，使之能够正常工作。<!--more-->

　　Adapter模式中有两种模式：对象适配器和类适配器，主要使用的是对象适配器这种模式。
**对象适配器**:
![](/image/Design Pattern/design pattern2.png)

　　对象适配器采用对象的组合(老的系统和适配器捆绑成一个组合)，通过引用一个类与另一个类接口在对象适配器中通过组合获得*Adaptee*(被适配者)对象。
　　通过调用Adapter对象的方法，转换后返回Target结果。这里做了两步，第一是继承新的接口Target，让Target可以使用里氏替换任意使用Adapter；第二是调用老系统Adaptee，在Adapter内部对老系统的方法实现进行转换，以可用于新系统中。
　　特点就是通过维护一个Adaptee的引用(老方法)来让新老系统兼容，只要能拿到Adaptee的内部实现就可以了，不用管Adaptee这个类是什么(接口、类、密封类……)，因为无需让Adapter和Adaptee发生继承关系。

**类适配器**：
![](/image/Design Pattern/design pattern3.png)

　　类适配器通过多继承对一个接口与另一个接口进行匹配。
　　Target定义了Client使用的与特定领域相关的接口，Client通过调用Target实现某一个特定的操作。Adaptee是一个已经存在的类，需要与Target协同工作，这个接口需要适配。  Adapter适配器适配Adaptee和Target接口。在类的适配器中，通过继承的方式获得Adaptee中的方法。
　　.net中不支持多重继承，因此当Target是一个类，而不是一个接口时，是无法实现类适配器的，这是需要使用对象适配器。
　　通过继承老的类实现的，如果新老两个都是类，就无法让适配器同时继承两个类；再如果老的类是一个sealed类，那么就无法对老的类进行继承，也无法实现。

　　下面我们写一个例子：在手机产品中，使用的充电传输接口有两种老的microUSB和最新的Type-C，我们假设现在我们只有一条Type-C接口，但是要给拥有Lumia950XL和小米note进行充电和下载，其中Lumia950XL本身是Type-C接口的手机，而小米note则是旧的microUSB接口，所以为了给小米note充电和下载需要一个适配器，我们用代码实现一下这个适配器的功能：
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Adapter
{
    class Program
    {
        static void Main(string[] args)
        {
            ChargingAndDownload lumia = new TypeC() { Name = "Lumia950XL" };
            ChargingAndDownload xiaomi = new USB_Adapter() ;

            lumia.Charging();
            lumia.Download();
            Console.WriteLine("—————————————————————————————————————");
            xiaomi.Charging();
            xiaomi.Download();
            Console.ReadLine();
        }
    }

    /// <summary>
    /// 抽象充电和下载，因为不管是lumia950XL还是小米note都拥有这两项功能
    /// </summary>
    public abstract class ChargingAndDownload
    {
        public string Name { get; set; }

        public abstract void Charging();
        public abstract void Download();
    }

    /// <summary>
    /// 使用Type-C的手机
    /// </summary>
    public class TypeC : ChargingAndDownload
    {
        public override void Charging()
        {
            Console.WriteLine("{0}使用Type-C充电",base.Name);
        }

        public override void Download()
        {
            Console.WriteLine("{0}使用Type-C下载",base.Name);
        }
    }

    /// <summary>
    /// 使用microUSB的手机
    /// </summary>
    public class MicroUSB 
    {
        public string Name { get; set; }
        public void Charging()
        {
            Console.WriteLine("{0}使用microUSB充电", this.Name);
        }

        public void Download()
        {
            Console.WriteLine("{0}使用microUSB下载", this.Name);
        }
    }

    public class USB_Adapter : ChargingAndDownload
    {
        //拿到被适配者
        private MicroUSB _adapter = new MicroUSB() { Name="小米note"};
        public override void Charging()
        {
            //借力打力
            this._adapter.Charging();  
        }

        public override void Download()
        {
            //移花接木
            this._adapter.Download();
        }
    }
}

```
　　运行结果：
![](/image/Design Pattern/design pattern4.png)
　　可以看到我们使用了适配器，调用最新的接口的时候，虽然同样使用的**ChargingAndDownload**类实例，但是新旧传输接口都可以正常工作了。

例子二：
　　我们都知道姚明是NBA球星，假如姚明去NBA打球时不会说英语，那么就需要一个翻译，这个翻译就充当了Adapter的作用，在NBA中英语就是新系统，汉语就是旧系统，我们用代码实现一下教练指挥姚明打球的功能。
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Adapter2
{
    class Program
    {
        static void Main(string[] args)
        {
            Sports kobe = new USA_Sports() { Name="kobe"};
            Sports yaoming = new Translate();

            kobe.Shot();
            kobe.Dribble();
            Console.WriteLine("------------------------------");
            yaoming.Shot();
            yaoming.Dribble();
            Console.ReadKey();
        }
    }

    /// <summary>
    /// 抽象出运动员投篮和运球的功能
    /// </summary>
    public abstract class Sports
    {
        public string Name { get; set; }
        public abstract void Shot();
        public abstract void Dribble();
    }

    /// <summary>
    /// 美国球员打球
    /// </summary>
    public class USA_Sports : Sports
    {
        public override void Dribble()
        {
            Console.WriteLine("{0} the Shot",base.Name);
        }

        public override void Shot()
        {
            Console.WriteLine("{0} the Dribble", base.Name);
        }
    }

    /// <summary>
    /// 中国球员
    /// </summary>
    public class CN_Sports
    {
        public string Name { get; set; }
        public void Shot()
        {
            Console.WriteLine("{0}投篮",this.Name);
        }
        public void Dribble()
        {
            Console.WriteLine("{0}运球", this.Name);
        }
    }

    public class Translate : Sports
    {
        CN_Sports _adapter = new CN_Sports() {Name="姚明" };
        public override void Dribble()
        {
            this._adapter.Shot();
        }

        public override void Shot()
        {
            this._adapter.Dribble();
        }
    }
}

```
　　运行结果：
![](/image/Design Pattern/design pattern5.png)
　　可以看到我们只要调用新的接口**Sports**，不管是科比还是姚明都可以投篮和运球，不管姚明听不听得懂英语，这一切都交给了**Translate**(适配器)进行处理。


**实现要点**：
　　适配器模式重在转换接口，它能够使原本不能再一起工作的两个类，在一起工作，所以经常用在类库的复用、代码迁移等方面，有一种亡羊补牢的味道。

**效果**:
　　通过类的继承或者对象的组合转换*已有接口*为*目标接口*。

**适用性**：
　　需要使用一个已经存在的类(老系统)，但接口与设计要求不符(也就是在新系统中无法使用)。
　　希望创建一个可以复用的类(也就是类库的相似功能)，该类可以与其他不相关的类或者是将来不可预见的类协同工作。

**总结**：
　　Adapter模式主要应用于“希望复用一些现存的类，但是接口又与复用环境要求不一致的情况”，在遗留代码复用、类库迁移等方面非常有用。
　　GoF23定义了两种Adapter模式的实现结构：对象适配器和类适配器，但类适配器采用了“多继承”的实现方式，带来了不良的高耦合，所以一般不推荐使用。对象适配器采用“对象组合”的方式，更符合松耦合的精神。
　　Adapter模式可以实现的非常灵活，不必拘泥于GoF23中定义的两种结构。例如，完全可以将Adapter模式中的“现存对象”作为新的接口方法参数，来达到适配的目的。