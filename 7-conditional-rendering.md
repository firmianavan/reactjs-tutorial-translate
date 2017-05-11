# 选择性渲染
> 译自[reactjs官方文档](https://facebook.github.io/react/docs/conditional-rendering.html)
可以根据需要预先创建封装了不同逻辑的对象, 然后根据条件渲染其中的一个或多个.
根据条件渲染(conditional rendering)和javascript中的条件判断是一样的. 例如根据用户是否登录返回不同的greeting:
```
function Greeting(props) {
  const isLoggedIn = props.isLoggedIn;
  if (isLoggedIn) {
    return <UserGreeting />;
  }
  return <GuestGreeting />;
}

ReactDOM.render(
  // Try changing to isLoggedIn={true}:
  <Greeting isLoggedIn={false} />,
  document.getElementById('root')
);
```

### Element变量
可以使用变量存储elements. 这在你只需要条件性的渲染对象中的一部分而其他保持不变时会用到.
比如用条件性渲染将下面的两个对象改造成一个:
```
function LoginButton(props) {
  return (
    <button onClick={props.onClick}>
      Login
    </button>
  );
}

function LogoutButton(props) {
  return (
    <button onClick={props.onClick}>
      Logout
    </button>
  );
}
```
结果是一个有状态的对象(stateful component):
```
class LoginControl extends React.Component {
  constructor(props) {
    super(props);
    this.handleLoginClick = this.handleLoginClick.bind(this);
    this.handleLogoutClick = this.handleLogoutClick.bind(this);
    this.state = {isLoggedIn: false};
  }

  handleLoginClick() {
    this.setState({isLoggedIn: true});
  }

  handleLogoutClick() {
    this.setState({isLoggedIn: false});
  }

  render() {
    const isLoggedIn = this.state.isLoggedIn;

>    let button = null;
>    if (isLoggedIn) {
>      button = <LogoutButton onClick={this.handleLogoutClick} />;
>    } else {
>      button = <LoginButton onClick={this.handleLoginClick} />;
>    }

    return (
      <div>
>        <Greeting isLoggedIn={isLoggedIn} />
>        {button}
      </div>
    );
  }
}

ReactDOM.render(
  <LoginControl />,
  document.getElementById('root')
);
```
当然使用变量和if语句实现条件性渲染是一个很好的办法, 但有时你会想要你一个更简短的语法, 就像下面提到的.
### 行内逻辑判断和`&&`操作符
你可以用打括号包围的方式[在jsx中插入表达式](https://github.com/firmianavan/reactjs-tutorial-translate/blob/master/2-jsx.md). 我们将使用`&&`逻辑操作符, 这将使得在一个element中使用条件渲染更见便捷. 
```
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
>       {unreadMessages.length > 0 &&
>         <h2>
>           You have {unreadMessages.length} unread messages.
>         </h2>
>       }
    </div>
  );
}

const messages = ['React', 'Re: React', 'Re:Re: React'];
ReactDOM.render(
  <Mailbox unreadMessages={messages} />,
  document.getElementById('root')
);
```
