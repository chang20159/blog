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

### 数组
#### spread运算符
spread运算符是将数组或类数组对象展开成一系列用逗号隔开的值
```javascript
let array = [1,2,3];
let new_array = [...array,4,5];    //数组合并
console.log(new_array);
let copy_array = [...array];   //数组深拷贝
console.log(copy_array);
```
#### rest运算符
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

### 对象
#### spread运算符
```javascript
let object = {a:1,b:2};
let object1 = {...object};
object1.a = 2;
//object1 : {a:2,b:2}
//object : {a:1,b:2}
```
#### rest运算符

```javascript
let object = {a:1,b:2};
let {...object2} = {a:1,b:2};

console.dir(object2);
//{a:1,b:2}
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
  **如果希望多个请求并发执行，可以使用Promise.all方法**
  例如：p = new Promise([p1,p2,p3])
  - 只有p1,p2,p3的状态都是已完成时，p的状态才会变成已完成，并将p1,p2,p3的返回值组成一个数组，传递给p的回调函数；
  - 只要p1,p2,p3有一个失败，p的状态就会变成失败，第一个被reject的实例的返回值，会传递给p的回调函数
  - 注意：p1、p2、p3都是Promise对象的实例，如果不是，就会先调用Promise.resolve方法，将参数转为Promise实例
4、Promise.race()  只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。率先改变状态的Promise 实例的返回值，传递给p的回调函数。
5、Promise.resolve()  返回一个新的 Promise 实例,实例的状态为resolved
6、Promise.reject()  返回一个新的 Promise 实例，实例的状态为rejected
7、Promise内部的错误不会冒泡到全局，可以通过在回调链尾端添加done方法抛出可能出现的错误
```javascript
Promise.prototype.done = function (onFulfilled, onRejected) {
  this.then(onFulfilled, onRejected)
    .catch(function (reason) {
      // 抛出一个全局错误
      setTimeout(() => { throw reason }, 0);
    });
};
```
8、finally(callback) 用于指定不管Promise对象最后状态如何，都会执行的操作.
接受一个普通的回调函数作为参数，该函数不管怎样都必须执行

## Generator函数
Generator函数与yield关键字配合可以让代码分布执行，异步操作也是将一个任务分段执行，因此 可以使用Generator函数实现异步编程，拿到第一步的执行结果后再执行第二步，但是程序的写法却跟同步写法一样。看个例子：
```javascript
function* generator(){
  let x = 1;
  let y = yield x+1;
  let z =  yield x + 2;
  console.log(z);
}

g = generator();
let step1 = g.next()
console.dir(step1)
let step2 = g.next(step1.value)
console.dir(step2)
let step3 = g.next(step2.value)
console.dir(step3)
```
结果如下，执行Generator函数返回一个指针对象，调用指针对象的next方法移动指针到yield语句，每次执行都从上一次结束的位置开始。next方法可以传入参数，这个参数是上一个阶段的执行结果。
```
>Object
  >done: false
  >value: 2_
  >_proto__: Object
>Object
  >done: false
  >value: 3_
  >_proto__: Object
3
>Object
  >done: true
  >value: undefined
  >__proto__: Object
