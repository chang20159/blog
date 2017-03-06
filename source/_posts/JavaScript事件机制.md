---
title: JavaScript事件
tag:
- JavaScript
categories:
- JavaScript
---
在web浏览器中操作页面时，会发生各种不同的事件，比如光标停留，鼠标点击。我们可以使用javascript应用程序关注特定类型的事件，并注册当事件发生时要调用的函数,即事件处理器。
事件有几个重要的概念：这几个概念：
- 事件类型 event type
- 事件目标 event target
- 事件处理程序（event handler）或 事件监听程序（event listener）
- 事件对象：与特定事件相关的，包含该事件详细信息的对象，作为参数传递给事件处理函数。
<!-- more -->

### 事件注册和移除
#### 设置事件目标的onxxx属性
```html
    <div onclick="clickHandler()">点我</div>
    <script type="text/javascript">
    function clickHandler(){
        console.log("2017，你好。");
    }
    </script>
```
或者 
```html
    <div id="id0">点我</div>
    <script type="text/javascript">
        document.getElementById("id0").onclick = function clickHandler(){
            console.log("2017，你好。");
        }
    </script>
```
这种设置事件处理程序属性的方法适用于所有浏览器的所有常用事件类型，但是对于每个事件目标的每个事件类型最多只有一个处理程序，如果给某一个事件目标的某一个事件类型绑定多个事件处理程序，后面的事件处理程序会覆盖前面的，就是说只会执行最后一个事件处理函数。
例如下面的元素id0绑定了两个click事件函数，点击后控制台只会输出 【2016，再见。】
```html
    <div id="id0">点我</div>
    <script type="text/javascript">
        document.getElementById("id0").onclick = function clickHandler(){
            console.log("2017，你好。");
        }
        document.getElementById("id0").onclick = function clickHandler(){
            console.log("2016，再见。");
        }
    </script>
```
设置属性的方法注册事件，那移除事件自然是移除onxxx方法。
```javascript
    document.getElementById("id0").removeAttribute("onclick");
```
#### addEventListener(eventType,eventHandler,boolean)方法
- 第一个参数 eventType 注册处理程序的事件类型
- 第二个参数 eventHandler 事件处理函数
- 第三个参数 boolean  是一个布尔值，表示是否注册为事件捕获处理函数，默认是false

**注：**addEventListener的更多用法参见 [MDN > Web 技术文档 > Web API 接口 > EventTarget > EventTarget.addEventListener()](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener)
```html
    <div id="id0">点我</div>
    <script type="text/javascript">
        document.getElementById("id0").addEventListener("click",function clickHandler(){
            console.log("2017，你好。");
        });
    </script>
```
addEventListener方法在IE9之前的IE浏览器版本不支持，使用的方法是attachEvent()。
使用addEventListener可以对同一事件目标的同一事件类型绑定多个处理函数并且一次执行。控制台输出：
2017，你好。    
2016，再见。
```html
    <div id="id0">点我</div>
    <script type="text/javascript">
        document.getElementById("id0").addEventListener("click",function clickHandler(){
            console.log("2017，你好。");
        });
        document.getElementById("id0").addEventListener("click",function clickHandler(){
            console.log("2016，再见。");
        });
    </script>
```
并且不会修改或影响其他方式注册的事件处理程序，如下还是会打印两条信息
```html
    <div id="id0">点我</div>
    <script type="text/javascript">
        document.getElementById("id0").addEventListener("click",function clickHandler(){
            console.log("2016，再见。");
        });
        document.getElementById("id0").onclick = function clickHandler(){
            console.log("2017，你好。");
        }
    </script>
```
如果使用相同的参数在同一对象上调用，只会注册一次处理程序。如下只会打印一条信息。
```html
    <div id="id0">点我</div>
    <script type="text/javascript">
        document.getElementById("id0").addEventListener("click",clickHandler);
        document.getElementById("id0").addEventListener("click",clickHandler);
        function clickHandler(){
            console.log("2016，再见。");
        }
    </script>
```
与addEventListener注册事件对应的移除事件的方法是removeEventListener方法，参数与addEventListener方法相同。
可见，使用addEventListener可以将事件注册为两种，默认都是冒泡事件处理程序
- 冒泡事件处理程序 在冒泡阶段调用
- 捕获事件处理程序 在捕获阶段调用

#### IE浏览器中的attachEvent和detachEvent方法
IE5 ~ IE8 这些版本支持attachEvent和detachEvent方法，与addEventListener和removeEventListener方法类似。但也有几点不同：
- 只接收两个参数，这也说明IE9版本之前的IE浏览器不支持事件捕获。
- 第一个参数事件类型带前缀‘on’
- 如果使用相同的参数在同一对象上调用，调用次数和注册次数相同。而且调用顺序可能和注册顺序不同。

### 事件对象
调用事件处理程序时，会向其传入一个参数：事件对象event,但在IE8及以前的IE版本中不会传入，需要通过window.event获取事件对象 ,所以获取对象时可以这样写: event || window.event
### 事件传播
如果事件对象是文档或者文档元素，大部分事件会冒泡到DOM树根，再到document对象，再到window对象。有几种特例：
- focus、blur、srcoll事件不会冒泡；
- 还有load事件会冒泡到document对象停止，不再冒泡到window对象

**事件传播的三个阶段：**
- 事件捕获阶段 
    依次调用 window对象的捕获处理程序 》 document对象的捕获处理程序 》 body对象的捕获处理程序 》 ...（DOM树这条路径上的捕获处理程序）... 》 事件目标父元素的捕获处理程序
- 目标阶段 
  会触发目标对象上的所有监听器,不管是冒泡还是捕获事件处理程序
- 事件冒泡阶段  与捕获阶段的调用顺序相反，但调用的是这条路径上注册的冒泡处理程序
注： 事件捕获在IE9之前的IE中无法使用，一般用在程序调试或事件取消技术中。

**阻止事件传播:**
- event.stopPropagation()
    IE9之前的IE不支持
    阻止事件在其他对象上的传播，在事件目标上的其他事件程序仍然会被调用
    适用于捕获阶段、目标对象本身、冒泡阶段
- event.stopImmediatePropagation()
    部分浏览器支持
    阻止事件在其他对象上的传播，在事件目标上的其他事件程序也会被阻止调用
    适用于捕获阶段、目标对象本身、冒泡阶段
    jquery、YUI提供跨浏览器的stopImmediatePropagation()方法
- event.cancelBubble = true 
    IE的事件对象提供
    阻止事件在其他对象上的传播
    适用于目标对象本身、冒泡阶段

### 事件代理
利用事件冒泡可以实现事件代理，在目标元素的祖先元素上注册事件处理程序，并可以通过事件对象的event.target || event.srcElement获取目标对象。

### 参考
- Javascript权威指南(第六版) 第17章 事件处理
- [MDN > Web 技术文档 > Web API 接口 > 文档对象模型 (DOM) > 事件及DOM](https://developer.mozilla.org/zh-CN/docs/Web/API/Document_Object_Model/Events)
- [MDN > Web 技术文档 > Web API 接口 > EventTarget
 EventTarget.addEventListener()](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener)
 




