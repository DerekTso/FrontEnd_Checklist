# 前端面试 - 面试基础题

## 目录
- [严格模式需要显式地引用全局作用域](#严格模式需要显式地引用全局作用域)
- [变量和函数的声明都被提前至函数体的顶部](#变量和函数的声明都被提前至函数体的顶部)
- [Ionic和Cordova的区别](#Ionic和Cordova的区别)

### 严格模式需要显式地引用全局作用域

```
(function() {
  var a = b = 5;
})();
console.log(b); // 输出：5

1. a是用关键字 var 来声明的，a是局部变量
2. b则被赋予为全局变量
3. 它并没有使用严格模式（use strict），如果启用了严格模式，代码就会报错："Uncaught ReferenceError: b is not defined"
4. 严格模式需要显式地引用全局作用域，如：
(function() {
'use strict';
  var a = window.b = 5;
})();
console.log(b); // 输出：5
```

### 变量和函数的声明都被提前至函数体的顶部

```
function test() {
  console.log(a);
  console.log(foo());
  var a = 1;
  function foo() {
    return 2;
  }}
test(); // undefined 2

1. 变量和函数的声明都被提前至函数体的顶部
2. 变量并没有被赋值。虽然 a 已经被声明，但仍然是undefined
```

### Ionic和Cordova的区别

1. Ionic是一款基于AngularJS的HTML5移动App开发框架，以PhoneGap为蓝本，让开发者可以通过命令行工具快速生成android、ios移动App应用
2. Ionic的打包插件是基于phonegap/cordova的
3. Ionic是Angular的衍生品，对Angular进行了扩展，利用Angular实现了很多符合移动端应用的组件，并搭建了很完善的样式库
4. Ionic/Angular负责页面的实现，而Cordova负责将实现的页面包装成原生应用（Android:apk；iOS:ipa）
5. 就像花生，最内层的花生仁是Angular，花生仁的表皮是Ionic，而最外层的花生壳则是Cordova。包装完成之后的页面才有可能调用设备的Native API，最后才能上传到应用商店被用户使用
6. Cordova插件的作用是提供一个桥梁供页面和Native通信
