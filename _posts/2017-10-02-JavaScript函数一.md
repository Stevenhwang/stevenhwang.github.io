---
layout: post
title: JavaScript函数一
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
>function 指出这是一个函数定义，abs 是函数的名称，(x) 括号内列出函数的参数，多个参数以 , 分隔。{ ... } 之间的代码是函数体，可以包含若干语句，甚至可以没有任何语句。如果没有 return 语句，函数执行完毕后也会返回结果，只是结果为 undefined。

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
>在这种方式下，function (x) { ... } 是一个匿名函数，它没有函数名。但是，这个匿名函数赋值给了变量 abs ，所以，通过变量 abs 就可以调用该函数。上述两种定义完全等价，注意第二种方式按照完整语法需要在函数体末尾加一个 ; ，表示赋值语句结束。

## 调用函数
&emsp;&emsp;调用函数时，按顺序传入参数即可。由于 JavaScript **允许传入任意个参数而不影响调用**，因此传入的参数比定义的参数多和少都没有问题。要避免参数收到 undefined，可以对参数进行检查：
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

## 变量作用域
&emsp;&emsp;在 JavaScript 中，用 var 申明的变量是有作用域的。如果一个变量在函数体内部声明，则该变量的作用域为整个函数体，在函数体外不可引用该变量。不同函数内部的同名变量互相独立，互不影响。由于 JavaScript 的函数可以嵌套，此时，内部函数可以访问外部函数定义的变量，反过来则不行。JavaScript 的函数在查找变量时从自身函数定义开始，从“内”向“外”查找。如果内部函数定义了与外部函数重名的变量，则内部函数的变量将“屏蔽”外部函数的变量。

* JavaScript 的函数定义有个特点，它会先扫描整个函数体的语句，把所有申明的变量“提升”到函数顶部:
```javascript
  function foo() {
      var x = 'Hello, ' + y;
      alert(x);
      var y = 'Bob';
  }

  foo();  // Hello, undefined
```
>上面代码运行并不报错，原因是变量 y 在稍后申明了。这正是因为 JavaScript 引擎自动 **提升了变量 y 的声明，但不会提升变量 y 的赋值**。由于 JavaScript 的这一怪异的“特性”，我们在函数内部定义变量时，请严格遵守“在函数内部首先申明所有变量”这一规则。

&emsp;&emsp;不在任何函数内定义的变量就具有全局作用域。实际上，JavaScript 默认有一个全局对象 window ，全局作用域的变量实际上被绑定到 window 的一个属性。由于函数定义有两种方式，以变量方式 var foo = function () {} 定义的函数实际上也是一个全局变量，因此，顶层函数的定义也被视为一个全局变量，并绑定到 window 对象。

&emsp;&emsp;全局变量会绑定到 window 上，不同的 JavaScript 文件如果使用了相同的全局变量，或者定义了相同名字的顶层函数，都会造成命名冲突，并且很难被发现。减少冲突的一个方法是把自己的所有变量和函数全部绑定到一个全局变量中，也就是使用命名空间。

&emsp;&emsp;由于 JavaScript 的变量作用域实际上是函数内部，我们在 for 循环等语句块中是无法定义具有局部作用域的变量的。为了解决块级作用域，ES6 引入了新的关键字 let，用 let 替代 var 可以申明一个块级作用域的变量。

&emsp;&emsp;由于 var 和 let 申明的是变量，如果要申明一个常量，在 ES6 之前是不行的，我们通常用全部大写的变量来表示“这是一个常量，不要修改它的值”。ES6标准引入了新的关键字 const 来定义常量，const 与 let 都具有块级作用域。
