---
layout: post
title: JavaScript语法
categories: [JavaScript学习]
tags: JavaScript
---

## 基本语法

* 每个语句应该以 ; 结束， 虽然 JavaScript 并没有强制要求而且浏览器的 JS 引擎会自动补上 ; 。
* 以 // 开头直到行末的内容被视为行注释，用 `/*...*/` 把多行字符包裹起来的内容是块注释。
* JavaScript 严格区分大小写。
* 如果一个变量没有通过 var 声明就被使用，那么该变量就自动被声明为全局变量，所以要坚持使用 var 来声明变量。

## 数据类型

#### Number
&emsp;&emsp;JavaScript 不区分整数和浮点数，统一用 Number 表示，以下都是合法的 Number：
```javascript
  123; // 整数123
  0.456; // 浮点数0.456
  1.2345e3; // 科学计数法表示1.2345x1000，等同于1234.5
  -99; // 负数
  NaN; // 表示Not a Number，当无法计算结果时用NaN表示
  Infinity; // 表示无限大，当数值超过了JavaScript的Number所能表示的最大值时，就表示为Infinity
```

#### 字符串
&emsp;&emsp;字符串是以单引号或双引号括起来的任意文本。
* ES6 新增一种多行字符串的表示方法，用反引号来表示: ``` `...` ``` 。
* ES6 新增一种嵌入式字符串，也是使用反引号：``` `...${...}...` ``` ，但是 ```${}``` 内的变量会被替换。
* 字符串是不可变的，如果对字符串的某个索引赋值，不会有任何错误，但是，也没有任何效果。
* 常用的字符串方法：
```javascript
  var s = 'Hello, world';
  s.length; // 12
  s[7]; // 'w'
  s[13]; // undefined  超出范围的索引不会报错，但一律返回undefined
  s.toUpperCase(); // 'HELLO, WORLD'
  s.toLowerCase(); // 'hello, world'
  s.indexOf('world'); // 7
  s.substring(0, 5); // 从索引0开始到5（不包括5），返回'hello'
  s.substring(7); // 从索引7开始到结束，返回'world'
```

#### 布尔值
&emsp;&emsp;一个布尔值只有 true、false 两种值。
* JavaScript 的相等运算符 == 会自动转换数据类型再比较而 === 不会，所以要坚持使用 === 。
* NaN 这个特殊的 Number 与所有其他值都不相等包括它自己，唯一判断 NaN 的方法是使用 isNaN() 函数。
* null 表示一个“空”的值，undefined 和 null 类似，表示“未定义”。大多数情况都应该用 null 。

#### 数组
&emsp;&emsp;数组是一组按顺序排列的集合，数组元素可以是任意数据类型，例如：
```javascript
  [1, 2, 3.14, 'Hello', null, true];
```

* 如果通过索引赋值时，索引超过了范围，会引起 Array 大小的变化而 JavaScript 不会报错。

#### 对象
&emsp;&emsp;JavaScript 的对象是一组由键-值组成的无序集合，例如：
```javascript
  var person = {
      name: 'Bob',
      age: '20',
      tags: ['js', 'web', 'mobel'],
      city: 'Beijing',
      hasCar: true,
      zipcode: null
  };
```
>JavaScript 对象的键都是字符串类型，值可以是任意数据类型。上面 person 对象一共定义了6个键值对，其中每个键又称为对象的属性，例如， person 的 name 属性为 'Bob'，zipcode 属性为 null。要获取一个对象的属性，可以用 对象变量.属性名 的方式：person.name;
