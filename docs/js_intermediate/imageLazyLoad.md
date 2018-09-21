# Javascript进阶 - 图片懒加载

## 目录
- [实现思路](#实现思路)
- [html 部分代码](#html-部分代码)
- [js 代码](#js-代码)

### 实现思路

1. 页面加载后只让文档可视区内的图片显示，其它不显示
2. 随着用户滚动页面，判断其区域位置，生成 img 标签，让到可视区的图片加载出来
3. 对 window.onscroll 进行节流处理

### html 部分代码

```
<ul id="imgList">
    <li data-src="1.jpg"></li>
    <li data-src="2.jpg"></li>
    <li data-src="3.jpg"></li>
    <li data-src="4.jpg"></li>
    <li data-src="5.jpg"></li>
    <li data-src="6.jpg"></li>
    <li data-src="7.jpg"></li>
    <li data-src="8.jpg"></li>
    <li data-src="9.jpg"></li>
    <li data-src="10.jpg"></li>
</ul>
```

### js 代码

```
// 获取节点    
var oUl = document.getElementById('imgList');
var liTags = oUl.getElementsByTagName('li');
// 创建 img 标签函数
function createImg(oLi){
    var src = '';
    if(oLi.dataset.src){
        src = oLi.dataset.src;
    }else{
        src = oLi.getAttribute('data-src');
    }
    if(oLi.children.length <= 1){
        var img = document.createElement('img');
        img.src = src;
        oLi.appendChild(img);
    }
}
// 获取节点到文档顶部的距离，直到节点再没有父级为止
function getElementTopDistance(oLi){
  var h = 0;
  while(oLi){
      h += oLi.offsetTop;
      oLi = oLi.offsetParent;
  }
  return h;
}
// 节流函数
function throttle(fun,delay){
    return function() {
if(isTimeToRun) {
    isTimeToRun = false
    setTimeout(function(){
fun()
isTimeToRun = true
    }, delay)
}
    }
}
// 图片懒加载
function imageLazyLoad(){
    var t = document.documentElement.clientHeight + (document.body.scrollTop || document.documentElement.scrollTop);
    for(var i=0,len=liTags.length;i<len;i++){
// 当滚动距离大于节点到顶部距离时，创建一个 img
        if(getElementTopDistance(liTags[i]) < t){
            createImg(liTags[i])
        }
    }
}
var isTimeToRun = true
window.onscroll = throttle(imageLazyLoad, 500)
// 页面加载完，便执行一次滚动函数
window.onload = function(){
    window.onscroll();
}
```
