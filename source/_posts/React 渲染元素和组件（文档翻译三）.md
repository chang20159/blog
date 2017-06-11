---
title: React 渲染元素和组件（文档翻译三）
tag:
- React
categories:
- React
---

元素是React应用最小构建块，每一个元素都描述了呈现在屏幕上的内容。不同于真是的DOM元素，React元素就是一个普通的对象，创建成本很低，React DOM负责更新真实DOM与React元素匹配。

**注意：不要混淆元素与组件的概念，组件是由元素构成的**


## 将元素渲染到DOM中
假设你的HTML文件中有一个&lt;div&gt;,这个节点里面的内容都由 React DOM来管理。
```html
<div id="root"></div>
```

使用React构建的应用程序通常具有单个根DOM节点，要将React元素渲染到根DOM节点中，需要将他们传递给ReactDOM.render()
```javascript
const element = <h1>Hello, world</h1>;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

### 更新已渲染元素
**React元素是不可变的**，创建元素后无法更改其子项或属性,一个元素就像一个电影中的一帧：它代表了某个时间点的UI。

根据我们目前的知识，**更新UI的唯一方法是创建一个新元素，并将其传递给ReactDOM.render()**。
看下面这个显示时间的例子,每秒钟都会执行一次ReactDOM.render() 

```javascript
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(
    element,
    document.getElementById('root')
  );
}
setInterval(tick, 1000);
```

**注意: 实际上，大多数React应用只会调用ReactDOM.render()一次,在接下来的章节中，我们会学习如何将这些代码封装到有状态的组件中。**

### React仅在必要时更新
尽管我们创建了一个描述整个UI树的元素，但只有内容已经改变的节点才被React DOM更新。因为**ReactDOM会将元素及其子元素与上一个元素进行比较，并且只更新实际改变的DOM。**

## Components and Props
使用组件可以将UI拆分成独立的可重复使用的部分，然后可以单独考虑每个组件。从概念上来讲，组件就像JavaScript函数，接受任意输入（称为“props”），并返回描述页面呈现的React元素。

### Functional and Class Components
定义组件的最简单的方法是编写一个JavaScript函数：
```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

这个函数是一个有效的React组件，因为它接受一个单一的“props”对象参数并返回一个React元素。 我们将这样的组件称为“functional组件”，因为它们在字面上是一个JavaScript函数。

还可以使用[ES6类](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Classes)来定义组件：
```javascript
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```
上面这两个组件是等同的，class组件有一些额外的功能，这会在下一章节讨论，这里先使用functional组件来讨论

### 渲染组件
之前我们遇到的元素，都是DOM标签：
```javascript
const element = <div />;
```

我们也可以自定义元素，代表一个组件
```javascript
const element = <Welcome name="Sara" />;
```
当React知道这是一个表示用户定义组件的元素时，它将JSX属性作为单个对象传递给该组件。 我们称这个对象为“props”。
例如：[Try it on CodePen](https://codepen.io/gaearon/pen/YGYmEG?editors=0010)
```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```
我们来看一下这个例子发生了什么？

- 调用ReactDOM.render()渲染&lt;Welcome name =“Sara”/&gt;元素
- React以{name：'Sara'}作为props调用Welcome组件。
- Welcome组件返回一个&lt;h1&gt; Hello，Sara &lt;/ h1&gt;元素作为结果。
- React DOM有效地更新DOM来匹配&lt;h1&gt; Hello，Sara &lt;/ h1&gt; 。

**注意：组件名称要以大写字母开头，例如，&lt;div /&gt;表示一个DOM标签，但&lt;Welcome /&gt;表示一个组件，并且要求Welcome在作用范围内。**

我们可以创建一个组件，然后渲染多次
```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```
**注意：组件必须返回单个根元素，这也是为什么我们添加了一个&lt;div&gt;来包含所有的&lt;Welcome /&gt;元素。**

### props是只读的
不管将组件声明为functional还是class，它都不能修改自己的props。 
看下面这个函数，这样的函数成为‘纯函数’，因为它们不会尝试更改输入，并且总是为相同的输入返回相同的结果。

```javascript
function sum(a, b) {
  return a + b;
}
```
相比之下，这个函数是不纯的，因为它改变了自己的输入：
```javascript
function withdraw(account, amount) {
  account.total -= amount;
}
```

再看下组件声明：
```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```
React很灵活，但有一个严格的规则：**所有的React组件必须像纯函数一样不能改变props**
当然，应用程序UI是动态的，在下一节中，我们将介绍一个“state”，state允许React组件根据用户操作，网络响应以及其他任何内容更改组件输出，而不会违反此规则。

## 参考
- [https://facebook.github.io/react/docs/rendering-elements.html](https://facebook.github.io/react/docs/rendering-elements.html)
- [https://facebook.github.io/react/docs/components-and-props.html](https://facebook.github.io/react/docs/components-and-props.html)
