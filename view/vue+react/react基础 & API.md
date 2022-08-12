### 一、JSX简介
JSX是一个 JavaScript 的语法扩展。

**JSX 特定属性**
可以通过使用引号，来将属性值指定为字符串字面量：
```
const element = <div tabIndex="0"></div>;
```
也可以使用大括号，来在属性值中插入一个 JavaScript 表达式：
```
const element = <img src={user.avatarUrl}></img>;
```
在属性中嵌入 JavaScript 表达式时，不要在大括号外面加上引号。你应该仅使用引号（对于字符串值）或大括号（对于表达式）中的一个，对于同一属性不能同时使用这两种符号。

>因为 JSX 语法上更接近 JavaScript 而不是 HTML，所以 React DOM 使用 camelCase（小驼峰命名）来定义属性的名称，而不使用 HTML 属性名称的命名约定。例如，JSX 里的 `class` 变成了 `className`，而 `tabindex` 则变为 `tabIndex`。

**使用 JSX 指定子元素**
假如一个标签里面没有内容，你可以使用 /> 来闭合标签，就像 XML 语法一样：
```
const element = <img src={user.avatarUrl} />;
```
**JSX 防止注入攻击**
React DOM 在渲染所有输入内容之前，默认会进行[转义](https://stackoverflow.com/questions/7381974/which-characters-need-to-be-escaped-on-html)。它可以确保在你的应用中，永远不会注入那些并非自己明确编写的内容。所有的内容在渲染之前都被转换成了字符串。这样可以有效地防止 [XSS（cross-site-scripting, 跨站脚本）](https://en.wikipedia.org/wiki/Cross-site_scripting)攻击。

**更新已渲染的元素**
React DOM 会将元素和它的子元素与它们之前的状态进行比较，并只会进行必要的更新来使 DOM 达到预期的状态。

### 二、组件 & Props
**组件，从概念上类似于 JavaScript 函数。它接受任意的入参（即 “props”），并返回用于描述页面展示内容的 React 元素。**`组件名称必须以大写字母开头。`

**Props 具有只读性。**组件无论是使用`函数声明`还是通过 `class` 声明，都决不能修改自身的 props。
```
function sum(a, b) {
  return a + b;
}
```
这样的函数被称为[“纯函数”](https://en.wikipedia.org/wiki/Pure_function)，因为该函数不会尝试更改入参，且多次调用下相同的入参始终返回相同的结果。
React 非常灵活，但它也有一个严格的规则：**所有 React 组件都必须像纯函数一样保护它们的 props 不被更改。**

### 三、State
**setState()**
不要直接修改 State，`this.state.comment = 'Hello'`此代码不会重新渲染组件，正确的修改state方法应该是：
`this.setState({comment: 'Hello'});`  构造函数是唯一可以给 this.state 赋值的地方。

**State 的更新可能是异步的**
出于性能考虑，React 可能会把多个 setState() 调用合并成一个调用。
因为 this.props 和 this.state 可能会异步更新，所以你不要依赖他们的值来更新下一个状态。
例如，此代码可能会无法更新计数器：
```
this.setState({
  counter: this.state.counter + this.props.increment,
});
```
要解决这个问题，可以让 setState() 接收一个函数而不是一个对象。这个函数用上一个 state 作为第一个参数，将此次更新被应用时的 props 做为第二个参数：
```
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```

**State 的更新会被合并**
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
这里的合并是浅合并，所以 this.setState({comments}) 完整保留了 this.state.posts， 但是完全替换了 this.state.comments。

**数据是向下流动的**

### 四、事件处理

* React 事件的命名采用小驼峰式（camelCase），而不是纯小写。
* 使用 JSX 语法时你需要传入一个函数作为事件处理函数，而不是一个字符串。

例如，绑定点击事件：
```
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

**向事件处理程序传递参数**
```
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
```

### 五、条件渲染
React 中的条件渲染和 JavaScript 中的一样，使用 JavaScript 运算符 `if`或者`条件运算符`去创建元素来表现当前的状态，然后让 React 根据它们来更新 UI。
**if条件渲染**

```
  render() {
    const { isLoggedIn } = this.state;
    let button;
    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick} />;
    } else {
      button = <LoginButton onClick={this.handleLoginClick} />;
    }

    return (
      <div>
        <Greeting isLoggedIn={isLoggedIn} />
        {button}
      </div>
    );
  }
