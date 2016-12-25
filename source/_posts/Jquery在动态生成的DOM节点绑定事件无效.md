---
title: jQuery在动态生成的节点上绑定事件无效
tag:
- jQuery
- 事件委托
categories:
- jQuery
---
在有些情况下，需要在DOM节点存在之前就给这个节点绑定事件，在使用Jquery给尚不存在的dom节点绑定事件时发现并不起作用，事件并没有绑定在dom节点上，但也不报错。而使用原生的JavaScript绑定事件，如果绑定事件的DOM节点不存在，是会报错的。
<!-- more -->
### 原生JS绑定事件
```html
<html !DOCTYPE>
	<head>
		<meta charset="utf-8" />
		<title>原生js绑定事件</title>	
	</head>
	<body>
		<button id="J_confirm">确定</button>
		<div id="J_content">
			<!-- 动态生成dom节点 -->
			<!-- <button id="J_jump">跳转</button> -->
		</div>
		<script type="text/javascript">
			// 已存在节点
			document.getElementById('J_confirm').onclick=function(){
				console.log("J_confirm is clicked.");
			};

			// 在尚未生成的节点上绑定事件
			document.getElementById('J_jump').onclick=function(){
				console.log("J_jump is clicked without event delegate using onclick.");
			};

			// 在尚未生成的节点上绑定事件
			document.getElementById('J_jump').addEventListener('click',function(){
			 	console.log("J_jump is clicked without event delegate using addEventListener.");
			});

			// 在事件绑定之后才生成节点
			document.getElementById('J_content').appendChild();
			function genNode(){
				var node = document.createElement('button');
				node.setAttribute('id','J_jump');
				node.append("跳转");
				return node;
			}
		</script>
	</body>
</html>
```
先给id为J_jump的dom节点绑定事件，然后再生成此节点，刷新页面就会报错，动态产生的跳转按钮也不会出现在页面上。

```
Uncaught TypeError: Cannot read property 'addEventListener' of null

Uncaught TypeError: Cannot set property 'onclick' of null
```
### JQuery绑定事件
而使用Jquery在未生成的节点上绑定事件，跳转按钮是会正常展示的，但是点击没有反应。
```html
<html !DOCTYPE>
	<head>
		<meta charset="utf-8" />
		<title>动态生成的元素绑定事件</title>
	</head>
	<body>
		<button id="J_confirm">确定</button>
		<div id="J_content"></div>
		<script src="https://code.jquery.com/jquery-3.0.0.min.js"></script>
		<script type="text/javascript">
			$('#J_confirm').on('click',function(){
				console.log("J_confirm is clicked.");
			});
			$('#J_jump').on('click',function(){
				console.log("J_jump is clicked.");
			});
			$('#J_content').html('<button id="J_jump">跳转</button>');
		</script>
	</body>
</html>
```
### 原生JS事件委托
可以通过事件代理解决这个问题，在使用原生的javascript绑定事件时，可以将事件绑定在确定会存在的祖先节点上，委托祖先节点处理事件.
```html
<html !DOCTYPE>
	<head>
		<meta charset="utf-8" />
		<title>原生js绑定事件</title>	
	</head>
	<body>
		<button id="J_confirm">确定</button>
		<div id="J_content">
			<!-- <button id="J_jump">跳转</button> -->
			<!-- 动态生成dom节点 -->
		</div>
		<script type="text/javascript">
			// 已存在节点
			document.getElementById('J_confirm').onclick=function(){
				console.log("J_confirm is clicked.");
			};
			// 事件委托
			document.getElementById('J_content').onclick=function(e){
				var target = e.target;
				console.log(target.innerText + " is clicked with event delegate using onclick.");
			};

			// 事件委托
			document.getElementById('J_content').addEventListener('click',function(e){
			 	var target = e.target;
				console.log(target.innerText + " is clicked with event delegate using addEventListener.");
			});

			// 在事件绑定之后才生成节点
			document.getElementById('J_content').appendChild(genNode("跳转1"));
			document.getElementById('J_content').appendChild(genNode("跳转2"));
			function genNode(text){
				var node = document.createElement('button');
				node.setAttribute('id','J_jump');
				node.append(text);
				return node;
			}
		</script>
	</body>
</html>
```
输出：
```
跳转1 is clicked with event delegate using onclick.
跳转1 is clicked with event delegate using addEventListener.
跳转2 is clicked with event delegate using onclick.
跳转2 is clicked with event delegate using addEventListener.
```
### JQuery事件委托
在Jquery中还是可以用on方法实现事件代理。
```html
<html !DOCTYPE>
	<head>
		<meta charset="utf-8" />
		<title>动态生成的元素绑定事件</title>
	</head>
	<body>
		<button id="J_confirm">确定</button>
		<div id="J_content"></div>
		<script src="https://code.jquery.com/jquery-3.0.0.min.js"></script>
		<script type="text/javascript">
			$('#J_confirm').on('click',function(){
				console.log("J_confirm is clicked.");
			});
			$('#J_content').on('click','#J_jump1',function(){
				console.log($(this).text() + " is clicked with event delegate.");
			});
			$('#J_content').on('click','#J_jump2',function(e){
				console.log($(this).text() + " is clicked with event delegate.");
			});
			$('#J_content').append('<button id="J_jump1">跳转1</button>');	
			$('#J_content').append('<button id="J_jump2">跳转2</button>');
		</script>
	</body>
</html>
```
所以，动态生成的节点上绑定事件无效，其实是如果事件在dom节点不存在时绑定，才会无效，对于动态生成的节点如果事件是在节点存在之后绑定的，是没有问题的。

### 参考
- [JQuery Event Handler Attachment](http://api.jquery.com/category/events/event-handler-attachment/)
- [JQuery .on()](http://api.jquery.com/on/)
- [jQuery API ](http://jquery.cuishifeng.cn/index.html)
- [jQuery API 中文文档](http://www.jquery123.com/)