undefined
```
Generator函数能够让异步编程可以像写同步执行的代码一样，但是阶段执行的管理却很麻烦，我得知道什么时候执行那一步。有些方法可以让Generator函数自动执行，这里还没研究。

## Set和Map
### Set
Set实例是不重复值的集合

**Set实例的属性和方法**
let list = new Set([1,2,2,4])

**属性**

- size  返回成员数量

**操作方法**

- add(value)：添加某个值，返回的是当前的Set对象，可以采用链式写法
- delete(value)：删除某个值，返回一个布尔值，表示删除是否成功。
- has(value)：返回一个布尔值，表示该值是否为Set的成员。
- clear()：清除所有成员，没有返回值。

**遍历方法**

- keys()：返回键名的遍历器
- values()：返回键值的遍历器
- entries()：返回键值对的遍历器
- forEach()：使用回调函数遍历每个成员

注意：WeakSet 结构与 Set 类似，也是不重复的值的集合，但WeakSet的成员只能是对象。并且WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用

### Map
Map是键值对的集合，“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键
注意，只有对同一个对象的引用，Map 结构才将其视为同一个键。

**Map实例的属性和方法**
let map = new Map([['name1','chang'],['name2','chen']])

**属性**

- size  返回成员数量

**操作方法**

- set(value)：添加键值对，返回的是当前的Map对象，可以采用链式写法
- delete(value)：删除某个键，返回一个布尔值，表示删除是否成功。
- has(value)：返回一个布尔值，表示某个键是否在当前 Map 对象
- clear()：清除所有成员，没有返回值。

**遍历方法**

- keys()：返回键名的遍历器
- values()：返回键值的遍历器
- entries()：返回键值对的遍历器
- forEach()：使用回调函数遍历每个成员

注意：WeakMap结构与Map结构类似，也是用于生成键值对的集合，但WeakMap只接受对象作为键名（null除外），并且WeakMap的键名所指向的对象，不计入垃圾回收机制。

**提示：垃圾回收机制依赖引用计数，如果一个值的引用次数不为0，垃圾回收机制就不会释放这块内存**

## 类修饰器

## import和export
ES6 模块的设计思想，是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。
模块功能由export和import两个命令完成。export对外输出模块，import用于引入模块。
### export 写法
import接受一对大括号，指定从其他模块导入的变量名。大括号中的变量名，必须与被导入模块对外接口的名称相同。
```
// 写法一
export var value = 1;
export function func(){...};

// 写法二
var value = 1;
function func(){...};
export {value,func};

// 写法三
var value = 1;
function func(){...};
export {
  value as value1,
  func as func1,
  func as func2
};
```

### import写法
```
// 写法一
import { value, func} from 'module'

// 写法二
import * as Module from 'module'
```

### export default写法
```
export var value = 1;
export default function func(){...};

import func, {value} from 'module'
```

##  async 函数
其实是Generator函数的语法糖，将**Generator函数**和**自动执行器**，包装在一个函数里
Generator 函数需要调用next方法，每调一次执行一段。
- async函数返回一个Promise 对象，一旦遇到await就会先返回，等到异步操作完成再接着执行函数体内后面的语句。
- async函数的返回值，会作为then方法回调函数的参数。
- await命令后面是一个Promise 对象。如果不是，会被转成一个立即resolve的Promise 对象。
下面代码的执行结果是： 哈哈   嘿嘿    4
```
async function cal(x){
    let result = await x + 1;
    console.log('嘿嘿')
    return result;
}
cal(3).then(function(data){
  console.log(data)
})
console.log('哈哈')
```

与Generator 函数相比

- async函数内置了执行器，像普通函数一样执行
- async和await，比星号和yield语义更清楚。async表示函数里有异步操作，await表示紧跟在后面的表达式需要等待结果。
- async函数的返回值是 Promise 对象



## 箭头函数
```javascript
function func(x,y){
    if(x+y > 5){
       return x * y
    }
}
```
有了箭头函数，上面的方法可以写成：
```javascript
(x,y) => {
  if(x+y > 5){
      return x * y
  }
}
```
如果函数体只有一个return语句，可以将大括号和return省略
```javascript
(x,y) =>  x * y
```

如果返回的是个对象,得加上（），参数如果只有一个，可以不要()
```javascript
(name) =>  ({name:name})
```
箭头函数其实就是一个用箭头简化了的匿名函数，不过与匿名函数有点区别。
**箭头函数内部的this是词法作用域，由上下文确定，指向函数定义时的上下文。**
下面的代码，obj.sayHello()得到的结果是undefined,因为this.name指向window或undefined
```
var obj = {
    name: 'chang',
    sayHello: function () {
        var fn = function () {
            return this.name; // this指向window或undefined
        };
        return fn();
    }
};
```
为了正常执行我们会这样做
```
var obj = {
    name: 'chang',
    sayHello: function () {
        let _this = this
        var fn = function () {
            return _this.name; // this指向window或undefined
        };
        return fn();
    }
};
```
但是有了箭头函数就不需要了,可以写成这样
```
var obj = {
    name: 'chang',
    sayHello: function () {
        var fn = () => this.name;
        return fn();
    }
};
```
## 参考
- [ECMAScript 6 入门](http://es6.ruanyifeng.com/)
- [MDN Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise#参见)
- [深入浅出ES6 ](http://www.infoq.com/cn/es6-in-depth/)
- [Promises/A+](https://promisesaplus.com/)
