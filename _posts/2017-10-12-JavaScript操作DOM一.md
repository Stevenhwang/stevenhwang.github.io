---
layout: post
title: JavaScript操作DOM一
categories: [JavaScript学习]
tags: JavaScript
---

## 浏览器对象

### window
&emsp;&emsp;window 对象不但充当全局作用域，而且表示浏览器窗口。window 对象有 innerWidth 和 innerHeight 属性，可以获取浏览器窗口的内部宽度和高度(内部宽高是指除去菜单栏、工具栏、边框等占位元素后，用于显示网页的净宽高)。对应的，还有一个 outerWidth 和 outerHeight 属性，可以获取浏览器窗口的整个宽高。

### navigator
&emsp;&emsp;navigator 对象表示浏览器的信息，最常用的属性包括：
* navigator.appName：浏览器名称；
* navigator.appVersion：浏览器版本；
* navigator.language：浏览器设置的语言；
* navigator.platform：操作系统类型；
* navigator.userAgent：浏览器设定的User-Agent字符串。

### screen
&emsp;&emsp;screen 对象表示屏幕的信息，常用的属性有：
* screen.width：屏幕宽度，以像素为单位；
* screen.height：屏幕高度，以像素为单位；
* screen.colorDepth：返回颜色位数，如8、16、24。

### location
&emsp;&emsp;location 对象表示当前页面的 URL 信息。例如，一个完整的 URL：http://www.example.com:8080/path/index.html?a=1&b=2#TOP 。它可以用 location.href 获取。要获得 URL 各个部分的值，可以这么写：
```javascript
  location.protocol; // 'http'
  location.host; // 'www.example.com'
  location.port; // '8080'
  location.pathname; // '/path/index.html'
  location.search; // '?a=1&b=2'
  location.hash; // 'TOP'
```
&emsp;&emsp;要加载一个新页面，可以调用 location.assign()。如果要重新加载当前页面，调用 location.reload()方法非常方便。

### document
&emsp;&emsp;document 对象表示当前页面。由于 HTML 在浏览器中以 DOM 形式表示为树形结构，document 对象就是整个 DOM 树的根节点。比如 document 的 title 属性是从 HTML 文档中的 <title>xxx</title> 读取的，而且可以动态改变。要查找 DOM 树的某个节点，需要从 document 对象开始查找。最常用的查找是根据 ID 和 Tag Name，用 document 对象提供的 getElementById() 和 getElementsByTagName() 可以按ID获得一个 DOM 节点和按 Tag 名称获得一组 DOM 节点。

&emsp;&emsp;document 对象还有一个 cookie 属性，可以获取当前页面的 cookie。cookie 是由服务器发送的 key-value 标示符。因为 HTTP 协议是无状态的，但是服务器要区分到底是哪个用户发过来的请求，就可以用 cookie 来区分。当一个用户成功登录后，服务器发送一个 cookie 给浏览器，例如 user=ABC123XYZ(加密的字符串)...，此后，浏览器访问该网站时，会在请求头附上这个 cookie，服务器根据 cookie 即可区分出用户。cookie 还可以存储网站的一些设置，例如，页面显示的语言等等。由于 JavaScript 能读取到页面的 cookie，而用户的登录信息通常也存在 cookie 中，这就造成了巨大的安全隐患。为了解决这个问题，服务器在设置 cookie 时可以使用 httpOnly，设定了 httpOnly 的 cookie 将不能被 JavaScript 读取。这个行为由浏览器实现，主流浏览器均支持 httpOnly 选项，IE 从 IE6 SP1 开始支持。为了确保安全，服务器端在设置 cookie时，应该始终坚持使用 httpOnly。

### history
&emsp;&emsp;history 对象保存了浏览器的历史记录，JavaScript 可以调用 history 对象的 back() 或 forward ()，相当于用户点击了浏览器的“后退”或“前进”按钮。这个对象属于历史遗留对象，对于现代 Web 页面来说，由于大量使用 AJAX 和页面交互，简单粗暴地调用 history.back() 可能会让用户感到非常愤怒。新手开始设计 Web 页面时喜欢在登录页登录成功时调用 history.back()，试图回到登录前的页面。这是一种错误的方法。任何情况，你都不应该使用 history 这个对象了。

