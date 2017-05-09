# 事件处理
> 译自[reactjs官方文档](https://facebook.github.io/react/docs/handling-events.html)

React elements中的事件处理与DOM元素中的时间处理十分相似, 只有些语法上的区别:
* React事件使用驼峰命名,而不是小写. 
* 使用JSX时事件处理传递一个函数而不是HTML中的字符串
例如, html中:
```
<button onclick="activateLasers()">
  Activate Lasers
</button>
```
而在JSX中稍有不同:
```
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

另一个不同是你不可以在React中返回`false`告诉以阻止事件的默认行为, 必须显式调用`preventDefault`. 例如html中为了组织默认的超链接的打开一个新页面的默认行为, 你可以这样做:
```
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click merrf
</a>
```
而在React中, 应该是这个样子:
```
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```
其中`e`是一个React合成的事件, React参照(W3C标准)[https://www.w3.org/TR/DOM-Level-3-Events/]封装了这些事件, 所以你不必担心跨浏览器的适配性. 

使用React时, 通常不需要通过`addEventListener`在创建DOM元素后去为其添加监听, 而只要在render时提供一个监听即可.

当你使用ES6 class定义一个对象时, 事件处理器通常作为这个类中的一个方法. 例如: 下面的`Toggle`对象描述了一个在"ON"和"OFF"之间切换的按钮.
```
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);
```

在使用JSX回调时, 你必须注意`this`的含义. Javascript中, 对象方法中的this默认没有绑定到对象本身, 如果你忘记了`this.handleClick = this.handleClick.bind(this);`, handleClick中的this将会是`undefined`. 

这并不是React下的特定行为, 而是(javascript的一部分)[https://www.smashingmagazine.com/2014/01/understanding-javascript-function-prototype-bind/], 通常情况下, 如果你引用了一个方法而未在之后使用()调用时, 比如`onClick={this.handleClick}, 应当对这个方法进行绑定. 

如果你不喜欢这种绑定方式, 有两种办法绕开它.
* 如果在使用(property initializer syntax)[https://babeljs.io/docs/plugins/transform-class-properties/], 你可以作如下修改:
```
class LoggingButton extends React.Component {
  // This syntax ensures `this` is bound within handleClick.
  // Warning: this is *experimental* syntax.
  handleClick = () => {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```
这种预防在(Create React App)[https://github.com/facebookincubator/create-react-app]默认开启.

* 如果你没有使用`property initializer syntax`, 可以在回调处使用箭头函数(arrow function):
```
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    // This syntax ensures `this` is bound within handleClick
    return (
      <button onClick={(e) => this.handleClick(e)}>
        Click me
      </button>
    );
  }
}
```
这种方法的一个问题在于每次渲染时都会创建一个新的回调函数. 大部分情况下这都没问题, 然而如果这个回调作为prop传递给底层对象时, 这些对象需要进行额外的重新渲染. 我们建议在构造方法中进行绑定或者使用property initializer syntax, 以避免这种性能问题.

