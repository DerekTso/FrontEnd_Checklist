# HTML5基础 - WebWorker

## 目录
- [什么是 Web Worker](#什么是-web-worker)
- [Web Worker 实例](#web-worker-实例)
- [SharedWorker](#sharedworker)

### 什么是 Web Worker

1. web worker 是运行在后台的 JavaScript，独立于其他脚本，不会影响页面的性能
2. web worker 通常不用于简单的脚本，而是用于更耗费 CPU 资源的任务
3. Web Worker 无法访问 DOM 节点、全局变量或是全局函数、window、document 之类的浏览器全局变量、无法调用 alert() 或者 confirm 之类的函数

### Web Worker 实例

```
// 引用外部脚本
importScripts('aaa.js', 'bbb.js', 'ccc.js');

var w;
function startWorker() {
    if(typeof(Worker) !== "undefined") {
        if(typeof(w) == "undefined") {
            w = new Worker("demo_workers.js");
        }
        // 接收处理服务器端的信息
        w.onmessage = function(event) {
            document.getElementById("result").innerHTML = event.data;
        };
    } else {
        document.getElementById("result").innerHTML = " 抱歉，你的浏览器不支持 Web Workers...";
    }
}
function stopWorker() 
{
    // 终止 web worker，并释放浏览器 / 计算机资源
    w.terminate();
    w = undefined;
}
```

### SharedWorker

```
// SharedWorker 需要用到 port 属性，接收需要先 connect

// 主线程
worker.port.onmessage = function(e){}
worker.port.postMessage('data');
 
// 子线程
addEventListener('connect', function(event){
    var port = event.ports[0]
    //接收
    port.onmessage = function(event){
        console.log(event.data);
    };
    //发送
    port.postMessage("data");
    port.start();
});
```
