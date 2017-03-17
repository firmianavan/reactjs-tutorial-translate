# Components and Props
> 译自[reactjs官方文档](https://facebook.github.io/react/docs/components-and-props.html)

Components帮助把UI分割成独立的,可重用的片段, 而我们只需要孤立的考虑每个片段.
在概念上, components就像javascript的函数, 它们接受任意的输入(称为props), 然后返回描述UI的React Elements

### Components的函数式表达和类表达

定义一个component最简单的办法是通过javascript函数:
```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```
这个函数是一个有效的React component, 它接受一个单属性对象作为参数然后返回一个React element. 我们称这种对象是函数式的,因为他们字面上是javascript函数.

你也可以用ES6 class定义一个component:
 ```javascript
 class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```
上面两种表达在React看来是相等的

Classes有些其它功能将在下一节讨论, 在这之前我们使用简洁的函数式component.

### 渲染一个Component

在此之前我们只遇到过代表DOM元素的React elements:
```javascript
const element = <div />;
```
不过,elements也能表达用户自定义对象
```javascript
const element = <Welcome name="Sara" />;
```

当React发现某个element代表了一个用户自定义component, 它将使用一个对象打包jsx属性传递给component, 我们称之为"props"

下面的例子将会在页面渲染"Hello Sara":
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
我们回顾下这个例子里发生了什么:
1. 调用`ReactDOM.render()`渲染`<Welcome name="Sara" />`element.
2. React将`{name: 'Sara'}`作为props调用`Weclome` component. 
3. `Weclome` component返回`<h1>Hello, Sara</h1>`element作为结果. 
4. React 及时有效的更新DOM以匹配`<h1>Hello, Sara</h1>`. 

> 警告:
component首字母大写.
例如,`<div />`代表一个DOM元素, 但`<Welcom />`代表一个component, 而且要求Welcome component在可见的定义域内.

### 复合Components

Components可以在他们的输出中引用其它的components, 这可以使我们在任何维度的抽象上使用同一个component. 按钮,表单,对话框: 在react应用中, 所有这些都使用components表达.

例如, 我们创建一个`App` component, 它简单的多次渲染`Welcome`:
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

