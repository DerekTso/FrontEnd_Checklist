# Javascript基础 - 事件流

## 目录
- [事件捕获(Event capturing) 和 事件冒泡(Event bubbling)](#事件捕获event-capturing-和-事件冒泡event-bubbling)
- [事件委托(Event delegation)](#事件委托event-delegation)
- [IE 的 attachEvent 存在的问题](#ie-的-attachevent-存在的问题)
- [addEventListener 和 attachEvent 的区别](#addeventlistener-和-attachevent-的区别)
- [阻止事件传播和默认行为](#阻止事件传播和默认行为)
- [跨浏览器的事件对象](#跨浏览器的事件对象)

### 事件捕获(Event capturing) 和 事件冒泡(Event bubbling)

```
事件捕获 是从 document 到触发事件的那个节点，即自上而下的去触发事件。
事件冒泡 是自下而上的去触发事件。
绑定事件方法的第三个参数，是控制事件触发顺序是否为事件捕获。默认false，即事件冒泡。

// 这是HTML结构
<div id="parent">
　　<div id="child" class="child"></div>
</div>

现在我们给它们绑定上事件
document.getElementById("parent").addEventListener("click",function(e){
    alert("parent事件被触发，"+this.id);
})
document.getElementById("child").addEventListener("click",function(e){
    alert("child事件被触发，"+this.id);
})

结果：
child事件被触发，child 
parent事件被触发，parent

结论：先child，然后parent。事件的触发顺序自内向外，这就是事件冒泡。

现在改变第三个参数的值为true
document.getElementById("parent").addEventListener("click",function(e){
    alert("parent事件被触发，"+e.target.id);
},true)
document.getElementById("child").addEventListener("click",function(e){
    alert("child事件被触发，"+e.target.id)
},true)

结果：
parent事件被触发，parent 
child事件被触发，child

结论：先parent,然后child。事件触发顺序变更为自外向内，这就是事件捕获。
```

### 事件委托(Event delegation)

```
// 需求是这样的：鼠标放到li上对应的li背景变灰。
<ul>
    <li>item1</li>
    <li>item2</li>
    <li>item3</li>
    <li>item4</li>
    <li>item5</li>
    <li>item6</li>
</ul>

// 不建议这样写，它遍历所有li节点，性能不好
$("li").on("mouseover",function(){
    $(this).css("background-color","#ddd").siblings().css("background-color","white");
})

还有就是，如果我们在绑定事件完成后，页面又动态的加载了一些元素
$("<li>item7</li>").appendTo("ul");

这时候，由于绑定事件的时候 item7 还不存在，所以为了效果，我们还要给它再绑定一次事件。

// 建议用事件委托。利用事件冒泡实现
$("ul").on("mouseover",function(e){
    $(e.target).css("background-color","#ddd").siblings().css("background-color","white");
})
// 注：e.target是触发事件的元素。

// 事件委托利用事件的冒泡特性，将事件绑定到外层元素上，而不是触发事件的元素上。
// 可以减少绑定的事件数量，而且对于动态加载的内容来说，十分有利。
// 事件委托不仅实现相同了功能，而且大大减少了DOM操作。

```

### IE 的 attachEvent 存在的问题

1. IE下的 attachEvent 方法不支持捕获，和传统事件注册没多大区别(除了能绑定多个事件处理函数)。
2. IE的 attachEvent 方法存在内存泄漏问题。
3. 使用 attachEvent 时在事件处理函数内部，this指向了window，而不是obj。
4. 同一个函数可以被注册到同一个对象同一个事件上多次。

### addEventListener 和 attachEvent 的区别

1. attachEvent 的事件处理程序会在全局作用域中运行，this等于window对象
2. addEventLinstener 添加的事件处理程序是在其依附的元素的作用域中运行的，this等于绑定元素对象

### 阻止事件传播和默认行为

```
function stopEvent(evt) {
    var evt = evt || window.event;
    if (evt.preventDefault) {
        evt.preventDefault();
        evt.stopPropagation();
    } else {
        evt.returnValue = false;
        evt.cancelBubble = true;
    }
}
```

### 跨浏览器的事件对象

```
var EventUtil={
    getEvent:function(event){
        return event||window.event;
    },
    getTarget:function(event){
        return event.target||event.srcElement;
    },
    preventDefault:function(){
        if(event.preventDefault){
            event.preventDefault();
        }else{
            event.returnValue=false;
        }
    },
    stopPropagation:function(){
        if(event.stopPropagation){
            event.stopPropagation();
        }else{
            event.cancelBubble=true;
        }
    },
    addHandler:function(element,type,handler){
        if(element.addEventListener){
            element.addEventListener(type,handler,false);
        }else if(element.attachEvent){
            element["e"+type]=function(){
              handler.call(element)
            }
            element.attachEvent("on"+type,element["e"+type]);
        }else{
            element["on"+type]=handler;
        }
    },
    removeHandler:function(element,type,handler){
        if(element.removeEventListener){
            element.removeEventListener(type,handler,false);
        }else if(element.detachEvent){
            element.detachEvent("on"+type,element["e"+type]);
            element["e"+type]=null;    
        }else{
            element["on"+type]=null;
        }
    }
};

//使用
var patty = document.getElementById("patty");
var handler = function(event){
   var event = EventUtil.getEvent(event);
   var target = EventUtil.getTarget(event);
   alert(target.id);
   EventUtil.stopPropagation(event);
}
EventUtil.addHandler(patty,'click',handler);
```