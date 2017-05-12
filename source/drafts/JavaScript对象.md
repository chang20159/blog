---
title: JavaScript对象
tag:
- JavaScript
categories:
- JavaScript
---
所有的 JavaScript 对象继承于至少一个对象。被继承的对象被称作原型，并且继承的属性都在构造函数的 prototype属性中。通过对象的__proto__属性可以找出对象的原型链。
<!-- more -->

## 原始值与对象
JavaScript中的原始值有：undefined、null、字符串、数字、布尔值。对象有函数、数组等。对象可以访问自己的属性和方法，原始值也可以访问属性和方法    

	> var str = "chang";
	str.concat("20159");
	< "chang20159"
	
<!-- more -->
只要引用字符串的属性，javascript就会将字符串值通过调用new String(str)的方式转换成对象，这个对象继承了String的方法，并被用来处理属性的引用。一旦属性引用结束，这个新创建的对象就会销毁。数字和布尔值也有同样的方法new Number()和new Boolean().

访问字符串、数字或布尔值的属性和方法时创建的临时对象称为包装对象

如果执行下面的代码：

	> var str = "chang";
	str.len = 5;
	console.log("str is ",str);
	console.log("str.len is ",str.len);
	
	< str is chang
	str.len is undefined
	
说明字符串原始值虽然能够调用方法，但并不是对象，对属性赋值会被忽略。

如果直接使用构造函数创建字符串对象，则可以给这个对象赋值。运行以下代码可以看出str对象新增了属性len,值为5。

	> var str = new String("chang");
	str
	< String {0: "c", 1: "h", 2: "a", 3: "n", 4: "g", length: 5, [[PrimitiveValue]]: "chang"}     
	0: "c"
	1: "h"
	2: "a"
	3: "n"
	4: "g"
	length: 5
	__proto__: String
	[[PrimitiveValue]]: "chang"
	
	
	> str.len = 5;
	str
	< String {0: "c", 1: "h", 2: "a", 3: "n", 4: "g", len: 5, length: 5, [[PrimitiveValue]]: "chang"}
	0: "c"
	1: "h"
	2: "a"
	3: "n"
	4: "g"
	len: 5
	length: 5
	__proto__: String
	[[PrimitiveValue]]: "chang"


原始值与对象的区别

- 原始值是不可变的，对象是可修改的。
- 相同值的原始值和对象相等但不严格相等
- 对于字符串来说，原始值的数据类型是string,对象的数据类型是object

代码如下

	> var str1 = "chang";
	var str2 = new String("chang");
	console.log("str1 == str2",str1 == str2);
	console.log("str1 === str2",str1 === str2);
	console.log("typeof str1 :", typeof str1);
	console.log("typeof str2 :", typeof str2);
	
	< str1 == str2 true
	str1 === str2 false
	typeof str1 : string
	typeof str2 : object
	
**注: typeof的返回值一般是这些： number,string, boolean,object,function,undefined**


## 创建对象
### 使用对象直接量
对象直接量是一个表达式，每次运算都创建并初始化一个新的对象    
这种方式创建的对象，其原型是Object

	> var profile = {
	   name: "chang",
	   school: "SEU",
	   favourate: ["reading","singing"]
	};
	profile;
	
	< Object {name: "chang", school: "SEU", favourate: Array[2]}
		favourate: Array[2]
		name: "chang"
		school: "SEU"
		__proto__: Object
		
	> Object.getPrototypeOf(profile)
	< Object {}
	
	> Object.getOwnPropertyNames(profile);
	< ["name", "school", "favourate"]

### 使用new + 构造函数创建
new 操作符创建了一个新的通用对象，并将其 __proto__ 属性设置为 构造函数的prototype属性值。

new后跟随一个函数调用，这个函数称作构造函数。JavaScript中的原始类型都有内置构造函数。
使用内置函数作为构造函数创建

	> var profile = new Object({
		   name: "chang",
		   school: "SEU",
		   favourate: ["reading","singing"]
		});
	profile;
	
	< Object {name: "chang", school: "SEU", favourate: Array[2]}
	favourate: Array[2]
	name: "chang"
	school: "SEU"
	__proto__: Object
	
	> Object.getPrototypeOf(profile)
	< Object {}
	
	> Object.getOwnPropertyNames(profile);
	< ["name", "school", "favourate"]
	
