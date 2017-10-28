---
layout: post
title: JavaScript异步请求
categories: [JavaScript学习]
tags: JavaScript
---

## AJAX
AJAX 不是 JavaScript 的规范，它只是一个哥们“发明”的缩写：Asynchronous JavaScript and XML，意思就是用 JavaScript 执行异步网络请求。

如果仔细观察一个 Form 的提交，你就会发现，一旦用户点击“Submit”按钮，表单开始提交，浏览器就会刷新页面，然后在新页面里告诉你操作是成功了还是失败了。如果不幸由于网络太慢或者其他原因，就会得到一个404页面。

这就是 Web 的运作原理：一次 HTTP 请求对应一个页面。

如果要让用户留在当前页面中，同时发出新的 HTTP 请求，就必须用 JavaScript 发送这个新请求，接收到数据后，再用 JavaScript 更新页面，这样一来，用户就感觉自己仍然停留在当前页面，但是数据却可以不断地更新。

用 JavaScript 写一个完整的 AJAX 代码并不复杂，但是需要注意：AJAX 请求是异步执行的，也就是说，要通过回调函数获得响应。

在现代浏览器上写 AJAX 主要依靠 XMLHttpRequest 对象：
```javascript
  function success(text) {
      var textarea = document.getElementById('test-response-text');
      textarea.value = text;
  }

  function fail(code) {
      var textarea = document.getElementById('test-response-text');
      textarea.value = 'Error code: ' + code;
  }

  var request = new XMLHttpRequest(); // 新建XMLHttpRequest对象

  request.onreadystatechange = function () { // 状态发生变化时，函数被回调
      if (request.readyState === 4) { // 成功完成
          // 判断响应结果:
          if (request.status === 200) {
              // 成功，通过responseText拿到响应的文本:
              return success(request.responseText);
          } else {
              // 失败，根据响应码判断失败原因:
              return fail(request.status);
          }
      } else {
          // HTTP请求还在继续...
      }
  }

  // 发送请求:
  request.open('GET', '/api/categories');
  request.send();

  alert('请求已发送，请等待响应...');
```
当创建了 XMLHttpRequest 对象后，要先设置 onreadystatechange 的回调函数。在回调函数中，通常我们只需通过 readyState === 4 判断请求是否完成，如果已完成，再根据 status === 200 判断是否是一个成功的响应。XMLHttpRequest 对象的 open() 方法有3个参数，第一个参数指定是 GET 还是 POST，第二个参数指定 URL 地址，第三个参数指定是否使用异步，默认是 true，所以不用写。注意，千万不要把第三个参数指定为 false，否则浏览器将停止响应，直到 AJAX 请求完成。如果这个请求耗时10秒，那么10秒内你会发现浏览器处于“假死”状态。

最后调用 send() 方法才真正发送请求。GET 请求不需要参数，POST 请求需要把 body 部分以字符串或者 FormData 对象传进去。

## 安全限制
上面代码的 URL 使用的是相对路径。如果你把它改为 'http://www.sina.com.cn/'，再运行，肯定报错。在 Chrome 的控制台里，还可以看到错误信息。这是因为浏览器的同源策略导致的。默认情况下，JavaScript 在发送 AJAX 请求时，URL的域名必须和当前页面完全一致。

完全一致的意思是，域名要相同（www.example.com 和 example.com 不同），协议要相同（http 和 https 不同），端口号要相同（默认是:80端口，它和:8080就不同）。有的浏览器口子松一点，允许端口不同，大多数浏览器都会严格遵守这个限制。

那是不是用 JavaScript 无法请求外域（就是其他网站）的 URL 了呢？方法还是有的，大概有这么几种：
* 一是通过 Flash 插件发送 HTTP 请求，这种方式可以绕过浏览器的安全限制，但必须安装 Flash，并且跟 Flash 交互。不过 Flash 用起来麻烦，而且现在用得也越来越少了。
* 二是通过在同源域名下架设一个代理服务器来转发，JavaScript 负责把请求发送到代理服务器：`'/proxy?url=http://www.sina.com.cn'`，代理服务器再把结果返回，这样就遵守了浏览器的同源策略。这种方式麻烦之处在于需要服务器端额外做开发。
* 第三种方式称为 JSONP，它有个限制，只能用 GET 请求，并且要求返回 JavaScript。这种方式跨域实际上是利用了浏览器允许跨域引用 JavaScript 资源。JSONP 通常以函数调用的形式返回，例如，返回 JavaScript 内容如下：`foo('data');`，这样一来，我们如果在页面中先准备好 foo() 函数，然后给页面动态加一个 `<script>` 节点，相当于动态读取外域的 JavaScript 资源，最后就等着接收回调了。

## CORS
如果浏览器支持 HTML5，那么就可以一劳永逸地使用新的跨域策略：CORS 了。CORS 全称 Cross-Origin Resource Sharing，是 HTML5 规范定义的如何跨域访问资源。

Origin 表示本域，也就是浏览器当前页面的域。当 JavaScript 向外域（如 sina.com）发起请求后，浏览器收到响应后，首先检查 Access-Control-Allow-Origin 是否包含本域，如果是，则此次跨域请求成功，如果不是，则请求失败，JavaScript 将无法获取到响应的任何数据。

假设本域是 my.com，外域是 sina.com，只要响应头 Access-Control-Allow-Origin 为 http://my.com，或者是*，本次请求就可以成功。可见，跨域能否成功，取决于对方服务器是否愿意给你设置一个正确的 Access-Control-Allow-Origin，决定权始终在对方手中。

上面这种跨域请求，称之为“简单请求”。简单请求包括 GET、HEAD 和 POST（POST 的 Content-Type 类型
仅限 application/x-www-form-urlencoded、multipart/form-data 和 text/plain），并且不能出现任何自定义头（例如，X-Custom: 12345），通常能满足90%的需求。

无论你是否需要用 JavaScript 通过 CORS 跨域请求资源，你都要了解 CORS 的原理。最新的浏览器全面支持 HTML5，在引用外域资源时，都要验证 CORS。例如，当你引用了某个第三方 CDN 上的字体文件时：
```css
  /* CSS */
  @font-face {
    font-family: 'FontAwesome';
    src: url('http://cdn.com/fonts/fontawesome.ttf') format('truetype');
  }
```
>如果该 CDN 服务商未正确设置 Access-Control-Allow-Origin，那么浏览器无法加载字体资源。

对于 PUT、DELETE 以及其他类型如 application/json 的 POST 请求，在发送 AJAX 请求之前，浏览器会先发送一个 OPTIONS 请求（称为 preflighted 请求）到这个 URL 上，询问目标服务器是否接受：
```text
  OPTIONS /path/to/resource HTTP/1.1
  Host: bar.com
  Origin: http://my.com
  Access-Control-Request-Method: POST
```
服务器必须响应并明确指出允许的 Method：
```text
  HTTP/1.1 200 OK
  Access-Control-Allow-Origin: http://my.com
  Access-Control-Allow-Methods: POST, GET, PUT, OPTIONS
  Access-Control-Max-Age: 86400
```
浏览器确认服务器响应的 Access-Control-Allow-Methods 头确实包含将要发送的 AJAX 请求的 Method，才会继续发送 AJAX，否则，抛出一个错误。由于以 POST、PUT 方式传送 JSON 格式的数据在 REST 中很常见，所以要跨域正确处理 POST 和 PUT 请求，服务器端必须正确响应 OPTIONS 请求。
