---
layout: post
title: JavaScript面向对象编程二
categories: [JavaScript学习]
tags: JavaScript
---

## 原型继承
在传统的基于 Class 的语言如 Java、C++ 中，继承的本质是扩展一个已有的 Class，并生成新的 Subclass。由于这类语言严格区分类和实例，继承实际上是类型的扩展。但是，JavaScript 由于采用原型继承，我们无法直接扩展一个 Class，因为根本不存在 Class 这种类型。

但是办法还是有的。我们先回顾 Student 构造函数：
```javascript
  function Student(props) {
      this.name = props.name || 'Unnamed';
  }

  Student.prototype.hello = function () {
      alert('Hello, ' + this.name + '!');
  }
```
现在，我们要基于 Student 扩展出 PrimaryStudent，可以先定义出 PrimaryStudent：
```javascript
  function PrimaryStudent(props) {
      // 调用 Student 构造函数，绑定 this 变量:
      Student.call(this, props);
      this.grade = props.grade || 1;
  }
```
但是，调用了 Student 构造函数不等于继承了 Student，PrimaryStudent 创建的对象的原型链是：new PrimaryStudent() ----> PrimaryStudent.prototype ----> Object.prototype ----> null，必须想办法把原型链修改为：new PrimaryStudent() ----> PrimaryStudent.prototype ----> Student.prototype ----> Object.prototype ----> null。这样，原型链对了，继承关系就对了。新的基于 PrimaryStudent 创建的对象不但能调用 PrimaryStudent.prototype 定义的方法，也可以调用 Student.prototype 定义的方法。

我们必须借助一个中间对象来实现正确的原型链，这个中间对象的原型要指向 Student.prototype。为了实现这一点，参考道爷（就是发明 JSON 的那个道格拉斯）的代码，中间对象可以用一个空函数 F 来实现：
```javascript
  // PrimaryStudent 构造函数:
  function PrimaryStudent(props) {
      Student.call(this, props);
      this.grade = props.grade || 1;
  }

  // 空函数 F:
  function F() {
  }

  // 把F的原型指向 Student.prototype:
  F.prototype = Student.prototype;

  // 把 PrimaryStudent 的原型指向一个新的F对象，F 对象的原型正好指向 Student.prototype:
  PrimaryStudent.prototype = new F();

  // 把 PrimaryStudent 原型的构造函数修复为 PrimaryStudent:
  PrimaryStudent.prototype.constructor = PrimaryStudent;

  // 继续在 PrimaryStudent 原型（就是 new F() 对象）上定义方法：
  PrimaryStudent.prototype.getGrade = function () {
      return this.grade;
  };

  // 创建 xiaoming:
  var xiaoming = new PrimaryStudent({
      name: '小明',
      grade: 2
  });
  xiaoming.name; // '小明'
  xiaoming.grade; // 2

  // 验证原型:
  xiaoming.__proto__ === PrimaryStudent.prototype; // true
  xiaoming.__proto__.__proto__ === Student.prototype; // true

  // 验证继承关系:
  xiaoming instanceof PrimaryStudent; // true
  xiaoming instanceof Student; // true
```

## class继承
在前面我们看到了 JavaScript 的对象模型是基于原型实现的，缺点是继承的实现需要编写大量代码，并且需要正确实现原型链。有没有更简单的写法？有！新的关键字 class 从 ES6 开始正式被引入到 JavaScript 中，class 的目的就是让定义类更简单。如果用新的 class 关键字来编写前面的 Student，可以这样写：
```javascript
class Student {
    constructor(name) {
        this.name = name;
    }

    hello() {
        alert('Hello, ' + this.name + '!');
    }
}
```
比较一下就可以发现，class 的定义包含了构造函数 constructor 和定义在原型对象上的函数 hello()（注意没有 function 关键字），这样就避免了 Student.prototype.hello = function () {...}这样分散的代码。

用 class 定义对象的另一个巨大的好处是继承更方便了。想一想我们从 Student 派生一个 PrimaryStudent 需要编写的代码量。现在，原型继承的中间对象，原型对象的构造函数等等都不需要考虑了，直接通过 extends 来实现：
```javascript
  class PrimaryStudent extends Student {
      constructor(name, grade) {
          super(name); // 记得用 super 调用父类的构造方法!
          this.grade = grade;
      }

      myGrade() {
          alert('I am at grade ' + this.grade);
      }
  }
```
注意 PrimaryStudent 的定义也是 class 关键字实现的，而 extends 则表示原型链对象来自 Student。子类的构造函数可能会与父类不太相同，例如，PrimaryStudent 需要 name 和 grade 两个参数，并且需要通过 super(name) 来调用父类的构造函数，否则父类的 name 属性无法正常初始化。PrimaryStudent 已经自动获得了父类 Student 的 hello 方法，我们又在子类中定义了新的 myGrade 方法。

ES6 引入的 class 和原有的 JavaScript 原型继承有什么区别呢？实际上它们没有任何区别，class 的作用就是让 JavaScript 引擎去实现原来需要我们自己编写的原型链代码。简而言之，用 class 的好处就是极大地简化了原型链代码。但是现在用还早了点，因为不是所有的主流浏览器都支持 ES6 的 class。如果一定要现在就用上，就需要一个工具把 class 代码转换为传统的 prototype 代码，可以试试 Babel 这个工具。
