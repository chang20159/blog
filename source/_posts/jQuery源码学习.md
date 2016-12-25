---
title: jQuery源码学习
tag:
- jQuery
categories:
- jQuery
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

factory函数是构建jQuery的工厂，提供jQuery各种方法。当作为模块被引入时，输出jQuery，在浏览器中直接使用时，将jQuery赋给window.$。

```javascript
( function( global, factory ) {

	"use strict";

	if ( typeof module === "object" && typeof module.exports === "object" ) {

		module.exports = global.document ?
			factory( global, true ) :
			function( w ) {
				if ( !w.document ) {
					throw new Error( "jQuery requires a window with a document" );
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

	... 给jQuery和jQuery.prototype添加各种属性和方法 ...

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
```javascript
jQuery.extend = jQuery.fn.extend = function() {
	var options, name, src, copy, copyIsArray, clone,
		target = arguments[ 0 ] || {},
		i = 1,
		length = arguments.length,
		deep = false;

	// Handle a deep copy situation
	if ( typeof target === "boolean" ) {
		deep = target;

		// Skip the boolean and the target
		target = arguments[ i ] || {};
		i++;
	}

	// Handle case when target is a string or something (possible in deep copy)
	if ( typeof target !== "object" && !jQuery.isFunction( target ) ) {
		target = {};
	}

	// Extend jQuery itself if only one argument is passed
	if ( i === length ) {
		target = this;
		i--;
	}

	for ( ; i < length; i++ ) {

		// Only deal with non-null/undefined values
		if ( ( options = arguments[ i ] ) != null ) {

			// Extend the base object
			for ( name in options ) {
				src = target[ name ];
				copy = options[ name ];

				// Prevent never-ending loop
				if ( target === copy ) {
					continue;
				}

				// Recurse if we're merging plain objects or arrays
				if ( deep && copy && ( jQuery.isPlainObject( copy ) ||
					( copyIsArray = jQuery.isArray( copy ) ) ) ) {

					if ( copyIsArray ) {
						copyIsArray = false;
						clone = src && jQuery.isArray( src ) ? src : [];

					} else {
						clone = src && jQuery.isPlainObject( src ) ? src : {};
					}

					// Never move original objects, clone them
					target[ name ] = jQuery.extend( deep, clone, copy );

				// Don't bring in undefined values
				} else if ( copy !== undefined ) {
					target[ name ] = copy;
				}
			}
		}
	}

	// Return the modified object
	return target;
};
```

### extend的用法