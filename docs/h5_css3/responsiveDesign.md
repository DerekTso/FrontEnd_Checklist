# HTML5基础 - 响应式布局

## 目录
- [像素(pixel)](#像素pixel)
- [视口(viewport)](#视口viewport)
- [px与自适应](#px与自适应)
- [媒体查询(media query)](#媒体查询media-query)
- [百分比](#百分比)
- [百分比单位布局应用](#百分比单位布局应用)
- [百分比单位缺点](#百分比单位缺点)
- [自适应场景下的rem解决方案](#自适应场景下的rem解决方案)
- [rem布局的缺点](#rem布局的缺点)
- [通过vw/vh来实现自适应](#通过vwvh来实现自适应)
- [使用流式浮动栅格布局](#使用流式浮动栅格布局)
- [流式浮动栅格布局的缺点](#流式浮动栅格布局的缺点)
- [meta标签](#meta标签)
- [使用 media query 适配对应样式](#使用-media-query-适配对应样式)

### 像素(pixel)

```
像素分为两种类型：css像素 和 物理像素

// css像素
我们在 js 或者 css 代码中使用的 px 单位就是指的是 css像素

// 物理像素
物理像素也称设备像素，只与设备/硬件有关，同样尺寸的屏幕，设备的密度越高，物理像素也就越多
```

### 视口(viewport)

```
// 设备像素比DPR(Device pixel ratio)
1 DPR = 物理像素／分辨率
在不缩放的情况下，一个css像素就对应一个dpr
1 CSS像素 = 物理像素／分辨率

// 在移动端的布局中，我们可以通过 viewport meta 标签来控制布局
<meta id="viewport" name="viewport" content="width=device-width;initial-scale=1.0;user-scalable=no;">
```

### px与自适应

```
// pc端的布局视口通常情况下为 980px，移动端以 iphone6 为例，分辨率为 375 * 667
// 比如现在我们有一个 750px * 1134px 的视觉稿，一个css像素可以如下计算：

// PC端
1 CSS像素 = 物理像素／分辨率 = 750 ／ 980 =0.76 px

// iphone6
1 CSS像素 = 物理像素 ／分辨率 = 750 ／ 375 = 2 px

也就是说在PC端，一个CSS像素可以用0.76个物理像素来表示
而在iphone6中，一个CSS像素表示了2个物理像素。

此外不同的移动设备分辨率不同，也就是1个CSS像素可以表示的物理像素是不同的
因此如果在css中仅仅通过px作为长度和宽度的单位，造成的结果就是无法通过一套样式，实现各端的自适应
```

### 媒体查询(media query)

```
//使用 @media 媒体查询可以针对不同的媒体类型定义不同的样式

@media screen and (max-width: 960px){
    body{
      background-color:#FF6699
    }
}

@media screen and (max-width: 768px){
    body{
      background-color:#00FF66;
    }
}

@media screen and (max-width: 550px){
    body{
      background-color:#6633FF;
    }
}

@media screen and (max-width: 320px){
    body{
      background-color:#FFFF00;
    }
}

通过设置 max-width 对分辨率在0～320px，320px～550px，550px～768px以及768px～960px的屏幕设置了不同的背景颜色

但是媒体查询的缺点也很明显，如果在浏览器大小改变时，需要改变的样式太多，那么多套样式代码会很繁琐
```

### 百分比

```
1. 子元素 height 和 width 的百分比

子元素的 height 或 width 中使用百分比，是相对于子元素的直接父元素

<div class="parent">
  <div class="child"></div>
</div>

.father{
    width:200px;
    height:100px;
}
.child{
    width:50%; // 100px
    height:50%; // 50px
}

2. top和bottom 、left和right

子元素的top和bottom、left和right 中使用百分比，是相对于子元素的非static定位(默认定位)的父元素

.father{
    width:200px;
    height:100px;
}
.child{
    top:50%; // 100px
    left:50%; // 50px
}

3. padding/margin

子元素的 padding/margin 中使用百分比，垂直/水平方向，都相对于直接父亲元素的 width，与父元素的 height 无关

.parent{
  width:200px;
  height:100px;
  background:green;
}
.child{
  width:0px;
  height:0px;
  background:blue;
  color:white;
  padding-top:50%; // 100px
  padding-left:50%; // 100px
}

4. border-radius/translate/background-size

border-radius/translate/background-size 等使用百分比，是相对于自身的宽度

<div class="trangle"></div>

.trangle{
  width:200px;
  height:100px;
  border-radius:50%; // 100px
  background:blue;
  margin-top:10px;
}
```

### 百分比单位布局应用

```
// 比如我们要实现一个固定长宽比(4:3)的长方形，我们可以设置 padding-top 为百分比来实现
// 因为 padding 不管是垂直方向还是水平方向，百分比单位都相对于父元素的宽度

<div class="trangle"></div>

.trangle{
  height:0;
  width:100%;
  padding-top:75%;
}
```

### 百分比单位缺点

```
1. 计算困难，如果我们要定义一个元素的宽度和高度，按照设计稿，必须换算成百分比单位
2. 各个属性中如果使用百分比，相对父元素的属性并不是唯一的。
    - width/height 相对于父元素的 width/height
    - padding/margin 相对比父元素的 width
    - border-radius 相对于元素自身
```

### 自适应场景下的rem解决方案

```
1. rem单位

rem是一个灵活的、可扩展的单位，由浏览器转化像素并显示
与em单位不同，rem单位无论嵌套层级如何，都只相对于浏览器的根元素(HTML元素)的font-size

// 默认情况下，html元素的 font-size 为16px，所以：
1 rem = 12px
// 为了计算方便，通常将html的font-size设置成：
html{ font-size: 62.5% }
// 这种情况下：
1 rem = 10px

2. 通过rem来实现响应式布局

// 只需要根据视图容器的大小，动态的改变 font-size 即可

function refreshRem() {
    var docEl = doc.documentElement;
    var width = docEl.getBoundingClientRect().width;
    var rem = width / 10; // 视图容器分为10份
    docEl.style.fontSize = rem + 'px'; // 动态定义font-size
    flexible.rem = win.rem = rem;
}
win.addEventListener('resize', refreshRem);

3. px2rem(px to rem)

// 预处理以px为单位的css文件，处理后将所有的px变成rem单位

3.1 webpack loader的形式：

npm install px2rem-loader

在webpack的配置文件中：
module.exports = {
  // ...
  module: {
    rules: [{
      test: /\.css$/,
      use: [
        {loader: 'style-loader'}, 
        {loader: 'css-loader'},
        {loader: 'px2rem-loader',
            options: {
            remUni: 75,
            remPrecision: 8
        }
      }]
    }]
  }
}

3.2 webpack中使用postcss plugin

npm install postcss-loader

在webpack的plugin中:
var px2rem = require('postcss-px2rem');
module.exports = {
  module: {
    loaders: [
      {
        test: /\.css$/,
        loader: "style-loader!css-loader!postcss-loader"
      }
    ]
  },
  postcss: function() {
    return [px2rem({remUnit: 75})];
  }
}
```

### rem布局的缺点

```
1. 在响应式布局中，必须通过js来动态控制根元素font-size的大小，css样式和js代码有一定的耦合性
2. 必须将改变font-size的代码放在css样式之前
```

### 通过vw/vh来实现自适应

```
css3中引入了一个新的单位vw/vh，与视图窗口有关
vw表示相对于视图窗口的宽度，vh表示相对于视图窗口高度
任意层级元素，在使用vw单位的情况下，1vw都等于视图宽度的百分之一

// 比如对于iphone6/7 375*667的分辨率，那么px可以通过如下方式换算成vw：
1px = （1/375）*100 vw

// 也可以通过postcss的相应插件，预处理css做一个自动的转换
// postcss-px-to-viewport的默认参数为：
var defaults = {
  viewportWidth: 320,
  viewportHeight: 568, 
  unitPrecision: 5,
  viewportUnit: 'vw',
  selectorBlackList: [],
  minPixelValue: 1,
  mediaQuery: false
};
```

### 使用流式浮动栅格布局

```
1. 栅格布局方便在不同屏幕设备中进行增删或者移动从而实现各种大小屏幕的适配2. 如小屏幕一栏，大屏幕三栏，每栏的宽度用流式布局（width使用百分比）进行适应
3. 使用浮动进行横排。最外层包裹元素一般加个max-width，防止pc大屏太宽

<main>
    <aside></aside>
    <article></article>
    <aside></aside>
</main>
<style>
    main{
        max-width:1200px;
    }
    aside{
        float:left;
        width: 20%;
    }
    article{
        float:left;
        width: 60%;
    }
    @media (max-width:481px) {
    aside {
        float: none;
        width: auto;
    }
    article{
        float: none;
        width: auto;
    }
}
</style>
```

### 流式浮动栅格布局的缺点

```
1. 重置盒子模型

web浏览器的盒子模型默认是不把边框和内边距计算在内容区的width中的（IE低版本除外）
所以要设置 box-sizing：border; 将边框和内边距计算进内容区的width中

2. HTML代码的顺序

为了增强用户体验，让移动端的用户第一眼就能看到主要内容，而不用下拉
所以HTML编码时，应该让主内容区放到最上层，要浮动时，加个外层，让主内容区和相邻侧边分别浮动到两边即可

<div>
    <article></article>
    <aside></aside>
</div>
<aside></aside>

div{
    float:left;
    width: 80%;
}
div article{
    float:right;
    width: 75%;
}
div aside{
    float:left;
    width: 25%
}
aside{
    float:left;
    width: 20%
}

3. 图片和视频的大小

图片也要用百分比，不然会溢出布局之外，一般用max-width：100%
当没有溢出布局时，图片保持原来大小不变，但要溢出布局时，限制图片的最大宽度为布局宽度
注意图片的高度不要设置，不然高度固定的话，图片的宽度变化会导致变形，高度不设置，让它随宽度自动做等比例变化即可

本来移动端要展示的是小图片，但却要下载大图片造成流量的浪费
解决的方法挺多的，比如img最新的srcset属性（有兼容性问题，IE完全不支持）
或者是用js判断获取不同的图片
```

### meta标签

```
// 使用 viewport meta 标签在手机浏览器上控制布局
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no"/>

width=device-width 让宽度为设备宽度
initial-scale=1    设置初始缩放比例
user-scalable=no   是否允许手动缩放页面，默认值为yes

// 通过快捷方式打开时全屏显示
<meta name="apple-mobile-web-app-capable" content="yes" />

// 隐藏状态栏
<meta name="apple-mobile-web-app-status-bar-style" content="blank" />

// iPhone会将看起来像电话号码的数字添加电话连接，应当关闭
<meta name="format-detection" content="telephone=no" />
```

### 使用 media query 适配对应样式

```
// 设备类型(media type):
all 所有设备；
screen 电脑显示器；
print 打印用纸或打印预览视图；
handheld 便携设备；
tv 电视机类型的设备；
speech 语意和音频盒成器；
braille 盲人用点字法触觉回馈设备；
embossed 盲文打印机；
projection 各种投影设备；
tty 使用固定密度字母栅格的媒介，比如电传打字机和终端。

// 设备特性(media feature):
width 浏览器宽度；
height 浏览器高度；
device-width 设备屏幕分辨率的宽度值；
device-height 设备屏幕分辨率的高度值；
orientation 浏览器窗口的方向纵向还是横向，当窗口的高度值大于等于宽度时该特性值为 portrait，否则为 landscape；
aspect-ratio 比例值，浏览器的纵横比；
device-aspect-ratio 比例值，设备屏幕的纵横比。

// 在样式表中使用
/** iPad **/
@media only screen and (min-width: 768px) and (max-width: 1024px) {}

/** iPhone **/
@media only screen and (min-width: 320px) and (max-width: 767px) {}

// 在连接时调用
<link href="styles.css" rel="stylesheet" media="(max-width:480px)">

// 使用@import导入
@import url(styles.css) (max-width:480px)
```
