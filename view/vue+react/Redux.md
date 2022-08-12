### 一、 介绍
安装：`npm install --save redux`

**附加包**
多数情况下，你还需要使用 [React 绑定库](http://github.com/gaearon/react-redux)和[开发者工具](http://github.com/gaearon/redux-devtools)。
```
npm install --save react-redux
npm install --save-dev redux-devtools
```
**State**
Store对象包含所有数据。如果想得到某个时点的数据，就要对 Store 生成快照。这种时点的数据集合，就叫做 State。
当前时刻的 State，可以通过`store.getState()`拿到。
```
import { createStore } from 'redux';
const store = createStore(fn);

const state = store.getState();
```
应用中所有的 state 都以一个对象树的形式储存在一个单一的 store 中。 惟一改变 state 的办法是触发 action，一个描述发生什么的对象。 为了描述 action 如何改变 state 树，你需要编写 reducers。
```
import { createStore } from 'redux';
/**
 * 这是一个 reducer，形式为 (state, action) => state 的纯函数。
 * 描述了 action 如何把 state 转变成下一个 state。
 *
 * state 的形式取决于你，可以是基本类型、数组、对象、
 * 甚至是 Immutable.js 生成的数据结构。惟一的要点是
 * 当 state 变化时需要返回全新的对象，而不是修改传入的参数。
 *
 * 下面例子使用 `switch` 语句和字符串来做判断，但你可以写帮助类(helper)
 * 根据不同的约定（如方法映射）来判断，只要适用你的项目即可。
 */
function counter(state = 0, action) {
  switch (action.type) {
  case 'INCREMENT':
    return state + 1;
  case 'DECREMENT':
    return state - 1;
  default:
    return state;
  }
}

// 创建 Redux store 来存放应用的状态。
// API 是 { subscribe, dispatch, getState }。
let store = createStore(counter);

// 可以手动订阅更新，也可以事件绑定到视图层。
store.subscribe(() =>
  console.log(store.getState())
);

// 改变内部 state 惟一方法是 dispatch 一个 action。
// action 可以被序列化，用日记记录和储存下来，后期还可以以回放的方式执行
store.dispatch({ type: 'INCREMENT' });
// 1
store.dispatch({ type: 'INCREMENT' });
// 2
store.dispatch({ type: 'DECREMENT' });
// 1
```
你应该把要做的修改变成一个普通对象，这个对象被叫做 action，而不是直接修改 state。然后编写专门的函数来决定每个 action 如何改变应用的 state，这个函数被叫做` reducer`。
如果你以前使用 Flux，那么你只需要注意一个重要的区别。**Redux 没有 Dispatcher 且不支持多个 store**。相反，只有一个单一的 store 和一个根级的 reduce 函数（reducer）。随着应用不断变大，你应该**把根级的 reducer 拆成多个小的 reducers，分别独立地操作 state 树的不同部分，而不是添加新的 stores**。这就像一个 React 应用只有一个根级的组件，这个根组件又由很多小组件构成。

#### 1. 三大原则
* **单一数据源**： 整个应用的 state被储存在一棵 object tree 中，并且这个 object tree 只存在于唯一一个 store中。
* **state是只读的**： 唯一改变 state 的方法就是触发 action，action 是一个用于描述已发生事件的普通对象。
* **使用纯函数来执行修改**： 为了描述 action 如何改变 state tree ，你需要编写 reducers。

### 二、基础

#### 1. Action
**Action** 是把数据从应用（译者注：这里之所以不叫 view 是因为这些数据有可能是服务器响应，用户输入或其它非 view 的数据 ）传到 store 的有效载荷。它是 store 数据的**唯一**来源。一般来说你会通过 [`store.dispatch()`](https://www.redux.org.cn/docs/api/Store.html#dispatch) 将 action 传到 store。
我们约定，action 内必须使用一个字符串类型的 type 字段来表示将要执行的动作。多数情况下，type 会被定义成字符串常量。当应用规模越来越大时，建议使用单独的模块或文件来存放 action。
可以这样理解，Action 描述当前发生的事情。改变 State 的唯一办法，就是使用 Action。它会运送数据到 Store。

**Action创建函数**
Action创建函数就是生成 action 的方法。
在 Redux 中的 action 创建函数只是简单的返回一个 action:
```
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}
```
在 [传统的 Flux](http://facebook.github.io/flux) 实现中，当调用 action 创建函数时，一般会触发一个 dispatch，像这样：
```
function addTodoWithDispatch(text) {
  const action = {
    type: ADD_TODO,
    text
  }
  dispatch(action)
}
```
Redux 中只需把 action 创建函数的结果传给 dispatch() 方法即可发起一次 dispatch 过程。
```
dispatch(addTodo(text))
dispatch(completeTodo(index))
```
或者创建一个 被绑定的 action 创建函数 来自动 dispatch：
```
const boundAddTodo = text => dispatch(addTodo(text))
const boundCompleteTodo = index => dispatch(completeTodo(index))

// 直接调用
boundAddTodo(text);
boundCompleteTodo(index);
```
store 里能直接通过 [`store.dispatch()`](https://www.redux.org.cn/docs/api/Store.html#dispatch) 调用 `dispatch()` 方法，但是多数情况下你会使用 [react-redux](http://github.com/gaearon/react-redux) 提供的 `connect()` 帮助器来调用。[`bindActionCreators()`](https://www.redux.org.cn/docs/api/bindActionCreators.html) 可以自动把多个 action 创建函数 绑定到 `dispatch()` 方法上。

#### 2. Reducer
**Reducers** 指定了应用状态的变化如何响应 [actions](https://www.redux.org.cn/docs/basics/Actions.html) 并发送到 store 的，记住 actions 只是描述了*有事情发生了*这一事实，并没有描述应用如何更新 state。
Store 收到 Action 以后，必须给出一个新的 State，这样 View 才会发生变化。这种 State 的计算过程就叫做 Reducer。
**Reducer 是一个函数，它接受 Action 和当前 State 作为参数，返回一个新的 State。**

```
const defaultState = 0;
const reducer = (state = defaultState, action) => {
  switch (action.type) {
    case 'ADD':
      return state + action.payload;
    default: 
      return state;
  }
};

const state = reducer(1, {
  type: 'ADD',
  payload: 2
});
```
上面代码中，reducer函数收到名为ADD的 Action 以后，就返回一个新的 State，作为加法的计算结果。其他运算的逻辑（比如减法），也可以根据 Action 的不同来实现。
实际应用中，Reducer 函数不用像上面这样手动调用，store.dispatch方法会触发 Reducer 的自动执行。为此，Store 需要知道 Reducer 函数，做法就是在生成 Store 的时候，将 Reducer 传入createStore方法。
```
import { createStore } from 'redux';
const store = createStore(reducer);
```
`createStore`接受 Reducer 作为参数，生成一个新的 Store。以后每当`store.dispatch`发送过来一个新的 Action，就会自动调用 Reducer，得到新的 State。

**设计 State 结构：**
以 todo 应用为例，需要保存两种不同的数据：
* 当前选中的任务过滤条件；
* 完整的任务列表；
```
{
  visibilityFilter: 'SHOW_ALL',
  todos: [
    {
      text: 'Consider using Redux',
      completed: true,
    },
    {
      text: 'Keep all state in a single tree',
      completed: false
    }
  ]
}
```
>开发复杂的应用时，不可避免会有一些数据相互引用。建议你尽可能地把 state 范式化，不存在嵌套。把所有数据放到一个对象里，每个数据以 ID 为主键，不同实体或列表间通过 ID 相互引用数据。把应用的 state 想像成数据库。这种方法在 [normalizr](https://github.com/gaearon/normalizr) 文档里有详细阐述。例如，实际开发中，在 state 里同时存放 `todosById: { id -> todo }` 和 `todos: array<id>` 是比较好的方式，本文中为了保持示例简单没有这样处理。

**Action处理：**
现在我们已经确定了 state 对象的结构，就可以开始开发 reducer。reducer 就是一个纯函数，接收旧的 state 和 action，返回新的 state。（`(previousState, action) => newState`）
保持 reducer 纯净非常重要。**永远不要在 reducer 里做这些操作：**
* 修改传入参数；
* 执行有副作用的操作，如 API 请求和路由跳转；
* 调用非纯函数，如 Date.now() 或 Math.random()。(因为每次会得到不一样的结果)
**只要传入参数相同，返回计算得到的下一个 state 就一定相同。没有特殊情况、没有副作用，没有 API 请求、没有变量修改，单纯执行计算。**
```
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    default:
      return state
  }
}
```
注意：
1. 不要修改 state。
2. 在 default 情况下返回旧的 state。遇到未知的 action 时，一定要返回旧的 state。

**处理多个action**
```
import {
  ADD_TODO,
  TOGGLE_TODO,
  SET_VISIBILITY_FILTER,
  VisibilityFilters
} from './actions'

...

function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    case ADD_TODO:
      return Object.assign({}, state, {
        todos: [
          ...state.todos,
          {
            text: action.text,
            completed: false
          }
        ]
      })
    default:
      return state
  }
}
```
**拆分Reducer**
上面的代码看起来有些冗长，能否变得更通俗易懂？这里的 todos 和 visibilityFilter 的更新看起来是相互独立的。有时 state 中的字段是相互依赖的，需要认真考虑。
看下面这个例子：
```
const chatReducer = (state = defaultState, action = {}) => {
  const { type, payload } = action;
  switch (type) {
    case ADD_CHAT:
      return Object.assign({}, state, {
        chatLog: state.chatLog.concat(payload)
      });
    case CHANGE_STATUS:
      return Object.assign({}, state, {
        statusMessage: payload
      });
    case CHANGE_USERNAME:
      return Object.assign({}, state, {
        userName: payload
      });
    default: return state;
  }
};
```
上面代码中，三种 Action 分别改变 State 的三个属性。
* ADD_CHAT：chatLog属性
* CHANGE_STATUS：statusMessage属性
* CHANGE_USERNAME：userName属性
这三个属性之间没有联系，这提示我们可以把 Reducer 函数拆分。不同的函数负责处理不同属性，最终把它们合并成一个大的 Reducer 即可。
```
const chatReducer = (state = defaultState, action = {}) => {
  return {
    chatLog: chatLog(state.chatLog, action),
    statusMessage: statusMessage(state.statusMessage, action),
    userName: userName(state.userName, action)
  }
};
```
Reducer 函数被拆成了三个小函数，每一个负责生成对应的属性。
Redux 提供了一个`combineReducers`方法，用于 Reducer 的拆分。你只要定义各个子 Reducer 函数，然后用这个方法，将它们合成一个大的 Reducer。
```
import { combineReducers } from 'redux';

const chatReducer = combineReducers({
  chatLog,
  statusMessage,
  userName
})

export default todoApp;
```
这种写法有一个前提，就是 State 的属性名必须与子 Reducer 同名。如果不同名，就要采用下面的写法。
```
const reducer = combineReducers({
  a: doSomethingWithA,
  b: processB,
  c: c
})

// 等同于
function reducer(state = {}, action) {
  return {
    a: doSomethingWithA(state.a, action),
    b: processB(state.b, action),
    c: c(state.c, action)
  }
}
```
下面是combineReducer的简单实现,可以了解一下：
```
const combineReducers = reducers => {
  return (state = {}, action) => {
    return Object.keys(reducers).reduce(
      (nextState, key) => {
        nextState[key] = reducers[key](state[key], action);
        return nextState;
      },
      {} 
    );
  };
};
```
你可以把所有子 Reducer 放在一个文件里面，然后统一引入。
```
import { combineReducers } from 'redux'
import * as reducers from './reducers'

const reducer = combineReducers(reducers)
```


#### 3. Store
Store 就是保存数据的地方，你可以把它看成一个容器。整个应用只能有一个 Store。
Redux 提供`createStore`这个函数，用来生成 Store。
```
import { createStore } from 'redux';
const store = createStore(fn);
```
createStore函数接受另一个函数作为参数，返回新生成的 Store 对象。

**store.subscribe()**
Store 允许使用`store.subscribe`方法设置监听函数，一旦 State 发生变化，就自动执行这个函数。
```
import { createStore } from 'redux';
const store = createStore(reducer);

store.subscribe(listener);
```
store.subscribe方法返回一个函数，调用这个函数就可以解除监听。
```
let unsubscribe = store.subscribe(() =>
  console.log(store.getState())
);

unsubscribe();
```

**store的实现**
Store 提供了三个方法：
* store.getState()  // 获取 state
* store.dispatch()  // 更新state
* store.subscribe() // 监听state变化
```
import { createStore } from 'redux';
let { subscribe, dispatch, getState } = createStore(reducer);
```
createStore方法还可以接受第二个参数，表示 State 的最初状态。这通常是服务器给出的。
```
let store = createStore(todoApp, window.STATE_FROM_SERVER)
```
window.STATE_FROM_SERVER就是整个应用的状态初始值。注意，如果提供了这个参数，它会覆盖 Reducer 函数的默认初始值。

下面是createStore方法的一个简单实现，可以了解一下 Store 是怎么生成的。
```
const createStore = (reducer) => {
  let state;
  let listeners = [];

  const getState = () => state;

  const dispatch = (action) => {
    state = reducer(state, action);
    listeners.forEach(listener => listener());
  };

  const subscribe = (listener) => {
    listeners.push(listener);
    return () => {
      listeners = listeners.filter(l => l !== listener);
    }
  };

  dispatch({});

  return { getState, dispatch, subscribe };
};
```

#### 4、工作流程
**严格的单向数据流是 Redux 架构的设计核心**，这意味着应用中所有的数据都遵循相同的生命周期。
![image.png](https://upload-images.jianshu.io/upload_images/18030682-40c606c2c900c3ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 首先，用户发出 Action。`store.dispatch(action);`
2. 然后，Store 自动调用 Reducer，并且传入两个参数：当前 State 和收到的 Action。 Reducer 会返回新的 State 。`let nextState = todoApp(previousState, action);`State 一旦有变化，Store 就会调用监听函数。`store.subscribe(listener);`listener可以通过store.getState()得到当前状态。如果使用的是 React，这时可以触发重新渲染 View。
3. 根 reducer 应该把多个子 reducer 输出合并成一个单一的 state 树。
4. Redux store 保存了根 reducer 返回的完整 state 树。
```
function listerner() {
  let newState = store.getState();
  component.setState(newState);   
}
```

### 三、 高阶

#### 1. 异步Action

**Action 创建函数也可以是异步非纯函数。**
当调用异步 API 时，有两个非常关键的时刻：发起请求的时刻，和接收到响应的时刻（也可能是超时）。
这两个时刻都可能会更改应用的 state；为此，你需要 dispatch 普通的同步 action。一般情况下，每个 API 请求都需要 dispatch 至少三种 action：
* **通知 reducer 请求开始的 action**
  对于这种 action，reducer 可能会切换一下 state 中的 isFetching 标记。以此来告诉 UI 来显示加载界面。
* **通知 reducer 请求成功的 action**
对于这种 action，reducer 可能会把接收到的新数据合并到 state 中，并重置 isFetching。UI 则会隐藏加载界面，并显示接收到的数据。
* **通知 reducer 请求失败的 action**
对于这种 action，reducer 可能会重置 isFetching。另外，有些 reducer 会保存这些失败信息，并在 UI 里显示出来。

为了区分这三种 action，可能在 action 里添加一个专门的 status 字段作为标记位：
```
{ type: 'FETCH_POSTS' }
{ type: 'FETCH_POSTS', status: 'error', error: 'Oops' }
{ type: 'FETCH_POSTS', status: 'success', response: { ... } }
```
又或者为它们定义不同的 type：
```
{ type: 'FETCH_POSTS_REQUEST' }
{ type: 'FETCH_POSTS_FAILURE', error: 'Oops' }
{ type: 'FETCH_POSTS_SUCCESS', response: { ... } }
```

#### 2. 中间件 （middleware）

怎么才能 Reducer 在异步操作结束后自动执行呢？这就要用到新的工具：中间件（middleware）。
middleware 是指可以被嵌入在框架接收请求到产生响应过程之中的代码。
它提供的是位于 action 被发起之后，到达 reducer 之前的扩展点。 你可以利用 Redux middleware 来进行日志记录、创建崩溃报告、调用异步接口或者路由等等。
为了理解中间件，让我们站在框架作者的角度思考问题：如果要添加功能，你会在哪个环节添加？
*  Reducer：纯函数，只承担计算 State 的功能，不合适承担其他功能，也承担不了，因为理论上，纯函数不能进行读写操作。
* View：与 State 一一对应，可以看作 State 的视觉层，也不合适承担其他功能。
* Action：存放数据的对象，即消息的载体，只能被别人操作，自己不能进行任何操作。

只有发送 Action 的这个步骤，即store.dispatch()方法，可以添加功能。举例来说，要添加日志功能，把 Action 和 State 打印出来，可以对store.dispatch进行如下改造。
```
let next = store.dispatch;
store.dispatch = function dispatchAndLog(action) {
  console.log('dispatching', action);
  next(action);
  console.log('next state', store.getState());
}
```
上面代码中，对store.dispatch进行了重定义，在发送 Action 前后添加了打印功能。这就是中间件的雏形。
中间件就是一个函数，对store.dispatch方法进行了改造，在发出 Action 和执行 Reducer 这两步之间，添加了其他功能。

**redux-logger:**

```
import { applyMiddleware, createStore } from 'redux';
import createLogger from 'redux-logger';
const logger = createLogger();

const store = createStore(
  reducer,
  applyMiddleware(logger)
);
```

redux-logger提供一个生成器createLogger，可以生成日志中间件logger。然后，将它放在applyMiddleware方法之中，传入createStore方法，就完成了store.dispatch()的功能增强。

这里有两点需要注意：
（1）createStore方法可以接受整个应用的初始状态作为参数，那样的话，applyMiddleware就是第三个参数了。
```
const store = createStore(
  reducer,
  initial_state,
  applyMiddleware(logger)
);
```
（2）中间件的次序有讲究。
```
const store = createStore(
  reducer,
  applyMiddleware(thunk, promise, logger)
);
```
applyMiddleware方法的三个参数，就是三个中间件。有的中间件有次序要求，使用前要查一下文档。比如，logger就一定要放在最后，否则输出结果会不正确。

**applyMiddlewares:**是 Redux 的原生方法，作用是将所有中间件组成一个数组，依次执行。下面是它的源码。
```
export default function applyMiddleware(...middlewares) {
  return (createStore) => (reducer, preloadedState, enhancer) => {
    var store = createStore(reducer, preloadedState, enhancer);
    var dispatch = store.dispatch;
    var chain = [];

    var middlewareAPI = {
      getState: store.getState,
      dispatch: (action) => dispatch(action)
    };
    chain = middlewares.map(middleware => middleware(middlewareAPI));
    dispatch = compose(...chain)(store.dispatch);

    return {...store, dispatch}
  }
}
```
所有中间件被放进了一个数组chain，然后嵌套执行，最后执行store.dispatch。可以看到，中间件内部（middlewareAPI）可以拿到getState和dispatch这两个方法。

**redux-thunk:**
异步操作至少要送出两个 Action：用户触发第一个 Action，这个跟同步操作一样，没有问题；如何才能在操作结束时，系统自动送出第二个 Action 呢？
奥妙就在 `Action Creator` 之中。
```
class AsyncApp extends Component {
  componentDidMount() {
    const { dispatch, selectedPost } = this.props
    dispatch(fetchPosts(selectedPost))
  }
```
上面代码是一个异步组件的例子。加载成功后（componentDidMount方法），它送出了（dispatch方法）一个 Action，向服务器要求数据 fetchPosts(selectedSubreddit)。这里的fetchPosts就是 Action Creator。

```
const fetchPosts = postTitle => (dispatch, getState) => {
  dispatch(requestPosts(postTitle));
  return fetch(`/some/API/${postTitle}.json`)
    .then(response => response.json())
    .then(json => dispatch(receivePosts(postTitle, json)));
  };
};

// 使用方法一
store.dispatch(fetchPosts('reactjs'));
// 使用方法二
store.dispatch(fetchPosts('reactjs')).then(() =>
  console.log(store.getState())
);
```
上面代码中，fetchPosts是一个Action Creator（动作生成器），返回一个函数。这个函数执行后，先发出一个Action（requestPosts(postTitle)），然后进行异步操作。拿到结果后，先将结果转成 JSON 格式，然后再发出一个 Action（ receivePosts(postTitle, json)）。需要注意以下几点：
* fetchPosts返回了一个函数，而普通的 Action Creator 默认返回一个对象。
* 返回的函数的参数是dispatch和getState这两个 Redux 方法，普通的 Action Creator 的参数是 Action 的内容。
* 在返回的函数之中，先发出一个 Action（requestPosts(postTitle)），表示操作开始。
* 异步操作结束之后，再发出一个 Action（receivePosts(postTitle, json)），表示操作结束。

这样的处理，就解决了自动发送第二个 Action 的问题。但是，又带来了一个新的问题，Action 是由store.dispatch方法发送的。而store.dispatch方法正常情况下，参数只能是对象，不能是函数。
这时，就要使用中间件[`redux-thunk`](https://github.com/gaearon/redux-thunk)。
```
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import reducer from './reducers';

// Note: this API requires redux@>=3.1.0
const store = createStore(
  reducer,
  applyMiddleware(thunk)
);
```
上面代码使用redux-thunk中间件，改造store.dispatch，使得后者可以接受函数作为参数。
因此，**异步操作的第一种解决方案就是，写出一个返回函数的 Action Creator，然后使用redux-thunk中间件改造store.dispatch。**

**redux-promise:**
既然 Action Creator 可以返回函数，当然也可以返回其他值。另一种异步操作的解决方案，就是让 Action Creator 返回一个 Promise 对象。
```
import { createStore, applyMiddleware } from 'redux';
import promiseMiddleware from 'redux-promise';
import reducer from './reducers';

const store = createStore(
  reducer,
  applyMiddleware(promiseMiddleware)
); 
```
这个中间件使得store.dispatch方法可以接受 Promise 对象作为参数。这时，Action Creator 有两种写法。写法一，返回值是一个 Promise 对象。
```
const fetchPosts = 
  (dispatch, postTitle) => new Promise(function (resolve, reject) {
     dispatch(requestPosts(postTitle));
     return fetch(`/some/API/${postTitle}.json`)
       .then(response => {
         type: 'FETCH_POSTS',
         payload: response.json()
       });
});
```
写法二，Action 对象的`payload`属性是一个 Promise 对象。这需要从[`redux-actions`](https://github.com/acdlite/redux-actions)模块引入`createAction`方法，并且写法也要变成下面这样。
```
import { createAction } from 'redux-actions';

class AsyncApp extends Component {
  componentDidMount() {
    const { dispatch, selectedPost } = this.props
    // 发出同步 Action
    dispatch(requestPosts(selectedPost));
    // 发出异步 Action
    dispatch(createAction(
      'FETCH_POSTS', 
      fetch(`/some/API/${postTitle}.json`)
        .then(response => response.json())
    ));
  }
```
第二个dispatch方法发出的是异步 Action，只有等到操作结束，这个 Action 才会实际发出。注意，createAction的第二个参数必须是一个 Promise 对象。
看一下`redux-promise`的[源码](https://github.com/acdlite/redux-promise/blob/master/src/index.js)，就会明白它内部是怎么操作的。
```
export default function promiseMiddleware({ dispatch }) {
  return next => action => {
    if (!isFSA(action)) {
      return isPromise(action)
        ? action.then(dispatch)
        : next(action);
    }

    return isPromise(action.payload)
      ? action.payload.then(
          result => dispatch({ ...action, payload: result }),
          error => {
            dispatch({ ...action, payload: error, error: true });
            return Promise.reject(error);
          }
        )
      : next(action);
  };
}
```
从上面代码可以看出，如果 Action 本身是一个 Promise，它 resolve 以后的值应该是一个 Action 对象，会被dispatch方法送出（action.then(dispatch)），但 reject 以后不会有任何动作；如果 Action 对象的payload属性是一个 Promise 对象，那么无论 resolve 和 reject，dispatch方法都会发出 Action。

### 四、React-Redux
为了方便使用，Redux 的作者封装了一个 React 专用的库 [React-Redux](https://github.com/reactjs/react-redux)。这个库是可以选用的。实际项目中，你应该权衡一下，是直接使用 Redux，还是使用 React-Redux。后者虽然提供了便利，但是需要掌握额外的 API，并且要遵守它的组件拆分规范。

**1. UI组件**
React-Redux 将所有组件分成两大类：**UI 组件**（presentational component）和**容器组件**（container component）。
UI 组件有以下几个特征：
* 只负责 UI 的呈现，不带有任何业务逻辑
* 没有状态（即不使用this.state这个变量）
* 所有数据都由参数（this.props）提供
* 不使用任何 Redux 的 API
下面就是一个 UI 组件的例子。
```
const Title = value => <h1>{value}</h1>;
```
因为不含有状态，UI 组件又称为"纯组件"，即它纯函数一样，纯粹由参数决定它的值。

**2. 容器组件**
容器组件的特征恰恰相反：
* 负责管理数据和业务逻辑，不负责 UI 的呈现
* 带有内部状态
* 使用 Redux 的 API

总之，只要记住一句话就可以了：**UI 组件负责 UI 的呈现，容器组件负责管理数据和逻辑**。
如果一个组件既有 UI 又有业务逻辑，那怎么办？

你可能会问，如果一个组件既有 UI 又有业务逻辑，那怎么办？回答是，将它拆分成下面的结构：外面是一个容器组件，里面包了一个UI 组件。前者负责与外部的通信，将数据传给后者，由后者渲染出视图。

**connect**
React-Redux 提供connect方法，用于从 UI 组件生成容器组件。connect的意思，就是将这两种组件连起来。
```
import { connect } from 'react-redux'
const VisibleTodoList = connect()(TodoList);
```
上面代码中，TodoList是 UI 组件，VisibleTodoList就是由 React-Redux 通过connect方法自动生成的容器组件。

但是，因为没有定义业务逻辑，上面这个容器组件毫无意义，只是 UI 组件的一个单纯的包装层。为了定义业务逻辑，需要给出下面两方面的信息。
（1）输入逻辑：外部的数据（即state对象）如何转换为 UI 组件的参数
（2）输出逻辑：用户发出的动作如何变为 Action 对象，从 UI 组件传出去。

因此，connect方法的完整 API 如下。
```
import { connect } from 'react-redux'

const VisibleTodoList = connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)
```
上面代码中，connect方法接受两个参数：mapStateToProps和mapDispatchToProps。它们定义了 UI 组件的业务逻辑。前者负责输入逻辑，即将state映射到 UI 组件的参数（props），后者负责输出逻辑，即将用户对 UI 组件的操作映射成 Action。

**mapStateToProps**
mapStateToProps是一个函数。它的作用就是像它的名字那样，建立一个从（外部的）state对象到（UI 组件的）props对象的映射关系。
作为函数，mapStateToProps执行后应该返回一个对象，里面的每一个键值对就是一个映射。
```
const mapStateToProps = (state) => {
  return {
    todos: getVisibleTodos(state.todos, state.visibilityFilter)
  }
}
```
上面代码中，mapStateToProps是一个函数，它接受state作为参数，返回一个对象。这个对象有一个todos属性，代表 UI 组件的同名参数，后面的getVisibleTodos也是一个函数，可以从state算出 todos 的值。

下面就是getVisibleTodos的一个例子，用来算出todos。
```
const getVisibleTodos = (todos, filter) => {
  switch (filter) {
    case 'SHOW_ALL':
      return todos
    case 'SHOW_COMPLETED':
      return todos.filter(t => t.completed)
    case 'SHOW_ACTIVE':
      return todos.filter(t => !t.completed)
    default:
      throw new Error('Unknown filter: ' + filter)
  }
}
```
mapStateToProps会订阅 Store，每当state更新的时候，就会自动执行，重新计算 UI 组件的参数，从而触发 UI 组件的重新渲染。

mapStateToProps的第一个参数总是state对象，还可以使用第二个参数，代表容器组件的props对象。
```
// 容器组件的代码
//    <FilterLink filter="SHOW_ALL">
//      All
//    </FilterLink>

const mapStateToProps = (state, ownProps) => {
  return {
    active: ownProps.filter === state.visibilityFilter
  }
}
```
使用ownProps作为参数后，如果容器组件的参数发生变化，也会引发 UI 组件重新渲染。

connect方法可以省略mapStateToProps参数，那样的话，UI 组件就不会订阅Store，就是说 Store 的更新不会引起 UI 组件的更新。

**mapDispatchToProps**
mapDispatchToProps是connect函数的第二个参数，用来建立 UI 组件的参数到store.dispatch方法的映射。也就是说，它定义了哪些用户的操作应该当作 Action，传给 Store。它可以是一个函数，也可以是一个对象。

如果mapDispatchToProps是一个函数，会得到dispatch和ownProps（容器组件的props对象）两个参数。
```
const mapDispatchToProps = (
  dispatch,
  ownProps
) => {
  return {
    onClick: () => {
      dispatch({
        type: 'SET_VISIBILITY_FILTER',
        filter: ownProps.filter
      });
    }
  };
}
```
从上面代码可以看到，mapDispatchToProps作为函数，应该返回一个对象，该对象的每个键值对都是一个映射，定义了 UI 组件的参数怎样发出 Action。

如果mapDispatchToProps是一个对象，它的每个键名也是对应 UI 组件的同名参数，键值应该是一个函数，会被当作 Action creator ，返回的 Action 会由 Redux 自动发出。举例来说，上面的mapDispatchToProps写成对象就是下面这样。
```
const mapDispatchToProps = {
  onClick: (filter) => {
    type: 'SET_VISIBILITY_FILTER',
    filter: filter
  };
}
```

**<Provider> 组件**
connect方法生成容器组件以后，需要让容器组件拿到state对象，才能生成 UI 组件的参数。

一种解决方法是将state对象作为参数，传入容器组件。但是，这样做比较麻烦，尤其是容器组件可能在很深的层级，一级级将state传下去就很麻烦。

React-Redux 提供Provider组件，可以让容器组件拿到state。
```
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import todoApp from './reducers'
import App from './components/App'

let store = createStore(todoApp);

render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```
上面代码中，`Provider`在根组件外面包了一层，这样一来，`App`的所有子组件就默认都可以拿到`state`了。

它的原理是`React`组件的[`context`](https://facebook.github.io/react/docs/context.html)属性，请看源码。
```
class Provider extends Component {
  getChildContext() {
    return {
      store: this.props.store
    };
  }
  render() {
    return this.props.children;
  }
}

Provider.childContextTypes = {
  store: React.PropTypes.object
}
```
上面代码中，store放在了上下文对象context上面。然后，子组件就可以从context拿到store，代码大致如下。
```
class VisibleTodoList extends Component {
  componentDidMount() {
    const { store } = this.context;
    this.unsubscribe = store.subscribe(() =>
      this.forceUpdate()
    );
  }

  render() {
    const props = this.props;
    const { store } = this.context;
    const state = store.getState();
    // ...
  }
}

VisibleTodoList.contextTypes = {
  store: React.PropTypes.object
}
```
React-Redux自动生成的容器组件的代码，就类似上面这样，从而拿到store。

**总结一下整个流程：**
1、当view需要发起行为时，需要在当前位置触发action，即store.dispatch(addNote())
2、当Store 收到 Action 以后，必须给出一个新的 State，这样 View 才会发生变化。而这种 State 的计算过程就叫做 Reducer。而Reducer方法是由store.dispatch方法触发来自动执行的。为此，Store 需要知道 Reducer 函数，做法就是在生成 Store 的时候，将 Reducer 传入createStore方法。
`import { createStore } from 'redux'; const store = createStore(reducer);`
Reducer 是一个函数,且为纯函数，它接受 Action 和当前 State 作为参数，Reducer 函数里面不能改变 State，必须返回一个全新的state对象。
3、state更新，触发view发生改变，而在此之前必须把数据store和操作事件action绑定到需要使用的组件上，就需要用到connect函数（connect方法可参考这篇文章：[https://yq.aliyun.com/articles/59428](https://link.jianshu.com?t=https%3A%2F%2Fyq.aliyun.com%2Farticles%2F59428)）：
意思是先接受两个参数（数据绑定mapStateToProps和事件绑定mapDispatchToProps），再接受一个参数（将要绑定的组件本身）:
`export default connect(mapStateToProps,mapDispatchToProps)(App)`
其中：
**mapStateToProps** 就是将state作为props绑定到组件上
**mapDispatchToProps**是可选的，将 action 作为 props 绑定到组件上，如果不传这个参数redux会把dispatch作为属性注入给组件，可以手动当做store.dispatch使用
`//mapDispatchToProps 为actions里面的函数绑定dispatch，可直接调用this.props.actions.xxx()，即等同于没绑定情况下this.props.dispatch(xxx())，不需要手动dispatch`
`const mapDispatchToProps = dispatch => ({`
`actions: bindActionCreators(actions, dispatch)`
`});`
`//mapStateToProps 可返回当前数组需要的几个state属性值`
`const mapStateToProps = state => {`
`return{`
`notes : state.notes`
`}`
`};`
以上，便可以在组件内触发action，更新state，来更新view的变化

**实例： 计数器**
我们来看一个实例。下面是一个计数器组件，它是一个纯的 UI 组件。
```
class Counter extends Component {
  render() {
    const { value, onIncreaseClick } = this.props
    return (
      <div>
        <span>{value}</span>
        <button onClick={onIncreaseClick}>Increase</button>
      </div>
    )
  }
}
```
上面代码中，这个 UI 组件有两个参数：value和onIncreaseClick。前者需要从state计算得到，后者需要向外发出 Action。
接着，定义value到state的映射，以及onIncreaseClick到dispatch的映射。
```
function mapStateToProps(state) {
  return {
    value: state.count
  }
}

function mapDispatchToProps(dispatch) {
  return {
    onIncreaseClick: () => dispatch(increaseAction)
  }
}

// Action Creator
const increaseAction = { type: 'increase' }
```
然后，使用connect方法生成容器组件。
```
const App = connect(
  mapStateToProps,
  mapDispatchToProps
)(Counter)
```
然后，定义这个组件的 Reducer。
```
// Reducer
function counter(state = { count: 0 }, action) {
  const count = state.count
  switch (action.type) {
    case 'increase':
      return { count: count + 1 }
    default:
      return state
  }
}
```
最后，生成store对象，并使用Provider在根组件外面包一层。
```react
import { loadState, saveState } from './localStorage';

const persistedState = loadState();
const store = createStore(
  todoApp,
  persistedState
);

store.subscribe(throttle(() => {
  saveState({
    todos: store.getState().todos,
  })
}, 1000))

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```
完整代码：
```react
import React, { Component } from 'react'
import PropTypes from 'prop-types'
import ReactDOM from 'react-dom'
import { createStore } from 'redux'
import { Provider, connect } from 'react-redux'

// React component
class Counter extends Component {
  render() {
    const { value, onIncreaseClick } = this.props
    return (
      <div>
        <span>{value}</span>
        <button onClick={onIncreaseClick}>Increase</button>
      </div>
    )
  }
}

Counter.propTypes = {
  value: PropTypes.number.isRequired,
  onIncreaseClick: PropTypes.func.isRequired
}

// Action
const increaseAction = { type: 'increase' }

// Reducer
function counter(state = { count: 0 }, action) {
  const count = state.count
  switch (action.type) {
    case 'increase':
      return { count: count + 1 }
    default:
      return state
  }
}

// Store
const store = createStore(counter)

// Map Redux state to component props
function mapStateToProps(state) {
  return {
    value: state.count
  }
}

// Map Redux actions to component props
function mapDispatchToProps(dispatch) {
  return {
    onIncreaseClick: () => dispatch(increaseAction)
  }
}

// Connected Component
const App = connect(
  mapStateToProps,
  mapDispatchToProps
)(Counter)

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```