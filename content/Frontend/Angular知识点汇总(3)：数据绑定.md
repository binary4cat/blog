---
title: "Angular知识点汇总(3)：数据绑定"
date: 2018-10-16T22:36:15+08:00
tags: ["Angular"]
categories: ["Frontend"]
description : "Angular知识点汇总(3)：数据绑定"
draft: false
---

# 1. 事件绑定

## 1.1. 事件绑定的语法

事件绑定的语法：

```html
<input (input)="onInputEvent($event)">
<button (click)="onClickEvent()">
```

- `(eventName)`这种小括号包含事件名称的语法就是angular的事件绑定语法，可以将该事件绑定到组件中一个方法，该事件触发时，绑定的方法就会被执行。
- `<input (input)="onInputEvent($event)">`
    - `(input)`：事件绑定语法，绑定的是DOM的原生事件。
    - `onInputEvent`：组件中的一个方法名称，该事件触发的时候就会调用这个方法了。
    - `$event`：浏览器的事件对象，包含当前事件的数据。

## 1.2. 组件中绑定的方法

```typescript
...
export class TestComponent implements OnInit {
  constructor() { }
  ngOnInit() {}
  onInputEvent(event){
      console.log(`您正在输入的内容：${event.target.value}`)
  }
}
```

- 当输入事件触发的时候就会调用组件中的`onInputEvent`函数，在这里我们将输入的内容打印出来。
- 可以看到方法中接收一个浏览器的事件对象，要获取输入的值需要有两次调用`.target.value`。

## 1.3. 模板局部变量

上面的事件参数(`$event`)输入的是原生的浏览器事件对象，有时我们只需要它的value就可以，这时我们可以使用angular提供的模板局部变量，语法的格式是：`<input #valName>`。

在这里定义两个HTML标签，分别打印浏览器事件对象和模板局部变量：

```html
<input (input)="onInputEvent($event)">
<input #myInput (input)="onInputEvent(myInput)" id="myInput">
```

![模板局部变量](/image/Snipaste_2018-10-16_23-05-48.png)

- 可以看到浏览器事件对象的属性较多，如果有需要更多数据的话可以使用该变量。
- 而模板局部变量只是一个DOM的引用，相当于是`document.getElementById("myInput")`的引用。

在只需要传递值的时候，可以使用模板局部变量：

```html
<input #myInput (input)="onInputEvent(myInput.value)">
```

- 直接传入value值即可。
- 要注意写法：声明的时候要加`#`号(`#myInput`)，在取参的时候不需要`#`号(`myInput.value`)。

# 2. 属性绑定

## 2.1. HTML属性绑定和DOM属性绑定之间的区别

用例子演示一下：

```html
// template
<input value="123" (input)="onInputEvent($event)">

// component
onInputEvent(event: any) {
    console.log(`DOM属性value的值为：${event.target.value}`);
    console.log(`HTML属性value的值为：${event.target.getAttribute('value')}`);
}
```

输出效果：  
![输出](/image/Snipaste_2018-10-16_23-30-02.png)

- HTML属性在页面渲染完成后就确定下来了，以后就不再改变了，有点类似于上一篇说过的快照参数。
- DOM属性是当前DOM对象的属性，属于是浏览器的一员，所以总是反映当前属性的状态(值)。
- HTML属性的值不能改变，但是DOM属性的值是可以改变的。
- 因为DOM属性可变这个特性，模板绑定是通过DOM属性和事件结合来实现的。

## 2.2. DOM属性绑定

属性绑定语法：

```html
<img [src]="imageUrl">  <!--属性绑定形式-->
<img src="{{imageUrl}}">  <!--插值表达式形式-->
```

- 属性绑定的语法就是将DOM属性名字用`[]`中括号包括起来，然后付给一个组件中的属性作为值，就相当于给此DOM属性赋值了属性值。
- 使用插值表达式形式也可以给属性赋值，两种方式随意选择就好，只有写法上的区别。
- 这种绑定形式不会改变HTML属性的值，只作用于DOM属性的值范围。

## 2.3. HTML属性绑定

基本的HTML属性绑定语法：

```html
<td [attr.colspan]="tableColspan">...</td>
```

- 为什么有了DOM属性绑定又有HTML属性绑定呢？上面的代码解释了其意义，因为有些标签的属性是没有对应的DOM属性的，例如`td`标签的`colspan`属性只有HTML属性，这时如果直接给其绑定值`<td colspan="tableColspan">`就会抛出异常，说不能绑定`colsoan`。
- HTML绑定的语法也是使用中括号包括属性名字，不过HTML属性绑定中，属性有一个前缀`attr`，`attr`在这里就表示属性的意思。

### 2.3.1. CSS类绑定

可以给属性绑定CSS类，多用来修改样式。

```html
<div class="classA classB" [class]="myClass">
```

- 在已经有class属性的情况下，直接使用属性绑定这种方式，会完全替换原先已有的class属性。

```html
<div class="classA classB" [class.classB]="isShow">
```

- 上面这种class绑定的形式，可以控制已有class中的某一个是否起作用(不会影响其他的class属性)。

```html
<div class="classA classB" [ngClass]="{'classA' : isShowA, 'classB' : isShowB}">
```

