### 一、父子通信
#### 1. 父组件通过props向子组件传值
**vue:**
父组件

```html
<blog-post :title="My journey with Vue"></blog-post>
```
子组件接收
```javascript
Vue.component('blog-post', {
  props: {
    title: {
      type: String,
      default: ''
    }
  },
  template: '<h3>{{ title }}</h3>'
})
```
**react:**
父组件：

```react
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
    onRowClick: PropTypes.func,
    delayTime: PropTypes.number,
    isRefresh: PropTypes.bool,
  }
  static defaultProps = {
    maxLength: -1,
    onRowClick: () => {},
    delayTime: 0,
    isRefresh: true,
  }

  constructor(props) {
    super(props);
    this.state = {
      maxLength: this.props.maxLength // 子组件通过 this.props.属性名 来获取父组件传来的参数
    };
  }
```

#### 2. 子组件向父组件传参
**vue:**
子组件

```vue
<button @click="toParent">
Enlarge text
</button>

methods: {
    toParent() {
      this.$emit('enlarge-text', '我是传过来的值')
    }
}
```
父组件
```vue
<blog-post
...
@enlarge-text="changeTxt"
></blog-post>

methods: {
    changeTxt(val) {
      //do something
      console.log(val);
    }
}
```

**react：利用回调函数参数获取子组件传来的值。**
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

toParentEvt() {
  this.props.onRowClick('子组件信息')
}

  render() {
     return (
       <div>
        <ul>
          <li onClick={this.toParentEvt}>我是子组件信息</li>
        </ul>
      </div>
    );
  }
}
```

#### 3. 父组件访问子组件属性
**vue:**
父组件

```vue
<recodeList ref="recordChild"></recodeList>

this.$refs.recordChild.属性
this.$refs.recordChild.方法
```

**react:**
父组件：

```react
import React, { Component } from 'react';

export default class ListWrap extends Component {

getChild= (ref) => {
  this.childRef = ref;
}

  render() {
     return (
       <div getChildRef={this.getChild}>
        <sunComponent />
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
    getChildRef: PropTypes.func,
  }
  static defaultProps = {
    getChildRef: () => {},
  }

componentDidMount() {
   this.props.getChildRef(this)
}
```

#### 4. react使用context实现跨级组件传值

>context是一个**全局变量**,像是一个大容器,在任何地方都可以访问到,我们可以把要通信的信息放在context上,然后在其他组件中可以随意取到;
>但是React官方不建议使用大量context,尽管他可以减少逐层传递,但是当组件结构复杂的时候,我们并不知道context是从哪里传过来的;而且context是一个全局变量,全局变量正是导致应用走向混乱的罪魁祸首.

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

### 二、生命周期
**vue:**
**1. beforeCreate**
`在实例初始化之后`，数据观测 (data observer) 和 event/watcher 事件配置之前被调用。
**2. created**
`在实例创建完成后被立即调用`。在这一步，实例已完成以下的配置：数据观测 (data observer)，属性和方法的运算，watch/event 事件回调。然而，挂载阶段还没开始，\$el 属性目前尚不可用。
**3. beforeMount**
`在挂载开始之前被调用`
**4. mounted**
`实例被挂载后调用`，这时 el 被新创建的 vm.$el 替换了。如果根实例挂载到了一个文档内的元素上，当 mounted 被调用时 vm.$el 也在文档内。
**5. beforeUpdate**
`数据更新时调用`，发生在虚拟 DOM 打补丁之前。
**6. updated**
`数据更新后调用`，发生在虚拟 DOM 打补丁之后。在大多数情况下，你应该避免在此期间更改状态。如果要相应状态改变，通常最好使用[计算属性](https://cn.vuejs.org/v2/api/#computed)或 [watcher](https://cn.vuejs.org/v2/api/#watch) 取而代之。
注意 `updated` **不会**保证所有的子组件也都一起被重绘。如果你希望等到整个视图都重绘完毕，可以在 `updated` 里使用 [vm.$nextTick](https://cn.vuejs.org/v2/api/#vm-nextTick)：

```vue
updated: function () {
  this.$nextTick(function () {
    // Code that will run only after the
    // entire view has been re-rendered
  })
}
```
**7. activated**
`被 keep-alive 缓存的组件激活时调用`。
**8. deactivated**
`被 keep-alive 缓存的组件停用时调用`。
**9. beforeDestroy**
`实例销毁之前调用`。在这一步，实例仍然完全可用。
**10. destroyed**
`实例销毁后调用`。该钩子被调用后，对应 Vue 实例的所有指令都被解绑，所有的事件监听器被移除，所有的子实例也都被销毁。
**11. errorCaptured** (2.5.0+ 新增)
类型：`(err: Error, vm: Component, info: string) => ?boolean`
`当捕获一个来自子孙组件的错误时被调用`。此钩子会收到三个参数：错误对象、发生错误的组件实例以及一个包含错误来源信息的字符串。此钩子可以返回 false 以阻止该错误继续向上传播。

![image.png](https://upload-images.jianshu.io/upload_images/18030682-42a93fd710fb9307.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**beforeRouteEnter(to, from, next)**：进入路由之前执行的函数
**beforeRouteLeave(to, from, next)**：离开路由之前执行的函数

**react:**
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

### 三、路由
**Vue：**
**路由模式：** history、hash(默认)
**路由跳转：**

```vue
<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>
<router-link :to="{ path: '/abc'}" replace></router-link>
router.push(location, onComplete?, onAbort?)
router.push(location).then(onComplete).catch(onAbort)
router.replace(location, onComplete?, onAbort?)
router.replace(location).then(onComplete).catch(onAbort)
router.go(n)
router.back()
router.forward()
```
参数：
`{ name: xxx, params: {...}, query: {..} }`或者`{ path: xxx,  query: {..} }`
获取参数：`this.$route.params`、 `this.$route.query`

**生命周期：**
```vue
router.beforeEach((to, from, next) => {
  /* 必须调用 `next` */
})

router.beforeResolve((to, from, next) => {
  /* 必须调用 `next` */
})

router.afterEach((to, from) => {})
```

**react:**
**路由模式：** browserHistory、hashHistory、createMemoryHistory

**路由跳转：**
```react
<Link to={`/users/${user.id}`} activeClassName="active">{user.name}</Link>
this.props.history.push({...})
this.props.history.replace({...})
this.props.history.go(n)
this.props.history.goBack()
this.props.history.goForward()
```

参数：
`{ pathname: xxx, query: {..}, state: {...} }`
获取参数：`props.location.query`、`props.location.state`

**生命周期：**
`routerWillLeave(nextLocation)`