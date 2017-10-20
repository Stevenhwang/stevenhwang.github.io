---
layout: post
title: JavaScript基本数据类型
categories: [JavaScript学习]
tags: JavaScript
---

## Number
&emsp;&emsp;JavaScript **不区分整数和浮点数**，统一用 Number 表示，以下都是合法的 Number：
```javascript
  1.2345e3; // 科学计数法表示1.2345x1000，等同于1234.5
  NaN; // 表示Not a Number，当无法计算结果时用NaN表示
  Infinity; // 表示无限大，当数值超过了JavaScript的Number所能表示的最大值时，就表示为Infinity
```

## 字符串
&emsp;&emsp;字符串是以单引号或双引号括起来的任意文本。ES6 新增一种多行字符串的表示方法，用 **反引号** 来表示。同时还新增一种嵌入式字符串，也是使用反引号，其中 ${} 内的变量会被替换（类似于ruby的 #{}）。注意 **JavaScript 字符串是不可变的**，如果对字符串的某个索引赋值，不会有任何错误，但是也没有任何效果。
```javascript
  var s = 'Hello, world';
  s.length; // 12
  s[7]; // 'w'
  s[13]; // undefined  超出范围的索引不会报错，但一律返回undefined
  s.toUpperCase(); // 'HELLO, WORLD'
  s.toLowerCase(); // 'hello, world'
  s.indexOf('world'); // 7
  s.indexOf('World'); // 找不到，返回 -1
  s.substring(0, 5); // 从索引0开始到5（不包括5），返回'Hello'
  s.substring(7); // 从索引7开始到结束，返回'world'
```

## 布尔值
&emsp;&emsp;一个布尔值只有 true、false 两种值。
* JavaScript 的相等运算符 == 会自动转换数据类型再比较是否相等，而 === 不会先转换，所以要坚持使用 === 。
* NaN 这个特殊的 Number 与所有其他值都不相等包括它自己，唯一判断 NaN 的方法是使用 isNaN() 函数。
* JavaScript 把 null、undefined、0、NaN 和空字符串''视为 false，其他值一概视为 true。

## 数组
&emsp;&emsp;数组是一组按顺序排列的集合，数组元素可以是任意数据类型，**JavaScript 的数组是动态数组**。
```javascript
  var arr = ['A', 'B', 'C', 'D'];
  arr.length; // 4
  arr.indexOf('B'); // 1
  arr.slice(0, 3); // 从索引0开始，到索引3结束，但不包括索引3: ['A', 'B', 'C']
  arr.slice(3); // 从索引3开始到结束: ['D']
  arr.slice(); // 不传递任何参数，就从头到尾截取所有元素: ['A', 'B', 'C', 'D']
  arr.push('E', 'F'); // 返回 arr 新的长度: 6
  arr.pop(); // 返回被删除的最后一个元素: 'F'
  arr.unshift('G'); // 返回 arr 新的长度: 6
  arr.shift(); // 返回被删除的第一个元素: 'G'
  arr.sort(); // 返回排序后的数组，并且 arr 也会被修改: ['A', 'B', 'C', 'D', 'E']
  arr.reverse(); // 返回反转后的数组，并且 arr 也会被修改: ['E', 'D', 'C', 'B', 'A']
  arr.splice(2, 3, 'G'); // 从索引2开始删除3个元素，再添加‘G’，返回被删除的元素: ['C', 'B', 'A']
  arr.concat([1, 2, 3]); // 返回连接后的新数组: ['E', 'D', 'G, 1, 2, 3], arr 不变
  arr.join('-'); // 返回连接后的字符串: 'E-D-G'
```

## 对象
&emsp;&emsp;JavaScript 的对象是一组由键-值组成的无序集合，也是 **动态类型**，例如：
```javascript
  var person = {
      name: 'Bob',
      age: '20',
      tags: ['js', 'web', 'mobel'],
      city: 'Beijing',
      hasCar: true,
      zipcode: null
  };
  person.school; // undefined
  person.school = 'HUST'; //新增一个 school 属性
  delete person.school; // 删除 school 属性
  delete person['school']; // 删除一个不存在的属性也不会报错
  'name' in person; // true , 可以用 in 操作符检测对象是否拥有某一属性
```
&emsp;&emsp;注意如果 in 判断一个属性存在，这个属性不一定是 person 的，它可能是 person 继承得到的，比如 toString 定义在 object 对象中，而所有的对象最终都会在原型链上指向 object。要判断一个属性是否是 person 自身拥有的而不是继承得到的，可以用 hasOwnProperty() 方法。

## Map 和 Set
&emsp;&emsp;JavaScript 的默认对象表示方式 {} 可以被视为其他语言中的 Map 或 Dictionary 的数据结构，即一组键值对。但是 JavaScript 的对象有个小问题，就是键必须是字符串，但实际上 Number 或者其他数据类型作为键也是非常合理的。为了解决这个问题，最新的 ES6 规范引入了新的数据类型 Map 和 Set。

&emsp;&emsp;Map 是一组键值对的结构，具有极快的查找速度。初始化 Map 需要一个二维数组，或者直接初始化一个空 Map。例如：
```javascript
  var m = new Map([['Michael', 95], ['Bob', 75], ['Tracy', 85]]);
  m.get('Michael'); // 95
  m.set('Adam', 67); // 添加新的key-value
  m.has('Adam'); // 是否存在key 'Adam': true
  m.delete('Adam'); // 删除key 'Adam'
  m.get('Adam'); // undefined
```

&emsp;&emsp;Set 是一组 key 的集合，不存储 value。由于 key 不能重复，所以在 Set 中，没有重复的元素。要创建一个 Set，需要提供一个 Array 作为输入，或者直接创建一个空 Set。例如：
```javascript
  var s = new Set([1, 2, 3, 3, '3']);
  s.add(4)；// Set { 1, 2, 3, '3', 4 }
  s.delete(3); // true
```

&emsp;&emsp;遍历 Array 可以采用下标循环，遍历 Map 和 Set 就无法使用下标。为了统一集合类型，ES6 标准引入了新的 iterable 类型，Array、Map 和 Set 都属于 iterable 类型。具有 iterable 类型的集合可以通过新的 for...of 循环来遍历。

&emsp;&emsp;for...in 循环由于历史遗留问题，它遍历的实际上是对象的属性。一个 Array 数组实际上也是一个对象，它的每个元素的索引被视为一个属性。当我们手动给 Array 对象添加了额外的属性后，for...in 循环将带来意想不到的意外效果。for...of 循环则完全修复了这些问题，它只循环集合本身的元素。

&emsp;&emsp;更好的方式是直接使用 iterable 内置的 forEach() 方法，它接收一个函数，每次迭代就自动回调该函数: a.forEach(function (element, index, array) {...}); 。如果对某些参数不感兴趣，由于 JavaScript 的函数调用不要求参数必须一致，因此可以忽略它们。
