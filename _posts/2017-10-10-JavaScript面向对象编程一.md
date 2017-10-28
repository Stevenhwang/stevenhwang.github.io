---
layout: post
title: JavaScript面向对象编程一
categories: [JavaScript学习]
tags: JavaScript
---

## 原型prototype
在 JavaScript 中没有类和实例的概念，而是通过原型（prototype）来实现面向对象编程。例如：
```javascript
  var Student = {
      name: 'Robot',
      height: 1.2,
      run: function () {
          console.log(this.name + ' is running ...');
      }
  };

  var xiaoming = {
      name: '小明'
  };

  xiaoming.__proto__ = Student; // 这一行代码把 xiaoming 的原型指向了对象 Student

  xiaoming.name; // '小明'
  xiaoming.run(); // 小明 is running ...
```
JavaScript 的原型链和 Java 的 Class 区别就在，它没有“Class”的概念，所有对象都是实例，所谓继承关系不过是把一个对象的原型指向另一个对象而已。

在编写 JavaScript 代码时，不要直接用 `obj.__proto__` 去改变一个对象的原型，并且，低版本的IE也无法使用`__proto__`。`Object.create()`方法可以传入一个原型对象，并创建一个基于该原型的新对象，但是新对象什么属性都没有，因此，我们可以编写一个函数来创建 xiaoming：
```javascript
// 原型对象:
  var Student = {
      name: 'Robot',
      height: 1.2,
      run: function () {
          console.log(this.name + ' is running...');
      }
  };

  function createStudent(name) {
      // 基于Student原型创建一个新对象:
      var s = Object.create(Student);
      // 初始化新对象:
      s.name = name;
      return s;
  }

  var xiaoming = createStudent('小明');
  xiaoming.run(); // 小明 is running...
  xiaoming.__proto__ === Student; // true
```

## 创建对象
JavaScript 对每个创建的对象都会设置一个原型，指向它的原型对象。当我们用 obj.xxx 访问一个对象的属性时，JavaScript 引擎先在当前对象上查找该属性，如果没有找到，就到其原型对象上找，如果还没有找到，就一直上溯到 Object.prototype 对象，最后，如果还没有找到，就只能返回 undefined。

例如，创建一个 Array 对象：
```javascript
  var arr = [1, 2, 3];
```
其原型链是：arr ----> Array.prototype ----> Object.prototype ----> null 。Array.prototype 定义了 indexOf()、shift() 等方法，因此你可以在所有的 Array 对象上直接调用这些方法。

## 构造函数
除了直接用 { ... } 创建一个对象外，JavaScript 还可以用一种构造函数的方法来创建对象。它的用法是，先定义一个构造函数：
```javascript
  function Student(name) {
      this.name = name;
      this.hello = function () {
          alert('Hello, ' + this.name + '!');
      }
  }
```
这确实是一个普通函数，但是在 JavaScript 中，可以用关键字 new 来调用这个函数，并返回一个对象：
```javascript
  var xiaoming = new Student('小明');
  xiaoming.name; // '小明'
  xiaoming.hello(); // Hello, 小明!
```
**注意**，如果不写 new，这就是一个普通函数，它返回 undefined。但是，如果写了 new，它就变成了一个构造函数，它绑定的 this 指向新创建的对象，并默认返回 this，也就是说，不需要在最后写 return this;。

新创建的 xiaoming 的原型链是：xiaoming ----> Student.prototype ----> Object.prototype ----> null 。也就是说，xiaoming 的原型指向函数 Student 的原型。如果你又创建了 xiaohong、xiaojun，那么这些对象的原型与 xiaoming 是一样的。

不过还有一个小问题，注意观察：
```javascript
  xiaoming.name; // '小明'
  xiaohong.name; // '小红'
  xiaoming.hello; // function: Student.hello()
  xiaohong.hello; // function: Student.hello()
  xiaoming.hello === xiaohong.hello; // false
```
xiaoming 和 xiaohong 各自的 name 不同，这是对的，否则我们无法区分谁是谁了。xiaoming 和 xiaohong 各自的 hello 是一个函数，但它们是两个不同的函数，虽然函数名称和代码都是相同的！如果我们通过 new Student() 创建了很多对象，这些对象的 hello 函数实际上只需要共享同一个函数就可以了，这样可以节省很多内存。

要让创建的对象共享一个 hello 函数，根据对象的属性查找原则，我们只要把 hello 函数移动到 xiaoming、xiaohong 这些对象共同的原型上就可以了，也就是 Student.prototype。修改代码如下：
```javascript
  function Student(name) {
      this.name = name;
  }

  Student.prototype.hello = function () {
      alert('Hello, ' + this.name + '!');
  };
```
