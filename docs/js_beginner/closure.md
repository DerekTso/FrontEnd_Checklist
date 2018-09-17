# Javascript基础 - 闭包

## 目录
- [闭包是什么](#闭包是什么)
- [内存管理](#内存管理)

### 闭包是什么

```
var func = function(){
    var a = 'linxin';
    var func1 = function(){
        a += ' a';
        console.log(a);
    }
    return func1;
}
var func2 = func();
func2();  // linxin a
func2();  // linxin a a
func2();  // linxin a a a

可以看出，在第一次调用完 func2 之后，func 中的变量 a 变成 'linxin a'，而没有被销毁。
因为此时 func1 形成了一个闭包，导致了 a 的生命周期延续了。

闭包是一个函数，比如上面的 func1 函数。
闭包使用其他函数定义的变量，使其不被销毁。比如上面 func1 调用了变量 a
闭包存在定义该变量的作用域中，变量 a 存在 func 的作用域中，那么 func1 也必然存在这个作用域中。
```

```
for (var i = 0; i < 4; i++) {
    setTimeout(function () {
        console.log(i)
    }, 0)
}

我们可能会简单的以为控制台会打印出 0 1 2 3，可事实却打印出了 4 4 4 4，这又是为什么呢？
我们发现，setTimeout 函数时异步的，等到函数执行时，for循环已经结束了，此时的 i 的值为 4。
所以 function() { console.log(i) } 去找变量 i，只能拿到 4。

// 我们可以用闭包把 0 1 2 3 保存起来
for (var i = 0; i < 4; i++) {
    (function (i) {
        setTimeout(function () {
            console.log(i)
        }, 0)
    })(i)
}

当 i=0 时，把 0 作为参数传进匿名函数中，此时匿名函数 function(i){} 中的 i 的值为 0。
等到 setTimeout 执行时，顺着外层去找 i，这时就能拿到 0。如此循环，就能拿到想要的 0 1 2 3。
```

### 内存管理

```
在闭包中调用局部变量，会导致这个局部变量无法及时被销毁，相当于全局变量一样会一直占用着内存。
如果需要回收这些变量占用的内存，可以手动将变量设置为null。

然而在使用闭包的过程中，比较容易形成 JavaScript 对象和 DOM 对象的循环引用，就有可能造成内存泄露。
这是因为浏览器的垃圾回收机制中，如果两个对象之间形成了循环引用，那么它们都无法被回收。
function func() {
    var test = document.getElementById('test');
    test.onclick = function () {
        console.log('hello world');
    }
}

在上面例子中，func 函数中用匿名函数创建了一个闭包。
变量 test 是 JavaScript 对象，引用了 id 为 test 的 DOM 对象，DOM 对象的 onclick 属性又引用了闭包，而闭包又可以调用 test。
因而形成了循环引用，导致两个对象都无法被回收。要解决这个问题，只需要把循环引用中的变量设为 null 即可。
function func() {
    var test = document.getElementById('test');
    test.onclick = function () {
        console.log('hello world');
    }
    test = null;
}

如果在 func 函数中不使用匿名函数创建闭包，而是通过引用一个外部函数，也不会出现循环引用的问题。
function func() {
    var test = document.getElementById('test');
    test.onclick = funcTest;
}
function funcTest(){
    console.log('hello world');
}
```