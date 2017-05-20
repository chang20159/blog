---
title: ES6语法简要笔记
tag:
- JavaScript
categories:
- JavaScript
---

## let和const
### 块级作用域
let和const具有块级作用域，只在所在的代码块内有效。
```javascript
//块级作用域
var a = [];
for(var i = 0; i < 10; i++){
    a[i] = function(){
        console.log(i);
    };
}
a[6]();  //10
```
<!-- more -->
```javascript
var b = [];
for(let j = 0; j < 10; j++){
    b[j] = function(){
        println(j)
    };
}
b[6](); //6
```

### 不存在变量提升
let/const不像var那样会发生“变量提升”现象。所以，变量一定要在声明后使用，否则报错。
```javascript
// 变量提升
console.log(foo); // 输出undefined
console.log(bar); // 报错ReferenceError

var foo = 2;
let bar = 2;
```
### 暂时性死区
只要块级作用域内存在let/const命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部声明的影响。
所以在此区域内的，在声明之前不能使用该变量。
```javascript
//暂时性死区
var a = 20159;

{
    a = "chang";
    let a ;
}
```
### 不允许重复声明
let/const不允许在相同作用域内，重复声明同一个变量。
```javascript
//不允许重复声明
{
    let name;
    var name;
}
```
## 字符串模板
### 字符串中嵌入变量
```javascript
//字符串模板
let number = 20159;
console.log(`chang ${number}`);
```
### 支持多行字符串反斜杠
```javascript
let idea = `I like to do the front end.
How can I do ?`;
console.log(idea);
```
## 解构赋值
ES6允许按照一定模式，从数组和对象中提取值，对变量进行赋值,称为解构赋值。
解构赋值的规则是，只要等号右边的值不是对象，就先将其转为对象。按照此规则可以分为两类：数组的解构和对象的解构。
### 对象的解构赋值
等号左侧是{}，则按右侧数据进行对象解构，此时等号右侧必须是对象或可转为对象的值(undefined和null无法转为对象)。
**对象的结构根据属性名来匹配。**
如下，是先找到左侧的同名属性name，再赋给该属性对应的变量name1。最终将profile.name的值付给了name1。如果左侧的属性在对象中没有，对应的变量值将会是undefined,比如sex
```javascript
let profile = {
    name: "chang",
    number:20159
};
let { name:name1,number:number1,age:age1 = 24,sex:sex1 } = profile;
console.log("name1:%s,number1:%d,age1:%d,sex1:%s", name1,number1,age1,sex1);
alert(`name1:${name1},number1:${number1},age1:${age1},sex1:${sex1}`);
```
如果新的变量名与对象的属性名相同，可以简写成下面这样，等同于 let name = profile.name;
```javascript
let { name,number,age,sex } = profile;
```
### 数组的解构赋值
等号的右边的数据结构必须具Iterator接口，等号左边用[]表示
```javascript
let array = ["ch"]
```
### 指定变量的默认值
等号左侧是[],则对右侧数据进行数组解构，此时等号右侧必须是具有Iterator结构的值。
**数组解构根据元素的顺序进行模式匹配。**

### 解构赋值的应用
1.交换变量值
```javascript
[x,y] = [y,x];
```
2.提取对象属性
```javascript
{userInfo,shopInfo} = this.props;
```
3.解构赋值也可用于函数调用时传参，形参相当于解构等式左侧值，实际传入的参数相当于等式右侧的值。

## ... 运算符
..运算符有两种，spread运算符和rest运算符,作用相反
### spread运算符
spread运算符是将数组或类数组对象展开成一系列用逗号隔开的值
```javascript
let array = [1,2,3];
let new_array = [...array,4,5];    //数组合并
console.log(new_array);
let copy_array = [...array];   //数组深拷贝
console.log(copy_array);
```
### rest运算符
将逗号隔开的值序列组合成一个数组
```javascript
//结合数组的解构赋值
let [ value,...array ] = [1,2,3,4];
console.log(`value:${value}, array:${array}`); //value:1, array:2,3,4

//用于函数不定长的参数,可避免使用arguments对象
var rest = function(...args){
-    console.log(`rest.args:${args[1]}`); //rest.args:es5
};
rest("es6","es5","es3");
```

## for...of循环
for...of可遍历数组的键值，for...in循环只能获得键名
```javascript
let array = [10,20,30];
array.name = 'es6';
//只返回可数字索引的键值 of: 10   of: 20  of: 30
for(let value of array){  console.log(`of: ${value}`); } 

//in: 0  in: 1  in: 2  in: name
for(let value in array){  console.log(`in: ${value}`); } 

//forEach: 0:10  forEach: 1:20  forEach: 2:30
array.forEach(function(item,index){
    console.log(`forEach: ${index}:${item}`);  
});
```


## Promise对象
Promise是一个构造函数，函数的参数是一个未来才会结束的事件，而函数的实例是这个事件执行的状态。简洁的说，Promise对象代表一个异步操作，可以获取异步操作的状态。
有三种状态：
- pending 进行中
- resolved 已完成
- rejected 已失败

而且状态的改变只会从pending到resolved，或则从pending到rejected
注意：Promise一旦new了就会立即执行，无法中断

用法:
```javascript
var promise = new Promise(function(resolve, reject) {
    ...
  if (/* 异步操作成功 */){
    resolve(value);  //执行resolve函数将状态从Pending变为Resolved
  } else {
    reject(error);   //执行reject函数将状态从Pending变为rejected
  }
});

／/then方法分别指定Resolved状态和Reject状态的回调函数。
promise.then(function(value) {
  // Resolved状态时执行，value是resolve函数的参数
}, function(error) {
  // rejected状态时执行，value是reject函数的参数
});
```

实例方法：
1、Promise.prototype.then()  为Promise实例添加状态改变时的回调函数
2、Promise.prototype.catch() 指定发生错误时的回调函数
3、Promise.all()  接收多个Promise实例，返回一个新的Promise实例
  例如：p = new Promise([p1,p2,p3])
  - 只有p1,p2,p3的状态都是已完成时，p的状态才会变成已完成，并将p1,p2,p3的返回值组成一个数组，传递给p的回调函数；
  - 只要p1,p2,p3有一个失败，p的状态就会变成失败，第一个被reject的实例的返回值，会传递给p的回调函数
  - 注意：p1、p2、p3都是Promise对象的实例，如果不是，就会先调用Promise.resolve方法，将参数转为Promise实例
4、Promise.race()  只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。率先改变状态的Promise 实例的返回值，传递给p的回调函数。
5、Promise.resolve()  将现有对象转为Promise对象


## Generator函数

## async 函数

## Proxy和Reflect

## 类修饰器

## import和export

## Set和Map


## 参考
- [MDN Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise#参见)
- [深入浅出ES6 ](http://www.infoq.com/cn/es6-in-depth/)
- [Promises/A+](https://promisesaplus.com/)
