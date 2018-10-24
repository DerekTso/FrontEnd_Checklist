# HTML5基础 - 拖放排序

## 目录
- [拖放事件](#拖放事件)
- [dataTransfer 对象](#datatransfer-对象)
- [实现拖放排序](#实现拖放排序)

### 拖放事件

1. 当该元素的 draggable 属性为 true，允许拖放，为 false 时，将不允许拖放
2. img 元素和 a 元素都默认设置了 draggable 属性为 true，可直接拖放

* 源对象(被拖放的元素)

    - ```dragstart```：源对象开始拖放
    - ```drag```：源对象拖放过程中
    - ```dragend```：源对象拖放结束

* 过程对象(被经过的元素)

    - ```dragenter```：源对象开始进入过程对象范围内
    - ```dragover```：源对象在过程对象范围内移动
    - ```dragleave```：源对象离开过程对象的范围

* 目标对象(到达的元素)

    - ```drop```：源对象被拖放到目标对象内

```
<div id="source" draggable="true">a元素</div>
<div id="process">b元素</div>
<div id="target">c元素</div>

<script>
    var source = document.getElementById('source'),     // a元素
        process = document.getElementById('process'),   // b元素
        target = document.getElementById('target');     // c元素
    
    source.addEventListener('dragstart',function(ev){
        // dragstart事件由a元素产生
        console.log('a元素开始被拖动');
    },false)

    process.addEventListener('dragenter',function(ev){
        // dragenter事件由b元素产生
        console.log('a元素开始进入b元素');
    },false)
    process.addEventListener('dragleave',function(ev){
        // dragleave事件由b元素产生
        console.log('a元素离开b元素');
    },false)

    target.addEventListener('drop',function(ev){
        // drop事件由c元素产生
        console.log('a元素拖放到c元素了');
        ev.preventDefault();
    },false)
    
    document.ondragover = function(e){e.preventDefault();}
</script>
```

### dataTransfer 对象

1. 在所有拖放事件中提供了一个数据传递对象 dataTransfer，用于在源对象和目标对象间传递数据

* setData()

1. 该方法向 dataTransfer 对象中存入数据
2. 接收两个参数，第一个表示要存入数据种类的字符串：
    - text/plain：文本文字
    - text/html：HTML 文字
    - text/xml：XML 文字
    - text/uri-list：URL 列表，每个 URL 为一行
3. 第二个参数为要存入的数据，例如：
    - ```event.dataTransfer.setData('text/plain','Hello World');```
4. 存储一个键值对
    - ```event.dataTransfer.setData('k','v');```

* getData()

1. 该方法从 dataTransfer 对象中读取数据
2. 参数为在 setData 中指定的数据种类，例如：
    - ```event.dataTransfer.getData('text/plain');```

* clearData()

1. 该方法清除 dataTransfer 对象中存放的数据
2. 参数可选，为数据种类
3. 若参数为空，则清空所有种类的数据，例如：
    - ```event.dataTransfer.clearData();```

* setDragImage()

1. 该方法通过用 img 元素来设置拖放图标
2. 接收三个参数，第一个为图标元素，第二个为图标元素离鼠标指针的 X 轴位移量，第三个为图标元素离鼠标指针的 Y 轴位移量，例如：
```
var source = document.getElementById('source'),
    icon = document.createElement('img');
icon.src = 'img.png';
source.addEventListener('dragstart',function(ev){
    ev.dataTransfer.setDragImage(icon,-10,-10)
},false);
```

* effectAllowed 和 dropEffect 属性

1. 这两个属性结合起来设置拖放的视觉效果
2. IE 不支持 dataTransfer 对象

* effectAllowed（指定拖动时被允许的效果）

```
copy: 复制到新的位置
move: 移动到新的位置 
link: 建立一个源位置到新位置的链接
copyLink: 允许复制或者链接
copyMove: 允许复制或者移动
linkMove: 允许链接或者移动
all: 允许所有的操作
none: 禁止所有操作
uninitialized: 默认值, 相当于 all
```

* dropEffect（设置实际的放置效果）

```
copy: 复制到新的位置
move: 移动到新的位置
link: 建立一个源位置到新位置的链接
none: 禁止放置（禁止任何操作）
```

### 实现拖放排序

1. 在一个列表中，每个元素都可以被拖放，那首先要给每个元素设置 draggable 属性为 true
2. 监听每个元素的 dragstart 事件，对源对象做样式处理来区分
3. 监听每个元素的 dragenter 事件，当源对象进入到当前元素里，就把源对象添加到该元素之前。这样子后面的元素就会被源对象挤下去了，实现了排序的效果
4. 但是会发现，源对象无法排到最后一个去，只能在倒数第二
5. 这时就要监听 dragleave 事件，当过程对象是最后一个元素时，源对象离开了过程对象，这时就把源对象添加到最后去

```
var source = document.querySelectorAll('.list'),
    dragElement = null;

for(var i = 0; i < source.length; i++){
    source[i].addEventListener('dragstart',function(ev){
        dragElement = this;
    },false);

    source[i].addEventListener('dragenter', function(ev){
        if(dragElement != this){
            this.parentNode.insertBefore(dragElement,this);
        }
    }, false);

    source[i].addEventListener('dragleave', function(ev){
        if(dragElement != this){
            if(this == this.parentNode.lastElementChild || this == this.parentNode.lastChild){
                this.parentNode.appendChild(dragElement);
            }
        }
    }, false);
};
document.ondragover = function(e){e.preventDefault();}
document.ondrop = function(e){e.preventDefault();}
```
