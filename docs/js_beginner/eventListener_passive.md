# Javascript基础 - 事件监听器之Passive

## 目录
- [Event Listener](#event-listener)
- [Chrome浏览器的一些概念](#chrome浏览器的一些概念)
- [Passive Event Listener](#passive-event-listener)
- [如何调用 removeEventListener](#如何调用-removeeventlistener)

### Event Listener

```
// 很久以前，addEventListener() 的参数约定是这样的：
addEventListener(type, listener, useCapture)

// 后来，最后一个参数（控制监听器是在 捕获阶段 执行还是在 冒泡阶段 执行的 useCapture 参数），变成了可选参数：
addEventListener(type, listener[, useCapture ])

// 再后来 addEventListener() 的第三个参数可以是个对象值了
// 也就是说第三个参数现在可以是两种类型的值了：
addEventListener(type, listener[, useCapture ])
addEventListener(type, listener[, options ])

// 目前规范中 options 对象可用的属性有三个：
addEventListener(type, listener, {
    capture: false,
    passive: false,
    once: false
})

// 三个属性都是布尔类型的开关，默认值都为 false
// capture 属性等价于以前的 useCapture 参数
// once 属性表明该监听器是一次性的，执行一次后就被自动 removeEventListener 掉，还没有浏览器实现它
// passive 属性的值为 true 的时候，代表该监听器内部不会调用 preventDefault 函数来阻止默认滑动行为。目前 Chrome 主要利用该特性来优化页面的滑动性能，当前仅支持 mousewheel/touch 相关事件
```

### Chrome浏览器的一些概念

1. 绘制（Paint）：将绘制操作进行光栅化转换成为图像的过程
2. 图层（Paint Layer）：在Chrome中，页面的绘制是分层绘制的，页面内容变化的时候，浏览器仅需要重新绘制内容变化的图层，没有变化的图层不需要重新绘制；
3. 合成（Composite）：将绘制好的图层图像混合在一起，生成一张最终的图像，显示在屏幕上的过程；
4. 渲染（Render）：可以简单认为渲染等价于 绘制 + 合成；
5. UI线程（UI Thread）：浏览器的主线程，负责接收系统派发给浏览器窗口的事件、资源下载等；
6. 内核线程（Main/Render Thread）：Blink内核及V8引擎运行的线程，如DOM树构建、元素布局、绘制（main-thread side）、JavaScript执行等逻辑在该线程中执行；
7. 合成线程（Compositor Thread）：负责图像合成的线程，如绘制，合成等逻辑在该线程中执行

### Passive Event Listener

移动端的页面都会监听 ```touchstart``` 等 touch 事件，像这样：
```
document.addEventListener("touchstart", function(e){
    ... // 浏览器不知道这里会不会有 e.preventDefault()
})
```

1. 由于 ```touchstart``` 事件对象的 ```cancelable``` 属性为 true，也就是说它的默认行为可以被监听器通过 ```preventDefault()``` 方法阻止
2. 它的默认行为通常来说就是滚动当前页面（还可能是缩放页面）
3. 如果它的默认行为被阻止了，页面就必须静止不动
4. 但浏览器无法预先知道一个监听器会不会调用 ```preventDefault()```，它能做的只有等监听器执行完后再去执行默认行为
5. 而监听器执行是要耗时的，有些甚至耗时很明显，这样就会导致页面卡顿
6. 有 80% 的滚动事件监听器是不会阻止默认行为的，浏览器知道了一个监听器是 passive 的，它就可以在两个线程里同时执行监听器中的 JavaScript 代码和浏览器的默认行为
7. 在一个 passive 的监听器里执行了 ```preventDefault()```，```preventDefault()``` 不会产生任何效果，同时，浏览器的开发者工具也会发出警告：```Unable to preventDefault inside passive event listener invocation```
8. 对于在同一个 DOM 对象身上添加的同一类型事件的监听器，只要有一个不是 ```passive``` 的，那浏览器就无法优化

### 如何调用 removeEventListener

```
addEventListener("foo", listener, true)
=> removeEventListener("foo", listener, true)

addEventListener("foo", listener, {capture: true})
=> removeEventListener("foo", listener, {capture: true})

addEventListener("foo", listener, {once: true})
=> removeEventListener("foo", listener)

addEventListener("foo", listener, {passive: true})
=> removeEventListener("foo", listener)
```

```
// 一个监听器同时是 passive 和 非passive（以及同时是 once 和 非once），后添加的不算
addEventListener("foo", listener, {passive: true})
addEventListener("foo", listener, {passive: false}) // 这句不算

addEventListener("bar", listener, {passive: false})
addEventListener("bar", listener, {passive: true})  // 这句不算
```
