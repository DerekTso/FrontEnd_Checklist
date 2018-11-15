# 前端开发 - 面试题

## 目录
- [浏览器兼容性问题](#浏览器兼容性问题)
- [严格模式需要显式地引用全局作用域](#严格模式需要显式地引用全局作用域)
- [变量和函数的声明都被提前至函数体的顶部](#变量和函数的声明都被提前至函数体的顶部)
- [Ionic和Cordova的区别](#Ionic和Cordova的区别)
- [AngularJS的数据绑定采用什么机制?](#AngularJS的数据绑定采用什么机制?)
- [AngularJS中\$apply及\$digest的区别](#AngularJS中$apply及$digest的区别)
- [AngularJs中ng-show和ng-if的区别](#AngularJs中ng-show和ng-if的区别)
- [AngularJS中如何判断页面渲染完成](#AngularJS中如何判断页面渲染完成)

### 浏览器兼容性问题

1. IE8之前的浏览器都会给标签一个最小默认的行高的高度。即使你的标签是空的，这个标签的高度还是会达到默认的行高
2. 清除浮动
```
#box:after{ 
    content: “.”; 
    display: block; 
    height: 0; 
    clear: both; 
    visibility: hidden; 
}
```
3. 超链接CSS属性的排列顺序: L-V-H-A (link visited hover active)
4. 满足定宽和块状两个条件的元素，可以通过设置```margin: 0 auto;```来实现居中
5. table其长度根据其内文本长度决定


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

### AngularJS的数据绑定采用什么机制?

**脏检查机制：**
1. Angular 在 scope 模型上设置了一个监听队列($$watchlist)，用来监听数据变化并更新 view
2. 每次绑定一个东西到 view 上时 AngularJS 就会往监听队列里插入一条 $watch，用来检测它监视的 model 里是否有变化的东西
3. 当浏览器接收到可以被 Angular Context 处理的事件时(比如ng-click)，$digest 循环就会触发，遍历所有的 $watch，最后更新 DOM

### AngularJS中\$apply及\$digest的区别

1. AngularJS并不直接调用\$digest()，而是调用\$scope.\$apply()，后者会调用\$rootScope.\$digest()
2. \$digest循环是在\$rootScope开始，随后会访问所有的children scope中的watchers
3. 如果你在AngularJS上下文之外的任何地方修改了model，那么你就需要通过手动调用\$apply()来通知AngularJS
4. \$apply()方法有两种形式: 第一种会接受一个function作为参数，执行该function并且触发一轮\$digest循环。第二种会不接受任何参数，只是触发一轮\$digest循环
5. 总是应该使用接受一个function作为参数的\$apply()方法，因为当你传入一个function到\$apply()中的时候，这个function会被包装到一个try…catch块中，所以一旦有异常发生，该异常会被\$exceptionHandler service处理
6. 无论何时我们手动处理事件，使用第三方框架（比如jQuery、Facebook API） ，或者调用setTimeout() ，都可以使用 \$apply() 函数让Angular返回 \$digest 循环
7. \$digest循环最少也会运行两次，即使在listener函数中并没有改变任何model
8. \$digest循环的次数达到了10次后会抛出异常

### AngularJs中ng-show和ng-if的区别

1. ng-show在页面进行渲染的时候，已经将该元素加载，如果值为false，渲染出来的元素添加了一个ng-hide的class，以此来控制元素的显示与否
2. ng-if在页面进行渲染的时候，如果值为false，是不会将元素加载出来的，如果值变为true，则通过操作DOM的方式动态添加元素。注意：在这个时候，如果通过document，或者jQuery等方式获取元素进行操作的话，可能是获取不到的
3. ng-show实质是控制标签的显示和隐藏，内部是display: none和block
4. ng-if实质则是移除标签和插入标签
5. 当标签内容较少且需要不停动态操作标签的时候，运用ng-show
6. 当标签只使用一次就没起作用，建议使用ng-if，减少加载
7. ng-if指令会创建一个子级作用域(child scope)，在添加了ng-if指令的元素标签中使用的ng-click是不生效的，因为它访问的不是controller中的$scope作用域的数值，可以通过$parent去获取到其父级作用域，再操作对应的数值
8. ng-if中ng-model值无效的问题与ng-click不生效的问题类似，主要解决方法就是在绑定值时添加$parent标识，或者用ng-show指令代替ng-if指令
9. ng-click失效的两种情况：与ng-if一起使用；动态添加的ng-click

### AngularJS中如何判断页面渲染完成

```
$scope.$on('$viewContentLoaded', function(){
});
 
// 或者
$scope.$watch('$viewContentLoaded', function() { 
}); 
```
