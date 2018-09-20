# Javascript基础 - ES6之Proxy

## 目录
- [使用语法](#使用语法)
- [has](#has)
- [get](#get)
- [set](#set)
- [apply](#apply)
- [construct](#construct)
- [创建可解除 Proxy 对象](#创建可解除-proxy-对象)

### 使用语法

```
用法：new Proxy(target, handler)

const obj = {};
const proxy = new Proxy(obj, {
    // ...
})

1. 第一个参数为需要进行包装的目标对象
2. 第二个参数为用于监听目标对象行为的监听器
3. 监听器可以接受一些参数以监听相对应的程序行为

has(target, prop) 监听 in 语句的使用
get(target, prop, receiver) 监听目标对象的属性读取
set(target, prop, value, receiver) 监听目标对象的属性赋值
apply(target, thisArg, arguments) 监听目标函数(作为目标对象)的调用行为
construct(target, arguments, newTarget) 监听目标构造函数(作为目标对象)利用 new 而生成实例的行为

defineProperty(target, property, descriptor) 监听 Object.defineProperty() 的调用
getPrototypeOf(target) 监听 Object.getPrototypeOf() 的读取
setPrototypeOf(target, prototype) 监听 Object.setPrototypeOf() 的调用
deleteProperty(target, prop) 监听 delete 语句对目标对象的删除属性行为
getOwnPropertyDescriptor(target, prop) 监听 Object.getOwnPropertyDescriptor() 的调用
ownKeys(target) 监听 Object.getOwnPropertyName() 的读取

isExtensible(target) 监听 Object.isExtensible() 的读取
preventExtensions(target) 监听 Object.preventExtensions() 的读取

// Object.preventExtensions() 禁用属性拓展: 
1. 该对象无法再增加新的属性
2. 只能对当前已有的属性进行操作，包括读取、操作和删除
3. 但是一旦删除就无法再定义
```

### has

```
const p = new Proxy({}, {
    has(target, prop){
      console.log(`Checking "${prop}" is in the target or not`);
      return true;
    }
})
console.log('foo' in p);
// Checking "foo" is in the target or not
// true

1. 当目标对象被其他程序通过 Object.preventExtensions() 禁用了属性拓展功能，且被检查的属性键确实存在与目标对象中，该监听方法便不能返回 false

const obj = {foo: 1};
Object.preventExtensions(obj);
const p = new Proxy(obj, {
    has(target, prop){
      console.log(`Checking "${prop}" is in the target or not`);
      return false;
    }
})
console.log('foo' in p);    
// TypeError: 'has' on proxy: trap returned falsish for property 'foo' but the proxy target is not extensible

2. 当被检查的属性键存在与目标对象中，且该属性的 configurable 配置是 false 时，该监听方法不能返回 false

const obj = {};
Object.defineProperty(obj, 'foo', {
    configurable: false,
    value: 10
})
const p = new Proxy(obj, {
    has(target, prop){
      console.log(`Checking "${prop}" is in the target or not`);
      return false;
    }
})
console.log('foo' in p);
// TypeError: 'has' on proxy: trap returned falsish for property 'foo' which exists in the proxy target as non-configurable
```

### get

```
const obj = {foo: 1};
const p = new Proxy(obj, {
    get(target, prop){
      console.log(`Program is trying to fetch the property "${prop}".`);
      return target[prop];
    }
})
p.foo; // Program is trying to fetch the property "foo".
p.something; // Program is trying to fetch the property "something".

1. 当目标对象被读取属性的 configurable 和 writable 属性都为 false 时，监听方法最后返回的值必须与目标对象的原属性值一致

const obj = {};
Object.defineProperty(obj, 'foo', {
    configurable: false,
    enumberable: false,
    value: 10,
    writable: false
})
const p = new Proxy(obj, {
    get(target, prop){
      return 20;
    }
})
console.log(p.foo);
// TypeError: 'get' on proxy: property 'foo' is a read-only and non-configurable data property on the proxy target but the proxy did not return its actual value (expected '10' but got '20')
```

### set

```
const obj = {};
const p = new Proxy(obj, {
    set(target, prop, value){
      console.log(`Setting value "${value}" on the key "${prop}" in the target object`);
      target[prop] = value;
      return true;
    }
})
p.foo = 1;  
// Setting value "1" on the key "foo" in the target object

1. 如果目标对象自身的某个属性是不可写也不可配置的，那么 set 不得改变这个属性的值，只能返回同样的值，否则报错

const obj = {};
Object.defineProperty(obj, 'foo', {
    configurable: false,
    enumberable: false,
    value: 10,
    writable: false
})
const p = new Proxy(obj, {
    set(target, prop, value){
      console.log(`Setting value "${value}" on the key "${prop}" in the target object`);
      target[prop] = value;
      return true;
    }
})
p.foo = 1;  
console.log(p.foo);
// TypeError: 'set' on proxy: trap returned truish for property 'foo' which exists in the proxy target as a non-configurable and non-writable data property with a different value
```

### apply

```
const sum = function(...args){
    return args
              .map(Number)
              .filter(Boolean)
              .reduce((a, b) => a + b);
}
const p = new Proxy(sum, {
    apply(target, thisArg, args){
      console.log(`Function is being called with arguments [${args.join()}] and context ${thisArg}`);
      return target.call(thisArg, ...args);
    }
})
console.log(p(1, 2, 3));
// Function is being called with arguments [1,2,3] and context undefined
// 6
```

### construct

```
class Foo{};
const p = new Proxy(Foo, {
    construct(target, args, newTarget){
      return {arguments: args}
    }
})
const obj = new p(1, 2, 3);
console.log(obj.arguments);  // [1, 2, 3]
```

### 创建可解除 Proxy 对象

```
用法：Proxy.revocable(target, handler) : (proxy, revoke)

1. Proxy.revocable(target, handler) 会返回一个带有两个属性的对象
2. 其中一个 proxy 便是该函数所生成的可解除 Proxy 对象
3. 另一个 revoke 则是将刚才的 Proxy 对象解除的解除方法

const obj = {foo: 10};
const revocable = Proxy.revocable(obj, {
    get(target, prop){
      return 20;
    }
})
const proxy = revocable.proxy;
console.log(proxy.foo); // 20

revocable.revoke();
console.log(proxy.foo); 
// TypeError: Cannot perform 'get' on a proxy that has been revoked
```
