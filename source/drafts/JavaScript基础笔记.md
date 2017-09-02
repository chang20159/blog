---
title: JavaScript基础笔记
tag:
- JavaScript
categories:
- JavaScript
---

## 基本包装类型
ECMAScript提供3个特殊的引用类型： Boolean、Number、String

```javascript
var s1 = "some text";var s2 = s1.substring(2);
```
变量s1是包含一个字符串"some text"，字符串是基本数据类型，为什么在第二行可以调用substring方法？

执行第二行代码时会做下面几个操作：

- 创建一个String类型的实例
- 在实例上调用指定方法
- 销毁这个实例

相当于：

```javascript
var s1 = new String("some text");
var s2 = s1.substring(2);
s1 = null;
```

**引用类型与基本包装类型的主要区别就是对象的生存期。**

- new操作符创建的引用类型实例在离开执行环境前一直保存在内存中
- 自动创建的基本包装类型的对象只存在与一行代码的执行瞬间

	例如下面第三行代码是访问不到color属性的，因为在第二行执行结束就已经销毁了实例
	
	```
	var s1 = "some text";
	s1.color = "red";
	alert(s1.color);   //undefined
	```

## 单体内置对象
内置对象不必显示实例化
### Global对象
所有在全局作用域中定义的属性和函数，都是Global对象的属性。浏览器实现了承担该角色的window对象。

