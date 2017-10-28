---
layout: post
title: JavaScript使用Promise
categories: [JavaScript学习]
tags: JavaScript
---

## Promise
在 JavaScript 的世界中，所有代码都是单线程执行的。由于这个“缺陷”，导致 JavaScript 的所有网络操作，浏览器事件，都必须是异步执行。异步执行可以用 **回调函数** 实现：
```javascript
  function callback() {
      console.log('Done');
  }
  console.log('before setTimeout()');
  setTimeout(callback, 1000); // 1秒钟后调用callback函数
  console.log('after setTimeout()');
```
观察上述代码执行，在 Chrome 的控制台输出可以看到：
```text
  before setTimeout()
  after setTimeout()
  (等待1秒后)
  Done
```
可见，异步操作会在将来的某个时间点触发一个函数调用。

承诺将来会执行的对象在 JavaScript 中被称为 Promise 对象。我们先看一个最简单的 Promise 例子：生成一个0-2之间的随机数，如果小于1，则等待一段时间后返回成功，否则返回失败：
```javascript
  function test(resolve, reject) {
      var timeOut = Math.random() * 2;
      log('set timeout to: ' + timeOut + ' seconds.');
      setTimeout(function () {
          if (timeOut < 1) {
              log('call resolve()...');
              resolve('200 OK');
          }
          else {
              log('call reject()...');
              reject('timeout in ' + timeOut + ' seconds.');
          }
      }, timeOut * 1000);
  }
```
这个 test() 函数有两个参数，这两个参数都是函数，如果执行成功，我们将调用 resolve('200 OK')，如果执行失败，我们将调用 reject('timeout in ' + timeOut + ' seconds.')。可以看出，test() 函数只关心自身的逻辑，并不关心具体的 resolve 和 reject 将如何处理结果。有了执行函数，我们就可以用一个 Promise 对象来执行它，并在将来某个时刻获得成功或失败的结果：
```javascript
  var p1 = new Promise(test);
  var p2 = p1.then(function (result) {
      console.log('成功：' + result);
  });
  var p3 = p2.catch(function (reason) {
      console.log('失败：' + reason);
  });
```
Promise 对象可以串联起来，所以上述代码可以简化为：
```javascript
  new Promise(test).then(function (result) {
      console.log('成功：' + result);
  }).catch(function (reason) {
      console.log('失败：' + reason);
  });
```
可见 Promise 最大的好处是在异步执行的流程中，把执行代码和处理结果的代码清晰地分离了。

Promise 还可以做更多的事情，比如，有若干个异步任务，需要先做任务1，如果成功后再做任务2，任何任务失败则不再继续并执行错误处理函数。要串行执行这样的异步任务，不用 Promise 需要写一层一层的嵌套代码。有了 Promise，我们只需要简单地写：
```javascript
  job1.then(job2).then(job3).catch(handleError);
```

除了串行执行若干异步任务外，Promise 还可以并行执行异步任务。试想一个页面聊天系统，我们需要从两个不同的 URL 分别获得用户的个人信息和好友列表，这两个任务是可以并行执行的，用 Promise.all() 实现如下：
```javascript
  var p1 = new Promise(function (resolve, reject) {
      setTimeout(resolve, 500, 'P1');
  });
  var p2 = new Promise(function (resolve, reject) {
      setTimeout(resolve, 600, 'P2');
  });
  // 同时执行p1和p2，并在它们都完成后执行then:
  Promise.all([p1, p2]).then(function (results) {
      console.log(results); // 获得一个Array: ['P1', 'P2']
  });
```

有些时候，多个异步任务是为了容错。比如，同时向两个 URL 读取用户的个人信息，只需要获得先返回的结果即可。这种情况下，用 Promise.race() 实现：
```javascript
  var p1 = new Promise(function (resolve, reject) {
      setTimeout(resolve, 500, 'P1');
  });
  var p2 = new Promise(function (resolve, reject) {
      setTimeout(resolve, 600, 'P2');
  });
  Promise.race([p1, p2]).then(function (result) {
      console.log(result); // 'P1'
  });
```
由于 p1 执行较快，Promise 的then() 将获得结果'P1'。p2 仍在继续执行，但执行结果将被丢弃。

如果我们组合使用 Promise，就可以把很多异步任务以并行和串行的方式组合起来执行。
