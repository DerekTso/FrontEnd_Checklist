# 前端面试 - Javascript 开发

## 目录
- [面试中提问的问题](#面试中提问的问题)
- [JavaScript 相关](#JavaScript-相关)
- [服务器相关](#服务器相关)

### 面试中提问的问题

1. 问清楚公司做的什么产品，以后的发展方向
2. 问清楚公司的技术团队，技术栈
3. 问清楚自己如果入职了，自己的工作内容是什么
4. 问清楚公司的上班时间，上班地点，试用期情况，薪资福利，薪资发放时间，五险一金
5. 拿到 offer 后查清楚公司的企业信息，如：企查查就可以查

### JavaScript 相关

- [典型的JavaScript面试题](#典型的JavaScript面试题)
- [Q: 如何实现一个 bind 函数](#q-如何实现一个-bind-函数)
- [Q: 如何实现一个 call 函数](#q-如何实现一个-call-函数)
- [Q: 如何实现一个 apply 函数](#q-如何实现一个-apply-函数)
- [Q: 如何理解箭头函数中的this](#q-如何理解箭头函数中的this)
- [Q: window.onload 和 document.ready 的区别](#q-windowonload-和-documentready-的区别)
- [Q: 如何理解 V8 引擎字节码?](#q-如何理解-V8-引擎字节码?)
- [Q: cookie的主要应用场景](#q-cookie的主要应用场景)
- [Q: WebStorage 和 Cookie 的区别](#q-WebStorage-和-Cookie-的区别)
- [Q: sessionStorage 和 localStorage 的区别](#q-sessionStorage-和-localStorage-的区别)

### 服务器相关

- [Q: 从输入URL到页面加载发生了什么？](#q-从输入url到页面加载发生了什么)
- [Q: 在浏览器地址栏键入URL，按下回车之后会发生什么？](#q-在浏览器地址栏键入url按下回车之后会发生什么)
- [Q: 将静态资源放在其他域名的目的是什么？](#q-将静态资源放在其他域名的目的是什么)
- [Q: CORS(跨域资源共享)](#q-CORS跨域资源共享)
- [Q: postMessage解决跨域和跨页面通信的问题](#q-postMessage解决跨域和跨页面通信的问题)
- [Q: 跨域问题](#q-跨域问题)
- [Q: GET 和 POST 的区别](#q-get-和-post-的区别)
- [Q: 前端渲染与后端渲染](#q-前端渲染与后端渲染)

### Q: 如何实现一个 bind 函数

```
Function.prototype.myBind=function(obj,arg){
  var arg=Array.prototype.slice.call(arguments,1);
  var context=this;
  var bound=function(newArg){
    arg=arg.concat(Array.prototype.slice.call(newArg));
    return context.apply(obj,arg);
  }
  //寄生组合继承
  var F=function(){}
  F.prototype=context.prototype;
  bound.prototype=new F();
  return bound;
}

或者

Function.prototype.myBind = function (context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  var _this = this
  var args = [...arguments].slice(1)
  // 返回一个函数
  return function F() {
    // 因为返回了一个函数，我们可以 new F()，所以需要判断
    if (this instanceof F) {
      return new _this(...args, ...arguments)
    }
    return _this.apply(context, args.concat(...arguments))
  }
}
```

### Q: 如何实现一个 call 函数

```
Function.prototype.myCall = function (context) {
  var context = context || window
  context.fn = this
  var args = [...arguments].slice(1)
  var result = context.fn(...args)
  delete context.fn
  return result
}
```

### Q: 如何理解箭头函数中的this

1. 传统函数的 this 是动态的。它取决于 this 怎样被调用
2. 箭头函数的 this 是静态的。它是一个词法（lexical ）上的值，它取决于 this 在哪个作用域中定义
3. 用 ```call()``` 或者 ```apply()``` 调用箭头函数时，无法对 this 进行绑定，即传入的第一个参数被忽略
4. 由于箭头函数没有 [[Construct]] 内部方法，因此，它将不能用作构造函数。如果使用了 new 关键字创建新的函数，则会抛出错误
5. ```const value = () => foo()```解释为```const value = () => (foo())```
6. ```var func = () => ({foo: 1}); // Object对象要做个括号```
7. ```callback = callback || () => {}; // SyntaxError: invalid arrow-function arguments```
8. ```callback = callback || (() => {}); // ok```

### Q: 如何实现一个 apply 函数

```
Function.prototype.myApply = function (context) {
  var context = context || window
  context.fn = this
  var result
  if (arguments[1]) {
    result = context.fn(...arguments[1])
  } else {
    result = context.fn()
  }
  delete context.fn
  return result
}
```

### Q: window.onload 和 document.ready 的区别

1. window.onload 是在页面中包含图片在内的所有元素全部加载完成
2. document.ready 是文档结构加载完成，但不包含图片，其他媒体文件
3. 在 jQuery 中会看到 $(function(){}) 和 $(document).ready(function(){})，是在DOM树加载完成之后执行
4. window.onload 是在DOM树加载完以及所有文件加载完成才执行，因此慢于document.ready
5. 当页面文档加载并解析完毕之后会马上触发 DOMContentLoaded 事件，而不会等待样式文件、图片文件和子框架页面的加载

### Q: 如何理解 V8 引擎字节码?

1. 当 V8 编译 JavaScript 代码时，解析器(parser)将生成一个抽象语法树
2. 语法树是 JavaScript 代码的句法结构的树形表示形式
3. 解释器 Ignition 根据语法树生成字节码
4. TurboFan 是 V8 的优化编译器，TurboFan 将字节码生成优化的机器代码
![interview_basic_v8_bytecode](../../images/interview_basic_v8_bytecode.png)

### Q: cookie的主要应用场景

1. 保持登录
2. 保持上次查看的页面
3. 浏览计数
4. 广告追踪
5. 购物车的状态保持

### Q: WebStorage 和 Cookie 的区别

1. cookie安全性问题(CSRF)
2. cookie会在http请求中传送，浪费带宽
3. cookie只有4kb，而webStorage有平均5mb的本地存储
4. cookie根据expires设置过期时间
5. cookie在同源下只要没过期就会一直共享，localStorage在同源中没被删除情况也会共享，sessionStorage在窗口关闭之前在同一个窗口同源下才能共享

### Q: sessionStorage 和 localStorage 的区别

1. 在 html5 中 WebStorage 包括两种存储方式```sessionStorage``` 和 ```localStorage```
2. sessionStorage是非持久化的本地存储，用于存储一个会话(session)中的数据，并且当会话结束后数据也随之销毁
3. localstorage是持久化的本地存储，除非主动删除数据，否则数据是永远也不过期的
4. localstorage和sessionStorage都具有相同的操作方法，例如setItem,getItem, clear和removeItem等
5. 对于object对象可以用JSON.stringfy进行转化然后存储，用的时候利用JSON.parse转化回来使用
6. 对于图片存储我们可以将图片转化为DataURI格式进行存储；具体转化方式可以借用canvas提供的toDataURL
7. 对于webstorage本地存储各浏览器支持也是不同，平均对每个源分配的存储大小大约5M

---

### Q: 从输入URL到页面加载发生了什么？

1. DNS解析
2. TCP连接
3. 发送HTTP请求
4. 服务器处理请求并返回HTTP报文
5. 浏览器解析渲染页面
6. 连接结束

### Q: 在浏览器地址栏键入URL，按下回车之后会发生什么？

1. 浏览器向 DNS 服务器请求解析该 URL 中的域名所对应的 IP 地址
2. 解析出 IP 地址后，根据该 IP 地址和默认端口 80，和服务器建立TCP连接
3. 浏览器发出读取文件的 HTTP 请求，该请求报文作为 TCP 三次握手的第三个报文的数据发送给服务器
4. 服务器对浏览器请求作出响应，并把对应的 html 文本发送给浏览器
5. 释放 TCP 连接
6. 浏览器解析渲染页面

### Q: 将静态资源放在其他域名的目的是什么？

1. 在请求这些静态资源的时候不会发送 cookie，节省了流量（ cookie 是会发送给子域名/二级域名的，所以这些静态资源要放在一个单独的主域名下）
2. 浏览器对于一个域名会有请求数的限制，这种方法可以方便做CDN

### Q: CORS(跨域资源共享)

CORS(Cross-Origin Resource Sharing)允许浏览器向跨源(协议 + 域名 + 端口)服务器，发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制
 
* 配置项解释

Access-Control-Allow-Origin
Access-Control-Allow-Methods
Access-Control-Expose-Headers
Access-Control-Allow-Credentials
Access-Control-Max-Age

### Q: postMessage解决跨域和跨页面通信的问题

* postMessage

postMessage是html5引入的API，允许来自不同源的脚本采用异步方式进行有限的通信，可以实现跨文本档、多窗口、跨域消息传递

postMessage(data,origin) // 如果要指定和当前窗口同源的话，origin设置为"/"

* MessageEvent的属性

1. data：传递来的message
2. source：发送消息的窗口对象
3. origin：发送消息窗口的源（协议+主机+端口号）

```
// 同域父子页面间通讯
// 父页面a.html:

//> localhost:9011/a.html
<h1 class="header">page A</h1>
<div class="mb20">
    <textarea name="ta" id="data" cols="30" rows="5">hello world</textarea>
    <button style="font-size:20px;" onclick="send()">post message</button>
</div>
<!-- 不跨域的情况 -->
<iframe src="b.html" id="child" style="display: block; border: 1px dashed #ccc; height: 300px;"></iframe>

<script>
function send() {
    var data = document.querySelector('#data').value;
    window.frames[0].postMessage(data, '/'); // 触发同域子页面的message事件
    //window.frames[0].postMessage(data, 'http://localhost:9022/'); // 触发跨域子页面的messag事件
}

// 当前页面执行 window.postMessage(..)
// 或子页面执行 parent.postMessage(...) 都会触发下面的回调
// 只是messageEvent.source不同而已
window.addEventListener('message', function(messageEvent) {
    var data = messageEvent.data;
    console.info('message from child:', data);
}, false);
</script>

子页面b.html

//> localhost:9011/b.html
<h1 class="header">page B</h1>

<input type="text" id="inp" value="some contents..">
<button onclick="send()">send</button>

<script>
window.addEventListener('message', function(ev) {
    // if (ev.source !== window.parent) {return;}
    var data = ev.data;
    console.info('message from parent:', data);
}, false);

function send() {
    var data = document.querySelector('#inp').value;
    // window.postMessage(data, '*'); // 触发当前页面的message事件
    parent.postMessage(data, '*'); // 触发父页面的message事件
    // parent.postMessage(data, 'http://localhost:9011/'); // 若父页面的域名和指定的不一致，则postMessage失败
}
</script>
```

```
// 跨域父子页面间通讯
// 父页面a.html:

//> localhost:9011/a.html
<h1 class="header">page A</h1>
<div class="mb20">
    <textarea name="ta" id="data" cols="30" rows="5">hello world</textarea>
    <button style="font-size:20px;" onclick="send()">post message</button>
</div>
<!-- 跨域的情况 -->
<iframe src="http://localhost:9022/b.html" id="child" style="display: block; border: 1px dashed #ccc; height: 300px;"></iframe>

<script>
function send() {
    var data = document.querySelector('#data').value;
    window.frames[0].postMessage(data, 'http://localhost:9022/'); // 触发跨域子页面的messag事件
}

window.addEventListener('message', function(messageEvent) {
    var data = messageEvent.data; 
    console.info('message from child:', data);
}, false);
</script>

// 子页面b.html

//> localhost:9022/b.html
<h1 class="header">page B</h1>

<input type="text" id="inp" value="some contents..">
<button onclick="send()">send</button>

<script>
window.addEventListener('message', function(ev) {
    // if (ev.source !== window.parent) {return;}
    var data = ev.data;
    console.info('message from parent:', data);
}, false);

function send() {
    var data = document.querySelector('#inp').value;
    parent.postMessage(data, 'http://localhost:9011/'); // 若父页面的域名和指定的不一致，则postMessage失败
    // parent.postMessage(data, '*'); // 触发父页面的message事件
}
</script>
```

### Q: 跨域问题

1. 浏览器的同源策略导致了跨域
2. 用于隔离潜在恶意文件的重要安全机制
3. jsonp ，允许 script 加载第三方资源
4. nginx 反向代理（nginx 服务内部配置 Access-Control-Allow-Origin *）
5. cors 前后端协作设置请求头部，Access-Control-Allow-Origin 等头部信息
6. iframe 嵌套通讯，postmessage

### Q: GET 和 POST 的区别

1. GET 提交的数据会放在 URL 之后，以?分割 URL 和传输数据，参数之间以&相连，如EditPosts.aspx?name=test1&id=123456
2. POST 方法是把提交的数据放在 HTTP 包的 Body 中
3. GET 提交的数据大小有限制
4. POST 提交的数据没有限制
5. GET 方式需要使用 Request.QueryString 来取得变量的值
6. POST 方式通过 Request.Form 来获取变量的值
7. GET 方式提交数据，会带来安全问题，比如一个登录页面，通过GET方式提交数据时，用户名和密码将出现在URL上

### Q: 前端渲染与后端渲染

* 前端渲染的优势

1. 局部刷新
2. 懒加载
3. 富交互
4. 节约服务器成本
5. 关注分离设计
6. JS 一次学习，到处使用

* 前端渲染遇到的问题

1. 前端渲染主要面临的问题有两个 SEO、首屏性能
2. SEO：由于传统的搜索引擎只会从 HTML 中抓取数据，导致前端渲染的页面无法被抓取
3. 首屏性能：前端渲染常使用的 SPA 会把所有 JS 整体打包，无法忽视的问题就是文件太大，导致渲染前等待很长时间

* 后端渲染的优势

1. 服务端渲染不需要先下载一堆 js 和 css 后才能看到页面（首屏性能）
2. SEO
3. 服务端渲染不用关心浏览器兼容性问题
4. 对于电量不给力的手机或平板，减少在客户端的电量消耗很重要

* 后端渲染遇到的问题

1. 服务端优势其实只有首屏性能和 SEO 两点比较突出
2. 但现在这两点也慢慢变得微不足道了
3. React 这类支持同构的框架已经能解决这个问题

---
