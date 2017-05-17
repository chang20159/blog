---
title: CSS小经验
tag:
- CSS
categories:
- CSS
---
### 隐藏滚动条
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
<!-- more -->


### display:inline-block 元素之间有空白间隙
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


### 定宽不换行
white-space: nowrap;

### 单行文本溢出显示省略号
有两个条件
- 设置文本显示在一行  white-space:nowrap
- 溢出时隐藏内容   overflow:hidden
再设置：text-overflow:ellipsis       ellipsis翻译：（语法结构上的）省略

### 多行文本移除显示省略号
```css
// 仅适用于webkit内核浏览器
p{
  display: -webkit-box;   //设置display，将对象作为弹性伸缩盒子模型显示
  -webkit-box-orient: vertical;  //规定框的子元素应该被水平或垂直排列
  -webkit-line-clamp: 4;  //限制在一个块元素显示的文本的行数
  overflow: hidden;
}
```

### z-index无效
需要设置position属性为非static值


