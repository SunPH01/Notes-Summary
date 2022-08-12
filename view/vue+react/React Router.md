# 一、基础
**默认路由（IndexRoute）**
在解释 默认路由(IndexRoute) 的用例之前，我们来设想一下，一个不使用默认路由的路由配置是什么样的：

```
<Router>
  <Route path="/" component={App}>
    <Route path="accounts" component={Accounts}/>
    <Route path="statements" component={Statements}/>
  </Route>
</Router>
```
当用户访问 / 时, App 组件被渲染，但组件内的子元素却没有， App 内部的 this.props.children 为 undefined 。 你可以简单地使用 `{this.props.children || <home>}` 来渲染一些默认的 UI 组件。
但现在，Home 无法参与到比如 onEnter hook 这些路由机制中来。 在 Home 的位置，渲染的是 Accounts 和 Statements。 由此，router 允许你使用 IndexRoute ，以使 Home 作为最高层级的路由出现.

```
<Router>
  <Route path="/" component={App}>
    <IndexRoute component={Home}/>
    <Route path="accounts" component={Accounts}/>
    <Route path="statements" component={Statements}/>
  </Route>
</Router>
```
现在 App 能够渲染 {this.props.children} 了， 我们也有了一个最高层级的路由，使 Home 可以参与进来。

**Index Links**
如果你在这个 app 中使用 `<Link to="/">Home</Link> `, 它会一直处于激活状态，因为所有的 URL 的开头都是 / 。 这确实是个问题，因为我们仅仅希望在 Home 被渲染后，激活并链接到它。

如果需要在 Home 路由被渲染后才激活的指向 / 的链接，请使用 `<IndexLink to="/">Home</IndexLink>`

