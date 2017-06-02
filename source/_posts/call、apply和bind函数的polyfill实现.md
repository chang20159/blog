---
title: call、apply和bind函数的polyfill实现
tag:
- JavaScript
categories:
- JavaScript
---

```javascript
var name = 'chen'

var foo = {
	name: 'chang'
}

function sayHello(age,sex){
	alert(this.name + age + sex);
}
```
这里有一个对象foo，一个函数sayHello，如果直接调用sayHello(),结果是弹出`chenundefinedundefined	`	
我们希望调用函数sayHello,this能指向foo,通过this.name获得foo.name.
<!-- more -->
Javascript提供了几个改变函数this指向的方法，call、apply和ES5提出的bind.		
如果我们要寄己实现这些方法的polyfill呢，我们知道作为对象的方法调用，方法内的this是指向对象实例的。			
如果想让函数的this指向对象foo,可以让sayHello成为foo的属性，但在调用完成后要delete这个属性。

实现中要注意一点：

- 在非严格模式下使用函数的apply()或call()方法时，第一个参数为null或undefined时，会被转换为全局对象，也就是在浏览器中this指向window			
- 在严格模式下，函数的this值始终是指定的值，也就是说第一个参数指定是null,this就是null。（JavaScript高级程序设计 附录B 严格模式 B.7 抑制this）

那么执行下面代码会报错

```javascript
(function(){
	'use strict'
	sayHello.apply(null,[27,'男'])
})()
```

<pre style="color:red">Uncaught TypeError: Cannot read property 'name' of null</pre>

这里都按非严格模式来实现

## call函数
```javascript
Function.prototype.call_ = function(ctx){
	ctx = ctx || window;
    ctx.fn = this;
    let args = [];
    let length = arguments.length;
    for(let i=1;i<length;i++){
    	args.push(arguments[i]);
    }
    let result = ctx.fn(...args);
    delete ctx.fn;
    return result;
}
```

```javascript
//调用
sayHello.call_(foo,25,'女')  //chang25女
sayHello.call_(null,27,'男') //chen27男
```

## apply函数
```javascript
Function.prototype.apply_ = function(ctx){
	ctx = ctx || window;
	ctx.fn = this;
	let args = arguments[1] || [];
    let result = ctx.fn(...args);
    delete ctx.fn;
    return result;
}
```

```javascript
//调用
sayHello.apply_(foo,[25,'女'])  //chang25女
sayHello.apply_(null,[27,'男']) //chen27男
```

## bind函数
```javascript
Function.prototype.bind_ = function(ctx){
	let _this = this; //this指向Function实例，即调用bind_的函数
	let outerArgs = Array.prototype.slice.call_(arguments,1);
	return function(){
		let innerArgs = Array.prototype.slice.call_(arguments);
		let finalArgs = outerArgs.concat(innerArgs);
		_this.apply_(ctx,finalArgs);
	}
}
```

```javascript
//调用
fn = sayHello.bind_(foo,25);
fn('女')  //chang25女

fn = sayHello.bind_(null,27);
fn('男')  //chen27男
```

**注意**

- ctx是要改变的函数内部的执行环境，即this指向
- 每个函数在被调用时都会自动取得两个特殊变量：this和arguments,内部函数在搜索这两个变量时，只会搜索到其活动对象为止，永远不可能访问到外部函数中的这两个变量。如果想访问作用域链中的this和arguments，必须将他们的对象引用保存在另一个闭包能访问到的变量中。
- ES6提供spread运算符 ...，可将数组或类数组对象展开成一系列用逗号隔开的值

## 参考
- JavaScript高级程序设计 第5章  引用类型 5.5.4  函数内部属性   this和arguments
- JavaScript高级程序设计 第5章  引用类型 5.5.5  函数属性和方法 call()和apply()
- JavaScript高级程序设计 第7章  函数表达式 7.2.2 （闭包）关于this对象）
- JavaScript高级程序设计 第22章 高级技巧 22.1.4 函数绑定bind()
