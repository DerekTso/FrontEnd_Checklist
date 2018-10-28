# Javascript进阶 - 浏览器兼容性

## 目录
- [不同浏览器的标签默认的外边距和内边距不同](#不同浏览器的标签默认的外边距和内边距不同)
- [块属性标签float后，又有横行的margin情况下，在IE6显示margin比设置的大](#块属性标签float后又有横行的margin情况下在ie6显示margin比设置的大)
- [设置较小高度标签，在IE6，IE7，遨游中高度超出自己设置高度](#设置较小高度标签在ie6ie7遨游中高度超出自己设置高度)
- [行内属性标签，设置display:block后采用float布局，又有横行的margin的情况，IE6间距bug](#行内属性标签设置displayblock后采用float布局又有横行的margin的情况ie6间距bug)
- [图片默认有间距](#图片默认有间距)
- [标签最低高度设置min-height不兼容](#标签最低高度设置min-height不兼容)
- [透明度的兼容CSS设置](#透明度的兼容css设置)
- [解决多个div对齐时的间距不对问题](#解决多个div对齐时的间距不对问题)
- [CSS兼容](#CSS兼容)
- [针对firefox ie6 ie7的css样式](#针对firefox-ie6-ie7的css样式)
- [css布局中的居中问题](#css布局中的居中问题)
- [浮动ie产生的双倍距离](#浮动ie产生的双倍距离)
- [IE与宽度和高度的问题](#IE与宽度和高度的问题)
- [页面的最小宽度](#页面的最小宽度)
- [清除浮动](#清除浮动)
- [DIV浮动IE文本产生3象素的bug](#DIV浮动IE文本产生3象素的bug)
- [div ul li 的嵌套顺序](#div-ul-li-的嵌套顺序)
- [去掉ie有默认最低高度](#去掉ie有默认最低高度)
- [超链接点击过后hover样式就不出现的问题](#超链接点击过后hover样式就不出现的问题)
- [FF下文本无法撑开容器的高度](#FF下文本无法撑开容器的高度)
- [设置IE浏览器滚动条颜色](#设置IE浏览器滚动条颜色)
- [如何定义1px左右高度的容器](#如何定义1px左右高度的容器)
- [怎样使一个div层居中于浏览器中](#怎样使一个div层居中于浏览器中)
- [FF中嵌套div标签的居中问题的解决方法](#FF中嵌套div标签的居中问题的解决方法)
- [浏览器的内核](#浏览器的内核)
- [CSS兼容问题](#CSS兼容问题)

### 不同浏览器的标签默认的外边距和内边距不同

问题症状：随便写几个标签，不加样式控制的情况下，各自的margin和padding差异较大

解决方案：CSS里用通配符 * 来设置各个标签的内外边距是0

### 块属性标签float后，又有横行的margin情况下，在IE6显示margin比设置的大

问题症状：在IE6中后面的一块被顶到下一行

解决方案：在float的标签样式控制中加入```display:inline;```将其转化为行内属性

### 设置较小高度标签，在IE6，IE7，遨游中高度超出自己设置高度

问题症状：IE6、7和遨游里这个标签的高度不受控制，超出自己设置的高度

解决方案：给超出高度的标签设置```overflow:hidden;```或者设置行高```line-height;```小于你设置的高度

备注：这种情况一般出现在我们设置小圆角背景的标签里。出现这个问题的原因是IE8之前的浏览器都会给标签一个最小默认的行高的高度。即使你的标签是空的，这个标签的高度还是会达到默认的行高

### 行内属性标签，设置display:block后采用float布局，又有横行的margin的情况，IE6间距bug

问题症状：IE6里的间距超过设置的间距

解决方案：在```display:block;```后面加入```display:inline;display:table;```

备注：行内属性标签，为了设置宽高，我们需要设置display:block;(除了input标签比较特殊)。在用float布局并有横向的margin后，在IE6下，它就具有了块属性float后的横向margin的bug。不过因为它本身就是行内属性标签，所以我们再加上display:inline的话，它的高宽就不可设了。这时候我们还需要在display:inline后面加入display:table

### 图片默认有间距

问题症状：几个img标签放在一起的时候，有些浏览器会有默认的间距，加了CSS的通配符也不起作用

解决方案：使用float属性为img布局

备注：因为img标签是行内属性标签，所以只要不超出容器宽度，img标签都会排在一行里，但是部分浏览器的img标签之间会有个间距。去掉这个间距使用float是正道。（使用负margin，虽然能解决，但负margin本身就是容易引起浏览器兼容问题的用法，所以禁止使用）

### 标签最低高度设置min-height不兼容

问题症状：因为min-height本身就是一个不兼容的CSS属性，所以设置min-height时不能很好的被各个浏览器兼容

解决方案：如果我们要设置一个标签的最小高度200px，需要进行的设置为：```{min-height:200px; height:auto !ImportAnt; height:200px; overflow:visible;}```

备注：当内容小于一个值（如300px）时，容器的高度为300px；当内容高度大于这个值时，容器高度被撑高，而不是出现滚动条。这时候我们就会面临这个兼容性问题。

### 透明度的兼容CSS设置

很多兼容性问题都是因为浏览器对标签的默认属性解析不同造成的，只要我们稍加设置都能轻松地解决这些兼容问题。如果我们熟悉标签的默认属性的话，就能很好的理解为什么会出现兼容问题以及怎么去解决这些兼容问题。

### 解决多个div对齐时的间距不对问题 

在写HTML代码的时候，发现在Firefox等符合W3C标准的浏览器中，如果有一个DIV作为外部容器，内部的DIV如果设置了float样式，则外部的容器DIV因为内部没有clear，导致不能被撑开。

这个clearfix的CSS使用了after这个伪对象，它将在应用clearfix的元素的结尾添加content中的内容。在这里添加了一个句号"."，并且把它的display设置成block；高度设为0；clear设为both；visibility设为hidden。这样就达到了撑开容器的目的。

```
<style> 
/* Clear Fix */ 
.clearfix:after { 
    content: "."; 
    display: block; 
    height: 0; 
    clear: both; 
    visibility: hidden; 
}
.clearfix { 
    display:inline-block; 
} 
/* Hide from IE Mac \*/
.clearfix {display:block;}
/* End hide from IE Mac */
/* end of clearfix */
</style>
```

### CSS兼容

IE6认识的hacker是下划线 _ 和星号 *

IE7认识的hacker是星号 *

* !important(不是很推荐)

随着IE7对!important的支持，!important 方法现在只针对IE6的兼容(声明位置需要提前)

```
<style> 
#wrapper { 
    width: 100px!important; /* IE7+FF */ 
    width: 80px; /* IE6 */ 
} 
</style> 
```

* IE6/IE7 对 FireFox

```
// *+html 与 *html 是IE特有的标签，firefox 暂不支持
// 而 *+html 为 IE7 特有标签
<style> 
#wrapper { width: 120px; } /* FireFox */ 
*html #wrapper { width: 80px;} /* ie6 fixed */ 
*+html #wrapper { width: 60px;} /* ie7 fixed， 注意顺序 */ 
</style> 
```

注意: 
*+html 对IE7的兼容，必须保证HTML顶部有如下声明： 

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTDHTML 4.01 Transitional//EN"　"http://www.w3.org/TR/html4/loose.dtd">
```

### 针对firefox ie6 ie7的css样式 

```
height:100px; // IE FF 所有浏览器 公用
*height:120px; // IE6 IE7公用 
_height:150px; // IE6专用
```

```
#myDiv { color: #333; } /* FF */ 
* html #myDiv { color: #666; } /* IE6 */ 
*+html #myDiv { color: #999; } /* IE7 */ 
```

### css布局中的居中问题 
 
```
body {TEXT-ALIGN: center;} 
#center { MARGIN-RIGHT: auto; MARGIN-LEFT: auto; } 
```

首先在父级元素定义 ```TEXT-ALIGN: center;``` 是在父级元素内的内容居中；对于IE这样设定就已经可以了。 

但在mozilla中不能居中。解决办法就是在子元素定义时候设定时再加上```MARGIN-RIGHT: auto;MARGIN-LEFT: auto;```

### 浮动ie产生的双倍距离 

```
<style type="text/css"> 
    body {margin:0;} 
    div {float:left; margin-left:10px; width:200px; height:200px; border:1px solid red;} 
</style> 
```

浮动后本来外边距10px，但IE解释为20px，解决办法是加上```display:inline;```

```
#box{
    float:left;
    width:100px;
    margin:0 0 0 100px; //这种情况之下IE会产生200px的距离display:inline; //使浮动忽略
}
```

* block元素的特点是

总是在新行上开始，高度，宽度，行高，边距都可以控制(块元素);

* inline元素的特点是

和其他元素在同一行上，高度，宽度，行高，边距都不可控制(内嵌元素);

```
#box{
    display:block; //可以为内嵌元素模拟为块元素
    display:inline; //实现同一行排列的的效果
    diplay:table;
}
```

### IE与宽度和高度的问题 

IE不认得min-这个定义，如果只用min-width和min-height的话，IE下面等于没有设置宽度和高度。比如要设置背景图片，这个宽度是比较重要的。

要解决这个问题，可以这样： 
```
#box{
    width: 80px;
    height: 35px;
}
html>body #box{
    width: auto;
    height: auto;
    min-width: 80px;
    min-height: 35px;
}
```

### 页面的最小宽度 

IE不认得min-width，而它实际上把width当做最小宽度来使。

要解决这个问题，可以这样： 
```
#container{ 
    min-width: 600px; 
    width:expression(document.body.clientWidth < 600? "600px": "auto" ); 
}
```

第一个min-width是正常的；但第2行的width使用了Javascript，这只有IE才认得，这也会让你的HTML文档不太正规。它实际上通过Javascript的判断来实现最小宽度。

### 清除浮动 

```
.box{ 
    display:table; //将对象作为块元素级的表格显示 
}
```

```
.box{ 
    clear:both; 
}
```

```
#box:after{ 
    content: “.”; 
    display: block; 
    height: 0; 
    clear: both; 
    visibility: hidden; 
}
```

### DIV浮动IE文本产生3象素的bug 

左边对象浮动，右边采用外边距的左边距来定位，右边对象内的文本会离左边有3px的间距. 

```
// HTML代码
<DIV id=box> 
    <DIV id=left></DIV> 
    <DIV id=right></DIV> 
</DIV>

#box{
    float:left; 
    width:800px;
}
#left{
    float:left; 
    width:50%;
}
#right{
    width:50%; 
}
*html #left{ 
    margin-right:-3px;
}
```

### div ul li 的嵌套顺序 

```<div><ul><li></li></ul></div>```
```ul {margin:0;padding:0;list-style:none;}```

### 去掉ie有默认最低高度

```
<div style="height:2px;background:red;overflow:hidden;"></div>
```

其中height:2px为你要设的高度，overflow:hidden最为关键，它就是帮你去掉默认高度的

### 超链接点击过后hover样式就不出现的问题

被点击访问过的超链接样式不再具有hover和active样式了，解决方法是改变CSS属性的排列顺序: L-V-H-A (link visited hover active)

### FF下文本无法撑开容器的高度

```
div { height:auto!important; height:200px; min-height:200px; }
```

### 设置IE浏览器滚动条颜色

<style type="text/css"> 
    html {
 scrollbar-face-color:#f6f6f6; scrollbar-highlight-color:#fff; scrollbar-shadow-color:#eeeeee; scrollbar-3dlight-color:#eeeeee; scrollbar-arrow-color:#000; scrollbar-track-color:#fff; scrollbar-darkshadow-color:#fff;
    } 
</style>

### 如何定义1px左右高度的容器

IE6下这个问题是因为默认的行高造成的，解决的方法也有很多，例如：overflow:hidden | zoom:0.08 | line-height:1px

### 怎样使一个div层居中于浏览器中

```
<style type="text/css"> 
div {
    position:absolute; 
    top:50%; 
    left:50%; 
    margin:-100px 0 0 -100px; 
    width:200px; 
    height:200px; 
    border:1px solid red; 
}
</style> 
```
这里使用百分比绝对定位，与外边距负值的方法，负值的大小为其自身宽度高度除以二

### FF中嵌套div标签的居中问题的解决方法

```
<div id="a"> 
    <div id="b"></div> 
</div>
```

如果要实现b在a中居中放置，一般只需用CSS设置a的text-align属性为center。这样的方法在IE里看起来一切正常；但是在Firefox中b却会是居左的。

解决办法就是设置b的横向margin为auto```margin: 0 auto;```


### 浏览器的内核

Mozilla Firefox ( Gecko )

Internet Explorer ( Trident )

Opera ( Presto )

Safari ( WebKit )

Google Chrome ( WebKit )

解决ie7、ie8兼容性最好的办法是在head标签中加入meta 类型
```<meta http-equiv="X-UA-Compatible" content="IE=EmulateIE7" />```，只要IE8一读到这个标签，它就会自动启动IE7兼容模式

现在剩下ie6、ie7、Firefox、Chrome(Safari与Chrome使用同一内核)、Opera这几种浏览器的兼容性问题，我们需要使用CSS Hack来解决该问题。

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

<html>
<head>
<meta http-equiv="Content-Type"content="text/html; charset=utf-8"/>
<meta http-equiv="X-UA-Compatible" content="IE=EmulateIE7"/>

<title>浏览器兼容性问题</title>
<style type="text/css">
.t1
{
    color:#000000; /*所有浏览器都支持 此处填写Firefox的css*/
    *color:#0000FF;/* ie6 ie7 支持此处填写ie7的css*/
    _color:#66CCCC;/* ie6支持  此处填写ie6的css*/
}

@media all and (-webkit-min-device-pixel-ratio:10000)，not all and (-webkit-min-device-pixel-ratio:0)
{
    .t1{color:#9900FF} /* oprea支持  此处填写oprea的css*/
}

@media screen and (-webkit-min-device-pixel-ratio:0)
{
    .t1{color:#336600} /* Chrome、Safari支持  此处填写Chrome的css*/
}
</style>

</head>
<body>
<div class="t1">兼容性css</div>
</body>
</html>
```

### CSS兼容问题

1. 默认的内外边距不同

```*{margin:0;padding:0;}```

2. 水平居中的问题

设置 text-align: center  

ie6-7 文本居中，嵌套的块元素也会居中

ff/opera/safari/ie8 文本会居中，嵌套块不会居中

块元素设置
```margin:0 auto;```

3. 垂直居中的问题

给容器设置一个与其高度相同的行高: ```line-height```与容器的```height```一样

4. 关于高度问题

如果设定了高度，内容过多时，ie6下会自动增加高度、其他浏览器会超出边框

设置```overflow:hidden;```

高度自增```height:auto!important; height:100px;```

5. IE6 默认的div高度

ie6默认div高度为一个字体显示的高度，所在ie6下div的高度大于等于一个字的高度，因此在ie6下定义高度为1px的容器，显示的是一个字体的高度

设置```overflow:hidden;```

设置```line-height:1px;```

设置```zoom:0.08```

6. li前设置图片时，图片与其后的文字对齐问题

1、采用背景定位 和 字符缩进的方法
```
background:url() no-repeat left center;text-index:16px;
```

2、采用相对定位方法

li 设置 ```list-style:url();```

li的子元素 ```position:relative;top:-5px;```

7. ul设置float后，在ie中margin将变大

设置ul的display:inline，li的list-style-position:outside

8. li设置float后，在ie中margin将变大

设置li的display:inline

9. 嵌套使用ul、li的问题

ie的bug，嵌套使用ul、li时，里层的li设置float以后，外层li不设置float，里面的ul顶部和它外面的li总是有一段间距

设置里面的ul的zoom:1

10. li中有a且设置display:block时，ie6中列表间会出现空隙

1、li中加display:inline;

2、li使用float然后clear:both;

11. 当父元素设置position:relative;时，在ie6中第一个ul、ol、dl的背景颜色失效

ul、ol、dl 都设置为position:relative;
 
12. li中的内容以省略号显示

li中内容超过长度时，想以省略号显示，此方法适用于ie6-7-8、opera、safari浏览器，ff浏览器不支持

```
li{
    width:200px;
    white-space:nowrap;
    text-overflow:ellipsis;
    -o-text-overflow:ellipsis;
    overflow: hidden;
}
```

13. 隐藏滚动条

1、只有ie6-7支持```<body scroll="no">```

2、除ie6-7不支持 body{overflow:hidden}

3、所有浏览器 html{overflow:hidden}

14. 在IE下，文档没有载入完时，在body上appendChild会出现空白页面的问题

FF的 body 在 body 标签没有被浏览器完全读入之前就存在，而IE则必须在 body 完全被读入之后才存在。

一切在body上插入节点的动作，全部在onload后进行

15. innerText 在IE中能正常工作，但在FireFox中却不行

在非IE浏览器中使用textContent代替innerText

16. FF没有setCapture()、releaseCapture()方法

```
IE:
obj.setCapture(); 
obj.releaseCapture();

FF:
window.captureEvents(Event.MOUSEMOVE|Event.MOUSEUP);
window.releaseEvents(Event.MOUSEMOVE|Event.MOUSEUP);

if (!window.captureEvents) {
    obj.setCapture();
}else {
    window.captureEvents(Event.MOUSEMOVE|Event.MOUSEUP);
}
if (!window.captureEvents) {
    obj.releaseCapture();
}else {
    window.releaseEvents(Event.MOUSEMOVE|Event.MOUSEUP);
}
```
