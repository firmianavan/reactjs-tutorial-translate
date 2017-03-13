# jsx简介
> 译自[reactjs官方文档](https://facebook.github.io/react/docs/introducing-jsx.html)

看下面一段变量声明:
`const element = <h1>Hello, world!</h1>;` 
它既不是string,也不是html对象, 我们称之为jsx, 是javascript的一种扩展. 我们建议使用react时使用jsx描述展示层. jsx生成react对象, 我们将在下一部分学习将他们渲染到DOM. 下面是jsx的基本用法

### jsx中嵌入表达式
通过大括号向jsx中嵌入javascript表达式, 比如`2+2`,`user.firstName`以及`format(user)` 

```javascript
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

我们将这段jsx按行分开, 这不是必须的, 但这样做时建议括号括起来以避免自动插入分号([automatic semicolon insertion](http://stackoverflow.com/q/2846283))的陷阱.

###使用jsx定义html元素的属性

使用字符串字面量:
`const element = <div tabIndex="0"></div>;`
使用大括号围起来的javascript表达式:
`const element = <img src={user.avatarUrl}></img>;`
你或者使用引号括起来的字面量, 或者使用大括号括起来的javascript表达式, 但不要同时使用引号和大括号.
> 注意:
jsx相比html更像javascript, React DOM 使用驼峰命名代替的html中的一些属性名,例如`class`变成了`className`, `tabindex`变成了`tabIndex`

###子元素处理

如果标签是空的, 你可以像xml那样使用`/>`结束
`const element = <img src={user.avatarUrl} />;`

jsx也可以包含子元素
```javascript
const element = (
  <div>
    <h1>Hello!</h1>
    <h2>Good to see you here.</h2>
  </div>
);
```
###防范注入攻击

在jsx中直接使用用户输入是安全的:
```javascript
const title = response.potentiallyMaliciousInput;
// This is safe:
const element = <h1>{title}</h1>;
```
React DOM 默认会在渲染jsx之前转义嵌入其中的任何字符