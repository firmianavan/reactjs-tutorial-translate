# 选择性渲染
> 译自[reactjs官方文档](https://facebook.github.io/react/docs/lists-and-keys.html)
我们首先看下在Javascript中如何对一个list进行修改. 下面的例子中我们使用`map()`方法将一个list中的每个值加倍, 并在最后打印结果:
```
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((number) => number * 2);
console.log(doubled);
```
将会在控制台上打印`[2, 4, 6, 8, 10]`. 
在React中将list转换成elments几乎完全一样. 
### 渲染重复对象
你可以构建一个element集, 然后在JSF中使用`{}`引用它. 
下面我们使用`map()`遍历一个数组, 生成一个`<li>`集合并赋值给变量`listItems`:
```
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);
ReactDOM.render(
  <ul>{listItems}</ul>,
  document.getElementById('root')
);
```
### 基本的List对象
通常的做法是将list渲染到一个对象中.
我们将上面的例子重构到一个对象中, 这个对象接受一个数组作为属性, 然后输出一个无序列表.
```
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```
这段代码在运行时会抛出一个警告提示应该为生成的list提供一个key. 这个Key是当创建element列表时需要包含的一个特殊的字符串, 稍后将讨论为什么这个key如此重要. 
下面代码是修正了这个告警后的样子:
```
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>
      {number}
    </li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

### Keys
Keys帮助React判断哪一条被修改/被添加或被删除. 数组中的每个element应该被赋予一个key作为这个element的唯一标识. 
添加key的最好的方式是选择一个能在list中唯一确认一个条目的字符串, 通常可以用数据的id:
```
const todoItems = todos.map((todo) =>
  <li key={todo.id}>
    {todo.text}
  </li>
);
```
万不得已的时候(没有data id或其他唯一的字符串)使用数组下标:
```
const todoItems = todos.map((todo, index) =>
  // Only do this if items have no stable IDs
  <li key={index}>
    {todo.text}
  </li>
);
```
如果数组中的对象可能要重新排序, 不建议使用下标作为key, 因为这样会很慢. 感兴趣可以阅读[深度解释为什么key是必要的](https://facebook.github.io/react/docs/reconciliation.html#recursing-on-children)

### 提取出带有key的对象
Key只有在被数组包围的上下文环境中才起作用. 例如在抽象出一个`<ListItem />`对象后, 你需要将key附加在`<ListItem />`元素上, 而不是`<ListItem />`中的`<li >`元素. 
###### 错误示例:
```
function ListItem(props) {
  const value = props.value;
  return (
    // Wrong! There is no need to specify the key here:
    <li key={value.toString()}>
      {value}
    </li>
  );
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // Wrong! The key should have been specified here:
    <ListItem value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```
###### 正确的示例
```
function ListItem(props) {
  // Correct! There is no need to specify the key here:
  return <li>{props.value}</li>;
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // Correct! Key should be specified inside the array.
    <ListItem key={number.toString()}
              value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```
简而言之, 在`map()`方法中的元素需要添加Key. 
### Key只需在同级节点间保持唯一
即不需要保证全局唯一. 如下可以在两个数组中用同一套Key:
```
function Blog(props) {
  const sidebar = (
    <ul>
      {props.posts.map((post) =>
        <li key={post.id}>
          {post.title}
        </li>
      )}
    </ul>
  );
  const content = props.posts.map((post) =>
    <div key={post.id}>
      <h3>{post.title}</h3>
      <p>{post.content}</p>
    </div>
  );
  return (
    <div>
      {sidebar}
      <hr />
      {content}
    </div>
  );
}

const posts = [
  {id: 1, title: 'Hello World', content: 'Welcome to learning React!'},
  {id: 2, title: 'Installation', content: 'You can install React from npm.'}
];
ReactDOM.render(
  <Blog posts={posts} />,
  document.getElementById('root')
);
```
Key最为对React的提示而存在, 但它不会被作为属性传递到component内部. 如果你的component需要同一个值, 显式的用一个不同的名字传递:
```
const content = posts.map((post) =>
  <Post
    key={post.id}
    id={post.id}
    title={post.title} />
);

```
上面的例子中, `Post`对象中可以得到`props.id`, 但拿不到`props.key`. 

### 在JSX中嵌入map()
JSX允许在`{}`间嵌入任意表达式:
```
function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />
      )}
    </ul>
  );
}
```
有时候这会使得代码更清晰, 有时则显得更让人困惑. 为了可读性提取到一个变量是否值得将由你来决定. 如果`map()`方法中嵌套太复杂, 你该意识到是时候将这些提取到一个component了. 