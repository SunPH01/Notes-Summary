# 安装
```npm install vue-router```

```
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)
```
[Vue Router]([https://router.vuejs.org/zh/guide/](https://router.vuejs.org/zh/guide/)
)是 [Vue.js](http://cn.vuejs.org/) 官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌。包含的功能有：

*   嵌套的路由/视图表
*   模块化的、基于组件的路由配置
*   路由参数、查询、通配符
*   基于 Vue.js 过渡系统的视图过渡效果
*   细粒度的导航控制
*   带有自动激活的 CSS class 的链接
*   HTML5 历史模式或 hash 模式，在 IE9 中自动降级
*   自定义的滚动条行为

# 动态路由匹配
## 路由参数

```
const router = new VueRouter({
  routes: [
    // 动态路径参数 以冒号开头
    { path: '/user/:id', component: User }
  ]
})
```
一个“路径参数”使用冒号 : 标记。当匹配到一个路由时，参数值会被设置到 ```this.$route.params```，可以在每个组件内使用。js内获取 ```this.$route.params.参数名```,在模板中使用:```<div>User {{ $route.params.id }}</div>```。
可以在一个路由中设置多段“路径参数”，对应的值都会设置到 $route.params 中。例如：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-3cb7d87605101fdb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**提醒一下：**当使用路由参数时，例如从 /user/foo 导航到 /user/bar，原来的组件实例会被**复用**。因为两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效。不过，这也意味着**组件的生命周期钩子不会再被调用**。
## 路由监测
复用组件时，想对路由参数的变化作出响应的话，你可以简单地 watch (监测变化) $route 对象：

```
watch: {
    '$route' (to, from) {
      // 对路由变化作出响应...
    }
  }
```
或者使用 2.2 中引入的 beforeRouteUpdate 导航守卫：
```
const User = {
  template: '...',
  beforeRouteUpdate (to, from, next) {
    // react to route changes...
    // don't forget to call next()
  }
}
```
**路由匹配优先级**：匹配的优先级就按照路由的定义顺序：谁先定义的，谁的优先级就最高。

# 嵌套路由
嵌套路由的写法

```
const router = new VueRouter({
  routes: [
    { 
      path: '/user', 
      component: User,
      children: [
        {
          // 当 /user/:id/profile 匹配成功，
          // UserProfile 会被渲染在 User 的 <router-view> 中
          path: 'profile',
          component: UserProfile
        },
        {
          // 当 /user/:id/posts 匹配成功
          // UserPosts 会被渲染在 User 的 <router-view> 中
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
```
**要注意，以 / 开头的嵌套路径会被当作根路径。 这让你充分的使用嵌套组件而无须设置嵌套的路径。**
基于上面的配置，当你访问 '/user' 时，User 的出口是不会渲染任何东西，这是因为没有匹配到合适的子路由。如果你想要渲染点什么，可以提供一个 空的 子路由：
```
const router = new VueRouter({
  routes: [
    { 
      path: '/user', 
      component: User,
      children: [
        {
          path: 'profile',
          component: UserProfile
        },
        {
          path: 'posts',
          component: UserPosts
        },
       {
          path: '',// 当 /user时 匹配成功
          component: UserProfile
        }
      ]
    }
  ]
})
```
# 编程式导航
路由导航的两种方法
![image.png](https://upload-images.jianshu.io/upload_images/18030682-3f6303cf93406bd0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## router.push(location, onComplete?, onAbort?)
例如：

```
// 字符串
this.$router.push('home')
// 对象
this.$router.push({ path: 'home' })
// 命名的路由
this.$router.push({ name: 'user', params: { userId: '123' }})
// 带查询参数，变成 /register?plan=private
this.$router.push({ path: 'register', query: { plan: 'private' }})
```
如果提供了 path，params 会被忽略，上述例子中的 query 并不属于这种情况。取而代之的是下面例子的做法，你需要提供路由的 name 或手写完整的带有参数的 path：
```
const userId = '123'
this.$router.push({ name: 'user', params: { userId }}) // -> /user/123
this.$router.push({ path: `/user/${userId}` }) // -> /user/123
// 这里的 params 不生效
this.$router.push({ path: '/user', params: { userId }}) // -> /user
```
在 2.2.0+，可选的在 `router.push` 或 `router.replace` 中提供 `onComplete` 和 `onAbort` 回调作为第二个和第三个参数。这些回调将会在导航成功完成 (在所有的异步钩子被解析之后) 或终止 (导航到相同的路由、或在当前导航完成之前导航到另一个不同的路由) 的时候进行相应的调用。

**注意**： 如果目的地和当前路由相同，只有参数发生了改变 (比如从一个用户资料到另一个 `/users/1` -> `/users/2`)，你需要使用 [`beforeRouteUpdate`](https://router.vuejs.org/zh/guide/essentials/dynamic-matching.html#%E5%93%8D%E5%BA%94%E8%B7%AF%E7%94%B1%E5%8F%82%E6%95%B0%E7%9A%84%E5%8F%98%E5%8C%96) 来响应这个变化 (比如抓取用户信息)。

## router.replace(location, onComplete?, onAbort?)
跟 router.push 很像，唯一的不同就是，它不会向 history 添加新记录，而是跟它的方法名一样 —— 替换掉当前的 history 记录。

## router.go(n)
这个方法的参数是一个整数，意思是在 history 记录中向前或者后退多少步，类似 window.history.go(n)。
例如：

```
// 在浏览器记录中前进一步，等同于 history.forward()
this.$router.go(1)

// 后退一步记录，等同于 history.back()
this.$router.go(-1)

// 前进 3 步记录
this.$router.go(3)

// 如果 history 记录不够用，那就默默地失败呗
this.$router.go(-100)
this.$router.go(100)
```
# 命名路由
通过一个名称来标识一个路由显得更方便一些，特别是在链接一个路由，或者是执行一些跳转的时候。你可以在创建 Router 实例的时候，在 routes 配置中给某个路由设置名称。

```
const router = new VueRouter({
  routes: [
    {
      path: '/user/:userId',
      name: 'user',
      component: User
    }
  ]
})
```
要链接到一个命名路由，可以给 router-link 的 to 属性传一个对象：
```
//模板
<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>
//js
this.$router.push({ name: 'user', params: { userId: 123 }})
```
# 命名视图
有时候想同时 (同级) 展示多个视图，而不是嵌套展示，例如创建一个布局，有 sidebar (侧导航) 和 main (主内容) 两个视图，这个时候命名视图就派上用场了。你可以在界面中拥有多个单独命名的视图，而不是只有一个单独的出口。如果 router-view 没有设置名字，那么默认为 default。

```
<router-view class="view one"></router-view>
<router-view class="view two" name="a"></router-view>
<router-view class="view three" name="b"></router-view>
```
一个视图使用一个组件渲染，因此对于同个路由，多个视图就需要多个组件。确保正确使用 components 配置 (带上 s)：
```
const router = new VueRouter({
  routes: [
    {
      path: '/',
      components: {
        default: Foo,//组件
        a: Bar,//组件
        b: Baz//组件
      }
    },
  {
      path: '/user',
      components: {
        default: bar,
        a: list,
        b: market
      }
    }
  ]
})
```
# 嵌套命名视图
我们也有可能使用命名视图创建嵌套视图的复杂布局。这时你也需要命名用到的嵌套 router-view 组件。我们以一个设置面板为例：

```
/settings/emails                                       /settings/profile
+-----------------------------------+                  +------------------------------+
| UserSettings                      |                  | UserSettings                 |
| +-----+-------------------------+ |                  | +-----+--------------------+ |
| | Nav | UserEmailsSubscriptions | |  +------------>  | | Nav | UserProfile        | |
| |     +-------------------------+ |                  | |     +--------------------+ |
| |     |                         | |                  | |     | UserProfilePreview | |
| +-----+-------------------------+ |                  | +-----+--------------------+ |
+-----------------------------------+                  +------------------------------+
```
* Nav 只是一个常规组件。
* UserSettings 是一个视图组件。
* UserEmailsSubscriptions、UserProfile、
* UserProfilePreview 是嵌套的视图组件。
实现方法：
```
<!-- UserSettings.vue -->
<div>
  <h1>User Settings</h1>
  <NavBar/>
  <router-view/>
  <router-view name="helper"/>
</div>
```
```
{
  path: '/settings',
  // 你也可以在顶级路由就配置命名视图
  component: UserSettings,
  children: [{
    path: 'emails',
    component: UserEmailsSubscriptions
  }, {
    path: 'profile',
    components: {
      default: UserProfile,
      helper: UserProfilePreview
    }
  }]
}
```
# 重定向和别名
## 重定向
“重定向”的意思是，当用户访问 /a时，URL 将会被替换成 /b，然后匹配路由为 /b，如下：

```
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: '/b' }
  ]
})
```
重定向的目标也可以是一个命名的路由：
```
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: { name: 'foo' }}
  ]
})
```
甚至是一个方法，动态返回重定向目标：
```
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: to => {
      // 方法接收 目标路由 作为参数
      // return 重定向的 字符串路径/路径对象
    }}
  ]
})
```
**注意[导航守卫](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)并没有应用在跳转路由上，而仅仅应用在其目标上。在最后这个例子中，为 `/a` 路由添加一个 `beforeEach` 或 `beforeLeave` 守卫并不会有任何效果。**
## 别名
别名是什么意思呢？例如 `/a` 的别名是 `/b`，意味着，当用户访问 `/b` 时，URL 会保持为` /b`，但是路由匹配则为 `/a`，就像用户访问 `/a` 一样。

```
const router = new VueRouter({
  routes: [
    { path: '/a', component: A, alias: '/b' }
  ]
})
```
# 路由组件传参
在组件中使用 $route 会使之与其对应路由形成高度耦合，从而使组件只能在某些特定的 URL 上使用，限制了其灵活性。
使用 props 将组件和路由解耦：
```
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User }
  ]
})
```
**通过 props 解耦**
```
const User = {
  props: ['id'],
  template: '<div>User {{ id }}</div>'
}
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User, props: true },

    // 对于包含命名视图的路由，你必须分别为每个命名视图添加 `props` 选项：
    {
      path: '/user/:id',
      components: { default: User, sidebar: Sidebar },
      props: { default: true, sidebar: false }
    }
  ]
})
```