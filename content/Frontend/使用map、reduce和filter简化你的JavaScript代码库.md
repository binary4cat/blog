---
title: "使用map、reduce和filter简化你的JavaScript代码库"
date: 2019-02-24T12:02:52+08:00
tags: ["JavaScript"]
categories: ["Frontend"]
description : "使用map、reduce和filter简化你的JavaScript代码库"
draft: false
photos: 
---
> 原文：[How to simplify your codebase with map(), reduce(), and filter() in JavaScript](https://medium.freecodecamp.org/15-useful-javascript-examples-of-map-reduce-and-filter-74cbbb5e0a1f)

当你查阅有关于`Array.reduce`的资料以及它的用法有多酷以外，在很多资料中唯一的例子就是关于“数字之和”的示例，这对于我们了解`Array.reduce`的定义来说不是很有用。

此外，我也从来没有在真实的代码库中看到有使用它。但是我经常能看到一个7-8行的for循环代码解决的常规任务，使用`Array.reduce`只用一行就搞定了。

如果你有`map`、`reduce`函数更好的使用方式，请在文章评论区回复。

# 1. 从`strings`/`numbers`数组中去重

这是这篇文章中唯一不关于`map`/`reduce`/`filter`函数的使用方法，但是这个方法很有用，很难从这篇文章中排除，而且也会在后面的例子中用到。

```JavaScript
const values = [3, 1, 3, 5, 2, 4, 4, 4];
const uniqueValues = [...new Set(values)];

// uniqueValues 等于 [3, 1, 5, 2, 4]
```

# 2. 简单查找（大小写敏感）

`filter()`函数会创建一个新的数组，在下面的例子中新的数组将包含测试数组中符合条件的子项。

```javascript
const users = [
  { id: 11, name: 'Adam', age: 23, group: 'editor' },
  { id: 47, name: 'John', age: 28, group: 'admin' },
  { id: 85, name: 'William', age: 34, group: 'editor' },
  { id: 97, name: 'Oliver', age: 28, group: 'admin' }
];
let res = users.filter(it => it.name.includes('oli'));

// res 等于 []
```

# 3. 简单查找（不区分大小写）

```javascript
let res = users.filter(it => new RegExp('oli', "i").test(it.name));
// res 等于
[
  { id: 97, name: 'Oliver', age: 28, group: 'admin' }
]
```

# 4. 检查是否有用户拥有管理员权限

`some()`函数可以检查数组中是否有任何元素满足条件，这个条件时自定义的一个`function`：

```javascript
const hasAdmin = users.some(user => user.group === 'admin');
// hasAdmin 等于 true
```

# 5. 展开多维数组

使用`reduce()`函数将一个多维数组展开为一个数组。下面例子中的原始数组在第一次迭代时相当于将`[...[], ...[1,2,3]]`转换成了`[1,2,3]`，第二次迭代时值就是我们提供的`acc`变量，并且以此类推：

```javascript
const nested = [[1, 2, 3], [4, 5, 6], [7, 8, 9]];
let flat = nested.reduce((acc, it) => [...acc, ...it], []);

// flat 等于 [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

我们可以稍稍改进这个方法，去掉`reduce()`函数的第二个参数，这时，`nested`的第一个填充的值就是`acc`的值（从上面的描述中可知，如果有第二个参数，第一次迭代会先将第二个参数的数据填充进去）。感谢[Vladimir Efanov](https://medium.com/@vladimir_efanov?source=responses---------2---------------------)

```javascript
let flat = nested.reduce((acc, it) => [...acc, ...it]);

// flat 等于 [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

需要注意的是在`reduce()`函数中使用[*spread operator*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)会影响性能。

这个例子是排除了性能的情况下的使用方式。

感谢[Paweł Wolak](https://medium.com/@pawewolak?source=responses---------0---------------------)，他提供了一个不使用`Array.reduce`的例子：

```javascript
let flat = [].concat.apply([], nested);
```

所以产生了一个新的功能[`Array.flat`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flat)，不过到目前仍是一个实验性的功能。

# 6. 从一个对象中获取指定属性出现的频率（次数）

对一个对象数组中的每一个`age`属性进行分组和计数：

```javascript
const users = [
  { id: 11, name: 'Adam', age: 23, group: 'editor' },
  { id: 47, name: 'John', age: 28, group: 'admin' },
  { id: 85, name: 'William', age: 34, group: 'editor' },
  { id: 97, name: 'Oliver', age: 28, group: 'admin' }
];
const groupByAge = users.reduce((acc, it) => {
  acc[it.age] = acc[it.age] + 1 || 1;
  return acc;
}, {});

// groupByAge 等于 {23: 1, 28: 2, 34: 1}
```

感谢[sai krishna](https://medium.com/@krishnasai952?source=responses---------8-31--------------------)提出这个示例。

# 7. 给对象数组增加索引（查找表）

我们可以将对象数组构建成一个对象，使用对象的id作为key，这样可以优化查找。

```javascript
const uTable = users.reduce((acc, it) => (acc[it.id] = it, acc), {})

// uTable 等于:
{
  11: { id: 11, name: 'Adam', age: 23, group: 'editor' },
  47: { id: 47, name: 'John', age: 28, group: 'admin' },
  85: { id: 85, name: 'William', age: 34, group: 'editor' },
  97: { id: 97, name: 'Oliver', age: 28, group: 'admin' }
}
```

这是非常有用的，当你想通过id访问某个对象数据的时候，例如：`uTable[85].name`。

# 8. 循环数组提取指定key的唯一值

让我们常见一个已有的用户组。`map()`函数会从被调用的数组中，按照给定的`function`创建一个新的数组。数组中没有重复数据。

```javascript
const listOfUserGroups = [...new Set(users.map(it => it.group))];

// listOfUserGroups 是 ['editor', 'admin'];
```

上面的操作就相当于从`users`数组中获取指定的值，组成数组，`it`表示数组中的对象，`it.group`代表最终要取到的值。

# 9. 对象的键/值翻转

```javascript
const cities = {
  Lyon: 'France',
  Berlin: 'Germany',
  Paris: 'France'
};
let countries = Object.keys(cities).reduce(
  (acc, k) => (acc[cities[k]] = [...(acc[cities[k]] || []), k], acc) , {});

// countries 是
{
  France: ["Lyon", "Paris"],
  Germany: ["Berlin"]
}
```

这个单行操作看起来十分复杂，我在这使用了[*comma operator*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comma_Operator)，这就意味着每次都会返回圆括号中的最后一个值`acc`（这是comma operator的功能）。

让我来重写一下这个例子，让其更加接近真实使用的方式，而且性能更好：

```javascript
let countries = Object.keys(cities).reduce((acc, k) => {
  let country = cities[k];
  acc[country] = acc[country] || [];
  acc[country].push(k);
  return acc;
}, {});
```

这里我没有使用[*spread operator*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)，它会在每一个`reduce()`函数的调用中创建了一个新数组，这会造成很大的性能损耗，其算法复杂度为`O(n²)`。代替[*spread operator*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)的是比较好的旧功能`push()`函数。

# 10. 将一个“摄氏度”值数组转换成“华氏度”值数组

可以看作是使用特定的算法处理每一个元素：

```javascript
const celsius = [-15, -5, 0, 10, 16, 20, 24, 32]
const fahrenheit = celsius.map(t => t * 1.8 + 32);

// fahrenheit 是 [5, 23, 32, 50, 60.8, 68, 75.2, 89.6]
```

# 11. 将对象编码为查询字符串

将一个对象转换成URL的查询字符串：

```javascript
const params = {lat: 45, lng: 6, alt: 1000};
const queryString = Object.entries(params).map(p => encodeURIComponent(p[0]) + '=' + encodeURIComponent(p[1])).join('&')

// queryString 是 "lat=45&lng=6&alt=1000"
```

# 12. 仅使用指定的key（属性）将users用户对象数组转换成可读的字符串

有时候你可能想按照选择的key（属性）当作字符串打印一个对象数组，但是你应当已经认识到`JSON.stringify`并不能很好的实现这一点。

```javascript
const users = [
  { id: 11, name: 'Adam', age: 23, group: 'editor' },
  { id: 47, name: 'John', age: 28, group: 'admin' },
  { id: 85, name: 'William', age: 34, group: 'editor' },
  { id: 97, name: 'Oliver', age: 28, group: 'admin' }
];
users.map(({id, age, group}) => `\n${id} ${age} ${group}`).join('')

// 返回:
"
11 23 editor
47 28 admin
85 34 editor
97 28 admin"
```

`JSON.stringify`可以使字符串更具可读性（json格式），但是它不能打印成table形式：

```javascript
JSON.stringify(users, ['id', 'name', 'group'], 2);

// 返回:
"[
  {
    "id": 11,
    "name": "Adam",
    "group": "editor"
  },
  {
    "id": 47,
    "name": "John",
    "group": "admin"
  },
  {
    "id": 85,
    "name": "William",
    "group": "editor"
  },
  {
    "id": 97,
    "name": "Oliver",
    "group": "admin"
  }
]"
```

# 13. 从对象数组中查找并且替换`key-value`对

假设我们要改变“John's”的年龄，如果你知道对象所在数组的索引，那么完全可以这么写：`users[1].age = 29`，但是让我们来看看另外一种更灵活的写法：

```javascript
const users = [
  { id: 11, name: 'Adam', age: 23, group: 'editor' },
  { id: 47, name: 'John', age: 28, group: 'admin' },
  { id: 85, name: 'William', age: 34, group: 'editor' },
  { id: 97, name: 'Oliver', age: 28, group: 'admin' }
];

const updatedUsers = users.map(
  p => p.id !== 47 ? p : {...p, age: p.age + 1}
);

// John现在是29岁了
```

相反这里不是改变数组中的单个项，我们创建了一个仅有一个元素不同的新对象，现在我们可以试试用引用来比较我们的两个数组，例如：`updatedUsers == users`。React.js使用这种方式加速reconciliation过程。[Here is an explanation](https://blog.logrocket.com/immutability-in-react-ebe55253a1cc)。

# 14. 数组合并

下面的方法比使用[lodash库的`union`函数](https://lodash.com/docs/4.17.11#union)的代码量少:

```javascript
const arrA = [1, 4, 3, 2];
const arrB = [5, 2, 6, 7, 1];
[...new Set([...arrA, ...arrB])]; // returns [1, 4, 3, 2, 5, 6, 7]
```

# 15. 获取两个数组的交集

```javascript
const arrA = [1, 4, 3, 2];
const arrB = [5, 2, 6, 7, 1];

arrA.filter(it => arrB.includes(it)); // returns [1, 2]
```

# 16. 参考资料

[How to simplify your codebase with map(), reduce(), and filter() in JavaScript](https://medium.freecodecamp.org/15-useful-javascript-examples-of-map-reduce-and-filter-74cbbb5e0a1f)