---
title: normalize.css与reset.css
tag:
- css
categories:
- css
---

各大浏览器对html标签都有自己的默认样式，大部分的标签样式表现是一致的，但是也有一些标签在不同的浏览器上表现出不同的效果，还有比如html5新增的那些标签并不是所有的浏览器都支持，为了在不支持的浏览器中使用这些标签也不会造成影响，也需要做一些处理。normalize.css与reset.css都是对样式预先做一些格式化处理，从而在各个浏览器上表现一致。
那么，看看它们是怎么处理的。
<!-- more -->
找了一份reset.css，现在大家用的reset.css基本上跟这差不多，有一些根据自己的需求增改。

## reset.css
```css
body, h1, h2, h3, h4, h5, h6, hr, p, blockquote,dl, dt, dd, ul, ol, li,pre, 
fieldset, lengend, button, input, textarea,th, td { 
    margin: 0;
    padding: 0;
}
body,button, input, select, textarea {
    font: 12px/1 Tahoma, Helvetica, Arial, "\5b8b\4f53", sans-serif; 
}
h1 { font-size: 18px; }
h2 { font-size: 16px; }
h3 { font-size: 14px; }
h4, h5, h6 { font-size: 100%; }
address, cite, dfn, em, var { font-style: normal; } 
code, kbd, pre, samp, tt { font-family: "Courier New", Courier, monospace; } 
small { font-size: 12px; } 
ul, ol { list-style: none; }
a { text-decoration: none; }
a:hover { text-decoration: underline; }
abbr[title], acronym[title] {
 border-bottom: 1px dotted;
 cursor: help;
}
q:before, q:after { content: ''; }
legend { color: #000; } 
fieldset, img { border: none; }
button, input, select, textarea {
    font-size: 100%; 
}
table {
 border-collapse: collapse;
 border-spacing: 0;
}
hr {
    border: none;
    height: 1px;
}
html { overflow-y: scroll; }
```
可以看到reset.css的做法是把元素的样式都重置，比如：
- 清除了N多元素的内外边距
- 清除了列表元素ul, ol 的样式
- 清除了a标签的文本修饰
- 等等

## normalize.css
normalize.css的源码可以去[github Normalize.css](https://github.com/necolas/normalize.css)上看。与reset.css相比，

- 保留了有用的浏览器默认样式，而不是清除它们
- 覆盖了大部分html元素
- 修复了浏览器的bug并保证了一致性
- 通过小小的改进提高了可用性
- 代码有详细的注释

比如：
```css
/* Hidden
   ========================================================================== */

/**
 * Add the correct display in IE 10-.
 */

[hidden] {
  display: none;
}
```
## 参考
- [Normalize.css 源码](https://github.com/necolas/normalize.css)
- [about-normalize-css](http://nicolasgallagher.com/about-normalize-css/)
- [cssreset](https://yuilibrary.com/yui/docs/cssreset/)