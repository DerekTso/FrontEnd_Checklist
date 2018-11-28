# Javascript高阶 - Web安全漏洞之CSRF

## 目录
- [什么是 CSRF(Cross-Site Request Forgery)](#什么是-csrfcross-site-request-forgery)
- [CSRF 的危害](#csrf-的危害)
- [防御方法](#防御方法)

### 什么是 CSRF(Cross-Site Request Forgery)

1. session 登录权限验证机制：服务端使用一个键值对记录登录信息，同时在 cookie 中将 session id 存储到 cookie 中
2. 浏览器中 HTTP(s) 请求自动把 cookie 带上传给服务端
3. 请求时通过 cookie 获取 session id，在服务端获取登录信息即可完成用户权限的校验
4. 但是由于 cookie 太开放了，用户在 A 网站的登录信息，在访问 B 网站的时候，会在用户不知情的状况下，被带上传给服务端
5. 以上的过程就是跨站请求攻击，即 Cross-Site Request Forgery，即 CSRF
6. 简单总结 CSRF 漏洞就是利用网站权限校验方面的漏洞，在用户不知觉的情况下发送请求，达到"伪装"用户的目的
7. CSRF 有两个特点：利用 cookie 自动携带的特性以及跨站攻击

### CSRF 的危害

1. 攻击者能够欺骗受害用户完成该受害者所允许的任一状态改变的操作，比如：更新账号细节，完成购物，注销甚至登录等操作
2. 获取用户的隐私数据
3. 配合其他漏洞攻击
4. CSRF 蠕虫（用户访问恶意页面后通过CSRF获取其好友列表信息，然后再利用私信好友的CSRF漏洞给其每个好友发送一条指向恶意页面的信息，一传十，十传百）

### 防御方法

// 检查 Referer 字段

1. 通过在网站中校验请求 Referer 字段，判断请求是否是从本站发出
2. 拒绝一切非本站发出的请求，这样避免了 CSRF 的跨站特性
```
const { parse } = require('url');
module.exports = class CheckReferer {
  indexAction() {
    const referer = this.ctx.referer();
    const {host: refererHost} = parse(referer);
    if(refererHost !== 'xxx') {
      return this.fail('REFERER_ERROR');
    }
  }
}
```

// Token 验证

1. 校验信息不通过 cookie 传递，在其他参数中增加随机加密串进行校验
2. 随机字符串：由于 CSRF 攻击中攻击者是无从事先得知该随机字符串的值，所以服务端就可以通过校验该值拒绝可以请求
3. JWT：在前端记录登录 token，每次请求的时候通过在 Header 中添加认证头的方式来实现登录校验过程。由于 CSRF 攻击中攻击者无法知道该 token 值，通过这种方式也是可以防止 CSRF 攻击的
