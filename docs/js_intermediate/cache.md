# Javascript进阶 - 前端缓存

## 目录
- [Service Worker](#service-worker)
- [memory cache](#memory-cache)
- [disk cache](#disk-cache)
- [请求网络](#请求网络)
- [强制缓存](#强制缓存)
- [Expires](#expires)
- [Cache-control](#cache-control)
- [max-age=0 和 no-cache 等价吗？](#max-age0-和-no-cache-等价吗)
- [对比缓存/协商缓存](#对比缓存协商缓存)
- [Last-Modified & If-Modified-Since](#last-modified--if-modified-since)
- [Etag & If-None-Match](#etag--if-none-match)
- [浏览器的行为](#浏览器的行为)
- [缓存小结](#缓存小结)
- [缓存的应用模式](#缓存的应用模式)
    - [模式 1: 不常变化的资源](#模式-1-不常变化的资源)
    - [模式 2: 经常变化的资源](#模式-2-经常变化的资源)
- [如何解决因命中强缓存而导致无法呈现更新后的改动](#如何解决因命中强缓存而导致无法呈现更新后的改动)
- [HTTP缓存基本概念](#HTTP缓存基本概念)

### Service Worker

1. 缓存是永久性的，即关闭 TAB 或者浏览器，下次打开依然还在
2. 手动调用 API ```cache.delete(resource)``` 清除
3. 或者容量超过限制，被浏览器全部清空
4. 如果 Service Worker 没能命中缓存，一般情况会使用 ```fetch()``` 方法继续获取资源。这时候，浏览器就去 memory cache 或者 disk cache 进行下一次找缓存的工作了
5. 经过 Service Worker 的 ```fetch()``` 方法获取的资源，即便它并没有命中 Service Worker 缓存，甚至实际走了网络请求，也会标注为 ```from ServiceWorker```

### memory cache

1. memory cache 是内存中的缓存。按照操作系统的常理：先读内存，再读硬盘
2. 几乎所有的网络请求资源都会被浏览器自动加入到 memory cache 中，但只是个"短期存储"
3. 常规情况下，浏览器的 TAB 关闭后该次浏览的 memory cache 便告失效
4. ```<link rel="preload">```这些显式指定的预加载资源，也会被放入 memory cache 中
5. 使用 no-store 这个头部配置的话，即便是 memory cache 也不会缓存
6. 不受 HTTP 协议头的约束

### disk cache

1. disk cache(HTTP cache)，是存储在硬盘上的缓存，是持久存储
2. 当命中缓存之后，浏览器会从硬盘中读取资源，虽然比起从内存中读取慢了一些，但比起网络请求还是快了不少
3. 遵守 HTTP 协议头中的字段（强制缓存，对比缓存，Cache-Control 等）

### 请求网络

1. 如果一个请求在上述 3 个位置都没有找到缓存，那么浏览器会正式发送网络请求去获取内容
2. 为了提升之后请求的缓存命中率，自然要把这个资源添加到缓存中去：
    - 2.1. 根据 Service Worker 中的 handler 决定是否存入 Cache Storage (额外的缓存位置)
    - 2.2. 根据 HTTP 头部的相关字段(Cache-control, Pragma 等)决定是否存入 disk cache
    - 2.3. memory cache 保存一份资源的**引用**，以备下次使用

### 强制缓存

1. 当客户端请求后，会先访问缓存数据库看缓存是否存在。如果存在则直接返回；不存在则请求真的服务器，响应后再写入缓存数据库
2. 如果考虑使用缓存来优化网页性能的话，强制缓存应该是首先被考虑的
3. 可以造成强制缓存的字段是 Expires 和 Cache-control

### Expires

1. 这是 HTTP 1.0 的字段，表示缓存到期时间，是一个绝对的时间 (当前时间+缓存时间)，如 ```Expires: Thu, 10 Nov 2017 08:45:11 GMT```
2. 在响应消息头中，设置这个字段之后，就可以告诉浏览器，在未过期之前不需要再次请求
3. 由于是绝对时间，用户可能会将客户端本地的时间进行修改，而导致浏览器判断缓存失效，重新请求该资源
4. 时差或者误差等因素也可能造成客户端与服务端的时间不一致，致使缓存失效
5. 写法太复杂了。表示时间的字符串多个空格，少个字母，都会导致非法属性从而设置失效

### Cache-control

1. 在HTTP/1.1中，增加了一个字段Cache-control，该字段表示资源缓存的最大有效时间，在该时间内，客户端不需要向服务器发送请求
2. 它是一个相对时间，如 ```Cache-control: max-age=2592000```
3. Cache-control 字段常用的值:
    - 3.1. max-age：即最大有效时间
    - 3.2. must-revalidate：如果超过了 max-age 的时间，浏览器必须向服务器发送请求，验证资源是否还有效
    - 3.3. no-cache：从语义上表示下次请求不要直接使用缓存而需要比对后决定
    - 3.4. no-store: 真正意义上的"不要缓存"。所有内容都不走缓存，包括强制缓存和对比缓存
    - 3.5. public：所有的内容都可以被缓存 (包括客户端和代理服务器，如 CDN)
    - 3.6. private：所有的内容只有客户端才可以缓存，代理服务器不能缓存(默认值)
    - 3.7. 这些值可以混合使用，例如 ```Cache-control:public, max-age=2592000```
4. 自从 HTTP/1.1 开始，Expires 逐渐被 Cache-control 取代
5. Cache-control 是一个相对时间，即使客户端时间发生改变，相对时间也不会随之改变，这样可以保持服务器和客户端的时间一致性
6. Cache-control 的可配置性比较强大
7. Cache-control 的优先级高于 Expires，为了兼容 HTTP/1.0 和 HTTP/1.1，实际项目中两个字段我们都会设置
8. 在无法确定客户端的时间是否与服务端的时间同步的情况下，Cache-Control相比于expires是更好的选择，所以同时存在时，只有Cache-Control生效

### max-age=0 和 no-cache 等价吗？

1. 从规范的字面意思来说，max-age 到期是 应该(SHOULD) 重新验证，而 no-cache 是 必须(MUST) 重新验证
2. 但实际情况以浏览器实现为准，大部分情况他们俩的行为还是一致的
3. 如果是 ```max-age=0, must-revalidate``` 就和 ```no-cache``` 等价了

### 对比缓存/协商缓存

1. 当强制缓存失效(超过规定时间)时，就需要使用对比缓存，由服务器决定缓存内容是否失效
2. 浏览器先请求缓存数据库，返回一个缓存标识。之后浏览器拿这个标识和服务器通讯
3. 如果缓存未失效，则返回 HTTP 状态码 304 表示继续使用
4. 如果缓存失效，则返回新的数据和缓存规则，浏览器响应数据后，再把规则写入到缓存数据库
5. 如果是 304 的话，返回的仅仅是一个状态码，并没有实际的文件内容，通过减少响应体体积，来缩短网络传输时间
6. 对比缓存有 2 组字段：```Last-Modified & If-Modified-Since``` / ```Etag & If-None-Match```

### Last-Modified & If-Modified-Since

1. 服务器通过 Last-Modified 字段告知客户端，资源最后一次被修改的时间，例如 ```Last-Modified: Mon, 10 Nov 2018 09:10:11 GMT```
2. 浏览器将这个值和内容一起记录在缓存数据库中
3. 下一次请求相同资源时，浏览器从自己的缓存中找出"不确定是否过期的"缓存。在请求头中将上次的 Last-Modified 的值写入到 If-Modified-Since 字段
4. 服务器会将 If-Modified-Since 的值与 Last-Modified 字段进行对比。如果相等，则表示未修改，响应 304；反之，则表示修改了，响应 200 状态码，并返回数据
5. 如果资源更新的速度是秒以下为单位，那么该缓存是不能被使用的，因为它的时间单位最低是秒
6. 如果文件是通过服务器动态生成的，那么该方法的更新时间永远是生成的时间，尽管文件可能没有变化，所以起不到缓存的作用

### Etag & If-None-Match

1. Etag 存储的是文件的特殊标识(一般都是 hash 生成的)，服务器存储着文件的 Etag 字段
2. Etag 相对于 Last-Modified，Etag 所表示的文件 hash 相对于 Last-Modified 所表示的更新时间
3. If-None-Match 相对于 If-Modified-Since。服务器同样进行比较，命中返回 304, 不命中返回新资源和 200
4. Etag 的优先级高于 Last-Modified

### 浏览器的行为

1. 打开网页，地址栏输入地址： 查找 disk cache 中是否有匹配。如有则使用；如没有则发送网络请求
2. 普通刷新 (F5)：因为 TAB 并没有关闭，因此 memory cache 是可用的，会被优先使用(如果匹配的话)。其次才是 disk cache
3. 强制刷新 (Ctrl + F5)：浏览器不使用缓存，因此发送的请求头部均带有 ```Cache-control: no-cache```(为了兼容，还带了 ```Pragma: no-cache```)。服务器直接返回 200 和最新内容

### 缓存小结

当浏览器要请求资源时

1. 调用 Service Worker 的 fetch 事件响应
2. 查看 memory cache
3. 查看 disk cache。这里又细分：
    - 3.1. 如果有强制缓存且未失效，则使用强制缓存，不请求服务器。这时的状态码全部是 200
    - 3.2. 如果有强制缓存但已失效，使用对比缓存，比较后确定 304 还是 200
4. 发送网络请求，等待网络响应
5. 把响应内容存入 disk cache (如果 HTTP 头信息配置可以存的话)
6. 把响应内容 的引用 存入 memory cache (无视 HTTP 头信息的配置)
7. 把响应内容存入 Service Worker 的 Cache Storage (如果 Service Worker 的脚本调用了 ```cache.put()```)

### 缓存的应用模式

#### 模式 1: 不常变化的资源

1. 通常在处理这类资源资源时，给它们的 Cache-Control 配置一个很大的 max-age=31536000 (一年)，如 ```Cache-Control: max-age=31536000```
2. 为了解决更新的问题，需要在文件名(或者路径)中添加 hash， 版本号等动态字符，之后更改动态字符，达到更改引用 URL 的目的，从而让之前的强制缓存失效
3. 在线提供的类库 (如 jquery-3.3.1.min.js, lodash.min.js 等) 均采用这个模式。如果配置中还增加 public 的话，CDN 也可以缓存起来
4. 这个模式的一个变体是在引用 URL 后面添加参数 (例如 ?v=xxx 或者 ?_=xxx)，这样就不必在文件名或者路径中包含动态参数
5. 在项目每次构建时，更新额外的参数 (例如设置为构建时的当前时间)，则能保证每次构建后总能让浏览器请求最新的内容
6. 在处理 Service Worker 时，对待 sw-register.js(注册 Service Worker) 和 serviceWorker.js (Service Worker 本身) 需要格外的谨慎

#### 模式 2: 经常变化的资源

1. 这类资源的特点是 URL 不能变化，但内容经常变化。可以设置 ```Cache-Control: no-cache``` 来迫使浏览器每次请求都必须找服务器验证资源是否有效
2. 这种模式下，节省的并不是请求数，而是请求体的大小。所以它的优化效果不如模式 1 来的显著

### 如何解决因命中强缓存而导致无法呈现更新后的改动

1. 跳转时增加时间戳例如：
```
location.href = 'https://www.localhost:5000.com/index.html?t=201811221048001';
```

2. 修改response headers中的cache-control
```
cache-control: public, max-age=0
```

3. 使用HTML Meta 标签
```
<meta http-equiv="Cache-Control" content="no-cache, no-store, must-revalidate" />
<meta http-equiv="Pragma" content="no-cache" />
<meta http-equiv="Expires" content="0" />
```

4. 注意点
    1. 上述代码只有部分浏览器可以支持，而且所有缓存代理服务器都不支持，因为代理不解析HTML内容本身。
    2. 最好还是不要指定HTML标签，在HTML5中，这些```<meta HTTP等>```标签是无效的。只有HTML5规范中列出的HTTP等效值才被允许。

### HTTP缓存基本概念

1. HTTP 缓存分为：强缓存和协商缓存
2. 缓存关键步骤
    - 判断是否存在缓存
    - 判断缓存是否有效(即强缓存是否命中)
    - 请求服务端，判断服务端资源是否更新（即协商缓存是否命中）
    - 返回资源（若服务端返回的资源，本地保存请求，包括请求头信息）

