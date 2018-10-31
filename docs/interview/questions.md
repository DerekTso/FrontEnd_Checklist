# 前端面试 - 面试基础题

## 目录
- [典型的JavaScript面试题](#典型的JavaScript面试题)

### 典型的JavaScript面试题

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