自定义构造函数创建

	> function Person(name,age,favourate){
	   this.name = name;
	   this.age = age;
	   this.favourate = favourate;
	};
	var profile = new Person("chang","SEU",["reading","singing"]);
	profile;
	
	< Person {name: "chang", age: "SEU", favourate: Array[2]}
	age: "SEU"
	favourate: Array[2]
	name: "chang"
	__proto__: Object
	
	> Object.getPrototypeOf(profile);
	< Object {}
	
	> Object.getOwnPropertyNames(profile)
	< ["name", "age", "favourate"]

### Object.create()创建
	这种方式创建的对象，其原型是Object.create()的入参
	
	> var profile = Object.create({
			   name: "chang",
			   school: "SEU",
			   favourate: ["reading","singing"]
			});
	profile;
	
	< Object {}
		__proto__: Object
			favourate: Array[2]
			name: "chang"
			school: "SEU"
			__proto__: Object
			
	> Object.getPrototypeOf(profile);
	< Object {name: "chang", school: "SEU", favourate: Array[2]}
	
	> Object.getOwnPropertyNames(profile)
	< []
	
可见，通过Object.create(proto_object)创建的对象,proto_object是新对象的原型，新对象本身没有自有属性。

## 对象属性
### 属性的增删改查
添加属性

	profile.age = 100;
删除属性 
   
	delete profile.name;
设置属性值 
   
	profile.age = 24;
查看属性值

	profile.age;
	或 profile["age"];
检测属性
in运算符用来检测对象中是否包含属性（自有属性和继承属性）

	"age" in profile
可以用对象的hasOwnProperty()检测是否自有属性

	profile.hasOwnProperty("age");
	profile.hasOwnProperty("toString");

注意：   
- 全局范围内定义的属性无法删除
- delete运算符只能删除自有属性，不能删除继承属性

### 属性的特性
属性的特性有4个：	
    
- 属性的值 value    
- 可写性 writable	
- 可枚举性 enumerable	
- 可配置性 configurable	
	
可以通过Object.getOwnPropertyDescriptor()方法获得自有属性的特性

	> var profile = new Object({
			   name: "chang",
			   school: "SEU",
			   favourate: ["reading","singing"]
			});
	for(o in profile){
	    console.log(Object.getOwnPropertyDescriptor(profile,o));
	}
	
	< Object {value: "chang", writable: true, enumerable: true, configurable: true}
	Object {value: "SEU", writable: true, enumerable: true, configurable: true}
	Object {value: Array[2], writable: true, enumerable: true, configurable: true}
但是不能获得继承属性的特性

	> var profile = Object.create({
				   name: "chang",
				   school: "SEU",
				   favourate: ["reading","singing"]
				});
	for(o in profile){
	    console.log(Object.getOwnPropertyDescriptor(profile,o));
	};
	< undefined
	
Object.defineProperty 设置属性特性

	>var profile = Object.create({
				   name: "chang",
				   school: "SEU",
				   favourate: ["reading","singing"]
				});
	Object.defineProperty(profile,"name",{
		value: "chang", 
		writable: false, 
		enumerable: true, 
		configurable: true
	});
	
	< Object {name: "chang"}
	  name: "chang"
	  __proto__: Object
		  favourate: Array[2]
		  name: "chang"
		  school: "SEU"
		  __proto__: Object

profile是没有自有属性的，使用Object.defineProperty（）方法后创建了自有属性name，说明Object.defineProperty不能设置继承属性的特性。

	> profile.hasOwnProperty("name")
	
	<true
现在给对象创建了一个自有属性并且是不可修改的，如果执行以下操作，可以看到属性值没有变化。

	> profile.name = "chang20159";
	profile;
	
	< Object {name: "chang"}
	  name: "chang"
	  __proto__: Object
		  favourate: Array[2]
		  name: "chang"
		  school: "SEU"
		  __proto__: Object