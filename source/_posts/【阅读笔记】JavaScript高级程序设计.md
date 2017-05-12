---
title: 【阅读笔记】JavaScript高级程序设计-面向对象
categories:
- 阅读笔记
---
什么是对象？怎样创建对象？
构造函数、原型和实例之间有什么关系？原型链是怎样构成的？
JavaScript是怎样实现继承的？
如果不清楚，那就多看几遍【JavaScript高级程序设计】
<!-- more -->

## 原始值
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

## 对象
ECMA-262 把对象定义为:“无序属性的集合，其属性可以包含基本值、对象或者函数。每个对象都是基于一个引用类型创建的

### 对象属性
**属性的增删改查**
添加属性

    profile.age = 100;
删除属性 
   
    delete profile.name;
设置属性值 
   
    profile.age = 24;
查看属性值
- . 点运算符  book.name
- []运算符  book['name']  []括号内必须是字符串或可转为字符串

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

**属性特性**        
1、数据属性特性
    
- [[Configurable]] 是否可配置
- [[Enumerable]]   是否可枚举（for...in）
- [[Writable]]     是否可修改属性值
- [[Value]]        属性的数据值
    
2、访问器属性特性
    
- [[Configurable]] 是否可配置
- [[Enumerable]]   是否可枚举（for...in）
- [[Get]]         读取属性时调用
- [[Set]]         写入属性时调用

**相关方法**    
    
- Object.defineProperty(object,name,{..属性特性})   设置属性特性
- Object.defineProperties(object,{..})  设置属性特性
- Object.getOwnPropertyDescriptor(object,name) 获得自有属性的特性

例: 下面定义了一个对象的属性，p是没有自有属性的，使用Object.defineProperty（）方法后创建了自有属性，x和y是数据属性，z是访问器属性

```
var p = Object.defineProperties({},{
    x: { value:1, writable:true,enumerable:true,configurable:true},
    y: { value:2, writable:true,enumerable:true,configurable:true},
    z: {
        get: function(){ return Math.sqrt(this.x * this.x + this.y * this.y )},
        set: function(val){this.x=val},
        enumerable:true,
        configurable:true
    }
})
```


## 创建对象
### 对象直接量   
对象原型：Object.prototype
  
  ```
  let person = {
        name : 'chang',
        age : 25
  }
  ```
### 工厂模式        
对象原型：Object.prototype   
    
    ```
    function createPerson(name,age){
        let o = new Object();
        o.name = name,
        o.age = age
        o.sayHello = function(){
            console.log(`I am ${this.name}`)
        }
    }
    let person1 = createPerson('chang',25);
    let person2 = createPerson('chen',27);
    ```
### 构造函数模式      
对象原型：Person.prototype   
Person.prototype == person1._ _proto_ _     
person1._ _proto_ _ == person1.constructor.prototype    

    ```
    function Person(name,age){
        this.name = name,
        this.age = age
        this.sayHello = function(){
            console.log(`I am ${this.name}`)
        }
    }
    let person1 = new Person('chang',25);
    let person2 = new Person('chen',27);
    ```

**构造函数模式与工厂模式的不同之处**

- 没有显示创建对象
- 直接将属性和方法付给了this对象
- 没有return返回

注意：构造函数应该习惯用大写开头 区别于其他函数

**new一个实例的过程**

- 创建一个新对象
- 将构造函数的作用域赋给新对象,使this指向新对象
- 执行构造函数，为新对象添加属性
- 返回新对象

**将构造函数作为普通函数调用**   
任何函数，只要通过 new 操作符来调用，那它就可以作为构造函数;   
任何函数，如果不通过 new 操作符来调用，就跟普通函数一样。

```
let person1 = new Person('chang',25);  //this指向新建对象
person.sayHello(); //I am chang

Person('chang',25); //this指向Global对象 浏览器中就是window对象
window.sayHello(); //I am chang

let o = new Object();  //this指向另一个对象
Person.call(o,'chang',25);
```

### 原型模式    
    
```
function Person(){}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
    alert(this.name);
};
var person1 = new Person();
person1.sayName();   //"Nicholas"
var person2 = new Person();
person2.sayName(); //"Nicholas"
alert(person1.sayName == person2.sayName);  //true
```

原型模式的特点是不需要向构造函数传入初始化参数，
但也因为所有参数都在原型中，被实例共享，所有实例的属性值都相同
特别是当属性是一个引用时，实例1修改了引用指向的数据，实例2中的引用指向的数据同时会被修改
因此一般不会用原型模式创建对象


**关于原型**

每创建一个函数，就会同时创建它的 prototype 对象，这个对象也会自动获得一个默认的 constructor 属性。       
Person.prototype.constructor == Person  
实例可以访问原型属性      
因此person1.constructor == Person

```
Person.prototype
    >Object {constructor: function}
        >constructor:function Person(name,age)
        >__proto__:Object
```

