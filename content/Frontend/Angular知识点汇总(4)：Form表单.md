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
  - [4.1. Angular校验器](#41-angular校验器)
  - [4.2. 校验器的使用](#42-校验器的使用)
  - [4.3. 校验器信息的获取](#43-校验器信息的获取)
  - [4.4. 实现自定义的校验器](#44-实现自定义的校验器)
  - [4.5. 在模板中显示校验信息](#45-在模板中显示校验信息)
  - [4.6. 将校验失败的信息编写在校验器中](#46-将校验失败的信息编写在校验器中)
  - [4.7. 异步校验器](#47-异步校验器)
  - [4.8. 校验器的状态字段](#48-校验器的状态字段)
    - [4.8.1. `touched`/`untouched`](#481-toucheduntouched)
    - [4.8.2. `pristine`/`dirty`](#482-pristinedirty)
    - [4.8.3. `pending`](#483-pending)
  - [4.9. 带有状态的class属性](#49-带有状态的class属性)
- [5. 模板式表单校验](#5-模板式表单校验)
  - [5.1. Angular内置校验指令](#51-angular内置校验指令)
  - [5.2. 自定义指令](#52-自定义指令)
    - [5.2.1. 指令校验器](#521-指令校验器)
      - [5.2.1.1. 指令校验器的使用](#5211-指令校验器的使用)
  - [获取校验结果](#获取校验结果)
  - [错误消息](#错误消息)
  - [状态字段](#状态字段)
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

## 4.1. Angular校验器

不管是响应式表单校验还是模板式表单校验，都会用到Anglar校验器，Angular校验器其实是一个方法，它接收一个`AbstractControl`类型的参数，`FormControl`、`FormGroup`、`FormArray`都是继承自该参数类型的实例对象，也就是可以传入这三个模型数据中的任何一个，返回的值是一个任意的对象，只要该对象的key是一个string类型的数据。

```typescript
xxoo(control: AbstractControl): {[key: string]: any} {
  return {xo: true};
}
```

Angular中内置的校验器存在于`Validators`这个类中，例如`Validators.required`是校验必填项、`Validators.minLength()`是校验输入最少字符的。Angular内置的验证器可以查看此篇[文档内容](https://www.angular.cn/api/forms/Validators)。

## 4.2. 校验器的使用

```typescript
regist = this.fb.group({
    // account: ['账号', Validators.required],
    account: ['账号', [Validators.required, Validators.minLength(8)]],
    password: this.fb.group({
      pwd: ['密码', Validators.required],
      repwd: ['重复密码', Validators.required]
    },{validator: [Validators.max]}),
    address: this.fb.array(['地址1', '地址2', ''], [Validators.required])
  });
```

- 使用`FormBuilder`可以对响应式表单进行校验，只需要在代替`FormControl`的数组中传入第二个参数就可以对该`FormControl`进行校验。
- 校验器可以是一个也可以是多个，一个的时候就是直接传入参数，多个时需要写成数组形式。
- `FormGroup`的校验器传入需要在第二个参数传入一个对象，属性为`validator`，参数为校验器。
- `FormArray`的校验器与`FormControl`一致，都是在第二个参数传入一个或多个校验器。

## 4.3. 校验器信息的获取

可以获取单独的字段的校验结果和校验错误信息：

```typescript
onSubmit() {
    const isValid: boolean = this.regist.get('account').valid;
    console.log(`账号的校验结果是:${isValid}`);
    const err: any = this.regist.get('account').errors;
    console.log(`账号的校验错误信息是:${JSON.stringify(err)}`);
}
```

- `.valid`可以获取当前字段的校验结果，该结果是一个bool类型的值。
- `.errors`可以获取当前字段校验未通过时的错误信息，该结果是一个对象(any)类型。

## 4.4. 实现自定义的校验器

创建一个新的ts文件，在其中编写通用的校验器：

`user-info-valids.ts`:

```typescript
import { FormControl, FormGroup, FormArray } from '@angular/forms';

/**
 * email邮箱验证器
 * @param control FormControl
 */
export function emailValidator(control: FormControl): any {
    const reg = /[\w!#$%&'*+/=?^_`{|}~-]+(?:\.[\w!#$%&'*+/=?^_`{|}~-]+)*@(?:[\w](?:[\w-]*[\w])?\.)+[\w](?:[\w-]*[\w])?/;
    const valid = reg.test(control.value);
    return valid ? null : { email: 'email格式不正确' };
}
/**
 * 两次密码输入对比验证
 * @param group FormGroup
 */
export function passwordEqualValidator(group: FormGroup): any {
    const pwd: FormControl = group.get('pwd') as FormControl;
    const repwd: FormControl = group.get('repwd') as FormControl;
    // 返回密码对比的结果
    const valid: boolean = pwd.value === repwd.value;
    return valid ? null : { equal: '两次输入的密码不一致' };
}

/**
 * 验证地址都不能为空
 * @param array FormArray
 */
export function addressValidator(array: FormArray): any {
    for (let i = 0; i < array.controls.length; i++) {
        const val = array.controls[i] as FormControl;
        if (!val.value) {
            return { address: '地址不能为空' };
        }
    }
    return null;
}
```

然后在`test.component.ts`组件中调用：

```typescript
import { emailValidator, passwordEqualValidator, addressValidator } from '../myValid/user-info-valids';
...

  constructor(private fb: FormBuilder) { }
  regist = this.fb.group({
    // account: ['账号', Validators.required],
    account: ['账号', emailValidator],
    password: this.fb.group({
      pwd: ['密码', Validators.minLength(8)],
      repwd: ['重复密码']
    }, {validator: passwordEqualValidator}),
    address: this.fb.array(['地址1', '地址2', ''], addressValidator)
  });

  /**
   * 提交按钮响应事件，这里展示校验结果数据
   */
  onSubmit() {
    const isValid: boolean = this.regist.valid;
    console.log(`表单的校验结果是:${isValid}`);
    const err1: any = this.regist.get('account').errors;
    console.log(`账号的校验错误信息是:${JSON.stringify(err1)}`);
    const err2: any = this.regist.get('password').errors;
    console.log(`密码的校验错误信息是:${JSON.stringify(err2)}`);
    const err3: any = this.regist.get('address').errors;
    console.log(`地址的校验错误信息是:${JSON.stringify(err3)}`);
  }
```

页面展示：  
![页面](/image/Snipaste_2018-10-23_00-11-27.png)

- 可以看到只要有未校验通过的元素，整个表单的校验结果就是false；
- 如果校验未通过，就会展示我们在校验器中自定义的返回对象了，获取这个数据有助于我们个性化处理校验。

## 4.5. 在模板中显示校验信息

要在模板中显示校验的信息，那么就需要在模板中获取校验结果，下面我们看看怎么才能在模板中获取校验的数据；

沿用前面的`test.component.ts`文件中的代码，我们在`test.component.html`文件中使用HTML的`hidden`属性来模拟控制错误信息的展示与否：

```html
<form [formGroup]="regist" (submit)="onSubmit()">
  账号：<input formControlName="account">
  <p [hidden]="!regist.hasError('email','account')">账号必须是合法的邮箱地址！</p>
  <div formGroupName="password">
    密码：<input formControlName="pwd">
    <p [hidden]="!regist.hasError('minlength',['password','pwd'])">密码至少是8个字符</p>
    重复密码：<input formControlName="repwd">
    <p [hidden]="!regist.hasError('equal','password')">两次输入的密码不一致</p>
  </div>
  <br>
  <div>
    <ul formArrayName="address">
      <li *ngFor="let a of this.regist.get('address').controls; let i=index;">
        收货地址：<input type="text" [formControlName]="i">
      </li>
      <p [hidden]="!regist.hasError('address','address')">收货地址不能为空</p>
    </ul>
    <button type="button" (click)="addAddress()">增加地址栏</button>
  </div>
  <input type="submit" value="确定">
</form>
```

- 模型对象的`hasError()`函数可以获取指定的校验器是否有错误。
- 在`FormControl`上调用`hasError()`函数时，第一个参数是校验器校验失败时，返回对象的key值(*注意并不是校验器的名字，而是校验器校验失败时返回的对象的key的名字*)，可以看看前面我们定义的账号校验器失败时返回的对象的key是`email`；第二个参数是当前校验的字段的`formControName`。
- 校验`FormGroup`里面的一个字段时，传入`hasError()`的第二个参数有所变化，需要传入一个数组，数组的第一个参数是当前的`formGroupName`，第二个才是当前校验字段的`formControlName`; 看上面的代码中，因为我们在“密码”所在的字段上标注了`Validators.minLength(8)`校验器，所以它就是在`FormGroup`内部的校验；而两次密码不一致的校验属于是`FromGroup`的校验，这就可以直接传入与前面`FormControl`校验时类似的两个参数就可以。

看看页面上的效果 ：  
![页面](/image/Snipaste_2018-10-23_22-18-18.png)

## 4.6. 将校验失败的信息编写在校验器中

前面我们是将校验失败的信息写在了HTML中，我们也可以定义在校验器中，直接在HTML中用插值表达式绑定，这样更加的灵活：

`user-info-valids.ts`：

```typescript
/**
 * email邮箱验证器
 * @param control FormControl
 */
export function emailValidator(control: FormControl): any {
    const reg = /[\w!#$%&'*+/=?^_`{|}~-]+(?:\.[\w!#$%&'*+/=?^_`{|}~-]+)*@(?:[\w](?:[\w-]*[\w])?\.)+[\w](?:[\w-]*[\w])?/;
    const valid = reg.test(control.value);
    return valid ? null : { email: {errDesc: '邮箱的格式不正确！'} };
}
```

- 我们将校验失败返回对象的属性值改成了一个对象形式，对象的属性名可以任意，这里我起的是`errDesc`，它的值就是想要显示的错误消息。

`test.component.html`:

```html
账号：<input formControlName="account">
<p [hidden]="!regist.hasError('email','account')">
  {{regist.getError('email','account')?.errDesc}}
</p>
```

- 我们将之前硬编码在<p>标签中的错误消息换成了插值表达式绑定。
- 这里又用了一个新方法`getError()`，它的参数和之前的`hasError`一模一样，就是返回值不相同，`getError()`的返回值是校验器中定义的失败时返回的对象，然后调用该返回对象的属性名，就可以获得定义的错误消息了；这里为了避免返回一个空对象，使用了`?`避免报错。

再打开页面就可以看到，错误信息显示成我们在校验器中设置的内容了。

## 4.7. 异步校验器

响应式表单还支持一种“异步校验器”，它可以在需要调用远程服务器校验的情况下使用，其写法和普通的校验器一致，就是返回的结果是一个响应流(`Observable`)，关于`Observable`的信息会在下一节“HTTP服务”中讲到。

将`user-info-valids.ts`中的email校验改成异步校验器：

```typescript
import { of } from 'rxjs';
import { delay } from 'rxjs/operators';

/**
 * email邮箱验证器
 * @param control FormControl
 */
export function emailValidator(control: FormControl): any {
    const reg = /[\w!#$%&'*+/=?^_`{|}~-]+(?:\.[\w!#$%&'*+/=?^_`{|}~-]+)*@(?:[\w](?:[\w-]*[\w])?\.)+[\w](?:[\w-]*[\w])?/;
    const valid = reg.test(control.value);
    return of(valid ? null : { email: {errDesc: '邮箱的格式不正确！'} }).pipe(delay(3000));
}
```

- 上面的代码只更改了返回值，返回了一个可观测的流，为了演示使用了`delay(3000)`延迟3秒返回结果；
- 这里使用的是`rxjs`6的新语法，版本5和版本6的更改比较大，可以[在这里查阅](https://github.com/ReactiveX/rxjs/blob/master/docs_app/content/guide/v6/migration.md)更改。或者使用[Promise方式](https://www.joshmorony.com/username-availability-with-an-asynchronous-validator-in-angular/)也可以。

在页面上查看跟先前的校验没有区别，在本例中会等待3秒再显示错误消息。

## 4.8. 校验器的状态字段

在前面显示错误信息的一节，我们看到错误信息在一开始就出现在页面上的，而正确的验证一般都是用户选中、或者光标离开的时候才进行验证，Angular的状态字段可以获取验证字段的几种状态：

### 4.8.1. `touched`/`untouched`

用来表示某个元素有没有获取过焦点(被点击过)，如果获取过那么`touched`的值就是`true`，同时`untouched`的值就是`false`；反之亦然。

```html
账号：<input formControlName="account">
<div [hidden]="regist.get('account').valid||regist.get('account').untouched">
  <p [hidden]="!regist.hasError('email','account')">
    {{regist.getError('email','account')?.errDesc}}
  </p>
</div>
```

- 使用`FormFroup`的`get()`函数可以获取表单的验证状态`valid`和`touched`/`untouched`字段；
- 这里我们判断验证通过(`valid=true`)或者用户没有点击输入框(`untouched=true`)的时候，隐藏错误信息

### 4.8.2. `pristine`/`dirty`

用来判断一个字段的值有没有被修改过，如果修改过`pristine`的值就是`true`，同时`dirty`的值就是`false`，反之亦然。

```html
账号：<input formControlName="account">
<div [hidden]="regist.get('account').valid||regist.get('account').pristine">
  <p [hidden]="!regist.hasError('email','account')">
    {{regist.getError('email','account')?.errDesc}}
  </p>
</div>
```

- 调用的方式和之前一样，都在`FormGroup`的`get()`函数调用；
- 这时只有当我们修改字段的值(必须是修改值)，才会触发验证，进而展示错误提示。

### 4.8.3. `pending`

用来获取异步校验的状态，当一个字段处于异步校验过程中时，`pending`的值为true，这时就可根据需要显示一个验证中的提示，告知用户，获得更好的用户体验。

```html
账号：<input formControlName="account">
<div [hidden]="regist.get('account').valid||regist.get('account').pristine">
  <div [hidden]="!regist.get('account').pending">
    正在验证邮箱的格式是否正确，请稍后...
  </div>
  <p [hidden]="!regist.hasError('email','account')">
    {{regist.getError('email','account')?.errDesc}}
  </p>
</div>
```

- 我们在之前的验证中加了一个“请稍后”的提示，在异步验证的过程中会出现，异步验证结束会隐藏。

![pending](/image/20181024pending.gif)

## 4.9. 带有状态的class属性

Angular表单默认会在输入框等表单组件上根据状态增减class属性，我们可以对这些class属性进行自定义样式，从而定制字段DOM元素的外观样式。

![样式](/image/Snipaste_2018-10-24_22-04-54.png)

- 我们在第一次进入页面没有对页面的表单字段元素进行任何操作的时候，可以看到表单元素的class属性中默认有三个class属性：
  - `ng-untouched`：表示没有获得过焦点，可以自由发挥设置没有获得过焦点的样式。
  - `ng-pristine`：表示没有修改过字段的值，同样可以自由发挥编辑样式。
  - `ng-invalid`：表示当前的验证是无效的，这是因为没有输入过任何值。

![样式](/image/Snipaste_2018-10-24_22-09-23.png)

- 当我们在“账号”输入框输入格式正确的邮箱时，会发现字段元素的class属性动态的变化了：
  - `ng-dirty`：表示字段被修改过了
  - `ng-valid`：表示字段通过了验证
  - `ng-touched`：表示字段已经获取过焦点了

![样式](/image/Snipaste_2018-10-24_22-12-43.png)

- 在异步校验发生的时候，还会增加一个`ng-pending`的class属性，校验完成后会自动去掉，这个也可以自由控制样式。

# 5. 模板式表单校验

模板式表单的校验需要用到专门的指令来进行校验，Angular内置了一部分校验指令，也可以自定义校验指令。

指令(`directive`)和组件(`component`)差不多，只不过指令没有模板，除此之外可以和组件实现相同的功能。

## 5.1. Angular内置校验指令

angular内置有位数不多的几个校验指令`required`/`minlength`/`maxlength`等，具体可以在[这部分文档](https://www.angular.cn/api/forms#%E6%8C%87%E4%BB%A4)查看。

模板式表单的指令使用比较简单，不管是自定义的还是内置的，直接写在HTML元素的标签上即可：

```html
账号：<input ngModel name="account" type="text" required maxlength="50">
```

## 5.2. 自定义指令

自定义指令需要创建指令：

```shell
ng g directive myDriective/emailValidDriective
```

看看生成的指令`email-valid-driective-drictive.ts`：

```typescript
import { Directive } from '@angular/core';

@Directive({
  selector: '[appEmailValidDriective]'
})
export class EmailValidDriectiveDirective {

  constructor() { }

}
```

- 指令装饰器中的`selector`值是作为标签的一个属性的：

  ```html
  <input selectorValue>
  ```

- 组件装饰器的`selector`的值是标签选择器：

  ```html
  <selectorValue></selectorValue>
  ```

### 5.2.1. 指令校验器

只生成一个指令还不能作为校验器使用，要实现一个可以用来校验的指令，需要将一个普通的校验器包装起来，从而使该指令具有被包装校验器的功能。

我们将之前的`emailValidator`校验器包装成指令：

```typescript
import { Directive } from '@angular/core';
import { NG_VALIDATORS } from '@angular/forms';
import { emailValidator } from '../myValid/user-info-valids';

@Directive({
  selector: '[appEmailValidDriective]',
  providers: [{provide: NG_VALIDATORS, useValue: emailValidator, multi: true}]
})
export class EmailValidDriectiveDirective {
  constructor() { }
}
```

- 在指令装饰器的内部新增一个`providers`属性，在其值内部传入一个对象，该对象有三个属性：
  - `provide`：该属性存储一个“token”，通常是Angular提供的一个常量`NG_VALIDATORS`，代表当前的指令。
  - `useValue`：这个属性绑定了我们要包装的校验器。
  - `multi`：这个字段表示是否可以在同一个“token下包装多个不同的校验器，因为我们会在同一个`NG_VALIDATORS`下包装多个校验器，所以设置为true，代表允许多个。
- 指令的内部不需要任何实现，其实就是一个壳子，真正的校验工作是校验器的内部实现。

**注意**
要使自定义指令校验器生效，需要将指令导入到`app.module.ts`根模块中，一般只要我们使用`ng g drictive name`生成的指令都会自动导入，到根模块中，无需我们手动导入，但是如果指令没有生效，可以首先排查这里是否导入：

```typescript
import { EmailValidDriectiveDirective } from './myDriective/email-valid-driective.directive';
@NgModule({
  declarations: [
    ...
    EmailValidDriectiveDirective
  ],
  ...
})
export class AppModule { }
```

#### 5.2.1.1. 指令校验器的使用

和之前描述的一样，只要将我们写的指令校验器的`selector`的值写在需要校验的元素的标签上即可。

## 获取校验结果

由于模板式表单没有编写数据模型对象，所以不能直接在组件中直接通过`FormGroup`对象调用来得知表单的校验通过与否；我们可以通过模板局部变量将校验结果传到方法的参数中，拿到组件中进行使用：

```html
// template

<form #myForm="ngForm" (ngSubmit)="onSubmit(myForm.value,myForm.valid)">
  账号：<input ngModel name="account" type="text" required maxlength="50" appEmailValidDriective><br>
  <div ngModelGroup="password">
      密码：<input ngModel name="pwd" type="text"><br>
      重复密码：<input ngModel name="repwd" type="text"><br>
  </div>
  <button type="button" (click)="viewMyForm(myForm)">查看模板本地变量</button>
  <button type="submit">确定</button>
</form>
```

- 我们在表单中定义了一个模板局部变量`myForm`，通过将`myForm`变量的`value`和`valid`属性传递给`onSubmit`函数，可以在组件中获取表单的值和校验结果：

```typescript
onSubmit(val: any, valid: boolean) {
  console.log(val);
  console.log(valid);
}
```

页面效果：  
![页面](/image/Snipaste_2018-10-24_23-25-07.png)

## 错误消息

在模板式表单中依然可以使用`hasError`函数来获取错误消息，其参数和响应式表单完全一致，只不过在模板式表单中需要用模板局部变量的`form`属性来调用它：

```html
账号：<input ngModel name="account" type="text" required maxlength="50" appEmailValidDriective><br>
<div [hidden]="!myForm.form.hasError('email','account')">
  {{myForm.form.getError('email','account')?.errDesc}}
</div>
```

- 可以看到除了使用模板局部变量的`form`属性调用`hasError()`函数与`getError()`函数外，其余的参数等都是一致的，两个函数的第一个参数依然是校验器失败返回对象的key，第二个依然是字段的名称。

## 状态字段

状态字段同样可以使用模板局部变量来调用，状态的类型和响应式表单的几种类型一致，我们看一下使用方式

```html
账号：<input ngModel name="account" type="text" required maxlength="50" appEmailValidDriective><br>
<div [hidden]="myForm.valid||myForm.untouched">
  <div [hidden]="!myForm.form.hasError('email','account')">
    {{myForm.form.getError('email','account')?.errDesc}}
  </div>
</div>
```

# 6. 参考资料

[https://www.angular.cn/guide/reactive-forms](https://www.angular.cn/guide/reactive-forms)  
[https://www.angular.cn/guide/forms](https://www.angular.cn/guide/forms)  
[https://www.angular.cn/guide/form-validation](https://www.angular.cn/guide/form-validation)  
[https://www.joshmorony.com/username-availability-with-an-asynchronous-validator-in-angular/](https://www.joshmorony.com/username-availability-with-an-asynchronous-validator-in-angular/)  