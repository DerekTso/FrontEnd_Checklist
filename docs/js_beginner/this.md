# Javascript基础 - this指向

## 目录
- [this指向](#this指向)
- [默认指向](#默认指向)
- [隐式指向](#隐式指向)
- [显示指向](#显示指向)
- [通过new实例化一个对象](#通过new实例化一个对象)
- [ES6箭头函数中的this](#es6箭头函数中的this)
- [IIFE，立即调用函数表达式](#iife立即调用函数表达式)

### this指向

- 默认指向：对于一个函数，如果没有被打点调用，则该函数里的 this 指向全局(window)
- 隐式指向：对于一个函数，如果被打点调用，则该函数里的 this 指向调用该函数的对象
- 显式指向：通过 call，apply，bind 改变函数里的 this 指向
- new一个对象：构造函数的 this 指向该实例化对象

this指向的优先级为: new一个对象 > 显式指向 > 隐式指向 > 默认指向


### 默认指向

```
var age = 18;
function showAge () {
  var age = 100;
  console.log('my age is: ' + this.age);
}
showAge(); // 运行的结果为 my age is : 18

var age = 18;
var obj = {
    age : 100,
    showAge : function () {
      setTimeout(function () {
console.log('my age is: ' + this.age);
      }, 300);
      return '请稍等300毫秒'；
    }
}
obj.showAge(); // 运行的结果为my age is : 18
// 一般的回调函数的 this 指向全局对象window, e.g. window.setTimeout
```

### 隐式指向

```
var age = 18;
var obj = {
    age : 100,
    showAge : function () {
      console.log('my age is: ' + this.age);
    }
}
obj.showAge(); // 运行结果是my age is: 100

var obj1 = {
    age : 18,
    showAge : function () {
      console.log('my age is: ' + this.age);
    }
}
var obj2 = {
    age : 100,
    showAge : function () {}
}
obj2.showAge = obj1.showAge;
obj2.showAge(); // 运行结果是 my age is: 100
```

### 显示指向

```
// call、apply 都是改变一个函数的this指向并执行该函数，区别是传参列表不同: call(obj, 参数1, 参数2, ...) / apply(obj, arguments)
// bind 是改变一个函数的this指向并返回一个新的函数，传参结合 call 和 apply 的，只接收第一次绑定的this

var obj = {
 age : 100,
 showAge : function () {
  console.log('my age is: ' + this.age);
 }
}
var newObj = {
 age : 18
}
obj.showAge.call(newObj); // my age is: 18
//var func = obj.showAge.bind(newObj);
//func(); // my age is: 18
```

```
// 来模拟一下 call 方法

Function.prototype.myCall = function (context) {
 var ctx = null;
 //保证不污染原对象
 if (context) {
    ctx = JSON.parse(JSON.stringify(context));
 } else {
    ctx = window;
 }
 var arg = arguments, //保存函数的实参列表
 len = arg.length,
 args = [];

// 在隐式指向中我们知道，对于一个函数，如果被打点调用，则该函数里的 this 指向调用该函数的对象
// e.g. obj.showAge.myCall(newObj);中的 this 指向 showAge()，ctx.fn ＝ showAge()
 ctx.fn = this;

 for (var i = 1; i < len; i++) {
    //从实参列表的第一位开始遍历，因为第零位为指向的上下文
    args.push('arg[' + i + ']');
 }
 //遍历完后 args = [ 'arg[1]', 'arg[2]', ...]

 var result = eval( 'ctx.fn(' + args.join(',') + ')' ); //上述语句可理解为 eval('ctx.fn(arg[1], arg[2], ...)')

 delete ctx.fn;
 return result;
}
```

### 通过new实例化一个对象

```
function Person (name, age) {
 this.name = name;
 this.age = age;
 this.introduceYourself = function () {
    console.log('my name is ' + this.name, 'and I\'m ' + this.age + ' years old.');
 }
}
var person = new Person('xuyede', 18);
person.introduceYourself ();
// 运行结果为 my name is xuyede and I'm 18 years old.

把 new Person() 的过程分成4个步骤理解
1. 在 Person 函数里面的第一行隐式生成一个对象 var this = {}
2. 让该 this 的原型指向该函数的原型 this.__proto__ = Person.prototype
3. 执行 this.xxx = xxx
4. 在函数最后一行把这个 this 返回出去

// 剖析一下new这个东西到底干了什么

function myNew() {
 var func = [].shift.call(arguments); // 把实参列表的第一位截取出来，获得构造函数
 var instance = Object.create(func.prototype); // 声明一个上下文，原型为构造函数的原型
 func.apply(instance, arguments); // 执行该构造函数，并改变构造函数的 this 为 instance
 return instance; // 返回这个上下文
}
```

### ES6箭头函数中的this

```
// 箭头函数有几个使用注意点：
1. 函数体内的 this 对象，就是定义时所在的对象，而不是使用时所在的对象
2. 不可以当作构造函数，不可以使用 new 命令，否则会抛出一个错误
3. 不可以使用 arguments 对象，该对象在函数体内不存在
4. 不可以使用 yield 命令，因此箭头函数不能用作 Generator 函数

const car = {
  brand: 'Ford',
  model: 'Fiesta',
  start: function() {
    console.log(Started ${this.brand} ${this.model})
  },
  stop: () => {
    console.log(Stopped ${this.brand} ${this.model})
  }
}

car.start() // Started Ford Fiesta
car.stop() // Stopped undefined undefined

// 这是因为 this 的处理在两个函数声明样式中是不同的。
// 箭头函数中的 this 指的是封闭函数上下文，在本例中是window对象

// argumet 在箭头函数中不存在，那么使用 argument 将会使用外部函数的 argument
function foo() {
  setTimeout(() => {
    console.log('args:', arguments);
  }, 100);
}
foo(2, 4, 6, 8)
// args: [2, 4, 6, 8]
```

### IIFE，立即调用函数表达式

```
;(function dosomething() {
  console.log('executed')
})()

// 可以将结果分配给变量
const something = (function dosomething() {
  return 'something'
})()
```
