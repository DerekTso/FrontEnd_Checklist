# Javascript基础 - 原型

## 目录
- [prototype 属性](#prototype-属性)
- [实例内部指针__proto__](#实例内部指针__proto__)
- [函数的原型对象](#函数的原型对象)
- [Javascript原型链](#javascript原型链)

### prototype 属性

```
function Person(){
}

Person: function Person(){}
  prototype: Person
    constructor: function Person(){}
    __proto__: Object

Person 对象会自动获得 prototype 属性，而 prototype 也是一个对象，会自动获得一个 constructor 属性，该属性正是指向 Person 对象。
```

### 实例内部指针__proto__

```
function Person(name){
    this.name=name;
}

Person.prototype.printName=function(){
    alert(this.name);
}

var person1 = new Person('Byron');
var person2 = new Person('Frank');

当调用构造函数创建一个实例的时候，实例内部将包含一个内部指针（很多浏览器这个指针名字为__proto__）指向构造函数的 prototype。

Person: function Person(name){}
  person1: Person
    name: "Byron"
    __proto__: Person
    constructor: function Person(name){}
    printName: function(){}
    __proto__: Object

Person 的实例 person1 中包含了 name 属性，同时自动生成一个__proto__属性。
该属性指向 Person 的 prototype，可以访问到 prototype 内定义的 printName 方法。
```

### 函数的原型对象

```
prototype 和 __proto__ 都指向原型对象，任意一个函数（包括构造函数）都有一个 prototype 属性，指向该函数的原型对象

任意一个构造函数实例化的对象，都有一个 __proto__ 属性（或[[Prototype]]属性)，可通过 Object.getPrototypeOf() 方法访问该属性。
__proto__ 属性指向构造函数的原型对象。

function a() {
    //console.log("I'am a function.");
}
// b是实例化对象，a是构造函数
var b = new a();
console.log(b.__proto__ == a.prototype);  //true
console.log(Object.getPrototypeOf(b) == a.prototype);  //true

// 注意：a作为构造函数时的 prototype 属性与a作为普通函数时的 __proto__ 属性并不相等
console.log(a.prototype == a.__proto__);//false
```

### Javascript原型链

```
function a () {}
typeof a.prototype //"object"
typeof a.__proto__ //"function"
typeof a.__proto__.__proto__ //"object"
typeof a.prototype.__proto__ //"object"

console.log(a.__proto__);   //function (){}
console.log(a.__proto__ == Function.prototype);//true

console.log(a.prototype);   //a{}
console.log(a.prototype.__proto__);  //Object{}

console.log(a.__proto__.__proto__); //Object{}
console.log(a.__proto__.__proto__ == a.prototype.__proto__); //true
```
![Javascript原型链](../images/prototype.png)