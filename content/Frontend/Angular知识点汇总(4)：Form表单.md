---
title: "Angular知识点汇总(4)：Form表单"
date: 2018-10-18T23:03:59+08:00
tags: ["Angular"]
categories: ["Frontend"]
description : "Angular知识点汇总(4)：Form表单"
draft: false
---
<!-- TOC -->

- [1. Angular的两种表单API](#1-angular的两种表单api)
    - [1.1. 概述](#11-概述)
- [2. 响应式表单](#2-响应式表单)
    - [2.1. 如何建响应式表单数据模型](#21-如何建响应式表单数据模型)
        - [2.1.1. `FormControl`](#211-formcontrol)
        - [2.1.2. `FormGroup`](#212-formgroup)
        - [2.1.3. `FormArray`](#213-formarray)
    - [2.2. 响应式表单用到的指令](#22-响应式表单用到的指令)
    - [2.3. 响应式表单的特点](#23-响应式表单的特点)
    - [2.4. 响应式表单的例子](#24-响应式表单的例子)
        - [2.4.1. 使用`FormGroup`编写表单并且获取提交](#241-使用formgroup编写表单并且获取提交)
        - [2.4.2. `FormArray`绑定动态表单](#242-formarray绑定动态表单)
    - [2.5. 可单独使用的`formControl`指令](#25-可单独使用的formcontrol指令)
    - [2.6. `FormBuilder`](#26-formbuilder)
- [3. 模板式表单](#3-模板式表单)
    - [3.1. 概述](#31-概述)
    - [3.2. 模板式表单对<form>元素的处理](#32-模板式表单对form元素的处理)
    - [3.3. 模板式表单调用表单对象的实例](#33-模板式表单调用表单对象的实例)
    - [3.4. 模板式表单的提交](#34-模板式表单的提交)
    - [3.5. 模板式表单绑定`ngModel`](#35-模板式表单绑定ngmodel)
- [4. 响应式表单校验](#4-响应式表单校验)
- [5. 模板式表单校验](#5-模板式表单校验)
- [6. 参考资料](#6-参考资料)

<!-- /TOC -->

# 1. Angular的两种表单API

## 1.1. 概述

　　Angular中有两种表单API，分别是模板式表单和响应式表单。模板式表单是通过Angular提供的指令在模板中对表单进行操作的，由于受限于HTML的语法功能，模板式表单只适合一些简单的表单使用场景。而响应式表单是通过组件中自定义数据模型实现对表单的操控。所以更适合复杂表单功能。

　　Angular表单都需要使用一个数据模型来接收存储表单的数据，而这个数据模型并不是我们自定义的类型，而是需要使用Angular提供的特定类型`FormControl`、`FormGroup`、`FormArray`等来组成。

　　在模板式表单中上述的数据模型是在HTML中使用Angular指令，由Angular帮我们生成的；响应式表单相反，这些都需要我们在组件中自定义，所以会更加灵活一些。

　　模板式表单存在于`FormModule`模块中，响应式表单存在于`ReactiveFormModule`模块中，在使用的时候一定要将对应的模块import到`app.module.ts`根模块中。
![模块](/image/Snipaste_2018-10-20_16-33-45.png)  

# 2. 响应式表单

## 2.1. 如何建响应式表单数据模型

创建响应式表首先需要创建表单需要用到的数据模型，然后将数据模型使用指令“映射”到模板上。

要使用响应式表单需要将`ReactiveFormsModule`模块导入到`app.module.ts`模块中：

```typescript
import { ReactiveFormsModule } from '@angular/forms';
@NgModule({
  ...
  imports: [
    ...
    ReactiveFormsModule
  ],
  ...
})
export class AppModule { }
```

定义表单的数据模型需要用到Angular提供的三个类型`FormControl`、`FormGroup`、`FormArray`。

### 2.1.1. `FormControl`

`FormControl`是表单模型的最小单位，也就相当于数据模型对象中的一个属性，具体到表单中可以保存的是一个`input`元素的值和其他元数据。

```typescript
title: FormControl = new FormControl('默认标题');
```

- `FormControl`的构造函数可以传入一个参数，表示默认值，例如绑定在`input`元素上时，就是该元素的默认输入。

### 2.1.2. `FormGroup`

`FormGroup`从语义上看就是代表整个表单，但是也可以保存表单的一部分，它里面可以包含多个`FormControl`，可以包含多个`FormGroup`。

```typescript
regist: FormGroup = new FormGroup({
    account: new FormControl(),
    password: new FormGroup({
      pwd: new FormControl(),
      repwd: new FormControl()
    })
});
```

- `FormGroup`的构造函数需要传入一个对象，对象内部可以是`FormControl`和`FormGroup`。
- 在上面的代码中定义了一个演示的注册信息表单，包含了账号和密码两个主要数据，但是密码分成了两个`FormControl`，分别是密码和重复密码，这两个从业务上是同一个数据，所以在这里包含在一个`FormGroup`中了。

### 2.1.3. `FormArray`

`FormArray`本质上和`FormGroup`是一样的，只不过`FormGroup`中的`FormControl`数量是固定的，初始化多少个就是多少个；`FormArray`中的`FormControl`的数量是可变的，可以动态增减。

```typescript
address: FormArray = new FormArray([
    new FormControl('北京'),
    new FormControl('上海'),
    new FormControl()
]);
```

- `FormArray`的构造函数需要传入一个数组类型，数组中包含一个或多个`FormControl`对象。

**`FormGroup`和`FormArray`的另一个不同点就是访问内部`FormControl`的方式**：

- `FormGroup`可以通过对象的属性名来访问具体的`FormControl`。
- `FormArray`只能通过下标(索引)来访问`FormControl`。

## 2.2. 响应式表单用到的指令

| 类            | 指令(1)       | 指令(2)           |
| :------------ | :------------ | :---------------- |
| `FormGroup`   | `formGroup`   | `formGroupName`   |
| `FormControl` | `formControl` | `formControlName` |
| `FormArray`   |               | `formArrayName`   |

- 指令(1)列的指令都是需要用属性绑定语法使用的，指令(2)列的指令都是不需要属性绑定语法直接使用的。

## 2.3. 响应式表单的特点

- 响应式表单的指令都是以`form`开头的，模板式表单都是用`ng`开头的。
- 响应式表单中不能用模板本地变量。

## 2.4. 响应式表单的例子

### 2.4.1. 使用`FormGroup`编写表单并且获取提交

```typescript
// testComponent
...
export class TestComponent implements OnInit {
  regist: FormGroup = new FormGroup({
    account: new FormControl(),
    password: new FormGroup({
      pwd: new FormControl(),
      repwd: new FormControl()
    })
  });
  onSubmit() {
    console.log(this.regist);
  }
}
```

```html
<form [formGroup]="regist" (submit)="onSubmit()">
    账号：<input formControlName="account">
    <div formGroupName="password">
        密码：<input formControlName="pwd">
        重复密码：<input formControlName="repwd">
    </div>
    <input type="submit" value="确定">
</form>
```

- `[FormGroup]`将组件中定义的`FormGroup`数据模型绑定到表单上。
- `formControlName`与`fromGroupName`分别将数据模型内的`FormControl`和`FormGroup`对象绑定到相应的元素上面。
- 上面有使用属性绑定方式(`[xxx]=ooo`)，也有没有使用的，这是因为使用属性绑定的`regist`是组件中的一个属性，而表单内部的`account`、`password`、`pwd`、`repwd`都是一个字符串(属性名)，所以直接赋值了。

页面的输出：
![页面](/image/Snipaste_2018-10-20_19-13-49.png)

- 可以看到`FormGroup`这个对象中包含很多的属性和方法，如果只关注其表单值的话，可以直接使用其`value`属性。

### 2.4.2. `FormArray`绑定动态表单

```typescript
// testComponent
...
export class TestComponent implements OnInit {
  regist: FormGroup = new FormGroup({
    account: new FormControl(),
    password: new FormGroup({
      pwd: new FormControl(),
      repwd: new FormControl()
    }),
    address: new FormArray([
      new FormControl(),
      new FormControl()
    ])
  });
  onSubmit() {
    console.log(this.regist);
  }
  addAddress() {
    const address = this.regist.get('address') as FormArray;
    address.push(new FormControl());
  }
}
```

- 我们在`regist`数据模型中增加了一个`address`的`FormArray`的动态表单组合，默认有两个`FormControl`对象，也就是默认会有两个空的输入框。
- `addAddress()`函数是响应页面上的一个按钮，用来向动态表单增加`FormControl`，表现在页面上就是每点一次增加一个输入框。
- 可以看到在`addAddress()`函数中获取`address`的过程有点奇怪，这是因为`regist`是一个`FormGroup`对象，而我们定义的模型都是从构造函数传入的，所以在`FormGroup`对象中必须用它暴露的`get()`函数传入属性名来获取。并且需要使用类型转换后才能使用。

```html
// template

<form [formGroup]="regist" (submit)="onSubmit()">
    账号：<input formControlName="account">
    <div formGroupName="password">
        密码：<input formControlName="pwd">
        重复密码：<input formControlName="repwd">
    </div>
    <br>
    <div>
        <ul formArrayName="address">
            <li *ngFor="let a of this.regist.get('address').controls; let i=index;">
                收货地址：<input type="text" [formControlName]="i">
            </li>
        </ul>
        <button type="button" (click)="addAddress()">增加地址栏</button>
    </div>
    <input type="submit" value="确定">
</form>
```

- 在收货地址的input元素上绑定`formControlName`可以看到使用了属性绑定的方式，这里可能会有疑问：不是说包含`Name`结尾的指令都是不需要属性绑定的吗？ 这个需要看使用的情况了，而这也是`FormArray`的特殊的地方，它不能通过属性名去访问成员，只能用下标，所以这里我们传递给`formControlName`的值`i`并不是一个字符串(属性名)，而是一个实实在在的变量，所以需要使用属性绑定的方式传递。

输入页面效果：
![页面](/image/Snipaste_2018-10-20_19-43-38.png)

- 我们增加两个收货地址输入框，然后在其中三个输入了内容，填写其他的内容后，点击提交可以看到控制台输出了结果：`address`的值是一个数组形式，最后一个没有填的值为null。

## 2.5. 可单独使用的`formControl`指令

`fromControl`这个指令比较特殊，它不能出现在`formGroup`的内部，否则就会直接报错：

```typescript
// component
formModel: FormGroup = new FormGroup({
  youName: new FormControl('你的名字')
});
userNamr: FormControl = new FormControl('名字');

// template
<form [formGroup]="formModel">
  <input formControlName="youName">
  <input [formControl]="userName">
</form>
```

可以看到如果`[formControl]`出现在`[formGroup]`的内部就会引发错误：
![错误](/image/Snipaste_2018-10-20_20-03-36.png)

将`[formControl]`拿出去放在`[formGroup]`的外面就不会出现错误了。 之前曾在数据绑定一篇说过，插值表达式绑定的是单向绑定的，那么`[formControl]`的作用就显而易见了，可以创建一个需要双向绑定的数据和组件关联。

## 2.6. `FormBuilder`

Angualr还提供了一个`FormBuilder`对象来简化数据模型的创建，可以将之前的代码修改成`FormBuilder`创建的：

```typescript
export class TestComponent implements OnInit {
  constructor(private fb: FormBuilder) { }
  regist = this.fb.group({
    account: ['账号'],
    password: this.fb.group({
      pwd: ['密码'],
      repwd: ['重复密码']
    }),
    address: this.fb.array(['地址1', '地址2', ''])
  });
  ...
}
```

页面输出同样的内容：
![输出](/image/Snipaste_2018-10-20_20-15-11.png)

- `FormBuilder`对象只有三个方法：
![FormBuilder](/image/Snipaste_2018-10-20_20-18-12.png)
- 这三个方法分别对应着三个表单模型的对象。
- 使用`FormBuilder`会减少一些代码，并且方便接下来的表单验证的使用。

# 3. 模板式表单

## 3.1. 概述

模板式表单前面说过不用在组件中定义数据模型，Angular会隐式的帮助我们创建底层数据模型，其实对应的就是`FormControl`和`FormGroup`：

| 模板表单指令   | 表单模型对象  |
| :------------- | :------------ |
| `NgForm`       | `FormGroup`   |
| `NgModel`      | `FormControl` |
| `NgModelGroup` | `FormGroup`   |

模板式表单依赖`FormsModule`模块的功能，所以首先需要将该模块import到`app.module.ts`主模块中：

```typescript
import { FormsModule } from '@angular/forms';
@NgModule({
  ...
  imports: [
    FormsModule,
  ],
  ...
})
export class AppModule { }
```

## 3.2. 模板式表单对<form>元素的处理

如果一个Angular模板中出现了`<form>`标签，那么就会自动被Angular管理，其所有的原生属性和事件都会失去作用，完全交由Angular掌管。

还有一种显示的标注form表单的方式，就是给任意一个标签加上`ngForm`属性，使其变成一个Angular表单：

```html
<div ngForm>
    ...
</div>
```

如果不想一个form元素被Angular接管，那么需要在元素上标注`ngNoForm`表示该表达不需要Angular管理：

```html
<form ngNoForm>
    ...
</form>
```

## 3.3. 模板式表单调用表单对象的实例

在模板式表单中由于没有显示的定义`FormGroup`，所以想要获取表单的值或者调用表单对象的方法，需要用到模板局部变量。

```html
<form #myForm="ngForm" action="/regist" method="POST">
  账号：<input type="text"><br>
  密码：<input type="text"><br>
  重复密码：<input type="text"><br>
  <button type="button" (click)="viewMyForm(myForm)">查看模板本地变量</button>
  <button type="submit">确定</button>
</form>
```

- 将`ngForm`代表的`FormGroup`实例赋值给模板局部变量`myForm`。

在组件中编写“查看模板本地变量”按钮的点击事件：

```typescript
viewMyForm(obj: any) {
    console.log(obj);
}
```

输出内容：  
![输出](/image/Snipaste_2018-10-20_21-30-21.png)

- 可以看到虽然我们输入了内容，但是value属性中没有获取到数据，这是因为我们还没有为表单中的模型标签绑定属性。

这里我们可以看一下响应式表单的`FormGroup`和`NgForm`实例之间的对比，有大多属性、方法是一样的：
![对比](/image/Snipaste_2018-10-20_21-36-09.png)

## 3.4. 模板式表单的提交

Angular接管form表单后，默认会阻止原生的`submit`事件，取而代之的是Angular提供的`ngSubmit`事件：

```html
<form #myForm="ngForm" (ngSubmit)="onSubmit(myForm.value)">
  账号：<input type="text"><br>
  密码：<input type="text"><br>
  重复密码：<input type="text"><br>
  <button type="button" (click)="viewMyForm(myForm)">查看模板本地变量</button>
  <button type="submit">确定</button>
</form>
```

- 可以将`ngSubmit`事件绑定到组件中的自定义方法上，然后传入一个模板局部变量代表的`NgForm`的value属性参数，相当于是把表单填写的数据传递到了组件。

## 3.5. 模板式表单绑定`ngModel`

前面只演示了使用`ngModel`的value属性可以获取表单的填写内容，但是实际上什么都没获取到，因为对填写的标签没有进行任何的绑定，其填写的数据没法获取。
想要获取各个表单标签的数据，需要在对应的标签上面加上`ngModel`指令，并且指定一个`name`属性的值，这个`name`属性的值就对应了模型数据的属性名。

```html
// template

<form #myForm="ngForm" (ngSubmit)="onSubmit(myForm.value)">
  账号：<input #account="ngModel" ngModel name="account" type="text"><br>
  <div ngModelGroup="password">
      密码：<input ngModel name="pwd" type="text"><br>
      重复密码：<input ngModel name="repwd" type="text"><br>
  </div>
  <button type="button" (click)="viewMyForm(myForm)">查看模板本地变量</button>
  <button type="submit">确定</button>
</form>
```

- `#account="ngModel"`：同样可以将单个`ngModel`的值绑定到模板本地变量上，以供在模板中使用。
- `ngModelGroup`可以实现响应式表单中同样的功能，将一组`ngModel`组合起来。

这时我们点击“查看模板本地变量”按钮看看打印出的数据：
![打印](/image/Snipaste_2018-10-20_22-21-58.png)

# 4. 响应式表单校验

# 5. 模板式表单校验

# 6. 参考资料

[https://www.angular.cn/guide/reactive-forms](https://www.angular.cn/guide/reactive-forms)  
[https://www.angular.cn/guide/forms](https://www.angular.cn/guide/forms)  
[https://www.angular.cn/guide/form-validation](https://www.angular.cn/guide/form-validation)  