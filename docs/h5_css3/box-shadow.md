# CSS3基础 - box-shadow

## 目录
- [box-shadow 介绍](#box-shadow-介绍)
- [box-shadow 应用](#box-shadow-应用)

### box-shadow 介绍

1. box-shadow属性是用来设置盒模型阴影的，如 div,p,h1,h2,h3,h4,h5,h6 等
2. box-shadow属性不是用来设置文字阴影的，如果设置文字阴影请用 text-shadow
3. 兼容各主流浏览器并支持这些主浏览器的较低版本
    ```
    Chrome/Safari浏览器 -webkit-box-shadow
    Firefox浏览器       -moz-box-shadow
    Opera浏览器         -o-box-shadow
    IE>9 浏览器         -ms-box-shadow

    .box-shadow {  
        -webkit-box-shadow:0 0 10px rgba(0, 204, 204, .5);
        -moz-box-shadow:0 0 10px rgba(0, 204, 204, .5);
        box-shadow:0 0 10px rgba(0, 204, 204, .5);
    }  
    ```
4. box-shadow属性的语法
    ```
    box-shadow: h-shadow v-shadow blur spread color inset;
       - h-shadow	必需的。水平阴影的位置。允许负值
       - v-shadow	必需的。垂直阴影的位置。允许负值
       - blur	    可选。模糊距离。其值只能为正值
       - spread	    可选。阴影的大小。允许负值，表示阴影缩小
       - color	    可选。阴影的颜色。如不设定颜色，浏览器会取默认色，在Chrome/Safari下表现为透明色，在Firefox/Opera下表现为黑色
       - inset	    可选。默认为外阴影，加上inset关键词则为内阴影
       // 外阴影: 正值从边框外的右下向外偏移(边框外的右下有阴影)，负值从边框外的左上向外偏移(边框外的左上有阴影)
       // 内阴影: 正值从边框内的左上向内偏移(边框内的左上有阴影)，负值向边框内的右下向内偏移(边框内的右下有阴影)
    ```
5. box-shadow属性向边框添加一个或多个阴影，用逗号分隔阴影列表
    ```
    box-shadow:-10px 0px 10px red,   /*左边阴影*/ 
               0px -10px 10px #000,  /*上边阴影*/ 
               10px 0px 10px green,  /*右边阴影*/ 
               0px 10px 10px blue;" /*下边阴影*/
    ```

### box-shadow 应用

6. 阴影的单位我们还可以使用rgba等有透明效果的阴影
    ```
    box-shadow: 4px 4px 8px rgba(0,0,0,0.5);
    ```
7. 阴影效果会跟随元素的圆角也会显示圆角的阴影。
8. 外阴影效果会和外阴影合并，内阴影效果会和内阴影合并，外阴影效果不会和内阴影效果合并
    ```
    box-shadow: 4px 4px 8px rgba(0,0,0,0.5),
                4px 5px 3px green, // 外阴影的黑色和绿色合并了，但是没有和内阴影合并
                inset 4px 4px 8px #000;
    ```
9. 使用spread属性来扩大阴影，从而实现四周阴影的uqjs
    ```
    box-shadow: 0px 0px 8px 6px rgba(0,0,0,0.8);
    ```
10. box-shadow实现单边阴影
    ```
    // 右边阴影8px，下边阴影2px，blur阴影2px
    // 通过spread属性把阴影范围缩小了2px+2px=4px
    // 那么下边的阴影就隐藏到了div元素的后面
    box-shadow: 8px 2px 2px -4px rgba(0,0,0,0.8);
    ```
11. box-shadow实现立体按钮效果
    ```
    .shadowButton {
        display: inline-block;
        line-height: 33px;
        height: 36px;
        padding: 0 2em;
        font-size: 13px;
        background: #0C58A7;
        box-shadow: -3px -3px 2px #050533 inset; // 边框内右下有阴影
        text-decoration: none;
        color: #fff;
        border-radius: 4px;
        vertical-align: middle;
    }
    .shadowButton:active {
        line-height: 39px;
        box-shadow: 2px 3px 2px #050533 inset; // 边框内左上有阴影
    }
    // 用active伪类来实现按下时效果的动画，通过box-shadow的inset的内阴影的转换，形成视觉上突出和凹陷的效果
    // height用来定死高度，然后通过line-height的高度变化实现active时文字的下移效果，更逼真3D效果
    // 没有按下时：line-height = height - v-shadow
    // 按下active时：line-height = height + v-shadow
    ```
