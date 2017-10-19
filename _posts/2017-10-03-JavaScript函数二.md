---
layout: post
title: JavaScript函数二
categories: [JavaScript学习]
tags: JavaScript
---

## 方法
&emsp;&emsp;在一个对象中绑定函数，称为这个对象的方法。例如：
```javascript
  var xiaoming = {
      name: '小明',
      birth: 1990,
      age: function () {
          var y = new Date().getFullYear();
          return y - this.birth;
      }
  };

  xiaoming.age; // function xiaoming.age()
  xiaoming.age(); // 今年调用是27,明年调用就变成28了
```
>在一个方法内部，this 是一个特殊变量，它始终指向当前对象，也就是 xiaoming 这个变量。所以，this.birth 可以拿到 xiaoming 的 birth 属性。

&emsp;&emsp;也可以把函数和对象拆开写：
```javascript
  function getAge() {
      var y = new Date().getFullYear();
      return y - this.birth;
  }

  var xiaoming = {
      name: '小明',
      birth: 1990,
      age: getAge
};

xiaoming.age(); // 25, 正常结果
getAge(); // NaN
```
>J如果以对象的方法形式调用，比如 xiaoming.age()，该函数的 this 指向被调用的对象，也就是 xiaoming，这是符合我们预期的；如果单独调用函数，比如 getAge()，此时，该函数的 this 指向全局对象，也就是 window。由于这是一个设计错误，ECMA 决定，在 strict 模式下让函数的 this 指向 undefined。

&emsp;&emsp;要指定函数的 this 指向哪个对象，可以用函数本身的 apply 方法，它接收两个参数，第一个参数就是需要绑定的 this 变量，第二个参数是 Array，表示函数本身的参数：
```javascript
  function getAge() {
      var y = new Date().getFullYear();
      return y - this.birth;
  }

  var xiaoming = {
      name: '小明',
      birth: 1990,
      age: getAge
  };

  xiaoming.age(); // 25
  getAge.apply(xiaoming, []); // 25, this 指向 xiaoming, 参数为空
```

&emsp;&emsp;另一个与 apply() 类似的方法是 call()，唯一区别是：apply() 把参数打包成 Array 再传入，call() 把参数按顺序传入。比如调用 Math.max(3, 5, 4)，分别用 apply() 和 call() 实现如下：
```javascript
  Math.max.apply(null, [3, 5, 4]); // 5
  Math.max.call(null, 3, 5, 4); // 5
```
>对普通函数调用，我们通常把this绑定为null。

&emsp;&emsp;利用 apply()，我们还可以动态改变函数的行为。JavaScript 的所有对象都是动态的，即使内置的函数，我们也可以重新指向新的函数。

&emsp;&emsp;现在假定我们想统计一下代码一共调用了多少次 parseInt()，可以把所有的调用都找出来，然后手动加上 count += 1，不过这样做太傻了。最佳方案是用我们自己的函数替换掉默认的 parseInt()：
```javascript
  var count = 0;
  var oldParseInt = parseInt; // 保存原函数

  window.parseInt = function () {
      count += 1;
      return oldParseInt.apply(null, arguments); // 调用原函数
  };

  // 测试:
  parseInt('10');
  parseInt('20');
  parseInt('30');
  count; // 3
```