# 二、高级用法
## **1. 动态路由**
Route 可以定义 [`getChildRoutes`](http://react-guide.github.io/react-router-cn/docs/guides/advanced/docs/API.md#getchildrouteslocation-callback)，[`getIndexRoute`](http://react-guide.github.io/react-router-cn/docs/guides/advanced/docs/API.md#getindexroutelocation-callback) 和 [`getComponents`](http://react-guide.github.io/react-router-cn/docs/guides/advanced/docs/API.md#getcomponentslocation-callback) 这几个函数。它们都是异步执行，并且只有在需要时才被调用。我们将这种方式称之为 “逐渐匹配”。 React Router 会逐渐的匹配 URL 并只加载该 URL 对应页面所需的路径配置和组件。
如果配合 [webpack](http://webpack.github.io/) 这类的代码分拆工具使用的话，一个原本繁琐的构架就会变得更简洁明了。

```
const CourseRoute = {
  path: 'course/:courseId',

  getChildRoutes(location, callback) {
    require.ensure([], function (require) {
      callback(null, [
        require('./routes/Announcements'),
        require('./routes/Assignments'),
        require('./routes/Grades'),
      ])
    })
  },

  getIndexRoute(location, callback) {
    require.ensure([], function (require) {
      callback(null, require('./components/Index'))
    })
  },

  getComponents(location, callback) {
    require.ensure([], function (require) {
      callback(null, require('./components/Course'))
    })
  }
}
```

## 2. 跳转前确认
React Router 提供一个 [`routerWillLeave` 生命周期钩子](http://react-guide.github.io/react-router-cn/docs/guides/advanced/docs/Glossary.md#routehook)，这使得 React 组件 可以拦截正在发生的跳转，或在离开 route 前提示用户。`routerWillLeave`返回值有以下两种：

* `return false` 取消此次跳转
*  `return` 返回提示信息，在离开 route 前提示用户进行确认。

可以在 route 组件 中引入 `Lifecycle` mixin 来安装这个钩子。
```
import { Lifecycle } from 'react-router'

const Home = React.createClass({

  // 假设 Home 是一个 route 组件，它可能会使用
  // Lifecycle mixin 去获得一个 routerWillLeave 方法。
  mixins: [ Lifecycle ],

  routerWillLeave(nextLocation) {
    if (!this.state.isSaved)
      return 'Your work is not saved! Are you sure you want to leave?'
  },

  // ...

})
```
如果你想在一个深层嵌套的组件中使用 `routerWillLeave`钩子，只需在route 组件中引入 `RouteContext`mixin，这样就会把 `route` 放到 context 中。
```
import { Lifecycle, RouteContext } from 'react-router'

const Home = React.createClass({

  // route 会被放到 Home 和它子组件及孙子组件的 context 中，
  // 这样在层级树中 Home 及其所有子组件都可以拿到 route。
  mixins: [ RouteContext ],

  render() {
    return <NestedForm />
  }

})

const NestedForm = React.createClass({

  // 后代组件使用 Lifecycle mixin 获得
  // 一个 routerWillLeave 的方法。
  mixins: [ Lifecycle ],

  routerWillLeave(nextLocation) {
    if (!this.state.isSaved)
      return 'Your work is not saved! Are you sure you want to leave?'
  },

  // ...

})
```

## **3. 组件生命周期**
路由切换时，组件生命周期的变化情况

## **4. 在组件外部使用导航**
虽然在组件内部可以使用 this.context.router 来实现导航，但许多应用想要在组件外部使用导航。使用Router组件上被赋予的history可以在组件外部实现导航。

```
// your main file that renders a Router
import { Router, browserHistory } from 'react-router'
import routes from './app/routes'
render(<Router history={browserHistory} routes={routes}/>, el)

// somewhere like a redux/flux action file:
import { browserHistory } from 'react-router'
browserHistory.push('/some/path')
```

# 三、API接口
## **1. 组件--Router**
props:

*  **children**(required)：一个或多个的 `Route` 或 `PlainRoute`。
* **history**： Router 监听的 history 对象，由 history 包提供。

## **2. 组件--Link**
允许用户浏览应用的主要方式。<Link> 以适当的 href 去渲染一个可访问的锚标签。<Link> 可以知道哪个 route 的链接是激活状态的，并可以自动为该链接添加 activeClassName 或 activeStyle。
props:

* **to**：跳转链接的路径，如 /users/123。
* **query**：已经转化成字符串的键值对的对象。
* **hash**：URL 的 hash 值，如 #a-hash。
注意：React Router 目前还不能管理滚动条的位置，并且不会自动滚动到 hash 对应的元素上。如果需要管理滚动条位置，可以使用 [scroll-behavior](https://github.com/rackt/scroll-behavior) 这个库。
* **state**：保存在 location 中的 state。
* **activeClassName**：当某个 route 是激活状态时，<Link> 可以接收传入的 className。失活状态下是默认的 class。
* **activeStyle**：当某个 route 是激活状态时，可以将样式添加到链接元素上。
* **onClick(e)**：自定义点击事件的处理方法。如处理 <a> 标签一样 - 调用 e.preventDefault() 来防止过度的点击，同时 e.stopPropagation() 可以阻止冒泡的事件。

示例：
```
<Link to={`/users/${user.id}`} activeClassName="active">{user.name}</Link>
// 变成它们其中一个依赖在 History 上，当这个 route 是
// 激活状态的
<a href="/users/123" class="active">Michael</a>
<a href="#/users/123">Michael</a>

// 修改 activeClassName
<Link to={`/users/${user.id}`} activeClassName="current">{user.name}</Link>

// 当链接激活时，修改它的样式
<Link to="/users" style={{color: 'white'}} activeStyle={{color: 'red'}}>Users</Link>
```

## **3. Route (组件的配置)**
Route 是用于声明路由映射到应用程序的组件层。
props:

* **path**：URL 中的路径。它会组合父 route 的路径，除非它是从 / 开始的， 将它变成一个绝对路径。**注意**：在动态路由中，绝对路径可能不适用于 route 配置中。
如果它是 undefined，路由会去匹配子 route。
* **component**：当匹配到 URL 时，单个的组件会被渲染。它可以 被父 route 组件的 this.props.children 渲染。
```
const routes = (
  <Route component={App}>
    <Route path="groups" component={Groups}/>
    <Route path="users" component={Users}/>
  </Route>
)

class App extends React.Component {
  render () {
    return (
      <div>
        {/* 这会是 <Users> 或 <Groups> */}
        {this.props.children}
      </div>
    )
  }
}
```
* **components**：
Route 可以定义一个或多个已命名的组件，当路径匹配到 URL 时， 它们作为 name:component 对的一个对象去渲染。它们可以被 父 route 组件的 this.props[name] 渲染。
```
// 想想路由外部的 context — 如果你可拔插
// `render` 的部分，你可能需要这么做：
// <App main={<Users />} sidebar={<UsersSidebar />} />

const routes = (
  <Route component={App}>
    <Route path="groups" components={{main: Groups, sidebar: GroupsSidebar}}/>
    <Route path="users" components={{main: Users, sidebar: UsersSidebar}}>
      <Route path="users/:userId" component={Profile}/>
    </Route>
  </Route>
)

class App extends React.Component {
  render () {
    const { main, sidebar } = this.props
    return (
      <div>
        <div className="Main">
          {main}
        </div>
        <div className="Sidebar">
          {sidebar}
        </div>
      </div>
    )
  }
}

class Users extends React.Component {
  render () {
    return (
      <div>
        {/* 当路径是 "/users/123" 是 `children` 会是 <Profile> */}
        {/* UsersSidebar 也可以获取作为 this.props.children 的 <Profile> ，
            所以这有点奇怪，但你可以决定哪一个可以
            继续这种嵌套 */}
        {this.props.children}
      </div>
    )
  }
}
```
* **getComponent(location, callback)**：
与 component 一样，但是是异步的，对于 code-splitting 很有用。
callback signature
`cb(err, component)`
```
<Route path="courses/:courseId" getComponent={(location, cb) => {
  // 做一些异步操作去查找组件
  cb(null, Course)
}}/>
```
* **getComponents(location, callback)**：
```
<Route path="courses/:courseId" getComponents={(location, cb) => {
  // 做一些异步操作去查找组件
  cb(null, {sidebar: CourseSidebar, content: Course})
}}/>
```
* **children**：Route 可以被嵌套，this.props.children 包含了从子 route 组件创建的元素。
* **onEnter(nextState, replaceState, callback?)**：当 route 即将进入时调用。它提供了下一个路由的 state，一个函数重定向到另一个路径。this 会触发钩子去创建 route 实例。
当 callback 作为函数的第三个参数传入时，这个钩子将是异步执行的，并且跳转会阻塞直到 callback 被调用。
* **onLeave()**：当 route 即将退出时调用。

## **4. PlainRoute(组件的配置)**
route 定义的一个普通的 JavaScript 对象。Router 把 JSX 的 <Route> 转化到这个对象中，如果你喜欢，你可以直接使用它们。 所有的 props 都和 <Route> 的 props 一样，除了那些列在这里的。
props：

* **childRoutes**：子 route 的一个数组。
* **getChildRoutes(location, callback)**：与 childRoutes 一样，但是是异步的，并且可以接收 location。对于 code-splitting 和动态路由匹配很有用（给定一些 state 或 session 数据会返回不同的子 route）。`cb(err, routesArray)`
```
let myRoute = {
  path: 'course/:courseId',
  childRoutes: [
    announcementsRoute,
    gradesRoute,
    assignmentsRoute
  ]
}

// 异步的子 route
let myRoute = {
  path: 'course/:courseId',
  getChildRoutes(location, cb) {
    // 做一些异步操作去查找子 route
    cb(null, [ announcementsRoute, gradesRoute, assignmentsRoute ])
  }
}

// 可以根据一些 state
// 跳转到依赖的子 route
<Link to="/picture/123" state={{ fromDashboard: true }}/>

let myRoute = {
  path: 'picture/:id',
  getChildRoutes(location, cb) {
    let { state } = location

    if (state && state.fromDashboard) {
      cb(null, [dashboardPictureRoute])
    } else {
      cb(null, [pictureRoute])
    }
  }
}
```
* **indexRoute**：与在使用 JSX route 配置时指定一个 <IndexRoute> 子集一样。
* **getIndexRoute(location, callback)**：与 indexRoute 一样，但是是异步的，并且可以接收 location。与 getChildRoutes 一样，对于 code-splitting 和动态路由匹配很有用。`cb(err, route)`
```
// 例如：
let myIndexRoute = {
  component: MyIndex
}

let myRoute = {
  path: 'courses',
  indexRoute: myIndexRoute
}

// 异步的 index route
let myRoute = {
  path: 'courses',
  getIndexRoute(location, cb) {
    // 做一些异步操作
    cb(null, myIndexRoute)
  }
}
```

## **5. Redirect(组件的配置)**
在应用中 <Redirect> 可以设置重定向到其他 route 而不改变旧的 URL。

props：
* **from**：你想由哪个路径进行重定向，包括动态段。
* **to**：你想重定向的路径。
* **query**：默认情况下，query 的参数只会经过，但如果你需要你可以指定它们。
```
// 从 `/profile/123` 改变到 `/about/123`
<Route component={App}>
  <Route path="about/:userId" component={UserProfile}/>
  {/* /profile/123 -> /about/123 */}
  <Redirect from="profile/:userId" to="about/:userId" />
</Route>
```
注意，在 route 层 `<Redirect>` 可以被放在任何地方，尽管[正常的优先](http://react-guide.github.io/react-router-cn/docs/docs/guides/basics/RouteMatching.md#precedence) 规则仍适用。如果你喜欢将下一个重定向到它各自的 route 上，`from` 的路径会匹配到一个与 `path` 一样的正常路径。
```
<Route path="course/:courseId">
  <Route path="dashboard" />
  {/* /course/123/home -> /course/123/dashboard */}
  <Redirect from="home" to="dashboard" />
</Route>
```

## **6. Route Components**
当 route 匹配到 URL 时会渲染一个 route 的组件。路由会在渲染时将以下属性注入组件中：

* **history**：Router 的 history [history](https://github.com/rackt/history/blob/master/docs)。
对于跳转很有用的 `this.props.history.pushState(state, path, query)`
* **location**：当前的 location。
* **params**： URL的动态字段。
* **route**：渲染组件的 route。
* **routeParams**：this.props.params 是直接在组件中指定 route 的一个子集。例如，如果 route 的路径是 users/:userId 而 URL 是 /users/123/portfolios/345，那么 this.props.routeParams 会是 {userId: '123'}，并且 this.props.params 会是 {userId: '123', portfolioId: 345}。
* **children**：匹配到子 route 的元素将被渲染。如果 route 有[已命名的组件](https://github.com/rackt/react-router/blob/master/docs/API.md#named-components)，那么此属性会是 undefined，并且可用的组件会被直接替换到 `this.props` 上。
```
render((
  <Router>
    <Route path="/" component={App}>
      <Route path="groups" component={Groups} />
      <Route path="users" component={Users} />
    </Route>
  </Router>
), node)

class App extends React.Component {
  render() {
    return (
      <div>
        {/* 这可能是 <Users> 或 <Groups> */}
        {this.props.children}
      </div>
    )
  }
}
```

## **7. routerWillLeave(nextLocation)**
在组件中添加一个钩子，当路由要从 route 组件的配置中跳转出来时被调用，并且有机会去取消这次跳转。主要用于表单的部分填写。
在常规的跳转中， `routerWillLeave` 会接收到一个单一的参数：我们正要跳转的 location。去取消此次跳转，返回 false。

## **8. History**

* **pushState(state, pathname, query)**：跳转至一个新的 URL。state - location 的 state。pathname - 没有 query 完整的 URL。query - 通过路由字符串化的一个对象。
* **replaceState(state, pathname, query)**：在不影响 history 长度的情况下（如一个重定向），用新的 URL 替换当前这个。
* **go(n)**：在 history 中使用 n 或 -n 进行前进或后退。
* **goBack()**：在 history 中后退。
* **goForward()**：在 history 中前进。
* **createPath(pathname, query)**：使用路由配置，将 query 字符串化加到路径名中。
* **createHref(pathname, query)**：使用路由配置，创建一个 URL。例如，它会在 pathname 的前面加上 #/ 给 hash history。
* **isActive(pathname, query, indexOnly)**：根据当前路径是否激活返回 true 或 false。通过 pathname 匹配到 route 分支下的每个 route 将会是 true（子 route 是激活的情况下，父 route 也是激活的），除非 indexOnly 已经指定了，在这种情况下，它只会匹配到具体的路径。