```
**与运算符 &&**
通过花括号包裹代码，可以在 JSX 中嵌入任何表达式。这也包括 JavaScript 中的逻辑与 (&&) 运算符。它可以很方便地进行元素的条件渲染。
```
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
      }
    </div>
  );
}
```
之所以能这样做，是因为在 JavaScript 中，true && expression 总是会返回 expression, 而 false && expression 总是会返回 false。
因此，如果条件是 true，&& 右侧的元素就会被渲染，如果是 false，React 会忽略并跳过它。

**三目运算符**
```
render() {
    const { isLoggedIn } = this.state;
  return (
    <div>
      The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
    </div>
    <div>
      {isLoggedIn
        ? <LogoutButton onClick={this.handleLogoutClick} />
        : <LoginButton onClick={this.handleLoginClick} />
      }
    </div>
  );
}
```

**阻止组件渲染**
```
  render() {
  if (boolean) {
    return null
  }
    return (
      <div>
        <WarningBanner warn={this.state.showWarning} />
        <button onClick={this.handleToggleClick}>
          {this.state.showWarning ? 'Hide' : 'Show'}
        </button>
      </div>
    );
  }
```
虽然渲染被阻止了，但是并不会影响组件的生命周期。

### 六、列表 & key

```
render() {
  const { numbers } = this.props;
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>
      {number}
    </li>
  );
  return (
    <ul>{listItems}</ul>
  );
}
// 或者
render() {
  const { numbers } = this.props;
  return (
    <ul>
      {
        numbers.map((number) =>
          <li key={number.toString()}>
            {number}
          </li>
        )
      }
    </ul>
  );
}
```
key帮助 React 识别哪些元素改变了，比如被添加或删除。因此你应当给数组中的每一个元素赋予一个确定的标识。一个元素的 key 最好是这个元素在列表中拥有的一个独一无二的字符串。通常，我们使用数据中的 id 来作为元素的 key。元素没有确定 id 的时候，万不得已你可以使用元素索引 index 作为 key。
**key 会传递信息给 React ，但不会传递给你的组件。**如果你的组件中需要使用 key 属性的值，请用其他属性名显式传递这个值。
```
const content = posts.map((post) =>
  <Post
    key={post.id}
    id={post.id}
    title={post.title} />
);
```
### 七、表单
```
  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('提交的名字: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          名字:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="提交" />
      </form>
    );
  }
```
**处理多个输入**
当需要处理多个 input 元素时，我们可以给每个元素添加 name 属性，并让处理函数根据 event.target.name 的值选择要执行的操作。
```
class Reservation extends React.Component {
  ...
  handleInputChange(event) {
    const target = event.target;
    const value = target.name === 'isGoing' ? target.checked : target.value;
    const name = target.name;

    this.setState({
      [name]: value
    });
  }

