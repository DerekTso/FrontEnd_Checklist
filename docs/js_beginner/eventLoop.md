# Javascript基础 - 事件循环

## 目录
- [JavaScript Runtime 的运行机制](#javascript-runtime-的运行机制)
- [事件队列(Event Queue)](#事件队列event-queue)

### JavaScript Runtime 的运行机制

1. 主线程不断循环；
2. 对于同步任务，创建执行上下文（Execution Context），按顺序进入执行栈（参考 Calling scripts）；
3. 对于异步任务：
    - 与步骤 2 相同，同步执行这段代码；
    - 将相应的 Macrotask（或 Microtask）添加到 Event Loop 的任务队列；
    - 由其他线程来执行具体的异步操作。
        - 其他线程是指：尽管 JavaScript 是单线程的，但浏览器内核是多线程的，它会将 GUI 渲染、定时器触发、HTTP 请求等工作交给专门的线程来处理。
        - 另外，在 Node.js 中，异步操作会优先由 OS 或第三方系统提供的异步接口来执行，然后才由线程池处理。
4. 当主线程执行完当前执行栈中的所有任务，就会去读取 Event Loop 的任务队列，取出并执行任务；
5. 重复以上步骤

```
console.log('1');

// 记作 set1
setTimeout(function () {
    console.log('2');
    // set4
    setTimeout(function() {
        console.log('3');
    });
    // pro2
    new Promise(function (resolve) {
        console.log('4');
        resolve();
    }).then(function () {
        console.log('5')
    })
})

// 记作 pro1
new Promise(function (resolve) {
    console.log('6');
    resolve();
}).then(function () {
    console.log('7');
    // set3
    setTimeout(function() {
        console.log('8');
    });
})

// 记作 set2
setTimeout(function () {
    console.log('9');
    // 记作 pro3
    new Promise(function (resolve) {
        console.log('10');
        resolve();
    }).then(function () {
        console.log('11');
    })
})
// 结果：1 6 7 2 4 5 9 10 11 8 3
```

* 第一轮事件循环：
1. 整体script作为第一个宏任务进入主线程，遇到console.log，输出1。
2. 遇到set1，其回调函数被分发到宏任务Event Queue中。
3. 遇到pro1，new Promise直接执行，输出6。then被分发到微任务Event Queue中。
4. 遇到set2，其回调函数被分发到宏任务Event Queue中。
5. 主线程的整段js代码（宏任务）执行完，开始清空所有微任务；主线程执行微任务pro1，输出7；遇到set3，注册回调函数。

* 第二轮事件循环：
1. 主线程执行队列中第一个宏任务set1，输出2；代码中遇到set4，注册回调；又遇到了pro2，new promise()直接执行输出4，并注册回调；
2. set1宏任务执行完毕，开始清空微任务，主线程执行微任务pro2，输出5。

* 第三轮事件循环：
1. 主线程执行队列中第一个宏任务set2，输出9；代码中遇到了pro3，new promise()直接输出10，并注册回调；
2. set2宏任务执行完毕，开始情况微任务，主线程执行微任务pro3，输出11。

类似循环...
所以最后输出结果为(1、6、7)、(2、4、5)、(9、10、11)、(8、3)。

### 事件队列(Event Queue)

事件队列中分“宏任务队列(macrotask)”和“微任务队列(microtask)”。
每执行一次任务都可能注册新的宏任务或微任务到相应的任务队列中。
只要遵循“每执行一个宏任务，就会清空一次事件队列中的所有微任务”这一循环规则，就不会弄乱。

```
// 以下事件属于宏任务：
setInterval()
setTimeout()
xhr()
I/O
script代码块

// 以下事件属于微任务：
new Promise()
new MutaionObserver()
process.nextTick
```