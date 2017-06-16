---
title: Prism 轻量级代码语法高亮库
tag:
- 语法高亮
categories:
- 工具
---
Prism是一个轻量的、可扩展的代码语法高亮库，根据现代web标准设计。目前有很多网站在用，JavaScript的创始人Brendan Eich的个人博客也是用的Prism。Prism支持很多种语言，并且扩展性很好，它要求代码放在code标签下，code标签可包含在pre标签内。下面是多种写法下的显示效果。

![](/images/prism/QQ20161123-1@2x.png)
<!-- more -->
Prism支持多种代码高亮风格，多种语言，并且可以添加插件来丰富Prism的功能。只需要在HTML中引入prism.js和prism.css。

在[Prism官网](http://prismjs.com/)点击下载，依次选择：

- 是否压缩
- 选主题，代码风格这里用的okaidia
- 选语言，按需选择，选择语言越多文件越大
- 选插件，在prism.js中可以看到每个插件都对应一个立即执行的函数

下载prism.js文件和prism.css文件，引入html文件中
```html
	<!DOCTYPE html>
	<html>
	<head>
		<title></title>
		<link href="./prism.css" rel="stylesheet" />
	</head>
	<body>
		<section class="language-markup" id="different-markup">
	
			<!--这种写法需要插件File Highlight支持-->
			<pre data-src="./test.js"></pre>
	
			<pre class="language-css"><code>p { color: red; }</code></pre>
	
			<pre class="language-css"><code class="language-*">p { color: red; }</code></pre>
	
			<!--pre和code标签之间有空格ode标签之间，展示出来的效果可以看到代码与边框上下左右间隔不均匀-->
			<pre class="language-css">
				<code class="language-css">p { color: red; }</code>
				<code>p { color: red; }</code>
			</pre>
	
			<pre><code class="language-css">p { color: red; }</code></pre>
	
			<!-- No language, should inherit .language-markup -->
			<pre><code>p { color: red; }</code></pre>
	
			<code class="language-css">p { color: red; }</code>
	
			<!-- 没有设置language,会继承父节点的class .language-markup -->
			<code class="language-*">p { color: red; }</code>
	
			<!-- 没有高亮 -->
			<code class="language-none">p { color: red; }</code>
	
		</section>
	
		<script src="./prism.js"></script>
	</body>
	</html>
```
搞定~~

相关    
- [Prism GitHub](https://github.com/PrismLibrary)
- [Prism官网](http://prismjs.com/)
