---
layout: post
title: JavaScript错误处理
categories: [JavaScript学习]
tags: JavaScript
---

## 错误处理
在执行 JavaScript 代码的时候，有些情况下会发生错误。

错误分两种，一种是程序写的逻辑不对，导致代码执行异常。对于这种错误，要修复程序。一种是执行过程中，程序可能遇到无法预测的异常情况而报错，例如，网络连接中断，读取不存在的文件，没有操作权限等。对于这种错误，我们需要处理它，并可能需要给用户反馈。

## try ... catch ... finally
当代码块被 try { ... } 包裹的时候，就表示这部分代码执行过程中可能会发生错误，一旦发生错误，就不再继续执行后续代码，转而跳到 catch 块。catch (e) { ... } 包裹的代码就是错误处理代码，变量 e 表示捕获到的错误。最后，无论有没有错误，finally 一定会被执行。最后请注意，catch 和 finally 可以不必都出现。

## 错误类型
JavaScript 有一个标准的 Error 对象表示错误，还有从 Error 派生的 TypeError、ReferenceError 等错误对象。我们在处理错误时，可以通过 catch(e) 捕获的变量 e 访问错误对象。使用变量 e 是一个习惯用法，也可以以其他变量名命名，如 catch(ex)。
```javascript
  try {
      ...
  } catch (e) {
      if (e instanceof TypeError) {
          alert('Type error!');
      } else if (e instanceof Error) {
          alert(e.message);
      } else {
          alert('Error: ' + e);
      }
  }
```

## 抛出错误
程序也可以主动抛出一个错误，让执行流程直接跳转到 catch 块，抛出错误使用 throw 语句。例如，下面的代码让用户输入一个数字，程序接收到的实际上是一个字符串，然后用 parseInt() 转换为整数。当用户输入不合法的时候，我们就抛出错误：
```javascript
  var r, n, s;
  try {
      s = prompt('请输入一个数字');
      n = parseInt(s);
      if (isNaN(n)) {
          throw new Error('输入错误');
      }
      // 计算平方:
      r = n * n;
      alert(n + ' * ' + n + ' = ' + r);
  } catch (e) {
      alert('出错了：' + e);
  }
```
>实际上，JavaScript 允许抛出任意对象，包括数字、字符串。但是，最好还是抛出一个 Error 对象。

## 异步错误处理
编写 JavaScript 代码时，我们要时刻牢记，JavaScript 引擎是一个事件驱动的执行引擎，代码总是以单线程执行，而回调函数的执行需要等到下一个满足条件的事件出现后，才会被执行。

例如，setTimeout() 函数可以传入回调函数，并在指定若干毫秒后执行：
```javascript
  function printTime() {
      console.log('It is time!');
  }

  setTimeout(printTime, 1000);
  console.log('done');
```
>上面的代码会先打印 done，1秒后才会打印 It is time!。

如果 printTime() 函数内部发生了错误，我们试图用 try 包裹 setTimeout() 是无效的：
```javascript
  function printTime() {
      throw new Error();
  }

  try {
      setTimeout(printTime, 1000);
      console.log('done');
  } catch (e) {
      alert('error');
  }
```
原因就在于调用 setTimeout() 函数时，传入的 printTime 函数并未立刻执行！紧接着，JavaScript 引擎会继续执行 console.log('done'); 语句，而此时并没有错误发生。直到1秒钟后，执行 printTime 函数时才发生错误，但此时除了在 printTime 函数内部捕获错误外，外层代码并无法捕获。

所以，涉及到异步代码，无法在调用时捕获，原因就是在捕获的当时，回调函数并未执行。

类似的，当我们处理一个事件时，在绑定事件的代码处，无法捕获事件处理函数的错误。
