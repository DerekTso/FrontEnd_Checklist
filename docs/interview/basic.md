# 前端面试 - 基础题

#### Q: 将静态资源放在其他域名的目的是什么？

1. 在请求这些静态资源的时候不会发送 cookie，节省了流量（ cookie 是会发送给子域名/二级域名的，所以这些静态资源要放在一个单独的主域名下）
2. 浏览器对于一个域名会有请求数的限制，这种方法可以方便做CDN

#### Q: 跨域问题

1. 浏览器的同源策略导致了跨域
2. 用于隔离潜在恶意文件的重要安全机制
3. jsonp ，允许 script 加载第三方资源
4. nginx 反向代理（nginx 服务内部配置 Access-Control-Allow-Origin *）
5. cors 前后端协作设置请求头部，Access-Control-Allow-Origin 等头部信息
6. iframe 嵌套通讯，postmessage
