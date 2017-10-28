---
layout: post
title: JavaScript标准对象二
categories: [JavaScript学习]
tags: JavaScript
---

## Date
在 JavaScript 中，Date 对象用来表示日期和时间。要获取系统当前时间，用：
```javascript
  var now = new Date();
  now; // Wed Jun 24 2015 19:49:22 GMT+0800 (CST)
  now.getFullYear(); // 2015, 年份
  now.getMonth(); // 5, 月份，注意月份范围是0~11，5表示六月
  now.getDate(); // 24, 表示24号
  now.getDay(); // 3, 表示星期三
  now.getHours(); // 19, 24小时制
  now.getMinutes(); // 49, 分钟
  now.getSeconds(); // 22, 秒
  now.getMilliseconds(); // 875, 毫秒数
  now.getTime(); // 1435146562875, 以number形式表示的时间戳
```
如果要创建一个指定日期和时间的 Date 对象，有两种方法：
```javascript
  var d = new Date(2015, 5, 19, 20, 15, 30, 123);
  d; // Fri Jun 19 2015 20:15:30 GMT+0800 (CST)

  var d = Date.parse('2015-06-24T19:49:22.875+08:00');
  d; // 1435146562875
  var d = new Date(1435146562875);
  d; // Wed Jun 24 2015 19:49:22 GMT+0800 (CST)
```
Date 对象表示的时间总是按浏览器所在时区显示的，不过我们既可以显示本地时间，也可以显示调整后的 UTC 时间：
```javascript
  var d = new Date(1435146562875);
  d.toLocaleString(); // '2015/6/24 下午7:49:22'，本地时间（北京时区+8:00），显示的字符串与操作系统设定的格式有关
  d.toUTCString(); // 'Wed, 24 Jun 2015 11:49:22 GMT'，UTC 时间，与本地时间相差8小时
```

## RegExp
JavaScript 有两种方式创建一个正则表达式，第一种方式是直接通过 `/正则表达式/` 写出来，第二种方式是通过 `new RegExp('正则表达式')` 创建一个RegExp对象。两种写法是一样的：
```javascript
  var re1 = /ABC\-001/;
  var re2 = new RegExp('ABC\\-001');

  re1; // /ABC\-001/
  re2; // /ABC\-001/
```
>注意，如果使用第二种写法，因为字符串的转义问题，字符串的两个 \\ 实际上是一个 \ 。

先看看如何判断正则表达式是否匹配：
```javascript
  var re = /^\d{3}\-\d{3,8}$/;
  re.test('010-12345'); // true
  re.test('010-1234x'); // false
  re.test('010 12345'); // false
```
>RegExp对象的test()方法用于测试给定的字符串是否符合条件。

用正则表达式切分字符串：
```javascript
  'a b   c'.split(' '); // ['a', 'b', '', '', 'c']
  'a b   c'.split(/\s+/); // ['a', 'b', 'c']
  'a,b, c  d'.split(/[\s\,]+/); // ['a', 'b', 'c', 'd']
  'a,b;; c  d'.split(/[\s\,\;]+/); // ['a', 'b', 'c', 'd']
```

除了简单地判断是否匹配之外，正则表达式还有提取子串的强大功能。用 () 表示的就是要提取的分组（Group）。比如：
```javascript
  var re = /^(\d{3})-(\d{3,8})$/;
  re.exec('010-12345'); // ['010-12345', '010', '12345']
  re.exec('010 12345'); // null
```
>如果正则表达式中定义了组，就可以在 RegExp 对象上用 exec() 方法提取出子串来。exec() 方法在匹配成功后，会返回一个 Array，第一个元素是正则表达式匹配到的整个字符串，后面的字符串表示匹配成功的子串。

正则匹配默认是贪婪匹配，也就是匹配尽可能多的字符，如果需要非贪婪匹配，需要添加 ？。JavaScript的正则表达式还有几个特殊的标志，最常用的是g，表示全局匹配，还可以指定i标志，表示忽略大小写，m标志，表示执行多行匹配。

## JSON
JSON 是 JavaScript Object Notation 的缩写，它是一种数据交换格式。在 JSON 中，一共就这么几种数据类型：
* number：和 JavaScript 的 number 完全一致；
* boolean：就是 JavaScript 的 true 或 false；
* string：就是 JavaScript 的 string；
* null：就是 JavaScript 的 null；
* array：就是 JavaScript 的 Array 表示方式—— []；
* object：就是 JavaScript 的 { ... } 表示方式。

JSON 还规定了字符集必须是 UTF-8，表示多语言就没有问题了。为了统一解析，JSON 的字符串规定必须用双引号，Object 的键也必须用双引号。

几乎所有编程语言都有解析 JSON 的库，而在 JavaScript 中，我们可以直接使用 JSON，因为 JavaScript 内置了 JSON 的解析。把任何 JavaScript 对象变成 JSON，就是把这个对象 **序列化** 成一个 JSON 格式的字符串，这样才能够通过网络传递给其他计算机。如果我们收到一个 JSON 格式的字符串，只需要把它 **反序列化** 成一个 JavaScript 对象，就可以在 JavaScript 中直接使用这个对象了。

比如现在我们有一个 xiaoming 对象，那么可以使用 `JSON.stringify(xiaoming)` 来把它序列化成 JSON 格式的字符串。如果我们还想要精确控制如何序列化 xiaoming ，可以给 xiaoming 定义一个 toJSON() 的方法，直接返回 JSON 应该序列化的数据。

拿到一个 JSON 格式的字符串，我们可以直接用 `JSON.parse()` 把它反序列化成一个JavaScript对象。
