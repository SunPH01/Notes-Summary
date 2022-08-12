需要组件之进行通信的几种情况：
* 父组件向子组件通信
* 子组件向父组件通信
* 跨级组件通信
* 没有嵌套关系组件之间的通信

### 1. 父组件向子组件通信
React数据流动是单向的,父组件向子组件通信也是最常见的;父组件通过props向子组件传递需要的信息。

父组件：
```
<StrategyTable
  maxLength={2}
  styleName="strategy-able"
  onRowClick={this.onRowClick}
  delayTime={500}
  isRefresh={false}
 />
```

子组件：
```react
import PropTypes from 'prop-types';

  static propTypes = {
    maxLength: PropTypes.number,
    isSignalStk: PropTypes.bool, 
    onRowClick: PropTypes.func,
    getLastData: PropTypes.func,
    onRef: PropTypes.func,
    assetId: PropTypes.string,
    sessionId: PropTypes.string,
    isDataSeparate: PropTypes.bool,
    delayTime: PropTypes.number,
    isRefresh: PropTypes.bool,
  }
  static defaultProps = {
    maxLength: -1,
    isSignalStk: false,
    onRowClick: () => {},
    getLastData: () => {},
    onRef: () => {},
    assetId: '',
    sessionId: '',
    isDataSeparate: false,
    delayTime: 0,
    isRefresh: true,
  }

  constructor(props) {
    super(props);
    this.state = {
      maxLength: this.props.maxLength, // 子组件通过 this.props.属性名 来获取父组件传来的参数
    };
  }
```

### 2. 子组件向父组件通信
利用回调函数参数获取子组件传来的值。

父组件：
```react
import React, { Component } from 'react';

export default class ListWrap extends Component {

getSunData = (val) => {
  this.text = val;
}

  render() {
     return (
       <div>
        <sunComponent onRowClick={this.getSunData}/>
      </div>
    );
  }
}
```

子组件：
```react
import React, { Component } from 'react';
import PropTypes from 'prop-types';

export default class sunComponent extends Component {

  static propTypes = {
    onRowClick: PropTypes.func,
  }
  static defaultProps = {
    onRowClick: () => {},
  }

  render() {
     return (
       <div>
        <ul>
          <li onClick={() => {this.props.onRowClick('子组件信息')}}>我是子组件信息</li>
        </ul>
      </div>
    );
  }
}
```
### 3. 跨级组件通信
跨级组件通信有两种方法：
**1. 层层传递props**

>例如A组件和B组件之间要进行通信,先找到A和B公共的父组件,A先向C组件通信,C组件通过props和B组件通信,此时C组件起的就是中间件的作用

**2.使用context**
>context是一个**全局变量**,像是一个大容器,在任何地方都可以访问到,我们可以把要通信的信息放在context上,然后在其他组件中可以随意取到;
>但是React官方不建议使用大量context,尽管他可以减少逐层传递,但是当组件结构复杂的时候,我们并不知道context是从哪里传过来的;而且context是一个全局变量,全局变量正是导致应用走向混乱的罪魁祸首.

props传参就不多说了，接下来重点说一下context。

对于父组件，也就是Context生产者，需要通过一个静态属性childContextTypes声明提供给子组件的Context对象的属性，并实现一个实例getChildContext方法，返回一个代表Context的纯对象 (plain object) 。
```react
import React from 'react'
import PropTypes from 'prop-types'

class MiddleComponent extends React.Component {
  render () {
    return <ChildComponent />
  }
}

class ParentComponent extends React.Component {
  // 声明Context对象属性
  static childContextTypes = {
    propA: PropTypes.string,
    methodA: PropTypes.func
  }
  
  // 返回Context对象，方法名是约定好的
  getChildContext () {
    return {
      propA: 'propA',
      methodA: () => 'methodA'
    }
  }
  
  render () {
    return <MiddleComponent />
  }
}
```

而对于Context的消费者，通过如下方式访问父组件提供的Context。
```react
import React from 'react'
import PropTypes from 'prop-types'

class ChildComponent extends React.Component {
  // 声明需要使用的Context属性
  static contextTypes = {
    propA: PropTypes.string,
  }
  
  render () {
    const {
      propA,
      methodA
    } = this.context
    
    console.log(`context.propA = ${propA}`)  // context.propA = propA
    console.log(`context.methodA = ${methodA}`)  // context.methodA = undefined
    
    return ...
  }
}
```
子组件需要通过一个静态属性contextTypes声明后，才能访问父组件Context对象的属性，否则，即使属性名没写错，拿到的对象也是undefined。

**几个可以直接获取Context的地方:**
实际上，除了实例的context属性(this.context)，React组件还有很多个地方可以直接访问父组件提供的Context。比如构造方法：
`constructor(props, context)`

比如生命周期：
`componentWillReceiveProps(nextProps, nextContext)`
`shouldComponentUpdate(nextProps, nextState, nextContext)`
`componetWillUpdate(nextProps, nextState, nextContext)`

### 4. 没有嵌套关系的组件通信
在componentDidMount事件中,如果组件挂载完成,再订阅事件;在组件卸载的时候,在componentWillUnmount事件中取消事件的订阅;
以常用的发布/订阅模式举例,借用Node.js Events模块的浏览器版实现。

下面例子中的组件关系: List1和List2没有任何嵌套关系,App是他们的父组件;

实现这样一个功能: 点击List2中的一个按钮,改变List1中的信息显示
首先需要项目中安装events 包：
`npm install events --save`

在src下新建一个util目录里面建一个events.js
```
import { EventEmitter } from 'events';

export default new EventEmitter();
```
list1.jsx
```react
import React, { Component } from 'react';
import emitter from '../util/events';

class List extends Component {
    constructor(props) {
        super(props);
        this.state = {
            message: 'List1',
        };
    }
    componentDidMount() {
        // 组件装载完成以后声明一个自定义事件
        this.eventEmitter = emitter.addListener('changeMessage', (message) => {
            this.setState({
                message,
            });
        });
    }
    componentWillUnmount() {
        emitter.removeListener(this.eventEmitter);
    }
    render() {
        return (
            <div>
                {this.state.message}
            </div>
        );
    }
}

export default List;

```

List2.jsx
```react
import React, { Component } from 'react';
import emitter from '../util/events';

class List2 extends Component {
    handleClick = (message) => {
        emitter.emit('changeMessage', message);
    };
    render() {
        return (
            <div>
                <button onClick={this.handleClick.bind(this, 'List2')}>
                  点击我改变List1组件中显示信息
                </button>
            </div>
        );
    }
}

```

APP.jsx
```react
import React, { Component } from 'react';
import List1 from './components/List1';
import List2 from './components/List2';


export default class App extends Component {
    render() {
        return (
            <div>
                <List1 />
                <List2 />
            </div>
        );
    }
}
```


**总结:**
* 父组件向子组件通信: props
* 子组件向父组件通信: 回调函数
* 跨级组件通信: 层层组件传递props/context
* 没有嵌套关系组件之间的通信: EventEmitter