- `isNaN()`
- `isFinite()`
- `parseInt()`
- `parseFloat()`
- `encodeURI()/encodeURI()`
	
	用于对整个URI编码/解码，不会对本身属于URI的特殊字符进行编码/解码，		
	例如冒号(:)、正斜杠（/ ）、问号(?)、井号(#) 
- `encodeURIComponent()/decodeURIComponent()`
	
	用于对URI中的某一段编码/解码。一般情况下使用encodeURIComponent()方法要比 encodeURI()多，因为实践中最常见的是对查询字符串参数而不是对基础URL编码 。
- `eval()`
  
  像一个JS解析器。接收一个参数，即要执行的JS字符串

### Math对象
常用方法：

- `Math.min()、Math.max()`
- `Math.ceil()`
- `Math.floor()`
- `Math.round()`
- `Math.random()`
- `Math.abs()`
- `Math.exp()`
- `Math.power()`
- `Math.sqrt()`

**Math.random()在随机显示的场景很有用，从某个整数范围内随机取一个值：**

```
值 = Math.random() * 可能值总数 + 第一个可能的值
```

## 函数

### 函数声明与函数表达式
解析器在向执行环境加载数据时，会先读取函数声明（函数声明提升），使其在执行任何代码之前可用。

函数表达式必须等解析器执行到它所在的代码行才会被解释执行。 

### 函数内部属性
函数内部有两个特殊对象：arguments 和 this

#### arguments
一个类数组对象

- **arguments.callee**

	是一个指针，指向拥有这个arguments对象的函数。			
	严格模式下不能访问arguments.callee，会报错。			
	注意:函数名仅是一个包含指针的变量，在函数内部使用函数名调用自身时，引用函数名不能改变。

- **arguments.caller**

	ES5定义这个属性，只是为了区分函数的caller属性。
	严格模式下访问会报错，非严格模式下这个属性始终是undefined。
	
- arguments.length
	参数长度

#### this
this引用的是函数执行的环境对象（作用域）

### 函数属性
- name
	
	函数名称
- caller

	保存调用当前函数的函数的引用。
	在函数内部访问方式有两种：
	
	```javascript
	function func(){
		console.log(arguments.callee.caller)
	}
		
	function func(){
		console.log(func.caller)
	}
	```
- length
	
	length属性表示函数接收的命名参数的个数

- prototype

  对于JS中的引用类型，prototype通常是用于保存实例方法。在创建自定义引用类型及实现继承时，prototype属性很重要。prototype属性有一个唯一属性：constructor，指向拥有此prototype的函数。

### 函数的方法
每个函数都包含3个非继承而来的方法，可用于设置函数体内this对象的值。

- apply(this,[array])
- call(this,arg1,arg2,...)
- bind(this，arg1，arg2) 
	
	创建一个函数的实例this，this值会绑定到传入的this值。


## 什么是闭包？


每一段Js代码都有一个与之关联的作用域链，对于一个函数，函数定义时的作用域链在函数执行时依然有效，当函数执行时可以访问作用域链上的变量，而且无论这个函数将来在哪里执行都能够访问到函数定义时的作用域链中的变量。

函数的作用域链上除了函数本身的作用域对象之外，有最外层的全局作用域，还可能有外层函数的作用域，这种可以访问另一个函数作用域或全局作用域中的变量的函数就是一个闭包。

从这个角度讲，所有的Javascript函数都是闭包。

闭包的形成可归因于： JS的词法作用域和作用域链。（作用域链上有其他外层函数的作用域，函数可逐层搜索变量）

----
下面是之前整理的对于闭包的理解，先放着。

理解闭包先得理解词法作用域和作用域链
**词法作用域**
函数执行依赖于变量作用域，这个作用域是在函数定义是决定的，不是在函数调用时决定的。
为了实现这种词法作用域，javascript函数对象内部不仅包含函数的代码逻辑，还必须引用当前的作用域链
**作用域链**
- javascript是基于词法作用域的语言，每一段代码（全局代码或函数）都有一个与之关联的作用域链，这个作用域链是一个对象链表。
- 在javascript最顶层的代码中，作用域链由一个全局对象组成 
- 在不包含嵌套的函数体内，作用域上有两个对象，第一个是定义函数参数和局部变量的对象,第二个是全局对象
- 在一个嵌套的函数体内，作用域链上至少有三个对象。
- 当定义一个函数时，它实际上会保存一个作用域链。
- 当调用这个函数时，它创建一个新的对象来存储它的局部变量，并将这个对象添加至它保存的作用域链上，同时创建一个新的更长的表示函数调用作用域的链
- **对于嵌套函数，每次调用外部函数时，内部函数又会重新定义一遍，因为每次调用外部函数时作用域链都是不同的。**

闭包 由函数和函数定义是的作用域链组成
所有的javascript函数都是闭包，**闭包就是函数定义时的作用域链在函数执行时依然有效。**

根据以上规则，看看下面输出什么

```javascript
function func(){
    var count = 0;
    function cal(){
        var num= 0
        console.log('num',num++)
         console.log('count',count++ ) ;
    }
    return cal;
}
var func1 = func();    //每次调用func都会创建一个新的作用域链和新的私有变量
var func2 = func();   //func1与func2互不影响
func()();  //num 0  count 0
func()();  //num 0  count 0
func1();   //num 0  count 0
func1();   //num 0  count 1
func2();   //num 0  count 0
func2();   //num 0  count 1
```

javascript是基于词法作用域的，函数的作用域在函数定义时就决定了，如果一个函数内部有一个嵌套函数，并将这个函数返回了，那这个返回的函数韩式可以访问定义时就决定的作用域链，所以这个返回的函数在父函数外部能访问到父函数内部的变量，这样这个函数就形成了一个闭包。


## 垃圾回收机制
- **标记清除**

	JavaScript中最常用的垃圾回收方式，回收被标记的（需要回收的）变量。
		
	垃圾回收器在运行时，给存储在内存中的所有变量都添加标记，然后去掉环境中的变量以及被环境中的变量引用的变量的标记。在此之后再被加上标记的变量将被视为准备删除的变量，最后垃圾回收器消除那些带标记的值并回收内存空间。

	目前各大浏览器都是采用标记清除式策略，不过垃圾回收的时间间隔不同。

- **引用计数**
  
  跟踪记录每个值被引用的次数。
  
  如果一个值的被引用次数变成0，说明不能再访问到这个值了，就可以将占用的内存回收。垃圾回收器运行时会释放那些引用次数为0的值所占用的内存。
 
  IE中有一部分对象并不是原生Javascript对象，例如BOM、DOM中的对象是使用C++以COM（组件对象模型）对象的形式实现的。而COM对象的垃圾回收机制采用的是引用计数策略。尽管IE的JS引擎是使用标记清除策略，但JS访问的COM对象依然基于引用计数策略。
  
  当出现循环引用时，引用计数永远不会是0，当被多次调用时，会有大量内存得不到回收，容易造成内存泄漏。在IE中只要涉及COM对象就容易出现循环引用的问题。
  
	例如：
	  
	```javascript
	var element = document.getElementById("some_element");
	var myObject = new Object();
	myObject.element = element;
	element.someObject = myObject;
	```
  
  导致循环引用的情况不止如此。
  
  为解决此问题，IE9把BOM和DOM对象都转成了真正的Javasript对象，避免了常见的内存泄漏现象。 
  


## 对象
### 创建对象
#### 工厂模式
用函数来封装创建特定对象的细节，虽然能解决创建多个相似对象的问题，但不能解决对象识别的问题。

```javascript
function createPerson(name, age, job){
    var o = new Object();
        o.name = name;
        o.age = age;
        o.job = job;
        o.sayName = function(){
            alert(this.name);
    };
	return o; 
}
var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");
```
  
#### 构造函数模式

构造函数模式可以创建特定类型(自定义的构造函数)的对象。

```javascript
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function(){
        alert(this.name);
    }; 
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```

注意：构造函数应该以大写字母开头，非构造函数应该以小写字母开头，主要是为了区分。

要创建Person的实例，必须使用new操作符。用这种方式调用构造函数会经历4个步骤：

- 创建一个新对象
- 将构造函数的作用域赋给新对象，即this指向这个新对象
- 执行构造函数中代码，为新对象添加属性
- 返回新对象

##### 将构造函数当做函数

构造函数也是函数，任何函数用new操作符来调用，它就是一个构造函数。不用new操作符调用就是一个普通函数。

```javascript
//当做构造函数使用 
var person = new Person("Nicholas", 29, "Software Engineer"); 
person.sayName(); //"Nicholas"

//当普通函数调用，在全局环境中调用一个函数时，this对象重视指向Global对象
Person("Greg", 27, "Doctor"); //this指向window window.sayName()

// 在另一个对象的作用域中调用
var o = new Object();
Person.call(o, "Kristen", 25, "Nurse"); o.sayName(); //"Kristen"
```

##### 使用构造函数创建的问题

使用构造函数的一个主要问题是：

>每个方法在每个实例上都要重新创建一次。我们知道，函数也是对象，在前面的例子中，person1和person2都有一个Function的实例sayName，但person1.sayName != person2.sayName，他们的作用域链不同，标识符解析也不同。

创建两个完成相同功能的Function实例是没有必要的。因而我们可以像这样把方法提出来：

```javascript
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = sayName;
}
function sayName(){
    alert(this.name);
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```
在全局作用域定义方法，这样person1和person2就可以共享一个方法。但是这样所有的方法都得在全局作用域中定义，而且只能给特定对象使用，显然也不合理。

我们可以使用原型模式来解决这个问题


#### 原型模式

将属性和方法都添加到构造函数的prototype属性中。

```javascript
function Person(){}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
    alert(this.name);
};
var person1 = new Person();
var person2 = new Person();
person1.name = "Greg";
alert(person1.name);  //"Greg"
alert(person2.name);  //"Nicholas"    
delete person1.name;
alert(person1.name);  //"Nicholas"
```

##### 原型对象

要清楚构造函数、实例和原型对象三者之间的关系。

- 每个函数都有一个prototype属性，prototype属性是一个指针，指向函数的原型对象
- 所有原型对象默认都有一个constructor属性，这个属性指向prototype所属的构造函数。
- 当调用构造函数创建一个新实例后，这个实例内部有一个指针[[Prototype]]指向构造函数的原型对象。



注意：

- 我们可以通过对象实例访问原型中的值，但不能重写原型中的值。
- 当为对象实例添加一个属性时，会屏蔽原型中的同名属性。
- 即使给该属性赋值null也不行，使用delete操作符可以完全删除实例属性

##### 判断是否是原型中的属性

```javascript
function hasPrototypeProperty(object, name){
    return !object.hasOwnProperty(name) && (name in object);
}
```

##### 实例属性与原型属性相关操作符和方法

- `in 和 for...in`  所有能够访问到的属性（实例属性、原型属性）
- `hasOwnProperty()` 属性是否存在于实例中
- `isPrototypeOf()` 对象是否是实例的原型对象(实例内部是否有指向该对象的指针)
- `Object.keys()`   对象上所有可枚举的实例属性
- `Object.getOwnPropertyNames()`  所有实例属性，无论是否可枚举
- `Object.getPrototypeOf()`获取实例内部[[Prototype]]指针指向的原型对象

##### 重写整个原型对象的问题

前面的例子是逐个添加原型对象上的属性和方法，我们也可以直接给prototype对象整个赋值：

```javascript
function Person(){}

Person.prototype = {
    name : "Nicholas",
    age : 29,
    job: "Software Engineer",
    sayName : function () {
        alert(this.name);
    }
};

```
**但是这样原本Person.prototype中默认的constructor属性就没有了，当访问constructor属性时就是向上访问到Object的constructor属性，指向Object构造函数。**

也就是说，这个整体赋值，constructor属性不再是Person构造函数，而是指向Object构造函数。

person1 instanceof Person 还是返回true，但person1.constructor == Object  无法确定对象的类型了。

**注意： object instanceof constructor， 用来检测 constructor.prototype 是否存在于参数 object 的原型链上。**

##### 设置原型对象constructor属性的问题
如果constructor很重要，可以特意设置回原本默认的值：

```javascript
function Person(){}

Person.prototype = {
    constructor : Person,
    name : "Nicholas",
    age : 29,
    job: "Software Engineer",
    sayName : function () {
        alert(this.name);
    }
};

```

**但是，还是有问题！！这样重设的constructor属性是可枚举的，而默认情况下的原生constructor属性是不可枚举的。**

那我们还可以使用 Object.defineProperty()解决这个问题：

```javascript
//只适用于支持ES5的浏览器  
Object.defineProperty(Person.prototype, "constructor", {
     enumerable: false,
     value: Person
});
```

##### 使用原型模式创建的问题

- 省略了为构造函数传递初始化参数这一环节，每个实例默认情况下都有相同的属性值
- 原型对象是所有实例共享的，这个对于函数非常合适。
- 对于基本数据类型放在原型中也还没什么问题，因为包含基本值的属性会覆盖原型中的同名属性
- 但对于引用类型就会有问题，例如：
   
   ```javascript
   function Person(){}
   
	Person.prototype = {
	    constructor: Person,
	    name : "Nicholas",
	    age : 29,
	    job : "Software Engineer",
	    friends : ["Shelby", "Court"],
	    sayName : function () {
	        alert(this.name);
	} };
	var person1 = new Person();
	var person2 = new Person();
	person1.friends.push("Van");
	alert(person1.friends);    //"Shelby,Court,Van"
	alert(person2.friends);    //"Shelby,Court,Van"
	alert(person1.friends === person2.friends);  //true
   ```

#### 组合使用构造函数模式和原型模式
创建自定义类型的对象，最常用的方式是组合使用构造函数和原型，也是定义引用类型的一种默认模式。

- **构造函数模式用于定义实例属性**
- **原型模式用于定义方法和共享的属性**

```javascript
function Person(name, age, job){
	this.name = name; 3 this.age = age;
	this.job = job;
	this.friends = ["Shelby", "Court"];
}
Person.prototype = {
    constructor : Person,
    sayName : function(){
        alert(this.name);
    }
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```
在上面的例子中，实例属性都在狗在函数中定义，所有实例共享的属性constructor和方法sayName则在原型中定义。

#### 动态原型模式
在构造函数中动态初始化原型,在第一次执行构造函数时就完成初始化了

```javascript
function Person(name, age, job){
	this.name = name; 
	this.age = age; 
	this.job = job;
	
	if (typeof this.sayName != "function"){
	    Person.prototype.sayName = function(){
	        alert(this.name);
		}; 
	}
}
```

#### 寄生构造函数模式
在上面几种模式都不适用的情况下，可以使用寄生构造函数模式。

基本思想是创建一个函数，这个函数的作用仅仅是封装创建对象的代码。

```javascript
 function Person(name, age, job){
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function(){
        alert(this.name);
    };
	return o; 
}
var friend = new Person("Nicholas", 29, "Software Engineer");
friend.sayName();  //"Nicholas"
```

**注意： 构造函数在不返回值的情况下，默认会返回新的实例。通过在构造函数的末尾添加return语句，可以重写调用构造函数时返回的值。**

但是这种方式返回的对象与构造函数以及构造函数的原型之间没有联系。

####  稳妥构造函数模式

没有公共属性，方法也不引用this对象。

```javascript
function Person(name, age, job){
    o.sayName = function(){
        alert(name);
    };
	return o; 
}
var friend = Person("Nicholas", 29, "Software Engineer");
friend.sayName();  //"Nicholas"
```    
与寄生构造函数模式的不同：

- 新创建的实例方法不引用this
- 不适用new操作符调用函数
   

## 继承
### 原型链
#### 默认的原型
#### 原型和实例的关系
#### 原型链的问题

### 借用构造函数继承
### 组合继承
### 原型式继承
### 寄生式继承
### 寄生式组合式继承


## 参考

- JavaScript权威指南
- JavaScript高级程序设计