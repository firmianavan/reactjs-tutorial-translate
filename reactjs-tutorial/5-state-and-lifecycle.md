# 状态和生命周期   
> 译自[reactjs官方文档](https://facebook.github.io/react/docs/state-and-lifecycle.html)

考虑之间举的时钟的例子, 目前为止,我们只学了这一种方法来更新UI,即重新调用ReactDOM.render()对象:
```javascript
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}

function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

在这一节我们将学习如何将`Clock` component实现真正的封装与可重用. 它将会建立自己的计时器并且每秒自行更新.最终期望的效果是, `Clock`组件可以自行更新而我们只需要书写一次:
```javascript
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```
为了实现这个功能, 我们为'Clock'对象添加"状态(state)". 在之前的章节曾提到过类定义的components有额外的特性, 自身的状态(Local state)就是这样一个只在类中可用的特性

### 函数式生命转换为类声明

5步将一个函数式对象改为类声明:
1. 创建一个同样名字的[ES6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes), 继承`React.Component`.
2. 添加一个名为`render()`的空方法.
3. 将原来的函数体移动到'render()'下.
4. 将`render()`方法中的`props`替换为`this.props`.
5. 删除遗留的空的函数声明

类定义的'Clock':
```
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

### 向一个类中添加内在状态(Local State)

三步将`date`从props迁移到state:
1. 在`render()`方法中用`this.state.date`替换`this.props.date`
```javascript
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```
2. 添加一个class构造函数, 并在其中初始化`this.state`
```javascrpt
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```
注意代码是如何将props传递给父构造函数的:
```javascript
 constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
```
所有类形式定义components时都不要忘记将props传递给父构造函数.

3. 从`<Clock />` element中移除`date`属性
```
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

稍后我们将计时器添加到component内部
此时的代码看起来如下:
```
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

### 为Class添加生命周期方法

在一个有着众多components的应用中, 在components被销毁时及时的释放资源是一件很重要的事情. 

我们希望`Clock`对象在第一次被渲染到DOM时添加一个定时器. 这在React中被称为"挂载(mounting)"

我们也希望当'Clock'关联的DOM对象被移除时可以清除这个定时器, 这在React中被成为"卸载(unmounting)"

下面代码展示了如何在component类中声明一些特殊的方法以在该对象挂载或卸载是运行特定的逻辑:
```
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {

  }

  componentWillUnmount() {

  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```
这些方法被称为"生命周期钩子函数(有点拗口,原文是lifecycle hooks)".
`componentDidMount()`方法在component的输出被渲染到DOM后运行, 这是建立定时器的好地方(用到了ES6 的arrow function):
```
componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
}
```
注意我们将time ID存到了`this`中. 构造完成后如果需要你可以自由的向class中添加额外的属性. 如果有些属性没有在`render()`中用到, 那么它也不应该出现在state中. 

在`componentWillUnmount()`函数中取消定时器:
```
  componentWillUnmount() {
    clearInterval(this.timerID);
  }
```

最后, 实现`setInterval()`中用到的`tick()`方法, 她使用`this.setState()`定时更新state, 最终代码如下:
```
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

现在我们快速重新回顾下这是如何实现的:
1. 当`<Clock />`被传递给`ReactDOM.render()`时,React调用`Clock`对象的构造方法, 并初始化包含了当前时间的`this.state`用于稍后的更新.

2. React调用`Clock`对象的`render()`方法以获取应在页面展示的内容, 然后更新DOM以匹配`Clock`对象的渲染结果. 

3. DOM更新完成后React调用`componentDidMount()`生命周期钩子函数, 其中`Clock`对象请求浏览器建立定时器用来每秒调用一次`tick()`函数. 

4. 浏览器每秒调用一次`tick()`方法, 在方法内部`Clock`对象通过`setState()`进行UI的更新, 由于`setstate()`的调用, React得知对象的状态发生了变化, 然后再次调用`render()`以获知渲染内容并据此更新DOM.

5. 如果`Clock`对象从DOM中被移除, React调用`componentWillUnmout()`以终止定时器. 

### 正确的使用State
关于`setState()`的一些注意事项:

#### 不要直接修改`this.state`
例如, 下面的代码并不会重新渲染一个对象:
```
// Wrong
this.state.comment = 'Hello';
```
正确的方法是使用`setState()`:
```
// Correct
this.setState({comment: 'Hello'});
```
唯一可以对`this.state`赋值的地方是在构造函数中.

#### state更新可能是异步的(asynchronous)
React考虑效率可能会批量执行`setState()`而至进行一次更新. 
因为`this.props`和`this.state`可能被异步更新, 你不应该依赖它们的当前值用以获取下一个状态.(译者注:线程安全问题)
例如, 下面的代码可能会更新计时器失败:
```
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```
为了实现这一目的, 我们用`setState()`的第二种用法, 它接受一个函数作为参数, 该函数会传入两个参数, 第一个是上次的状态对象, 第二个是当前的props对象:
```
// Correct
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));
```
等同于:
```
// Correct
this.setState(function(prevState, props) {
  return {
    counter: prevState.counter + props.increment
  };
});
```

#### 状态更新会被合并到一起

当调用`setState()`时, React会将你提供的对象合并到当前的state对象中.
例如, 你的state可能包含几个独立的对象:
```
  constructor(props) {
    super(props);
    this.state = {
      posts: [],
      comments: []
    };
  }
```
你可以通过多次调用`setState()`分别更新它们:
```
  componentDidMount() {
    fetchPosts().then(response => {
      this.setState({
        posts: response.posts
      });
    });

    fetchComments().then(response => {
      this.setState({
        comments: response.comments
      });
    });
  }
```
`this.setState({comments})`将会保持`this.state.posts`完全不变, 但会整个替换`this.state.comments`. 

### 数据由顶层流向底层
无论是父对象还是子对象都无法获知某个对象是有状态的还是无状态的, 而且他们也不应该关系这个类是通过函数定义还是类定义. 
这就是为什么state被成为局部的或者封装的. 除了拥有它的对象, state对任何其他对象都是不可用的. 
但是, 一个对象可以选择将state作为props向它的子对象传递:
```
<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
```
对用户自定义对象也一样:
```
<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
```
`FormattedDate`对象将接受`date`作为其`props`的一个属性. 而起他不知道数据来自父对象的state还是props,或者手工输入. 

这通常被称为自上而下的(top-down)或者单向(unidirectional)数据流(data flow). 任一state总是属于某一对象且只能影响它们所构成的树状结构中的下级对象. 
如果将所有对象构成的树状模型想象成props的瀑布模型, 每个对象的state就像在这个点加入的一个额外的源头,但它也是向下流动的. 
为了证明所有对象是完全独立的, 我们可以创建一个包含了三个`Clock`对象的`App`:
```
function App() {
  return (
    <div>
      <Clock />
      <Clock />
      <Clock />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```
每个`Clock`创建了自己的定时器, 并独立的定时更新.
在React应用中, 一个对象是否是有状态应考虑到它随时间变化的实现细节, 你可以在一个有状态的对象中使用无状态对象, 反之亦然. 