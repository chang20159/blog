---
title: CSS小经验
tag:
- CSS
categories:
- CSS
---
1 隐藏滚动条
见[::-webkit-scrollbar](https://developer.mozilla.org/en-US/docs/Web/CSS/::-webkit-scrollbar)
```css
.visible-scrollbar, .invisible-scrollbar {
  display: block;
  width: 10em;
  overflow: auto;
}
.invisible-scrollbar::-webkit-scrollbar {
  display: none;
}
```


2 线性渐变  
见[linear-gradient](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Using_CSS_gradients)
```css
background: linear-gradient(to right,#ff6633,#ff8533);
```


3 display:inline-block 元素之间有空白间隙
```css
ul{
    width:100%;
}
li{
    width:50%;
    display:inline-block;
}
```
如果html结构写成这样，两个li中间会有间隙假设为n px ,那这一行就放不下两个li
```html
<ul>
    <li>第一个</li>
    <li>第二个</li>
</ul>
```
最好的解决办法是将html结构写成这样
```html
<ul>
    <li>
    第一个</li><li>
    第二个</li>
</ul>
```

4 设置float属性导致子节点溢出
这个需要清除浮动，清除浮动的方法有以下几个：
- 在父元素中设置overflow:hidden
- 加一个统计节点，设置节点的 clear: both;
- 
6 垂直居中
- 定高  line-height与高度相同

7 定宽不换行
white-space: nowrap;

8 溢出文本显示省略号效果
有两个条件
- 设置文本显示在一行  white-space:nowrap
- 溢出时隐藏内容   overflow:hidden
再设置：text-overflow:ellipsis       ellipsis翻译：（语法结构上的）省略

9 z-index无效
需要设置position属性为非static值

10 flex
display: flex;
flex-direction: column;
align-items: center;