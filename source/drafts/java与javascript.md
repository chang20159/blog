java和javascript语言的区别

javascript

- 弱类型语言，JavaScript 是一种基于原型的面向对象语言，而不是基于类的。
- 对象属性是可以动态添加和删除的。	
- 有基于原型的对象模型提供的动态继承，即独立对象的继承是可以改变的。    
- 支持匿名函数	
- 形式比较自由，不需要显示指定变量、参数、方法返回值的数据类型
- 变量类型不需要提前声明(动态类型),支持变量提升
- 不能直接自动写入硬盘。
- JavaScript 属性值的继承是在运行时通过检索对象的原型链来实现的。因为对象只有一个原型与之关联，所以 JavaScript 无法动态地从多个原型链中继承。
在 JavaScript 中，可以在构造器函数中调用多个其它的构造器函数。这一点造成了多重继承的假象。
    

JavaScript是区分大小写的语言，变量是无类型的，在缺少了分号就无法正确解析代码时会填补分号。JavaScript的数据类型分类两类：   
    
- 原始类型   数字、字符串和布尔值，两个特殊的原始值：null(空)、undefined(未定义)
- 对象类型 特殊的对象：全局对象、数组、函数

JavaScript中的内置对象

/Applications/WebStorm.app/Contents/plugins/JavaScriptLanguage/lib/

/Applications/WebStorm.app/Contents/plugins/JavaScriptLanguage/lib/com/intellij/lang/javascript/index/predefined

## 变量

JavaScript变量是无类型的

布尔值  任意JavaScript值都可以转换为布尔值。下面这些值被转换成false

- undefined
- null
- 0
- -0
- NaN
- "" 空字符串
这些值也成为假值

## 全局对象
当JavaScript解释器启动或者任何web浏览器加载新页面时，会创建一个新的全局对象，并给他一组初始属性

- 全局属性  undefined Infinity NaN
- 全局函数  isNaN  parseInt
- 构造函数  Date()  RegExp()  String() Object() Array()
- 全局对象  Math JSON
对客户端JavaScript，Window对象定义了一些额外的属性，如localStorage，sessionStorage
如果代码声明了一个全局变量，这个全局变量就是全局对象的一个属性
   


java

- java是强类型语言
- java中的对象属性一旦定义好就不能在删除或添加；
- 基于类，通过类层级实现继承
- 不能动态添加类的属性和方法
- 变量类型必须提前声明(静态类型)。
- 可以直接自动写入硬盘。
- 支持多重继承。也就是说，对象可以从无关的多个父对象中继承属性和属性值。JavaScript 不支持多重继承。


内存泄露  一个闭包必须保存它可见作用域中所有的参数和变量。

垃圾回收机制

使用arguments对象


es5与es6

声明变量  var let const

函数参数  默认参数 剩余参数function multiply(multiplier, ...theArgs) {...}

箭头函数
模板函数  反引号

JavaScript decodeURI()与decodeURIComponent()区别

decodeURI不能转义以下字符
; , / ? : @ & = + $