注意：如果在已经创建了实例的情况下重写原型，会切断现有实例与新原型之间的联系      
例如：     
重写了Person.prototype，Person.prototype.constructor也不再指向Person,        
但person instanceof Person == true

**对象实例访问保存在原型中的值，但却不能通过对象实例重写原型中的值，会创建新属性，覆盖原型中的同名属性**

**属性相关方法**  

- person1.hasOwnProperty('name') 
- 'name' in person1  in操作符可访问对象可访问可枚举的所有属性
- for...in  遍历对象可访问可枚举的所有属性
- Object.keys(o)  获取可枚举的自有属性

```
//判断属性是在实例中还是在原型中
function hasPrototypeProperty(object, name){
        return !object.hasOwnProperty(name) && (name in object);
}
```
   
### 组合使用构造函数模式和原型模式
 构造函数模式用于定义实例属性，        
 而原型模式用于定义方法和共享的属性,     
 这是最合适的创建对象的方法
 
 ```
 function Person(name, age){
    this.name = name; 
    this.age = age;
    this.friends = [];
  }
    Person.prototype = {
    constructor : Person,
    sayName : function(){
        alert(this.name);
    }
}
var person1 = new Person("chang", 25);
var person2 = new Person("chen", 27);
person1.friends.push("wang");
alert(person1.friends);    //"Shelby,Count,Van"
alert(person2.friends);    //"Shelby,Count"
alert(person1.friends === person2.friends); //false
alert(person1.sayName === person2.sayName); //true
 ```
### 动态原型模式
在构造函数中初始化原型（如果必要）

```
function Person(name, age){
    this.name = name; 
    this.age = age;
    if (typeof this.sayName != "function"){
        Person.prototype.sayName = function(){
            alert(this.name);
        };
    } 
}
```

## 继承
ECMAScript是基于原型链继承的
### 原型链
理解原型链，首先要清楚 构造函数、原型和实例的关系。          
每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，而实例都包含一个指向原型对象的内部指针。

如果这个原型对象是另一个类型的实例，那这个原型对象内部也有一个指针指向另一个原型对象，这样层层递进，就形成了最初那个实例的一个原型链。

注意：所有函数的默认原型都是 Object 的实例，因此默认原 型都会包含一个内部指针，指向 Object.prototype。

实现原型链有一种基本模式

```
function SuperType(){
        this.property = true;
}
SuperType.prototype.getSuperValue = function(){
    return this.property;
};
function SubType(){
    this.subproperty = false;
}
//继承了 SuperType
SubType.prototype = new SuperType();
SubType.prototype.getSubValue = function (){
    return this.subproperty;
};
var instance = new SubType();
alert(instance.getSuperValue());
//true
```
注意    
instance.constructor现在指向的是SuperType，因为   
instance.constructor读取的是instance的原型对象上的constructor   
instance的原型对象是SuperType的实例   
SuperType的实例的constructor指向SuperType   
所以 instance.constructor指向SuperType

啊 晕不晕 反正我是晕了

最后，原型链有哪些问题？

```
function SuperType(){
   this.colors = ["red", "blue", "green"];
}
function SubType(){}
//继承了 SuperType
SubType.prototype = new SuperType();
var instance1 = new SubType(); 
instance1.colors.push("black"); alert(instance1.colors); //"red,blue,green,black"
var instance2 = new SubType(); alert(instance2.colors); //"red,blue,green,black"
```
可以看到SubType的原型是SuperType的实例，具有实例属性colors = ["red", "blue", "green"]，这是个引用类型值，instance1改变colors的值时也会影响instance2

两个问题：           
1、原型链中如果包含引用类型值的，当被一个实例修改时，会反应在其他实例上，因为原型的属性是共享的            
2、在创建子类型的实例时，没有办法在不影响所有对象实例的情况下，给超类型的构造函数传递参数

因此，实践中很少会单独使用原型链实现继承

### 借用构造函数（经典继承）
在子类型构造函数的内部调用超类型构造函数

```
function SuperType(name){
    this.name = name;
    this.colors = ["red", "blue", "green"];
}
function SubType(name){
    //继承了 SuperType
    SuperType.call(this,name);
}
var instance1 = new SubType('chang');
instance1.colors.push("black"); 
console.log(instance1.name)  //chang
console.log(instance1.colors);    //"red,blue,green,black"
var instance2 = new SubType('chen'); //chang
console.log(instance2.name)
console.log(instance2.colors);    //"red,blue,green"

```
通过使用call()方法或apply()方法，         
在(未来将要)新创建 SubType 实例的环境下调用了 SuperType 构造函数,        
让SubType的每个实例就都具有自己的 colors 属性的副本，
也解决了原型链中子类型无法给超类型的构造函数传递参数的问题

但是如果只是借用构造函数，所有的超类型都在构造函数中定义，就无法链式继承或复用了。所以一般页不会单独只借用构造函数

### 原型链和构造函数组合继承
将原型链和借用构造函数的技术组合到一块，使用原型链实现对原型属性和方 法的继承，而通过借用构造函数来实现对实例属性的继承