# Javascript高阶 - Vue Two-Way Data Binding

## 目录
- [mvvm双向绑定](#mvvm双向绑定)
- [思路分析](#思路分析)
- [实现Observer](#实现observer)
- [实现Dep](#实现dep)
- [实现Compile](#实现compile)
- [实现Watcher](#实现watcher)

### mvvm双向绑定

- 数据变化更新视图，视图变化更新数据
- 视图变化更新数据可以通过事件监听即可
- 数据变化更新视图的重点是通过 ```Object.defineProperty()``` 对属性设置一个 ```set``` 函数，当数据变化了就会来触发这个函数

### 思路分析

1. 对数据进行劫持监听，设置一个监听器 ```Observer```，用来监听所有属性
2. 如果属性发上变化了，就告诉订阅者 ```Watcher```，看是否需要更新
3. 因为订阅者有很多个，需要一个消息订阅器 ```Dep``` 来专门收集这些订阅者，然后在监听器 ```Observer``` 和订阅者 ```Watcher``` 之间进行统一管理的
4. 通过指令解析器 ```Compile```，对每个节点元素进行扫描和解析，将相关指令对应初始化成一个订阅者 ```Watcher```，并替换模板数据或者绑定相应的函数
5. 当订阅者 ```Watcher``` 接收到相应属性的变化，就会执行对应的更新函数，从而更新视图
![js_advanced_Vue_Two-Way_Data_Binding](../../images/js_advanced_Vue_Two-Way_Data_Binding.png)

### 实现Observer

```
var data = {name: 'kindeng'};
observe(data);
data.name = 'dmq'; // 哈哈哈，监听到值变化了 kindeng --> dmq

function observe(data) {
 if (!data || typeof data !== 'object') {
  return;
 }
 // 取出所有属性遍历
 Object.keys(data).forEach(function(key) {
  defineReactive(data, key, data[key]);
 });
};

function defineReactive(data, key, val) {
 observe(val); // 监听子属性
 Object.defineProperty(data, key, {
  enumerable: true, // 可枚举
  configurable: false, // 不能再define
  get: function() {
   return val;
  },
  set: function(newVal) {
   console.log('哈哈哈，监听到值变化了 ', val, ' --> ', newVal);
   val = newVal;
  }
 });
}
```

### 实现Dep

```
// 维护一个数组，用来收集订阅者，数据变动触发 notify ，再调用订阅者的 update 方法

// Observer.js
// ... 省略
function defineReactive(data, key, val) {
 var dep = new Dep();
 observe(val); // 监听子属性

 Object.defineProperty(data, key, {
  get: function() {
    // 由于需要在闭包内添加watcher，所以通过Dep定义一个全局target属性，暂存watcher, 添加完移除
    Dep.target && dep.addDep(Dep.target);
    return val;
    }
  set: function(newVal) {
   if (val === newVal) return;
   console.log('哈哈哈，监听到值变化了 ', val, ' --> ', newVal);
   val = newVal;
   dep.notify(); // 通知所有订阅者
  }
 });
}

function Dep() {
 this.subs = [];
}
Dep.prototype = {
 addSub: function(sub) {
  this.subs.push(sub);
 },
 notify: function() {
  this.subs.forEach(function(sub) {
   sub.update();
  });
 }
};

// Watcher.js
Watcher.prototype = {
 get: function(key) {
  Dep.target = this;
  this.value = data[key]; // 这里会触发属性的getter，从而添加订阅者
  Dep.target = null;
 }
}
```

### 实现Compile

```
// compile主要做的事情是解析模板指令，将模板中的变量替换成数据
// 然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数
// 添加监听数据的订阅者，一旦数据有变动，收到通知，更新视图

// 因为遍历解析的过程有多次操作dom节点，为提高性能和效率，会先将跟节点el转换成文档碎片fragment进行解析编译操作
// 解析完成，再将fragment添加回原来的真实dom节点中

function Compile(el) {
 this.$el = this.isElementNode(el) ? el : document.querySelector(el);
 if (this.$el) {
  this.$fragment = this.node2Fragment(this.$el);
  this.init();
  this.$el.appendChild(this.$fragment);
 }
}
Compile.prototype = {
 init: function() { this.compileElement(this.$fragment); },
 node2Fragment: function(el) {
  var fragment = document.createDocumentFragment(), child;
  // 将原生节点拷贝到fragment
  while (child = el.firstChild) {
   fragment.appendChild(child);
  }
  return fragment;
 }
};

// compileElement方法将遍历所有节点及其子节点，进行扫描解析编译
// 调用对应的指令渲染函数进行数据渲染，并调用对应的指令更新函数进行绑定

Compile.prototype = {
 // ... 省略
 compileElement: function(el) {
  var childNodes = el.childNodes, me = this;
  [].slice.call(childNodes).forEach(function(node) {
   var text = node.textContent;
   var reg = /\{\{(.*)\}\}/; // 表达式文本
   // 按元素节点方式编译
   if (me.isElementNode(node)) {
    me.compile(node);
   } else if (me.isTextNode(node) && reg.test(text)) {
    me.compileText(node, RegExp.$1);
   }
   // 遍历编译子节点
   if (node.childNodes && node.childNodes.length) {
    me.compileElement(node);
   }
  });
 },

 compile: function(node) {
  var nodeAttrs = node.attributes, me = this;
  [].slice.call(nodeAttrs).forEach(function(attr) {
   // 规定：指令以 v-xxx 命名
   // 如 <span v-text="content"></span> 中指令为 v-text
   var attrName = attr.name; // v-text
   if (me.isDirective(attrName)) {
    var exp = attr.value; // content
    var dir = attrName.substring(2); // text
    if (me.isEventDirective(dir)) {
     // 事件指令, 如 v-on:click
     compileUtil.eventHandler(node, me.$vm, exp, dir);
    } else {
     // 普通指令
     compileUtil[dir] && compileUtil[dir](node, me.$vm, exp);
    }
   }
  });
 }
};

// 指令处理集合
var compileUtil = {
 text: function(node, vm, exp) {
  this.bind(node, vm, exp, 'text');
 },
 // ...省略
 bind: function(node, vm, exp, dir) {
  var updaterFn = updater[dir + 'Updater'];
  // 第一次初始化视图
  updaterFn && updaterFn(node, vm[exp]);
  // 实例化订阅者，此操作会在对应的属性消息订阅器中添加了该订阅者watcher
  new Watcher(vm, exp, function(value, oldValue) {
   // 一旦属性值有变化，会收到通知执行此更新函数，更新视图
   updaterFn && updaterFn(node, value, oldValue);
  });
 }
};

// 更新函数
var updater = {
 textUpdater: function(node, value) {
  node.textContent = typeof value == 'undefined' ? '' : value;
 }
 // ...省略
};
```

### 实现Watcher

```
function Watcher(vm, exp, cb) {
 this.cb = cb;
 this.vm = vm;
 this.exp = exp;
 // 此处为了触发属性的getter，从而在dep添加自己，结合Observer更易理解
 this.value = this.get(); 
}
Watcher.prototype = {
 update: function() {
  this.run(); // 属性值变化收到通知
 },
 run: function() {
  var value = this.get(); // 取到最新值
  var oldVal = this.value;
  if (value !== oldVal) {
   this.value = value;
   this.cb.call(this.vm, value, oldVal); // 执行Compile中绑定的回调，更新视图
  }
 },
 get: function() {
  Dep.target = this; // 将当前订阅者指向自己
  var value = this.vm[exp]; // 触发getter，添加自己到属性订阅器中
  Dep.target = null; // 添加完毕，重置
  return value;
 }
};
// 这里再次列出Observer和Dep，方便理解
Object.defineProperty(data, key, {
 get: function() {
  // 由于需要在闭包内添加watcher，所以可以在Dep定义一个全局target属性，暂存watcher, 添加完移除
  Dep.target && dep.addDep(Dep.target);
  return val;
 }
 // ... 省略
});
Dep.prototype = {
 notify: function() {
  this.subs.forEach(function(sub) {
   sub.update(); // 调用订阅者的update方法，通知变化
  });
 }
};
```
