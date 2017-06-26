# Rendering Elements
> 译自[reactjs官方文档](https://facebook.github.io/react/docs/rendering-elements.html)

Elements是React应用的最小构建块.
一个element是对你希望在屏幕上看到的东西的一个描述:
```
const element = <h1>Hello, world</h1>;
```

与DOM元素相比, react elements是普通的js对象, 而且创建代价低. React DOM将会处理DOM的更新以匹配react elements.

> 有些人可能对elements和一个更广泛的概念components感到迷惑, 我们将在下一节引入components, elements是components的组成部分.

### 渲染一个element到DOM

假设在你的html文件内有个div元素:
```html
<div id="root"></div>
```
我们称之为一个'root'DOM节点, 节点内的所有将由React DOM管理.
要将一个react element渲染到一个DOM节点, 只需将他们传递给函数`ReactDOM.render()`:
```javascript
const element = <h1>Hello, world</h1>;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

### 更新已经被渲染的元素

React Elements是不可变的(immutable), 一旦创建一个element, 你将不能改变它的属性或者子元素, 一个element就像电影里的一帧画面, 它代表了某个时间点的UI. 
聪明的你已经想到, 唯一更新UI的办法是创建一个新的element, 并交由`ReactDOM.render()`渲染.
下面是一个时钟的例子:
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
它通过`setInterval()`每秒进行一次渲染

> 注意:
实践中, 大部分React应用只调用`ReactDOM.render()`一次, 下面我们将会学习如何使用stateful components实现这种需求.
建议阅读时不要跳过某些主题, 因为他们构建在彼此之上. 

### React只更新必要的东西

ReactDOM在渲染时会将当前element和之前的做比较, 只进行必要的更新达到期望的效果
![](dom-updates.gif)
即使我们每秒创建了一个element描述整个UI, 但只有内容发生变化的text节点被ReactDOM更新.
因此, 只需要考虑在一个特定时间UI是什么样子, 而不是试图随着时间流转去改变它.


