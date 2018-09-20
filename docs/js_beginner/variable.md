# Javascript基础 - 变量

## 目录
- [变量(Variable)](#变量variable)
- [变量声明提升(Variable hoisting)](#变量声明提升variable-hoisting)
- [函数提升(Function hoisting)](#函数提升function-hoisting)
- [let 约束了变量提升](#let-约束了变量提升)
- [let 块级作用域](#let-块级作用域)
- [let 有暂时性死区](#let-有暂时性死区)
- [let 禁止重复声明变量](#let-禁止重复声明变量)
- [let 不会成为全局对象的属性](#let-不会成为全局对象的属性)
- [const变量声明时必须初始化](#const变量声明时必须初始化)

### 变量(Variable)

```
// JavaScript 标识符: 必须以字母（a-zA-Z）、下划线（_）或者美元符号（$）开头。
var foo;
var _bar;
var $baz;

// 全局变量(Global variables): 实际上是全局对象的属性。在网页中，缺省的全局对象是 window。
var abc = 1;
console.log(window.abc); // 输出 1

// 常量(Constants): 不可以通过赋值改变其值，也不可以在脚本运行时重新声明。它必须被初始化为某个值。
const COLOR = "red";

// 私有变量、函数
function Obj(){
    var a=0; //私有变量
    var fn=function(){ //私有函数
    }
}
var o=new Obj();
console.log(o.a); //undefined
console.log(o.fn); //undefined

// 静态变量、函数
function Obj(){
}
Obj.a=0; //静态变量
Obj.fn=function(){ //静态函数
}
console.log(Obj.a); //0
console.log(typeof Obj.fn); //function

var o=new Obj();
console.log(o.a); //undefined
console.log(typeof o.fn); //undefined

// 实例变量、函数
function Obj(){
    this.a=[]; //实例变量
    this.fn=function(){ //实例方法
    }
}
console.log(typeof Obj.a); //undefined
console.log(typeof Obj.fn); //undefined

var o=new Obj();
console.log(typeof o.a); //object
console.log(typeof o.fn); //function

// o1中的属性和方法与o2中的属性与方法虽然同名但却不是一个引用
function Obj(){
    this.a=[]; //实例变量
    this.fn=function(){ //实例方法
    }
}

var o1=new Obj();
o1.a.push(1);
o1.fn={};
console.log(o1.a); //[1]
console.log(typeof o1.fn); //object

var o2=new Obj();
console.log(o2.a); //[]
console.log(typeof o2.fn); //function

// 三种遍历对象的方法:
for in: 会输出自身以及原型链上可枚举的属性
Object.keys(): 用来获取对象自身可枚举的属性键
Object.getOwnPropertyNames(): 用来获取对象自身的全部属性名
```

### 变量声明提升(Variable hoisting)

```
// 变量提升: 在代码正式执行之前，编译器将会对代码进行预编译分析阶段，在这个阶段，当前作用域中的变量和函数，将被提升到作用域的顶部。
function hoistVariable() {
  var foo;
  console.log('foo:', foo); // foo: undefined
  foo = 3;
}
hoistVariable();
```

### 函数提升(Function hoisting)

```
// 对于函数，只有函数声明会被提升到顶部，而不包括函数表达式。
foo(); // "bar"
function foo() { // 函数声明
  console.log("bar");
}

baz(); // TypeError: baz is not a function
var baz = function baz() { // 函数表达式
  console.log("bar2");
};
// 此时的"baz"相当于一个声明的变量，类型为undefined。
// 由于baz只是相当于一个变量，因此浏览器认为"baz()"不是一个函数。
```

### let 约束了变量提升

```
// ES6 中，let（const）将不会提升变量到代码块的顶部。必须先声明，然后才可以使用，下面者段代码将会报错：
function nonHoistingFunc() {
  console.log('foo:', foo); // Uncaught ReferenceError
  let foo = 3;
}
nonHoistingFunc();
```

### let 块级作用域

```
// ES6 语句块中声明的变量将成为语句块所在代码段的局部变量。
if (true) {
  var foo = 3; // 在语句块内使用var声明一个变量，它在语句块外面仍旧是可见的
}
console.log(foo); // 3

if (true) {
  let y = 5; // let 添加了块级作用域
}
console.log(y); // ReferenceError: y is not defined

// i 在 for 循环中定义的是全局变量
var a = [];
for (var i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[0](); // 10
a[6](); // 10

// 用let来代替var，每一次的循环都生成了一个新的块级作用域，内部保存着i的值
var a = [];
for (let i = 0; i < 10; i++) {
 a[i] = function () {
   console.log(i);
 };
}
a[6](); // 6
```

### let 有暂时性死区(Temporal Dead Zone)

```
// 只要在语句块内有 let 命令，那么这个变量就绑定到了当前块作用域，不再受外部变量的影响，下面代码将会引发一个错误：
var foo = 3;
if (true) {
  foo = 5; // Uncaught ReferenceError: foo is not defined
  let foo;
}

// 在TDZ中使用 typeof 也会报错
console.log( typeof a); // undefined

console.log( typeof a); // ReferenceError: a is not defined
let a=10;
```

### let 禁止重复声明变量

```
// let 不允许在相同作用域中使用与其他 let 或 const，重复声明同一个变量
function func(arg){
  let arg // SyntaxError
}

function func() {
  let foo = 3; // SyntaxError
  var foo = 5;
}

function func() {
  let foo = 3; // SyntaxError
  let foo = 5;
}
```

### let 不会成为全局对象的属性

```
// 在ES6中，let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性。

var foo = 1;
console.log(window.foo); // 输出 1
let bar = 2;
console.log(window.bar); // 输出 undefined
```

### const变量声明时必须初始化

```
// 以上 let 所介绍的规则均适用于 const 命令，不同的是，const声明的变量不能重新赋值，const变量声明时必须初始化，不能留到以后赋值。
const a = 3;
a = 5; // Uncaught TypeError: Assignment to constant variable
const b; // Uncaught SyntaxError: Missing initializer in const declaration
```
