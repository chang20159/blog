---
title: 【jQuery源码】第一篇
tag:
- jQuery3.1.1源码
categories:
- JQuery
---
在使用jQuery的过程中遇到很多的疑惑。
jQuery为什么可以用美元符$表示？
事件绑定的几个方法bind、delegate、on之间有什么区别？
jQuery怎么判断checkbox是否被选中，有哪几种方法？
这样的问题有很多，还记不住，日常使用到的jQuery API，既然用到了，就学习一下源码吧~
理解了实现，就不怕忘记了。
目前学习的版本是3.1.1，代码已经1万+行了。最新源码可从[jquery GitHub](https://github.com/jquery/jquery)获取。
<!-- more -->
## 总体结构
JQuery的源码是一个立即执行的函数,带两个参数,对象global和函数factory
- global   >  typeof window !== "undefined" ? window : this
- factory  >  function( window, noGlobal ) {  ...  }
- 
factory函数是构建jQuery的工厂，提供jQuery各种方法。当作为模块被引入时，输出jQuery，在浏览器中直接使用时，将jQuery赋给window.$。
```javascript
( function( global, factory ) {
	"use strict";
	if ( typeof module === "object" && typeof module.exports === "object" ) {
		module.exports = global.document ?
			factory( global, true ) :
			function( w ) {
				if ( !w.document ) {
					throw new Error( "jQuery requires a window with a document");
				}
				return factory( w );
			};
	} else {
		factory( global );
	}
} )( typeof window !== "undefined" ? window : this, function( window, noGlobal ) {
	//jQuery 是一个函数
	var	jQuery = function( selector, context ) {
			return new jQuery.fn.init( selector, context );
		};
		// 核心方法
		// 回调系统
		// 异步队列
		// 数据缓存
		// 队列操作
		// 选择器引
		// 属性操作
		// 节点遍历
		// 文档处理
		// 样式操作
		// 属性操作
		// 事件体系
		// AJAX交互
		// 动画引擎
	if ( typeof define === "function" && define.amd ) {
		define( "jquery", [], function() {
			return jQuery;
		} );
	}
	//在没有作为模块引入时，将jQuery赋给window.$和window.jQuery
	if ( !noGlobal ) {
		window.jQuery = window.$ = jQuery;
	}
	return jQuery;
} );
```

- 当在其他模块中通过require(jQuery)的形式（CommonJS AMD等规范引入模块的方式）引入，并且在浏览器环境下，那这个代码的逻辑是这样的，将jQuery输出
```javascript
module.exports = function( window, true ) { 
	... 
	if ( typeof define === "function" && define.amd ) {
		define( "jquery", [], function() {
			return jQuery;
		} );
	}
}
```
- 当在其他模块中通过require(jQuery)的形式引入，但不是在浏览器环境下，比如在nodejs环境中，global.document是不存在的，那逻辑就是这样。输出模块时抛出错误，提示当前环境因为没有window.document而不支持jQuery
```javascript
module.exports = function( w ) {
				if ( !w.document ) {
					throw new Error( "jQuery requires a window with a document" );
				}
				return factory( w );
			};
```
- 如果不是用require(jQuery)的形式引入，比如直接使用window.$，逻辑就是这样的，将构建好的jQuery赋给window.$和window.jQuery，这样就可以直接使用$了。
```javascript
function( window, false ) { 
	... 
	window.jQuery = window.$ = jQuery;
	return jQuery;
}
```
在浏览器中 window == this，window.document存在，在nodejs环境下 global == this，不存在global.document。所以在nodejs环境中是不支持jQuery的。
## JQuery扩展方法extend
### 源码中extend方法的使用
给jQuery自身和jQuery原型都定义了一个extend方法，在jQuery源码内部大部分jQuery和jQuery原型的属性都是用extend方法扩展的，jQuery的extend方法也常用来扩展对象，或者用于jQuery插件的开发。在jQuery中定义和使用extend如下
```javascript
	//设置prototype属性
	jQuery.fn = jQuery.prototype = { ... };
	//给jQuery自身和jQuery原型都定义一个extend方法，
	//用于扩展jQuery和jQuery.prototype的属性
	jQuery.extend = jQuery.fn.extend = function() { ... };
	jQuery.fn.extend( {
		on: function( types, selector, data, fn ) { ... },
		one: function( types, selector, data, fn ) { ... },
		off: function( types, selector, fn ) { ... } 
	});
	jQuery.extend( {
		attr: function( elem, name, value ) { ... },
		removeAttr: function( elem, value ) { ... }
	} );

```

### extend方法实现解析
extend方法将参数都合并到target中,用法是这种形式： $.extend(target,src1,src2,...)
- 如果第一个参数是布尔值，target就是第二个参数
- 如果第一个参数不是布尔值，target就是第一个参数
- 如果只有一个参数，target就是jQuery本身
- 如果target不是object类型或者函数类型，target就是{}
扩展后返回的是target,所以如果不希望影响原有参数，可以设定一个空对象{}作为target,如$.extend({},src1,src2,...)或$.extend(true,{},src1,src2,...)
```javascript
jQuery.extend = jQuery.fn.extend = function() {
	var options, name, src, copy, copyIsArray, clone,
		target = arguments[ 0 ] || {},  i = 1, length = arguments.length,deep =false;
	//如果第一个参数是一个布尔值，根据这个布尔值决定是否深拷贝
	if ( typeof target === "boolean" ) { 
		deep = target;
		// 如果第一个参数是一个布尔值，第二个参数作为target
		target = arguments[ i ] || {};
		i++;
	}
	// 如果target不是对象也不是函数，则将{}作为target，并且这个非对象的target不会被合并
	if ( typeof target !== "object" && !jQuery.isFunction( target ) ) {
		target = {};
	}
	// target就是jQuery
	if ( i === length ) {
		target = this;
		i--;
	}
	for ( ; i < length; i++ ) {
		// 只处理非null 非undefined值
		if ( ( options = arguments[ i ] ) != null ) { 
			// 合并每个参数对象的每一个属性
			for ( name in options ) {
				src = target[ name ];
				copy = options[ name ];
				// 已经合并的到target的就不再合并，如果要合并的参数中有相同的属性，但是属性值不同，会被覆盖
				if ( target === copy ) {
					continue;
				}
				// 如果属性值是对象或者数组，并且deep为true,递归扩展，clone是子对象扩展的target
				if ( deep && copy && ( jQuery.isPlainObject( copy ) || ( copyIsArray = jQuery.isArray( copy ) ) ) ) {
					if ( copyIsArray ) {
						copyIsArray = false;
						clone = src && jQuery.isArray( src ) ? src : [];
					} else {
						clone = src && jQuery.isPlainObject( src ) ? src : {};
					}
					//子对象合并后的结果
					target[ name ] = jQuery.extend( deep, clone, copy );
				// 如果属性值是undefined，不会合并到target中
				} else if ( copy !== undefined ) {
					target[ name ] = copy;
				}
			}
		}
	}
	// 返回最终合并的结果
	return target;
};
```

### extend的用法
#### 如果有相同属性，且属性值都是对象
var src1 = {name:"chang",info:{age:24,sex:1,time:"2016"}};
var src2 = {name:"chen",info:{age:26,sex:2,birth:"1945"}};
##### 子对象递归合并
$.extend(true,{},src1,src2);
```
  Object
  	info: Object
  		age: 26
  		birth: "1945"
  		sex: 2
  		time: "2016"
  		__proto__: Object
  	name: "chen"
  	__proto__: Object
```
##### 子对象后者属性值覆盖前者
$.extend({},src1,src2); $.extend(false,{},src1,src2);
```
Object
	info: Object
		age: 26
		birth: "1945"
		sex: 2
		__proto__: Object
	name: "chen"
	__proto__: Object
```
#### 有相同属性，但是只有一个属性是对象的情况
var src1 = {namee:"chang",info:{age:24,sex:1,time:"2016"}};
var src2 = {namee:"chen",info:1234};
##### true + 子对象info合并时不进行深度合并
最后的src2合并时由于不是对象，所以不会进行深度合并，直接覆盖target中已有的info
$.extend(true,{},src1,src2);
```
Object {namee: "chen", info: 1234}
```
##### true + 子对象info合并时,target不是对象则不合并
子对象info合并时，是这样的，$.extend(true,1234,{age:24,sex:1,time:"2016"})
第一个合并参数不是对象，所有target是{}
$.extend(true,{},src2,src1)
```
Object
	info: Object
		age: 24
		sex: 1
		time: "2016"
		__proto__: Object
	namee: "chang"
	__proto__: Object
```
#### 合并到自身
$.extend(src1);
$.info;
```
Object {age: 24, sex: 1, time: "2016"}
```
## 参考
- [jquery官网](https://jquery.com/)
- [jquery GitHub](https://github.com/jquery/jquery)