## 操作DOM
&emsp;&emsp;由于 HTML 文档被浏览器解析后就是一棵 DOM 树，要改变 HTML 的结构，就需要通过 JavaScript 来操作 DOM。操作一个 DOM 节点实际上就是这么几个操作：
* 更新：更新该 DOM 节点的内容，相当于更新了该 DOM 节点表示的 HTML 的内容；
* 遍历：遍历该 DOM 节点下的子节点，以便进行进一步操作；
* 添加：在该 DOM 节点下新增一个子节点，相当于动态增加了一个 HTML 节点；
* 删除：将该节点从 HTML 中删除，相当于删掉了该 DOM 节点的内容以及它包含的所有子节点。

&emsp;&emsp;在操作一个 DOM 节点前，我们需要通过各种方式先拿到这个 DOM 节点。最常用的方法是 document.getElementById() 和 document.getElementsByTagName()，以及 CSS 选择器 document.getElementsByClassName()。

&emsp;&emsp;由于 ID 在 HTML 文档中是唯一的，所以 document.getElementById() 可以直接定位唯一的一个 DOM 节点。document.getElementsByTagName() 和 document.getElementsByClassName() 总是返回一组 DOM 节点。要精确地选择 DOM，可以先定位父节点，再从父节点开始选择，以缩小范围。例如：
```javascript
  // 返回 ID 为 'test' 的节点：
  var test = document.getElementById('test');
  // 先定位 ID 为 'test-table' 的节点，再返回其内部所有 tr 节点：
  var trs = document.getElementById('test-table').getElementsByTagName('tr');
  // 先定位 ID 为 'test-div' 的节点，再返回其内部所有 class 包含 red 的节点：
  var reds = document.getElementById('test-div').getElementsByClassName('red');
  // 获取节点 test 下的所有直属子节点:
  var cs = test.children;
  // 获取节点 test 下第一个、最后一个子节点：
  var first = test.firstElementChild;
  var last = test.lastElementChild;
```
&emsp;&emsp;第二种方法是使用 querySelector() 和 querySelectorAll()，需要了解 selector 语法，然后使用条件来获取节点，更加方便：
```javascript
  // 通过 querySelector 获取 ID 为 q1 的节点：
  var q1 = document.querySelector('#q1');
  // 通过 querySelectorAll 获取 q1 节点内的符合条件的所有节点：
  var ps = q1.querySelectorAll('div.highlighted > p');
```

&emsp;&emsp;严格地讲，我们这里的 DOM 节点是指 Element，但是 DOM 节点实际上是 Node，在 HTML 中，Node 包括 Element、Comment、CDATA_SECTION 等很多种，以及根节点 Document 类型，但是，绝大多数时候我们只关心 Element，也就是实际控制页面结构的 Node，其他类型的 Node 忽略即可。根节点 Document 已经自动绑定为全局变量 document。

### 删除DOM
删除一个 DOM 节点是操作 DOM 中最简单的，要删除一个节点，首先要获得该节点本身以及它的父节点，然后，调用父节点的 removeChild 把自己删掉：
```javascript
  // 拿到待删除节点:
  var self = document.getElementById('to-be-removed');
  // 拿到父节点:
  var parent = self.parentElement;
  // 删除:
  var removed = parent.removeChild(self);
  removed === self; // true
```
&emsp;&emsp;注意到删除后的节点虽然不在文档树中了，但其实它还在内存中，可以随时再次被添加到别的位置。当你遍历一个父节点的子节点并进行删除操作时，要注意，children 属性是一个只读属性，并且它在子节点变化时会实时更新。例如，对于如下 HTML 结构：
```html
  <div id="parent">
      <p>First</p>
      <p>Second</p>
  </div>
```
&emsp;&emsp;当我们用如下代码删除子节点时：
```javascript
  var parent = document.getElementById('parent');
  parent.removeChild(parent.children[0]);
  parent.removeChild(parent.children[1]); // <-- 浏览器报错
```
&emsp;&emsp;浏览器报错：parent.children[1] 不是一个有效的节点。原因就在于，当 `<p>First</p>` 节点被删除后，parent.children 的节点数量已经从2变为了1，索引[1]已经不存在了。因此，删除多个节点时，要注意 children 属性时刻都在变化。
