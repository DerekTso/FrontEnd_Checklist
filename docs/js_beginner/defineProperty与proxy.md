# Javascript基础 - defineProperty与proxy

## 目录
- [Object.defineProperty](#objectdefineproperty)
    - [不能监听数组的变化](#不能监听数组的变化)
    - [必须遍历对象的每个属性](#必须遍历对象的每个属性)
    - [必须深层遍历嵌套的对象](#必须深层遍历嵌套的对象)
- [Proxy](#proxy)
    - [针对对象](#针对对象)
    - [支持数组](#支持数组)
    - [嵌套支持](#嵌套支持)
- [Proxy 优势和劣势](#proxy-优势和劣势)
- [应用场景](#应用场景)
    - [多继承](#多继承)
    - [隐藏私有变量](#隐藏私有变量)
    - [对象属性的设定时校验](#对象属性的设定时校验)
    - [各类容错检查](#各类容错检查)

### Object.defineProperty

#### 不能监听数组的变化

```
let arr = [1,2,3]
let obj = {}
Object.defineProperty(obj, 'arr', {
  get () {
    console.log('get arr')
    return arr
  },
  set (newVal) {
    console.log('set', newVal)
    arr = newVal
  }
})
obj.arr.push(4) // 只会打印 get arr, 不会打印 set
obj.arr = [1,2,3,4] // 这个能正常 set
```

```
变异方法 (mutation method):
push、pop、shift、unshift、splice、sort、reverse

非变异方法 (non-mutating method):
filter、concat、slice 等

// Vue 把变异方法重写来实现数组的劫持

const aryMethods = ['push', 'pop', 'shift', 'unshift', 'splice', 'sort', 'reverse'];
const arrayAugmentations = [];
aryMethods.forEach((method)=> {

// 这里是原生 Array 的原型方法
let original = Array.prototype[method];

// 将 push, pop 等封装好的方法定义在对象 arrayAugmentations 的属性上
// 注意：是实例属性而非原型属性
arrayAugmentations[method] = function () {
    console.log('我被改变啦!');

    // 调用对应的原生方法并返回结果
    return original.apply(this, arguments);
  };
});
let list = ['a', 'b', 'c'];
// 将我们要监听的数组的原型指针指向上面定义的空数组对象
// 这样就能在调用 push, pop 这些方法时走进我们刚定义的方法，多了一句 console.log
list.__proto__ = arrayAugmentations;
list.push('d');  // 我被改变啦！
// 这个 list2 是个普通的数组，所以调用 push 不会走到我们的方法里面。
let list2 = ['a', 'b', 'c'];
list2.push('d');  // 不输出内容
```

#### 必须遍历对象的每个属性

```
// 使用 Object.defineProperty() 多数要配合 Object.keys() 和遍历

Object.keys(obj).forEach(key => {
  Object.defineProperty(obj, key, {
    // ...
  })
})
```

#### 必须深层遍历嵌套的对象

```
// 类似以下的嵌套对象，必须逐层遍历
// 直到把每个对象的每个属性都调用 Object.defineProperty() 为止

let obj = {
  info: {
    name: 'eason'
  }
}
```

### Proxy

#### 针对对象

```
// Proxy 是针对 整个对象，而不是 对象的某个属性
// 不需要对 keys 进行遍历
// 另外 Reflect.get 和 Reflect.set 可以理解为类继承里的 super，即调用原来的方法

let obj = {
  name: 'Eason',
  age: 30
}
let handler = {
  get (target, key, receiver) {
    console.log('get', key)
    return Reflect.get(target, key, receiver)
  },
  set (target, key, value, receiver) {
    console.log('set', key, value)
    return Reflect.set(target, key, value, receiver)
  }
}
let proxy = new Proxy(obj, handler)
proxy.name = 'Zoe' // set name Zoe
proxy.age = 18 // set age 18
```

#### 支持数组

```
let arr = [1,2,3]
let proxy = new Proxy(arr, {
    get (target, key, receiver) {
console.log('get', key)
return Reflect.get(target, key, receiver)
    },
    set (target, key, value, receiver) {
console.log('set', key, value)
return Reflect.set(target, key, value, receiver)
    }
})
proxy.push(4)
// get push     (寻找 proxy.push 方法)
// get length   (获取当前的 length)
// set 3 4      (设置 proxy[3] = 4)
// set length 4 (设置 proxy.length = 4)
```

#### 嵌套支持

```
// 本质上，Proxy 也是不支持嵌套的，这点和 Object.defineProperty() 是一样的
// 需要通过逐层遍历来解决: 在 get 里面递归调用 Proxy 并返回

let obj = {
  info: {
    name: 'eason',
    blogs: ['webpack', 'babel', 'cache']
  }
}
let handler = {
  get (target, key, receiver) {
    console.log('get', key)
    // 递归创建并返回
    if (typeof target[key] === 'object' && target[key] !== null) {
      return new Proxy(target[key], handler)
    }
    return Reflect.get(target, key, receiver)
  },
  set (target, key, value, receiver) {
    console.log('set', key, value)
    return Reflect.set(target, key, value, receiver)
  }
}
let proxy = new Proxy(obj, handler)
// 以下两句都能够进入 set
proxy.info.name = 'Zoe'
proxy.info.blogs.push('proxy')
```

### Proxy 优势和劣势

- Proxy 的第二个参数可以有 13 种拦截方法，这比起 Object.defineProperty() 要更加丰富
- Proxy 的兼容性不如 Object.defineProperty()
- Proxy 不能使用 polyfill 来处理兼容性

### 应用场景

#### 多继承

```
let foo = {
  foo () {
    console.log('foo')
  }
}
let bar = {
  bar () {
    console.log('bar')
  }
}
// 正常状态下，对象只能继承一个对象，要么有 foo()，要么有 bar()
let sonOfFoo = Object.create(foo);
sonOfFoo.foo();     // foo
let sonOfBar = Object.create(bar);
sonOfBar.bar();     // bar

let sonOfFooBar = new Proxy({}, {
  get (target, key) {
    return target[key] || foo[key] || bar[key];
  }
})
// 我们创造了一个对象同时继承了两个对象，foo() 和 bar() 同时拥有
sonOfFooBar.foo();   // foo 有foo方法，继承自对象foo
sonOfFooBar.bar();   // bar 也有bar方法，继承自对象bar
```

#### 隐藏私有变量

```
function getObject(rawObj, privateKeys) {
  return new Proxy(rawObj, {
    get (target, key, receiver) {
      if (privateKeys.indexOf(key) !== -1) {
throw new ReferenceError(`${key} 是私有属性，不能访问。`)
      }

      return target[key]
    }
  })
}
let rawObj = {
  name: 'Zoe',
  age: 18,
  isFemale: true
}
let obj = getObject(rawObj, ['age'])
console.log(obj.name) // Zoe
console.log(obj.age) // 报错
```

#### 对象属性的设定时校验

```
let person = {
  name: 'Eason',
  age: 30
}
let handler = {
  set (target, key, value, receiver) {
    if (key === 'name' && typeof value !== 'string') {
      throw new Error('用户姓名必须是字符串类型')
    }
    if (key === 'age' && typeof value !== 'number') {
      throw new Error('用户年龄必须是数字类型')
    }
    return Reflect.set(target, key, value, receiver)
  }
}
let personForUser = new Proxy(person, handler)
personForUser.name = 'Zoe' // OK
personForUser.age = '18' // 报错
```

#### 各类容错检查

```
// 故意设置一个错误的 data1，即 response.data = undefined
let response = {
  data1: {
    message: {
      from: 'Eason',
      text: 'Hello'
    }
  }
}
// 也可以根据 key 的不同给出更友好的提示
let dealError = key => console.log('Error key', key)
let isOK = obj => !obj['HAS_ERROR']
let handler = {
  get (target, key, receiver) {
    // 基本类型直接返回
    if (target[key] !== undefined && typeof target[key] !== 'object') {
      return Reflect.get(target, key, receiver)
    }

    // 如果是 undefined，把访问的的 key 传递到错误处理函数 dealError 里面
    if (!target[key]) {
      if (!target['HAS_ERROR']) {
dealError(key)
      }
      return new Proxy({HAS_ERROR: true}, handler)
    }

    // 正常的话递归创建 Proxy
    return new Proxy(target[key], handler)
  }
}
let resp = new Proxy(response, handler)
if (isOK(resp.data.message.text) && isOK(resp.data.message.from)) {
  console.log(`你收到了来自 ${response.data.message.from} 的信息：${response.data.message.text}`)
}
```
