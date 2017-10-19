---
layout: post
title: JavaScript函数
categories: [JavaScript学习]
tags: JavaScript
---

## 定义函数
&emsp;&emsp;在 JavaScript 中，定义函数的方式如下：
```javascript
  function abs(x) {
      if (x >= 0) {
          return x;
      } else {
          return -x;
      }
  }
```
&emsp;&emsp;function 指出这是一个函数定义，abs 是函数的名称，(x) 括号内列出函数的参数，多个参数以 , 分隔。{ ... } 之间的代码是函数体，可以包含若干语句，甚至可以没有任何语句。如果没有 return 语句，函数执行完毕后也会返回结果，只是结果为 undefined。

&emsp;&emsp;由于 JavaScript 的函数也是一个对象，上述定义的 abs() 函数实际上是一个函数对象，而函数名 abs 可以视为指向该函数的变量。因此，第二种定义函数的方式如下：
```javascript
  var abs = function (x) {
      if (x >= 0) {
          return x;
      } else {
          return -x;
      }
  };
```
&emsp;&emsp;在这种方式下，function (x) { ... } 是一个匿名函数，它没有函数名。但是，这个匿名函数赋值给了变量 abs ，所以，通过变量 abs 就可以调用该函数。上述两种定义完全等价，注意第二种方式按照完整语法需要在函数体末尾加一个 ; ，表示赋值语句结束。

## 调用函数
&emsp;&emsp;调用函数时，按顺序传入参数即可。由于 JavaScript 允许传入任意个参数而不影响调用，因此传入的参数比定义的参数多也没有问题，少也没有问题：
```javascript
  abs(-9, 'haha', 'hehe', null); // 返回9
  abs(); // 此时 abs(x) 函数的参数 x 将收到 undefined，计算结果为 NaN
```
&emsp;&emsp;要避免收到 undefined，可以对参数进行检查：
```javascript
  function abs(x) {
      if (typeof x !== 'number') {
          throw 'Not a number';
      }
      if (x >= 0) {
          return x;
      } else {
          return -x;
      }
  }
```

## arguments
&emsp;&emsp;JavaScript 还有一个免费赠送的关键字 arguments，它只在函数内部起作用，并且永远指向当前函数的调用者传入的所有参数。arguments 类似 Array 但它不是一个 Array。利用 arguments，你可以获得调用者传入的所有参数。也就是说，即使函数不定义任何参数，还是可以拿到参数的值：
```javascript
  function abs() {
      if (arguments.length === 0) {
          return 0;
      }
      var x = arguments[0];
      return x >= 0 ? x : -x;
  }

  abs(); // 0
  abs(-9); // 9
```
&emsp;&emsp;ES6 标准引入了 rest 参数，用法如下：
```javascript
  function foo(a, b, ...rest) {
      console.log('a = ' + a);
      console.log('b = ' + b);
      console.log(rest);
  }

  foo(1, 2, 3, 4, 5);
  // a = 1
  // b = 2
  // Array [ 3, 4, 5 ]
```
&emsp;&emsp;rest 参数只能写在最后，前面用 ... 标识，从运行结果可知，传入的参数先绑定 a、b，多余的参数以数组形式交给变量 rest，所以，不再需要 arguments 我们就获取了全部参数。如果传入的参数连正常定义的参数都没填满，也不要紧，rest 参数会接收一个空数组。