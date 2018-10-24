# HTML5基础 - 前端开发规范

## 目录
- [HTML规范](#html规范)
    - [所有html代码小写](#所有html代码小写)
    - [使用html5的doctype](#使用html5的doctype)
    - [页面编码使用utf-8](#页面编码使用utf-8)
    - [页面内容lang为zh-Hans-CN](#页面内容lang为zh-hans-cn)
    - [IE compatibility mode](#ie-compatibility-mode)
    - [页面头部说明注释](#页面头部说明注释)
    - [include语句的地址为相对地址](#include语句的地址为相对地址)
    - [文件命名](#文件命名)
    - [图片格式/大小](#图片格式大小)
    - [图片引用](#图片引用)
    - [图片命名](#图片命名)
    - [图片服务器](#图片服务器)
    - [注释](#注释)
    - [脚本中文转unicode](#脚本中文转unicode)
    - [去掉CSS/JS类型属性](#去掉cssjs类型属性)
    - [去掉自闭合元素'/'闭合符](#去掉自闭合元素闭合符)
    - [对"<" ">"之类的符号进行实体转义](#对-之类的符号进行实体转义)
    - [元素嵌套规范](#元素嵌套规范)
- [CSS规范](#css规范)
    - [命名规范](#命名规范)
    - [以字母开头](#以字母开头)
    - [全小写，并使用 '-' 连字符](#全小写并使用---连字符)
    - [命名应简约而不失语义](#命名应简约而不失语义)
    - [文件命名举列](#文件命名举列)
    - [常用类/ID命名举列](#常用类id命名举列)
    - [Reset参考](#reset参考)
    - [css代码风格](#css代码风格)
    - [css属性书写顺序](#css属性书写顺序)
    - [css浏览器私有前缀书写格式](#css浏览器私有前缀书写格式)
    - [用CSS控制交互或视觉的变化，JS只需要增减className](#用css控制交互或视觉的变化js只需要增减classname)
    - [css注释](#css注释)
- [JS规范](#js规范)
    - [命名规范](#命名规范)
    - [代码格式](#代码格式)

### HTML规范

#### 所有html代码小写

```<ul class="news-list"><li></li><li></li></ul>```

#### 使用html5的doctype

```<!doctype html>```

#### 页面编码使用utf-8

```<meta charset="utf-8">```

#### 页面内容lang为zh-Hans-CN

```<html lang="zh-Hans-CN">```

```
// 这条规范基于国际标准RFC 4646
// 使用单一的 zh 和 zh-CN 均属于废弃用法
// 与中文有关的 lang 值如下：

zh-Hans 简体中文
zh-Hans-CN 大陆地区使用的简体中文
zh-Hans-HK 香港地区使用的简体中文
zh-Hans-MO 澳门使用的简体中文
zh-Hans-SG 新加坡使用的简体中文
zh-Hans-TW 台湾使用的简体中文
zh-Hant 繁体中文
zh-Hant-CN 大陆地区使用的繁体中文
zh-Hant-HK 香港地区使用的繁体中文
zh-Hant-MO 澳门使用的繁体中文
zh-Hant-SG 新加坡使用的繁体中文
zh-Hant-TW 台湾使用的繁体中文
```

#### IE compatibility mode

```
// 告诉IE浏览器，IE8/9都会以IE8引擎来渲染页面
<meta http-equiv="X-UA-Compatible" content="IE=8">

// 告诉IE浏览器，IE8/9及以后的版本都会以最高版本IE来渲染页面
<meta http-equiv="X-UA-Compatible" content="IE=edge">
```

#### 页面头部说明注释

必须在 head 区域中加上对页面相关人员注释，方便在产品环境中的查看

```
<!-- 页面设计：xxx | 页面制作：xxx | 团队博客：http://xxx.com/ -->
```

#### include语句的地址为相对地址

```
<!--#include virtual="/xxxx.html" -->
```

#### 文件命名

1. 文件名中只可由英文字母a~z、排序数字0~9或间隔符 - 组成
2. 文件名中禁止包含特殊符号，比如空格、$等
3. 文件名区分大小写，统一使用小写字母
4. 为更好的表达语义，文件名使用英文名词命名，或英文简写

```
index.shtml 引导页&首页
main.shtml 首页
download.shtml 下载页面
act.shtml 活动列表页面
video.shtml 视频
cdkey.shtml CDKEY页面
wallpaper.shtml 壁纸页面
```

#### 图片格式/大小

1. 图片格式：图片允许采用gif/jpg/png/webp
2. 平衡图片质量与文件大小，适当运用 css sprite 理念合并修饰类图片
3. 不过分损失质量情况下尽量减小页面下载数据量
4. 图片单张体积不能超过150K，jpg图片必须压缩，一般60%品质即可，如果图片质量不好，可提高到80%

#### 图片引用

1. 图片路径需要使用绝对路径方式
2. 所有img元素必须加上alt属性，修饰性图片alt属性内容留空，内容性质图片alt属性写相应内容
3. 必须加上width和height属性，值为它的原大小，但不要用来对它进行缩放
```
<img src="http://xxx.com/images/logo.png" width="172" height="72" alt="xxx">
```

#### 图片命名

1. 图片后缀命名一律小写
2. 使用间隔符```-```进行连接
3. 背景图片以```bg-```开头 e.g. ```bg-body.jpg```
4. 测试图片以```img-```开头 e.g. ```img-promo.jpg```
5. 按钮图片以```btn-```开头 e.g. ```btn-submit.png```
6. 图标图片以```icon-```开头 e.g. ```icon-game.png```
7. 聚合图以```spr-```开头 e.g. ```spr-home.png```
8. 后跟英文单词，不推荐使用汉语拼音，如果名称过长，适当使用缩写

#### 图片服务器

图片必须上传至图片服务器，CSS文件和JS文件可以按需处理

#### 注释

1. 感叹号后面2个横线，结束时2个横线
2. 不要在注释内容中使```--```，```--```只能发生在XHTML注释的开头和结束
3. 在内容中它们不再有效
4. 可以用等号或者空格替换内部的虚线
```
<!--这里是注释============这里是注释-->
```
5. IE条件注释（IE10已不支持条件注释）
```
<!-- [if IE]>
这里只有ie浏览器才可以显示
<![endif]-->
 
<!-- [if !IE]>
这里只有非ie浏览器才可以显示
<! <![endif]-->
 
<!--[if IE 6]>
这里只有ie6浏览器才可以显示
<![endif]-->
 
<!--[if lt IE 9]>
这里只有ie9以下浏览器才可以显示
<![endif]-->
 
<!--[if lte IE 8]>
这里只有ie8以及ie8以下浏览器才可以显示
<![endif]-->
```

#### 脚本中文转unicode

为防止外链脚本未申明正确编码导致乱码的问题，脚本中如用到中文，必须转为unicode码
```document.write("\u5173\u4e8e\u6e38\u620f")```

#### 去掉CSS/JS类型属性

1. 不要为CSS、JS使用类型属性，特别说明类型（type）属性是多余的
2. 在HTML5中默认已包含
```
<!-- External CSS -->
<link rel="stylesheet" href="code-guide.css">

<!-- In-document CSS -->
<style>
    /* ... */
</style>

<!-- JavaScript -->
<script src="code-guide.js"></script>
```

#### 去掉自闭合元素'/'闭合符

1. 为半闭合元素加上'/'闭合符是没必要的
2. 申明为html5的doctype后，所有浏览器都会正确处理
3. 常见的半闭合元素：```img``` ```input``` ```hr``` ```br```
```
<br ><hr ><img src="/wiki/" ><input type="text" >
```

#### 对"<" ">"之类的符号进行实体转义

1. 在HTML中不能使用小于号（<）和大于号（>），这是因为浏览器会误认为它们是标签
2. 如果希望正确地显示预留字符，我们必须在HTML源代码中使用字符实体
```
<a href="/wiki/">more&gt;&gt;</a>
```

#### 元素嵌套规范

段落元素与标题元素只能嵌套内联元素
```
<!--不推荐-->
<p><div></div></p>
<h2><div></div></h2>

<!--推荐-->
<p><span><span></span></span></p>
<h2><span></span></h2>
```

### CSS规范

#### 命名规范

1. 使用类选择器，尽量避免使用ID选择器定义样式
2. ID在一个页面中的唯一性导致了如果以ID为选择器来写CSS，就无法重用

#### 以字母开头

1. 必须以字母开头命名选择器，这样可保证在所有浏览器下都能兼容
2. 不允许单个字母的类选择器出现
3. 不允许命名带有广告等英文的单词，例如ad,adv,adver,advertising，已防止该模块被浏览器当成垃圾广告过滤掉。任何文件的命名均如此

#### 全小写，并使用 '-' 连字符

1. 下划线 '_' 禁止出现在class命名中
2. 类名禁止驼峰式命名，统一使用 '-' 连字符 ```.element-content {}```
3. id采用驼峰式命名 ```#myDialog {}```
4. scss中的变量、函数、混合、placeholder采用驼峰式命名 ```$colorBlack: #000;```

#### 命名应简约而不失语义

1. 避免过度简写，.ico足够表示这是一个图标，而.i不代表任何意思
2. 使用有意义的名称，使用结构化或者作用目标相关的，而不是抽象的名称

#### 文件命名举列

```
基本样式：base.css
框架布局：layout.css
模块样式：module.css
全局样式：global.css
字体样式：font.css
首页样式：index.css
链接样式：link.css
打印样式：print.css
```

#### 常用类/ID命名举列

```
整页.wp
页眉.header
页脚.footer
导航.nav
主体内容.main
侧边栏.side
标志.logo
搜索.search
登录.login
注册.reg
标题.tit-...
副标题.subtit-...
按钮.btn-...
链接.link-...
背景图片.bg-...
列表.list-...
表格.tb-...
标签.tag-...
视频.video-...
联系.contact
```

#### Reset参考

```css
/* 通用版（基本适用于所有的页面）*/
body,div,dl,dt,dd,ul,ol,li,h1,h2,h3,h4,h5,h6,pre,code,form,fieldset,legend,input,textarea,p,blockquote,th,td{margin:0;padding:0}
table{border-collapse:collapse;border-spacing:0}
fieldset,img{border:0}
address,caption,cite,code,dfn,em,strong,th,var{font-style:normal;font-weight:normal}
ol,ul{list-style:none}
caption,th{text-align:left}
h1,h2,h3,h4,h5,h6{font-size:100%;font-weight:normal}
q:before,q:after{content:''}
abbr,acronym{border:0;font-variant:normal}
sup{vertical-align:text-top}
sub{vertical-align:text-bottom}
input,textarea,select{font-family:inherit;font-size:inherit;font-weight:inherit}
input,textarea,select{*font-size:100%}
```

```css
/* 通用版（支持html5）*/
body,div,dl,dt,dd,ul,ol,li,h1,h2,h3,h4,h5,h6,pre,code,form,fieldset,legend,input,textarea,p,blockquote,th,td,hr,button,article,aside,details,figcaption,figure,footer,header,hgroup,menu,nav,section{margin:0;padding:0}
article,aside,details,figcaption,figure,footer,header,hgroup,menu,nav,section { display:block; }
table{border-collapse:collapse;border-spacing:0}
audio,canvas,video { display: inline-block;*display: inline;*zoom: 1;}
fieldset,img{border:0}
address,caption,cite,code,dfn,em,strong,th,var{font-style:normal;font-weight:normal}
ol,ul{list-style:none}
caption,th{text-align:left}
h1,h2,h3,h4,h5,h6{font-size:100%;font-weight:normal}
q:before,q:after{content:''}
abbr,acronym{border:0;font-variant:normal}
sup{vertical-align:text-top}
sub{vertical-align:text-bottom}
input,textarea,select{font-family:inherit;font-size:inherit;font-weight:inherit}
input,textarea,select{*font-size:100%}
```

#### css代码风格

1. 使用4个空格做为缩进，部分内容为可选
2. css属性值需要用到引号时，统一使用单引号
```
selectors{ font-family:'\5FAE\8F6F\96C5\9ED1';}
```
3. 为单个css选择器或新申明开启新行
```
.home-count .hd,
.content-title,
.select-game-title .title{}
.nav{}
```
4. 不要以没有语义的标签作为选择器
```
.m-xxx div{}
```
5. 简写css颜色属性值
```
selectors{ color:#000; background-color:#def;}
```
6. 删除css属性值为0的单位
```
selectors{ margin:0; padding:0;}
```
7. 不要用内联式样式

#### css属性书写顺序

建议遵循：布局定位属性 自身属性 文本属性 其他属性 CSS3属性
```
/* 布局定位属性 */
display
list-style
position（相应的 top,right,bottom,left）
float
clear 
visibility
overflow

/* 自身属性 */
width
height
margin
padding
border
background

/* 文本属性 */
color
font
text-decoration
text-align
vertical-align
white-space
break-word

/* 其他（CSS3）*/
content
cursor
border-radius
box-shadow
text-shadow
background:linear-gradient...
```

```
.declaration-order {
    /* Positioning */
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    z-index: 100;

    /* Box-model */
    display: block;
    float: right;
    width: 100px;
    height: 100px;

    /* Typography */
    font: normal 13px "Helvetica Neue", sans-serif;
    line-height: 1.5;
    color: #333;
    text-align: center;

    /* Visual */
    background-color: #f5f5f5;
    border: 1px solid #e5e5e5;
    border-radius: 3px;

    /* Misc */
    opacity: 1;
}
```

```
// css property order
var order = [
    'position'
    , 'top'
    , 'right'
    , 'bottom'
    , 'left'
    , 'z-index'
    , 'display'
    , 'float'
    , 'width'
    , 'height'
    , 'max-width'
    , 'max-height'
    , 'min-width'
    , 'min-height'
    , 'padding'
    , 'padding-top'
    , 'padding-right'
    , 'padding-bottom'
    , 'padding-left'
    , 'margin'
    , 'margin-top'
    , 'margin-right'
    , 'margin-bottom'
    , 'margin-left'
    , 'margin-collapse'
    , 'margin-top-collapse'
    , 'margin-right-collapse'
    , 'margin-bottom-collapse'
    , 'margin-left-collapse'
    , 'overflow'
    , 'overflow-x'
    , 'overflow-y'
    , 'clip'
    , 'clear'
    , 'font'
    , 'font-family'
    , 'font-size'
    , 'font-smoothing'
    , 'osx-font-smoothing'
    , 'font-style'
    , 'font-weight'
    , 'hyphens'
    , 'src'
    , 'line-height'
    , 'letter-spacing'
    , 'word-spacing'
    , 'color'
    , 'text-align'
    , 'text-decoration'
    , 'text-indent'
    , 'text-overflow'
    , 'text-rendering'
    , 'text-size-adjust'
    , 'text-shadow'
    , 'text-transform'
    , 'word-break'
    , 'word-wrap'
    , 'white-space'
    , 'vertical-align'
    , 'list-style'
    , 'list-style-type'
    , 'list-style-position'
    , 'list-style-image'
    , 'pointer-events'
    , 'cursor'
    , 'background'
    , 'background-attachment'
    , 'background-color'
    , 'background-image'
    , 'background-position'
    , 'background-repeat'
    , 'background-size'
    , 'border'
    , 'border-collapse'
    , 'border-top'
    , 'border-right'
    , 'border-bottom'
    , 'border-left'
    , 'border-color'
    , 'border-image'
    , 'border-top-color'
    , 'border-right-color'
    , 'border-bottom-color'
    , 'border-left-color'
    , 'border-spacing'
    , 'border-style'
    , 'border-top-style'
    , 'border-right-style'
    , 'border-bottom-style'
    , 'border-left-style'
    , 'border-width'
    , 'border-top-width'
    , 'border-right-width'
    , 'border-bottom-width'
    , 'border-left-width'
    , 'border-radius'
    , 'border-top-right-radius'
    , 'border-bottom-right-radius'
    , 'border-bottom-left-radius'
    , 'border-top-left-radius'
    , 'border-radius-topright'
    , 'border-radius-bottomright'
    , 'border-radius-bottomleft'
    , 'border-radius-topleft'
    , 'content'
    , 'quotes'
    , 'outline'
    , 'outline-offset'
    , 'opacity'
    , 'filter'
    , 'visibility'
    , 'size'
    , 'zoom'
    , 'transform'
    , 'box-align'
    , 'box-flex'
    , 'box-orient'
    , 'box-pack'
    , 'box-shadow'
    , 'box-sizing'
    , 'table-layout'
    , 'animation'
    , 'animation-delay'
    , 'animation-duration'
    , 'animation-iteration-count'
    , 'animation-name'
    , 'animation-play-state'
    , 'animation-timing-function'
    , 'animation-fill-mode'
    , 'transition'
    , 'transition-delay'
    , 'transition-duration'
    , 'transition-property'
    , 'transition-timing-function'
    , 'background-clip'
    , 'backface-visibility'
    , 'resize'
    , 'appearance'
    , 'user-select'
    , 'interpolation-mode'
    , 'direction'
    , 'marks'
    , 'page'
    , 'set-link-source'
    , 'unicode-bidi'
    , 'speak'
]
```

#### css浏览器私有前缀书写格式

私有在前，标准在后。先写带有浏览器私有标志的，后写W3C标准的
```
selectors{
    background: -webkit-gradient(linear, left top, left bottom, color-stop(0%,#ffffff), color-stop(100%,#eff2f4));
    background: -webkit-linear-gradient(top, #ffffff 0%,#eff2f4 100%);
    background: -moz-linear-gradient(top, #ffffff 0%, #eff2f4 100%);
    background: -ms-linear-gradient(top, #ffffff 0%,#eff2f4 100%);
    background: -o-linear-gradient(top, #ffffff 0%,#eff2f4 100%);
    background: linear-gradient(to bottom, #ffffff 0%,#eff2f4 100%);
    filter: progid:DXImageTransform.Microsoft.gradient( startColorstr='#ffffff', endColorstr='#eff2f4',GradientType=0 );
}
```

#### 用CSS控制交互或视觉的变化，JS只需要增减className

1. 利用CSS一次性更改多个节点样式，避免多次渲染，提高渲染效率
2. 需要在结构中注释此类交互

#### css注释

1. 使用简单的英文注释
2. 在版本发布的时候，尽量剔除注释内容，尽可能的减少文件的字节数
```
/*行间注释: 直接写于属性值后面*/
.search{background: #333 url(../img/search.gif) no-repeat;/*定义搜索框的背景*/}

/*整段注释: 分别在开始及结束地方加入注释*/
/*===== search start =====*/
.search {background: #333 url(../img/search.gif) no-repeat;}
/*===== start end =====*/
```

### JS规范

#### 命名规范

```
// 常量: 采用下划线分隔的全大写字符串来定义
例如：PAGEBASECOLOR、COOKIR_PREFIX

变量: 首字母小写，驼峰命名法
例如：videoId、totalPages

函数: 首字母小写、驼峰命名法
例如：login()、showVideo()

类及命名空间: 首字母大写、驼峰命名法
例如：LoginManager、LazyLoader

文件名: 全小写、单词间可以不分隔或用连接线"-"分隔
例如：common.js、milo.min.js、common.debug.js
```

#### 代码格式

1. 在处理字符串时，优先使用单引号
2. 对于代码块，始终用大括号包裹
3. 所有的语句必须用分号结尾
4. 所有的代码块不得使用分号结尾
