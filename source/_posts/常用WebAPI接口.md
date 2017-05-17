---
title: 常用 Web API 接口
tag:
- JavaScript
categories:
- JavaScript
---

有时候是不是很容易头大，很多常见的api分不清，看下面这些，能弄明白它们的区别么？

- document.location 与 window.location
- nodeValue、innerText 、textContent、innerHTML、outerHTML、outerText
- clientHeight、scrollHeight、offsetHeight（Left/Top/Width）

<!-- more -->
要想知道它们有什么不同，还是得先捋捋它们都是谁的属性和方法，看看常用web api的继承关系，想想它们都有哪些属性和方法

![](/images/note_web_api.png)

## EventTarget
### 方法
- EventTarget.addEventListener()
- EventTarget.removeEventListener()
- EventTarget.dispatchEvent()

## Node
Node是一个接口，许多DOM类型从这个接口继承,如Document，Element
### 属性
- Node.baseURI
- Node.childNodes      所有子节点
- Node.firstChild      第一个子节点
- Node.lastChild       最后一个子节点
- Node.previousSibling 前一个兄弟节点
- Node.nextSibling     下一个兄弟节点
- Node.parentNode      父节点(可能是元素节点也可能是文档节点)
- Node.parentElement   当前节点的父元素节点
- Node.nodeValue   返回或设置当前节点的值
- Node.nodeType    节点的类型
- Node.nodeName    节点名称
- Node.innerText    一个节点及其后代的“渲染”文本内容的属性   
- Node.textContent  一个节点及其后代的文本内容
- Node.replaceChild(newChild, oldChild)

### 方法
- parentNode.appendChild(child)  将一个节点添加到指定父节点的子节点列表末尾
- parentNode.removeChild(child) 
- Node.cloneNode  返回调用该方法的节点的一个副本
- parentElement.contains( otherNode )   传入的节点是否为该节点的后代节点
- parentElement.hasChildNodes() 当前节点是否包含有子节点
- parentElement.insertBefore(newElement, referenceElement)

### 节点类型常量
|常量  |值  |描述
|:---|:---|:---|
|Node.ELEMENT_NODE   |1   |一个 元素 节点，例如 &lt;p&gt; 和 &lt;div&gt;
|Node.TEXT_NODE  |3   |Element 或者 Attr 中实际的  文字
|Node.PROCESSING_INSTRUCTION_NODE    |7   |一个用于XML文档的 ProcessingInstruction ，例如 &lt;?xml-stylesheet ... ?&gt; 声明
|Node.COMMENT_NODE  | 8   |一个 Comment 节点
|Node.DOCUMENT_NODE  |9   |一个 Document 节点。
|Node.DOCUMENT_TYPE_NODE |10  |描述文档类型的 DocumentType 节点。例如 &lt;!DOCTYPE html&gt;  就是用于 HTML5 的。
|Node.DOCUMENT_FRAGMENT_NODE |11  |一个 DocumentFragment 节点

## ParentNode
### 属性
- childElementCount
- children
- firstElementChild
- lastElementChild

### 方法
- append()
- prepend()
- querySelectorAll()


## ChildNode
### 方法
- after()
- before()
- remove()

## Document
### 属性
- document.body  
- document.characterSet
- document.doctype
- document.domain
- document.head
- document.body
- document.location
- document.documentURI
- document.URL

### 方法
- document.createElement()
- document.createTextNode
- document.getElementById()
- document.getElementsByClassName()
- document.getElementsByName()
- document.getElementsByTagName()
- document.querySelector()
- document.querySelectorAll()
- document.write()
- document.writeln()

## Element
### 属性
- Element.attributes
- Element.classList
    - Element.classList.add()
    - Element.classList.remove()
    - Element.classList.item()
    - Element.classList.toggle()
    - Element.classList.contains()
- Element.className
- Element.tagName
- Element.id
- Element.name

- Element.innerHTML
- Element.outerHTML

- Element.clientHeight
- Element.clientLeft
- Element.clientTop
- Element.clientWidth

- Element.scrollHeight
- Element.scrollLeft
- Element.scrollTop
- Element.scrollWidth

### 方法
- Element.getElementsByClassName()
- Element.getElementsByTagName()
- Element.querySelector()
- Element.querySelectorAll()
- Element.getAttribute()
- Element.removeAttribute()

## HTMLElement
### 属性
- HTMLElement.offsetHeight
- HTMLElement.offsetLeft
- HTMLElement.offsetTop
- HTMLElement.offsetWidth
- HTMLElement.offsetParent 返回一个指向最近的包含该元素的定位元素
- HTMLElement.style
    ```
    elt.style.cssText = "color: blue"; // 设置多个样式属性 
    elt.setAttribute("style", "color: blue"); // 设置多个样式属性 
    elt.style.color = "blue"; // 直接设置样式属性
    ```
- HTMLElement.dataset  获取自定义的数据属性
- HTMLElement.contentEditable  表明元素是否是可编辑的
- HTMLElement.dir  获取或设置当前元素的元素内容的文本书写方向

### 方法
- HTMLElement.click() 可以用来模拟鼠标左键单击一个元素
- HTMLElement.blur()  用来移除当前元素所获得的键盘焦点
- HTMLElement.focus() 可以设置指定元素获取焦点

## Window
###  属性 
- window.console
- window.document
- window.history 用来获取History 对象的引用
- window.innerHeight 浏览器窗口的视口（viewport）高度（以像素为单位），包括水平滚动条
- window.innerWidth 浏览器视口（viewport）宽度（单位：像素）包括垂直滚动条
- window.location
- window.navigator 查询一些关于运行当前脚本的应用程序的相关信息
- window.top  返回窗口体系中的最顶层窗口的引用
- window.parent 返回当前窗口的父对象

### 方法
- window.setInterval  重复调用一个函数或执行一个代码段，以固定的时间延迟在每次调用之间。返回一个 intervalID
- window.setTimeout 在定时到期后执行一段代码
- window.resizeTo(aWidth, aHeight)   动态调整窗口的大小

## 区分相似接口属性
- document.location 与 window.location
- Node.nodeValue、Node.innerText 、Node.textContent 、Element.innerHTML、Element.outerHTML、HTMLElement.outerText
- Element.clientHeight、Element.scrollHeight、HTMLElement.offsetHeight（Left/Top/Width）

## 参考
- [EventTarget](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget)
- [Window](https://developer.mozilla.org/zh-CN/docs/Web/API/Window)
- [Node](https://developer.mozilla.org/zh-CN/docs/Web/API/Node)
- [ParentNode](https://developer.mozilla.org/zh-CN/docs/Web/API/ParentNode)
- [ChildNode](https://developer.mozilla.org/zh-CN/docs/Web/API/ChildNode)
- [Element](https://developer.mozilla.org/zh-CN/docs/Web/API/Element)
- [Document](https://developer.mozilla.org/zh-CN/docs/Web/API/Document)
- [HTMLElement](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement)
- [GlobalEventHandlers](https://developer.mozilla.org/zh-CN/docs/Web/API/GlobalEventHandlers)