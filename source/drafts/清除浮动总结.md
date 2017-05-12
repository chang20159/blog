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
   