# Javascript基础 - 防抖和节流

## 目录
- [函数防抖(debounce)](#函数防抖debounce)
- [函数节流(throttle)](#函数节流throttle)
- [总结](#总结)
- [结合应用场景](#结合应用场景)

### 函数防抖(debounce)

> 当调用动作过n毫秒后，才会执行该动作，若在这n毫秒内又调用此动作则将重新计算执行时间
```
// 在窗口滚动过程中会连续打印出很多的 hello world
window.onscroll = function () {
    console.log('hello world');
};
```
```
// 防抖函数
const debounce = (fn, wait = 0) => {
    let inDebounce;
    return function () {
        const context = this;
        const args = arguments;
        clearTimeout(inDebounce);
        inDebounce = setTimeout(() => fn.apply(context, args), wait);
    };
};
// 在窗口滚动后 250ms 后执行 console.log('hello world')
window.addEventListener(
    'scroll',
    debounce(function() {
        console.log('hello world');
    }, 250)
)
```

```
// 模拟一段ajax请求
function ajax(content) {
  console.log('ajax request ' + content)
}

function debounce(fun, delay) {
    return function () {
        const context = this
        const args = arguments
        clearTimeout(fun.id)
        fun.id = setTimeout(function () {
            fun.apply(context, args)
        }, delay)
    }
}

let inputb = document.getElementById('debounce')
let debounceAjax = debounce(ajax, 500)

inputb.addEventListener('keyup', function (e) {
    debounceAjax(e.target.value)
})

我们加入了防抖以后，当你在频繁的输入时，并不会发送请求。
只有当你在指定间隔内没有输入时，才会执行函数。
如果停止输入但是在指定间隔内又输入，会重新触发计时。 
```

```
let biu = function () {
    console.log('biu biu biu',new Date().Format('HH:mm:ss'))
}

let boom = function () {
    console.log('boom boom boom',new Date().Format('HH:mm:ss'))
}

setInterval(debounce(biu,500),1000)
setInterval(debounce(boom,2000),1000)

如果在时间间隔内执行函数，会重新触发计时。
biu会在第一次1.5s执行后，而boom一次也不会执行。
因为它的时间间隔是2s，而执行时间是1s，所以每次都会重新触发计时

函数防抖就是法师发技能的时候要读条，技能读条没完再按技能就会重新读条。
```

### 函数节流(throttle)

```
规定在一个单位时间内，只能触发一次函数。
如果这个单位时间内触发多次函数，只有一次生效。

function throttle(fun, delay) {
    let last, deferTimer
    return function (args) {
        let that = this
        let _args = arguments
        let now = +new Date()
        if (last && now < last + delay) {
            clearTimeout(deferTimer)
            deferTimer = setTimeout(function () {
                last = now
                fun.apply(that, _args)
            }, delay)
        }else {
            last = now
            fun.apply(that,_args)
        }
    }
}

let throttleAjax = throttle(ajax, 1000)

let inputc = document.getElementById('throttle')
inputc.addEventListener('keyup', function(e) {
    throttleAjax(e.target.value)
})

我们在不断输入时，ajax会按照我们设定的时间，每1s执行一次。
```

```
let biubiu = function () {
    console.log('biu biu biu', new Date().Format('HH:mm:ss'))
}

setInterval(throttle(biubiu,1000),10)

不管我们设定的执行时间间隔多小，总是1s内只执行一次。

函数节流就是fps游戏的射速，就算一直按着鼠标射击，也只会在规定射速内射出子弹。
```

### 总结

函数防抖和函数节流都是防止某一时间频繁触发，但是这两兄弟之间的原理却不一样。

函数防抖是某一段时间内只执行一次，而函数节流是间隔时间执行。

### 结合应用场景

* debounce

1. search搜索联想，用户在不断输入值时，用防抖来节约请求资源。
2. window触发resize的时候，不断的调整浏览器窗口大小会不断的触发这个事件，用防抖来让其只触发一次


* throttle

1. 鼠标不断点击触发，mousedown(单位时间内只触发一次)
2. 监听滚动事件，比如是否滑到底部自动加载更多，用throttle来判断