  render() {
    return (
      <form>
        <label>
          参与:
          <input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          来宾人数:
          <input
            name="numberOfGuests"
            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}
```

### 八、父子组件交互
**子组件向父组件传参**
父组件：

```
  getBtnText = (val) => {
    console.log(`父组件收到子组件传来的参数${val}`);
  }

  render() {
    return (
      <div>
        <StrategyTable
          getBtnText={this.getBtnText}
        />
      </div>
    );
  }
```

子组件：
```
  static propTypes = {
    getBtnText: PropTypes.func,
  }
  static defaultProps = {
    getBtnText: () => {},
  }

  queryStrategyChangeList = async () => {
        this.props.getBtnText('parent-msg');
  };
```

**父组件调用子组件函数**
父组件：
```
  onRef = (ref) => {
    this.child = ref;
  }

  goTradeP = (data, e) => {
    this.child.goTradeC('', e);
  }

  render() {
    const { footerBtnText } = this.state;
    return (
      <div>
        <StrategyTable
          isHeight
          isSignalStk
          colSize={[3, 4, 5]}
          getBtnText={this.getBtnText}
          onRef={this.onRef}
        />
        <footer>
          <Button onClick={(e) => { this.goTradeP('', e); }}>{footerBtnText}</Button>
        </footer>
      </div>
    );
  }
```
子组件：
```
  static propTypes = {
    onRef: PropTypes.func,
  }
  static defaultProps = {
    onRef: () => {},
  }

  componentDidMount() {
    this.props.onRef(this);
  }

goTradeC = () => {
  //子组件函数
  console.log('该函数为子组件函数');
}

```
**getChildContext**： 父组件可以通过getChildContext向所有子组件传参，不用一层一层的传递，所有组件可以直接拿到。
父组件
```
    childContextTypes: {//用于说明所传递的数据类型
         name: PropTypes.string.isRequired，
         fruit： PropTypes.string，
    },

    getChildContext: function() {//getChildContext表示该组件通过context传递数据，该方法返回的对象就是context需要传递的数据
         return {
             name: "Jonas",
             fruit: "Banana"
         };
    },
```
子组件
```
    contextTypes: {//在子组件中用于说明context接收的数据类型
        name: PropTypes.string.isRequired，
        fruit： PropTypes.string，
    },

    render: function() {
        return <div>My favorite fruit is: {this.context.fruit}</div>;
    }
```
### 九、生命周期
**组件的生命周期可分成三个状态：**

* Mounting：已插入真实 DOM
* Updating：正在被重新渲染
* Unmounting：已移出真实 DOM

**生命周期的方法有：**
* **componentWillMount：** 在渲染前调用,在客户端也在服务端。
* **componentDidMount :**  在第一次渲染后调用，只在客户端。之后组件已经生成了对应的DOM结构，可以通过this.getDOMNode()来进行访问。 如果你想和其他JavaScript框架一起使用，可以在这个方法中调用setTimeout, setInterval或者发送AJAX请求等操作(防止异步操作阻塞UI)。
* **componentWillReceiveProps：**  在组件接收到一个新的 prop (更新后)时被调用。这个方法在初始化render时不会被调用。
* **shouldComponentUpdate ：** 返回一个布尔值。在组件接收到新的props或者state时被调用。在初始化时或者使用forceUpdate时不被调用。 
可以在你确认不需要更新组件时使用。
* **componentWillUpdate：** 在组件接收到新的props或者state但还没有render时被调用。在初始化时不会被调用。
* **componentDidUpdate：**  在组件完成更新后立即调用。在初始化时不会被调用。
* **componentWillUnmount：** 在组件从 DOM 中移除之前立刻被调用。
![image.png](https://upload-images.jianshu.io/upload_images/18030682-529b3a96170ed7aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 十、 API
**unmountComponentAtNode()**

```
ReactDOM.unmountComponentAtNode(container)
```
从 DOM 中卸载组件，会将其事件处理器（event handlers）和 state 一并清除。如果指定容器上没有对应已挂载的组件，这个函数什么也不会做。如果组件被移除将会返回 true，如果没有组件可被移除将会返回 false。

**className**
className 属性用于指定 CSS 的 class，此特性适用于所有常规 DOM 节点和 SVG 元素，如 <div>，<a> 及其它标签。

**合成事件**
* 剪切板事件：onCopy onCut onPaste
* 键盘事件：onKeyDown onKeyPress onKeyUp 
* 焦点事件：onFocus onBlur
* 表单事件：onChange onInput onInvalid onReset onSubmit 
* 通用事件：onError onLoad
* 鼠标事件：onClick onContextMenu onDoubleClick onDrag onDragEnd onDragEnter onDragExit
onDragLeave onDragOver onDragStart onDrop onMouseDown onMouseEnter onMouseLeave
onMouseMove onMouseOut onMouseOver onMouseUp
* 选择事件： onSelect
* 触摸事件：onTouchCancel onTouchEnd onTouchMove onTouchStart
* UI事件：onScroll onWheel onToggle

### 其它
1. js路由跳转
```
    this.props.history.push({
      pathname: '/ipo/atm/home',
      search: `?skin=${skin}&lang=${lang}`,
      query: { userId:'12345', theme:'white' },
      state: {...},
    });
```
获取参数：
```
this.props.location.query.XXX
this.props.location.state.XXX
this.props.location.search
```
2. 多个类名 静态类名 动态类名
```
import classnames from 'classnames';// 导入插件
      const cls = classnames({
        'first-row': idx === 0,
          'buy-sell': true,//静态类名
          'bg-red': gt(changePct, 0),//动态类名
          'bg-green': gt(0, changePct),
        });

        return (
          <div styleName="date table-cell">
            <p styleName="top">02-15</p>
            <p styleName="bottom">15:24</p>
            <a styleName={cls}>买</a>
          </div>
        );
```