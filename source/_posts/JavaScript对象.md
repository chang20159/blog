---
title: JavaScript对象
tag:
- JavaScript
categories:
- JavaScript
---
所有的 JavaScript 对象继承于至少一个对象。被继承的对象被称作原型，并且继承的属性都在构造函数的 prototype属性中。通过对象的__proto__属性可以找出对象的原型链。
<!-- more -->
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