- `ngClass`是Angular内置的指令，它的值是一个对象，可以同时控制多个class是否起作用。

### 2.3.2. 样式绑定

可以直接在HTML标签中设置样式代码：

```html
<div [style.color]="isColor ? 'red' : 'yellow'">
```

- 这种直接使用中括号包括的`style`点样式属性的方式，可以直接给样式的这个属性赋值。

```html
<div [ngStyle]="{'color': isColor? 'red': 'yellow'}">
```

- `ngStyle`是Angular内置的指令，它的值是一个对象，可以直接给多个CSS样式赋值。



## 2.4. 双向绑定

前面的属性绑定都属于是单向绑定，也就是绑定在这些属性上的组件属性变量，只能由组件内部进行改变，HTML对其任何改变都不影响这个属性变量的值：

```html
<input [value]="name">
```

- 上面的代码给`input`标签的`value`DOM属性绑定了一个组件的属性变量`name`，那么value的值是多少是随`name`的值改变而改变的，但是如果我们使用DOM操作修改了value的值，并不会造成`name`的值改变。 这就是单向绑定。

双向绑定可以让我们实时的接收DOM元素的值的改变：

```html
<input [(ngModel)]= "name">
```

- `ngModel`是Angular内置的指令，结合`[()]`“中括号包围小括号”的语法，可以实现双向绑定，将input用户输入的值，实时的反映给`name`属性变量。
- 双向绑定多用在form表单上，获取用户的输入。

# 3. 管道(pipe)

## 3.1. 内置管道
Angular管道可以把输入的数据转换成指定格式输出，可以简化为数据的个性化操作。

演示一个Angular内置的管道`date`的使用：

```html
<p>当前的时间是：{{time | date }}</p>  <!--格式化时间的管道-->
<p>当前的时间是：{{time | date | uppercase}}</p> <!--可以多个管道连接接使用-->
<p>当前的时间是：{{time | date:'yyyy-MM-dd HH:mm:ss'}}</p>  <!--管道也可以接收参数-->
```

![管道](/image/Snipaste_2018-10-17_23-13-32.png)

- 第一个是使用`date`这个管道将时间格式化成默认的时间格式
- 第二个是使用管道的连接，将`date`管道的输出作为`uppercase`管道的输入，最终输出了默认格式时间的大写形式
- 第三个是给管道传入了参数，例子里面传入了一个时间格式化格式字符串，`date`会将时间格式化成参数规定的格式。

Angular其余的内置管道可以看[这部分文档](https://www.angular.cn/guide/pipes#built-in-pipes)了解。

## 3.2. 自定义管道

使用angular-cli命令`ng g pipe [name]`生成一个管道，该管道的作用是从一个数组中筛选出包含关键字的数据：

```shell
ng g pipe mypipes/filterKeyword
```

生成的`filter-keyword.pipe.ts`文件初始内容：

```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'filterKeyword'
})
export class FilterKeywordPipe implements PipeTransform {

  transform(value: any, args?: any): any {
    return null;
  }
}
```

- `@Pipe`是管道的装饰器，可以修饰一个普通的class作为管道。
    - `name`：装饰器属性，它的值是此管道的名称，也就是在实际在页面上使用的时候用的名字。
- `PipeTransform`：自定义管道必须实现的接口。
- `transform`：实现`Pipetransform`接口后必须实现的方法，它默认有两个参数，第一个数传入管道的数据，也就是需要被管道处理的数据；第二个参数是一个可空的参数，也就是管道需要使用的参数，可以有多个管道使用的参数，可空与否看自己的实际需求定义。
- 管道的返回值就是最终输出的结果。

编写完成的管道：

```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'filterKeyword'
})
export class FilterKeywordPipe implements PipeTransform {

  transform(list: string[], keyword: string): any {
    // 如果没有关键字，就返回全部的数组数据
    if (!keyword) {
      return list;
    }
    // 按照关键词过滤，获取过滤后的数组
    const result = list.filter(item => {
      return item.indexOf(keyword) >= 0;
    });
    return result;
  }
}
```

我们在组件中演示一下：

首先将我们自定义的管道加入到主模块`app.module.ts`的声明中：

```typescript
@NgModule({
  declarations: [
    ...
    FilterKeywordPipe
  ],
  ...
})
export class AppModule { }
```

在组件中定义：

```typescript
// component
names: string[] = ['张三', '李四', '王五', '王六'];

// template
<p *ngFor="let name of names | filterKeyword:'王'">{{name}}</p>  <!--按照'王'字过滤-->
```

最终会在页面上循环输出“王五”“王六”。

# 4. 参考资料

[https://my.oschina.net/u/3412211/blog/896636](https://my.oschina.net/u/3412211/blog/896636)  
[https://www.angular.cn/guide/displaying-data](https://www.angular.cn/guide/displaying-data)  
[https://www.angular.cn/guide/pipes](https://www.angular.cn/guide/pipes)  
[https://www.angular.cn/guide/template-syntax#html-attribute-vs-dom-property](https://www.angular.cn/guide/template-syntax#html-attribute-vs-dom-property)  
[https://www.angular.cn/guide/pipes](https://www.angular.cn/guide/pipes)  