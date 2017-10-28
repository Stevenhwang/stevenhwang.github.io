---
layout: post
title: JavaScript操作DOM二
categories: [JavaScript学习]
tags: JavaScript
---

## 更新DOM
拿到一个 DOM 节点后，我们可以对它进行更新。可以直接修改节点的文本，方法有两种。

一种是修改 innerHTML 属性，这个方式非常强大，不但可以修改一个 DOM 节点的文本内容，还可以直接通过 HTML 片段修改 DOM 节点内部的子树：
```javascript
  // 获取 <p id="p-id">...</p>
  var p = document.getElementById('p-id');
  // 设置文本为 abc:
  p.innerHTML = 'ABC'; // <p id="p-id">ABC</p>
  // 设置 HTML:
  p.innerHTML = 'ABC <span style="color:red">RED</span> XYZ';
  // <p>...</p> 的内部结构已修改
```
>用 innerHTML 时要注意，是否需要写入 HTML。如果写入的字符串是通过网络拿到了，要注意对字符编码来避免 XSS 攻击。

第二种是修改 innerText 或 textContent 属性，这样可以自动对字符串进行 HTML 编码，保证无法设置任何 HTML 标签：
```javascript
  // 获取 <p id="p-id">...</p>
  var p = document.getElementById('p-id');
  // 设置文本:
  p.innerText = '<script>alert("Hi")</script>';
  // HTML被自动编码，无法设置一个<script>节点:
  // <p id="p-id">&lt;script&gt;alert("Hi")&lt;/script&gt;</p>
```
>innerText 和 textContent 的区别在于读取属性时，innerText 不返回隐藏元素的文本，而 textContent 返回所有文本。另外注意 IE<9 不支持 textContent。

修改 CSS 也是经常需要的操作。DOM 节点的 style 属性对应所有的 CSS，可以直接获取或设置。因为 CSS 允许 font-size 这样的名称，但它并非 JavaScript 有效的属性名，所以需要在 JavaScript 中改写为驼峰式命名 fontSize：
```javascript
  // 获取 <p id="p-id">...</p>
  var p = document.getElementById('p-id');
  // 设置 CSS:
  p.style.color = '#ff0000';
  p.style.fontSize = '20px';
  p.style.paddingTop = '2em';
```

## 插入DOM
当我们获得了某个 DOM 节点，想在这个 DOM 节点内插入新的 DOM，应该如何做？如果这个 DOM 节点是空的，例如，`<div></div>`，那么，直接使用 `innerHTML = '<span>child</span>'`就可以修改 DOM 节点的内容，相当于“插入”了新的 DOM节点。如果这个 DOM 节点不是空的，那就不能这么做，因为 innerHTML 会直接替换掉原来的所有子节点。

有两个办法可以插入新的节点。一个是使用 appendChild，把一个子节点添加到父节点的最后一个子节点。例如有如下 html 结构：
```html
  <!-- HTML结构 -->
  <p id="js">JavaScript</p>
  <div id="list">
      <p id="java">Java</p>
      <p id="python">Python</p>
      <p id="scheme">Scheme</p>
  </div>
```
要把 `<p id="js">JavaScript</p>` 添加到 `<div id="list">` 的最后一项：
```javascript
  var
      js = document.getElementById('js'),
      list = document.getElementById('list');
  list.appendChild(js);
```
现在，HTML 结构变成了这样：
```html
  <!-- HTML结构 -->
  <div id="list">
      <p id="java">Java</p>
      <p id="python">Python</p>
      <p id="scheme">Scheme</p>
      <p id="js">JavaScript</p>
  </div>
```
因为我们插入的 js 节点已经存在于当前的文档树，因此这个节点首先会从原先的位置删除，再插入到新的位置。然而，更多的时候我们会从零创建一个新的节点，然后插入到指定位置，那么可以这样做：
```javascript
  var
      list = document.getElementById('list'),
      haskell = document.createElement('p');
  haskell.id = 'haskell';
  haskell.innerText = 'Haskell';
  list.appendChild(haskell);
```
这样我们就动态添加了一个新的节点：
```html
  <!-- HTML结构 -->
  <div id="list">
      <p id="java">Java</p>
      <p id="python">Python</p>
      <p id="scheme">Scheme</p>
      <p id="haskell">Haskell</p>
  </div>
```
如果我们要把子节点插入到指定的位置怎么办？可以使用 parentElement.insertBefore(newElement, referenceElement);，子节点会插入到 referenceElement 之前。还是以上面的 HTML 为例，假定我们要把 Haskell 插入到 Python 之前：
```javascript
  var
      list = document.getElementById('list'),
      ref = document.getElementById('python'),
      haskell = document.createElement('p');
  haskell.id = 'haskell';
  haskell.innerText = 'Haskell';
  list.insertBefore(haskell, ref);
```
