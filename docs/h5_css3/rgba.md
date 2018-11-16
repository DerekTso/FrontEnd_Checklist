# CSS3基础 - rgba

## 目录

### rgba介绍

例子：
```background: rgba(255, 0, 0, 0.6);```
```color: rgba(255, 0, 0, 0.5);```
```border: 5px solid rgba(0, 0, 0, 0.5);```
- R：红色值。正整数(0~255)
- G：绿色值。正整数(0~255)
- B：蓝色值。正整数(0~255)
- A：透明度。取值0~1之间

### rgba与opacity的区别

1. rgba 比 设置CSS的透明度(opacity)更好，因为它不会影响元素其他的属性，比如边框，字体样式等
2. 使用 opacity 来做透明时，父元素中设置了 opacity，那么其后代元素都会受其影响
```
opacity: 0.5;
filter:alpha(opaity=50);
```
