---
layout: post
title: JavaScript函数四
categories: [JavaScript学习]
tags: JavaScript
---

## 闭包
高阶函数除了可以接受函数作为参数外，还可以 **把函数作为结果值** 返回。

通常情况下，求和的函数是这样定义的：
```javascript
  function sum(arr) {
     return arr.reduce(function (x, y) {
         return x + y;
     });
  }

  sum([1, 2, 3, 4, 5]); // 15
```
但是，如果不需要立刻求和，而是要在后面的代码中，根据需要再计算怎么办？可以不返回求和的结果，而是返回求和的函数！
```javascript
  function lazy_sum(arr) {
      var sum = function () {
          return arr.reduce(function (x, y) {
              return x + y;
          });
      }
      return sum;
  }

  var f = lazy_sum([1, 2, 3, 4, 5]); // function sum()
  f(); // 15
  var f1 = lazy_sum([1, 2, 3, 4, 5]);
  var f2 = lazy_sum([1, 2, 3, 4, 5]);
  f1 === f2; // false
```
这个例子中，我们在函数 lazy_sum 中又定义了函数 sum，并且，内部函数 sum 可以引用外部函数 lazy_sum 的参数和局部变量，当 lazy_sum 返回函数 sum 时，相关参数和变量都保存在返回的函数中，这种称为“闭包（Closure）”的程序结构拥有极大的威力。还需要注意的是，当我们调用 lazy_sum() 时，每次调用都会返回一个新的函数，即使传入相同的参数。注意到返回的函数在其定义内部引用了局部变量 arr，所以，当一个函数返回了一个函数后，其内部的局部变量还会被新函数引用：
```javascript
  function count() {
      var arr = [];
      for (var i=1; i<=3; i++) {
          arr.push(function () {
              return i * i;
          });
      }
      return arr;
  }

  var results = count();
  var f1 = results[0];
  var f2 = results[1];
  var f3 = results[2];
  f1(); // 16
  f2(); // 16
  f3(); // 16
```
3个函数的执行结果全部都是16！原因就在于返回的函数引用了变量 i，但它并非立刻执行。等到3个函数都返回时，它们所引用的变量 i 已经变成了4，因此最终结果为16。

JavaScript 还有一种创建一个匿名函数并立刻执行的语法：
```javascript
  (function (x) {
      return x * x;
  })(3); // 9
```

在面向对象的程序设计语言里，比如 Java 和 C++，要在对象内部封装一个私有变量，可以用 private 修饰一个成员变量。在没有 class 机制，只有函数的语言里，借助闭包，同样可以封装一个私有变量。我们用 JavaScript 创建一个计数器：
```javascript
  function create_counter(initial) {
      var x = initial || 0;
      return {
          inc: function () {
              x += 1;
              return x;
          }
      };  // 返回一个匿名对象
  }

  var c1 = create_counter();
  c1.inc(); // 1
  c1.inc(); // 2
  c1.inc(); // 3
```
>在返回的对象中，实现了一个闭包，该闭包携带了局部变量 x，并且，从外部代码根本无法访问到变量 x。换句话说，**闭包就是携带状态的函数**，并且它的状态可以完全对外隐藏起来。

闭包还可以把多参数的函数变成单参数的函数。例如，要计算 x^y 可以用 Math.pow(x, y) 函数，不过考虑到经常计算 x^2或x^3，我们可以利用闭包创建新的函数 pow2 和 pow3 ：
```javascript
  function make_pow(n) {
      return function (x) {
          return Math.pow(x, n);
      }
  }

  // 创建两个新函数:
  var pow2 = make_pow(2);
  var pow3 = make_pow(3);

  pow2(5); // 25
  pow3(7); // 343
```
