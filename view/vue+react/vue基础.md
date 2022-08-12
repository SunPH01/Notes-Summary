#### 一、 计算属性与方法比较
**计算属性：**
```
<p>Computed reversed message: "{{ reversedMessage }}"</p>
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
```
**方法：**
```
<p>Reversed message: "{{ reversedMessage() }}"</p>
methods: {
  reversedMessage: function () {
    return this.message.split('').reverse().join('')
  }
}
```
可以将同一函数定义为一个方法而不是一个计算属性。两种方式的最终结果确实是完全相同的。然而，不同的是`计算属性是基于它们的响应式依赖进行缓存的。只在相关响应式依赖发生改变时它们才会重新求值。`这就意味着只要 message 还没有发生改变，多次访问 reversedMessage 计算属性会立即返回之前的计算结果，而不必再次执行函数。
这也同样意味着下面的计算属性将不再更新，因为 Date.now() 不是响应式依赖：
```
computed: {
  now: function () {
    return Date.now()
  }
}
```
**相比之下，每当触发重新渲染时，调用方法将总会再次执行函数。**

#### 二、条件渲染
 [用 `key` 管理可复用的元素](https://cn.vuejs.org/v2/guide/conditional.html#%E7%94%A8-key-%E7%AE%A1%E7%90%86%E5%8F%AF%E5%A4%8D%E7%94%A8%E7%9A%84%E5%85%83%E7%B4%A0)
Vue 会尽可能高效地渲染元素，`通常会复用已有元素而不是从头开始渲染`。这么做除了使 Vue 变得非常快之外，还有其它一些好处。例如，如果你允许用户在不同的登录方式之间切换：

```
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address">
</template>
```
那么在上面的代码中切换 loginType 将不会清除用户已经输入的内容。因为两个模板使用了相同的元素，<input> 不会被替换掉——仅仅是替换了它的 placeholder。
这样也不总是符合实际需求，如果想要每次切换时，输入框都将被重新渲染。只需添加一个具有唯一值的 key 属性即可：

```
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```
注意，<label> 元素仍然会被高效地复用，因为它们没有添加 key 属性。

>注意，v-show 不支持 <template> 元素，也不支持 v-else。

#### [`v-if` vs `v-show`](https://cn.vuejs.org/v2/guide/conditional.html#v-if-vs-v-show "v-if vs v-show")
* **v-if 是“真正”的条件渲染**，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。

* **v-if 也是惰性的**：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。

* 相比之下，v-show 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。

**一般来说，v-if 有更高的切换开销，而 v-show 有更高的初始渲染开销。**因此，如果需要非常频繁地切换，则使用 v-show 较好；如果在运行时条件很少改变，则使用 v-if 较好。

#### 三、列表渲染
**1. 数组**
由于 JavaScript 的限制，Vue 不能检测以下数组的变动：

1. 当你**利用索引直接设置一个数组项时**，例如：vm.items[indexOfItem] = newValue
2. 当你**修改数组的长度时**，例如：vm.items.length = newLength

解决第一类问题：
```
vm.$set(vm.items, indexOfItem, newValue)
// 或
vm.items.splice(indexOfItem, 1, newValue)
```
解决第二类问题，可以使用 splice：
```
vm.items.splice(newLength)
```
**2. 对象**
**对象变更检测注意事项：**
还是由于 JavaScript 的限制，Vue 不能检测对象属性的添加或删除：
```
var vm = new Vue({
  data: {
    a: 1
  }
})
// `vm.a` 现在是响应式的

vm.b = 2
// `vm.b` 不是响应式的
```
解决方法：
`vm.$set(vm.userProfile, 'age', 27)`
如果想添加多个新的响应式属性，不要像这样：
```
// 不是响应式
Object.assign(vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```
应该这样做：
```
vm.userProfile = Object.assign({}, vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```
#### 四、事件处理
**1. \$event**
在一些传了参数的事件里，有时也需要在内联语句处理器中访问原始的 DOM 事件。可以用特殊变量 `$event` 把它传入方法：

```
<button v-on:click="warn(true, $event)">
  Submit
</button>
```
**2. 事件修饰符**
在事件处理程序中调用 event.preventDefault() 或 event.stopPropagation() 是非常常见的需求。Vue.js 为 v-on 提供了事件修饰符。之前提过，修饰符是由点开头的指令后缀来表示的。
* .stop：阻止单击事件继续传播
* .prevent：阻止默认事件
* .capture：添加事件监听器时使用事件捕获模式，即内部元素触发的事件先在此处理，然后才交由内部元素进行处理（捕获冒泡，即有冒泡发生时，有该修饰符的dom元素会先执行，如果有多个，从外到内依次执行，然后再按自然顺序执行触发的事件。）
* .self：只当在 event.target 是当前元素自身时触发处理函数
* .once：只执行一次
* .passive：滚动事件的默认行为 将会立即触发，而不会等待事件完成，这其中包含 `event.preventDefault()` 的情况

```
<!-- 只有修饰符 -->
<a v-on:click.stop></a>
```
>使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。因此，用 v-on:click.prevent.self 会阻止所有的点击，而 v-on:click.self.prevent 只会阻止对元素自身的点击。

>不要把 .passive 和 .prevent 一起使用，因为 .prevent 将会被忽略，同时浏览器可能会向你展示一个警告。请记住，.passive 会告诉浏览器你不想阻止事件的默认行为。

#### 五、表单输入
**1. 修饰符**
`.lazy`

```
<!-- 在“change”时而非“input”时更新 -->
<input v-model.lazy="msg">
```
`.trim`
```
<!-- 自动过滤输入的首尾空白字符 -->
<input v-model.trim="msg">
```
#### 六、组件
**1. `.sync` 修饰符**
在有些情况下，可能需要对一个 prop 进行“双向绑定”。不幸的是，真正的双向绑定会带来维护上的问题，因为子组件可以修改父组件，且在父组件和子组件都没有明显的改动来源。
这也是为什么推荐以 update:myPropName 的模式触发事件取而代之。

```
<text-document :title.sync="doc.title"></text-document>
this.$emit('update:title', newTitle)
```
**2. 具名插槽**
```
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
// v-slot: 可以缩写为 #
<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```
**注意 v-slot 只能添加在 <template> 上**

**3. 动态组件**
```
<!-- 组件会在 `currentTabComponent` 改变时改变 -->
<component v-bind:is="currentTabComponent"></component>
```

**keep-alive**
和keep-alive相关的生命周期有：activated 和 deactivated。
**组件首次激活**：beforeCreate--created--beforeMount--mounted--activated
再次激活：activated
失活： deactivated

#### 七、处理边界情况

>这里记录的都是和处理边界情况有关的功能，即一些需要对 Vue 的规则做一些小调整的特殊情况。不过注意这些功能都是有劣势或危险的场景的。在每个案例中注明，所以当你使用每个功能的时候请稍加留意。

**1. 访问根实例**
在每个 new Vue 实例的子组件中，其根实例可以通过 $root 属性进行访问。例如，在这个根实例中：
```
// Vue 根实例
new Vue({
  data: {
    foo: 1
  },
  computed: {
    bar: function () { /* ... */ }
  },
  methods: {
    baz: function () { /* ... */ }
  }
})
```
所有的子组件都可以将这个实例作为一个全局 store 来访问或使用。
```
// 获取根组件的数据
this.$root.foo

// 写入根组件的数据
this.$root.foo = 2

// 访问根组件的计算属性
this.$root.bar

// 调用根组件的方法
this.$root.baz()
```
**2. 访问父级组件及实例**
`this.$parent.xx`

**3. 访问子组件**
```
<base-input ref="usernameInput"></base-input>
```
`this.$refs.usernameInput.xx`

**4. 依赖注入**
如果父级组件的子组件都要访问该组件的其中一个函数，那么就可以用到了两个新的实例选项：provide 和 inject。
`provide` 选项允许指定想要提供给后代组件的数据/方法。
例如在父组件：
```
provide: function () {
  return {
    getMap: this.getMap
  }
}
```
然后在任何后代组件里，都可以使用 inject 选项来接收指定的想要添加在这个实例上的属性：
```
inject: ['getMap']
```
>然而，依赖注入还是有负面影响的。它将你应用程序中的组件与它们当前的组织方式耦合起来，使重构变得更加困难。同时所提供的属性是非响应式的。这是出于设计的考虑，因为使用它们来创建一个中心化规模化的数据跟[使用 `$root`](https://cn.vuejs.org/v2/guide/components-edge-cases.html#%E8%AE%BF%E9%97%AE%E6%A0%B9%E5%AE%9E%E4%BE%8B)做这件事都是不够好的。如果你想要共享的这个属性是你的应用特有的，而不是通用化的，或者如果你想在祖先组件中更新所提供的数据，那么这意味着你可能需要换用一个像 [Vuex](https://github.com/vuejs/vuex) 这样真正的状态管理方案了。

**5. 通过 v-once 创建低开销的静态组件**
渲染普通的 HTML 元素在 Vue 中是非常快速的，但有的时候你可能有一个组件，这个组件包含了大量静态内容。在这种情况下，你可以在根元素上添加 v-once attribute 以确保这些内容只计算一次然后缓存起来，就像这样：

```
Vue.component('terms-of-service', {
  template: `
    <div v-once>
      <h1>Terms of Service</h1>
      ... a lot of static content ...
    </div>
  `
})
```
>不要过度使用这个模式。当你需要渲染大量静态内容时，极少数的情况下它会给你带来便利，除非你非常留意渲染变慢了，不然它完全是没有必要的——再加上它在后期会带来很多困惑。例如，设想另一个开发者并不熟悉 v-once 或漏看了它在模板中，他们可能会花很多个小时去找出模板为什么无法正确更新。

#### 八、过渡 & 动画
Vue 在插入、更新或者移除 DOM 时，提供多种不同方式的应用过渡效果。包括以下工具：
* 在 CSS 过渡和动画中自动应用 class
* 可以配合使用第三方 CSS 动画库，如 Animate.css
* 在过渡钩子函数中使用 JavaScript 直接操作 DOM
* 可以配合使用第三方 JavaScript 动画库，如 Velocity.js

**1. 单元素/组件的过渡**
Vue 提供了 `transition` 的封装组件，在下列情形中，可以给任何元素和组件添加进入/离开过渡
```
  <transition name="fade">
    <p v-if="show">hello</p>
  </transition>

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
```
当插入或删除包含在 transition 组件中的元素时，Vue 将会做以下处理：
1.  自动嗅探目标元素是否应用了 CSS 过渡或动画，如果是，在恰当的时机添加/删除 CSS 类名。
2.  如果过渡组件提供了 [JavaScript 钩子函数](https://cn.vuejs.org/v2/guide/transitions.html#JavaScript-%E9%92%A9%E5%AD%90)，这些钩子函数将在恰当的时机被调用。
3.  如果没有找到 JavaScript 钩子并且也没有检测到 CSS 过渡/动画，DOM 操作 (插入/删除) 在下一帧中立即执行。(注意：此指浏览器逐帧动画机制，和 Vue 的 `nextTick` 概念不同)

**过渡的类名**
在进入/离开的过渡中，会有 6 个 class 切换。
`v-enter`：定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除。

`v-enter-active`：定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数。

`v-enter-to`：2.1.8 版及以上定义进入过渡的结束状态。在元素被插入之后下一帧生效 (与此同时 v-enter 被移除)，在过渡/动画完成之后移除。

`v-leave`：定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。

`v-leave-active`：定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在过渡/动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。

`v-leave-to`：2.1.8 版及以上定义离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 v-leave 被删除)，在过渡/动画完成之后移除。

对于这些在过渡中切换的类名来说，如果你使用一个没有名字的 <transition>，则 v- 是这些类名的默认前缀。如果你使用了 <transition name="my-transition">，那么 v-enter 会替换为 my-transition-enter。

**自定义过渡的类名**
可以通过以下 attribute 来自定义过渡类名，他们的优先级高于普通的类名：
* enter-class
* enter-active-class
* enter-to-class (2.1.8+)
* leave-class
* leave-active-class
* leave-to-class (2.1.8+)

```
<transition
    name="custom-classes-transition"
    enter-active-class="animated tada"
    leave-active-class="animated bounceOutRight"
  >
    <p v-if="show">hello</p>
  </transition>
```
**显性的过渡持续时间(毫秒计)**
```
<transition :duration="1000">...</transition>
<transition :duration="{ enter: 500, leave: 800 }">...</transition>
```
**javaScript钩子**
```
<transition
  v-on:before-enter="beforeEnter"
  v-on:enter="enter"
  v-on:after-enter="afterEnter"
  v-on:enter-cancelled="enterCancelled"

  v-on:before-leave="beforeLeave"
  v-on:leave="leave"
  v-on:after-leave="afterLeave"
  v-on:leave-cancelled="leaveCancelled"
>
  <!-- ... -->
</transition>
```
```
  // 当与 CSS 结合使用时
  // 回调函数 done 是可选的
  enter: function (el, done) {
    // ...
    done()
  }
```
>当只用 JavaScript 过渡的时候，在 enter 和 leave 中必须使用 done 进行回调。否则，它们将被同步调用，过渡会立即完成。
>推荐对于仅使用 JavaScript 过渡的元素添加 v-bind:css="false"，Vue 会跳过 CSS 的检测。这也可以避免过渡过程中 CSS 的影响。

**2.多个元素的过渡** 
当有相同标签名的元素切换时，需要通过 `key` 设置唯一的值来标记以让 Vue 区分它们，否则 Vue 为了效率只会替换相同标签内部的内容。即使在技术上没有必要，给在 <transition> 组件中的多个元素设置 key 是一个更好的实践。
```
<transition>
  <button v-if="isEditing" key="save">
    Save
  </button>
  <button v-else key="edit">
    Edit
  </button>
</transition>
```
在一些场景中，**也可以通过给同一个元素的 key 设置不同的状态来代替 v-if 和 v-else**，上面的例子可以重写为：
```
<transition>
  <button v-bind:key="isEditing">
    {{ isEditing ? 'Save' : 'Edit' }}
  </button>
</transition>
```
**3. 过渡模式**
同时生效的进入和离开的过渡不能满足所有要求，所以 Vue 提供了**过渡模式**
* **in-out**：新元素先进行过渡，完成之后当前元素过渡离开。
* **out-in**：当前元素先进行过渡，完成之后新元素过渡进入。
```
<transition name="fade" mode="out-in">
  <!-- ... the buttons ... -->
</transition>
```

**4. 多个组件过渡**
多个组件的过渡简单很多，不需要使用 `key` attribute。只需要使用动态组件：
```
<transition name="component-fade" mode="out-in">
  <component v-bind:is="view"></component>
</transition>
```
**5. 列表过渡**
使用 <transition-group> 组件来实现列表过渡。这个组件的特点：
*   不同于 `<transition>`，它会以一个真实元素呈现：默认为一个 `<span>`。你也可以通过 `tag` attribute 更换为其他元素。
*   过渡模式不可用，因为我们不再相互切换特有的元素。
*   内部元素**总是需要**提供唯一的 `key` 属性值。
*   CSS 过渡的类将会应用在内部的元素中，而不是这个组/容器本身。

**1. 列表的进入/离开过渡**
```
  <transition-group name="list" tag="p">
    <span v-for="item in items" v-bind:key="item" class="list-item">
      {{ item }}
    </span>
  </transition-group>

.list-item {
  display: inline-block;
  margin-right: 10px;
}
.list-enter-active, .list-leave-active {
  transition: all 1s;
}
.list-enter, .list-leave-to
/* .list-leave-active for below version 2.1.8 */ {
  opacity: 0;
  transform: translateY(30px);
}
```
这个例子有个问题，当添加和移除元素的时候，周围的元素会瞬间移动到他们的新布局的位置，而不是平滑的过渡，下面看列表的排序过渡`v-move`会解决这个问题。
**2. 列表排序的过渡**
<transition-group> 组件还有一个特殊之处。不仅可以进入和离开动画，还可以改变定位。要使用这个新功能只需了解新增的 `v-move` attribute，它会在元素的改变定位的过程中应用。像之前的类名一样，可以通过 name 属性来自定义前缀，也可以通过 move-class 属性手动设置。
 ```
 <transition-group name="flip-list" tag="ul">
    <li v-for="item in items" v-bind:key="item">
      {{ item }}
    </li>
  </transition-group>

.flip-list-move {
  transition: transform 1s;
}
 ```
内部的实现，Vue 使用了一个叫 [FLIP](https://aerotwist.com/blog/flip-your-animations/) 简单的动画队列
使用 transforms 将元素从之前的位置平滑过渡新的位置。
上面的例子就可以改为：
```
  <transition-group name="list" tag="p" move-class="list">
    <span v-for="item in items" v-bind:key="item" class="list-item">
      {{ item }}
    </span>
  </transition-group>

.list{
  transition: all 1s;
}
.list-item {
  display: inline-block;
  margin-right: 10px;
}
.list-enter-active,
.list-leave-active {
  transition: all 1s;
}
.list-enter, .list-leave-to
/* .list-leave-active for below version 2.1.8 */ {
  opacity: 0;
  transform: translateY(30px);
}
.list-leave-active {
  position: absolute;
}
```
>**需要注意的是使用 FLIP 过渡的元素不能设置为 display: inline 。作为替代方案，可以设置为 display: inline-block 或者放置于 flex 中**

**3. 可复用的过渡**
过渡可以通过 Vue 的组件系统实现复用。要创建一个可复用过渡组件，你需要做的就是将 <transition> 或者 <transition-group> 作为根组件，然后将任何子组件放置在其中就可以了。

#### 九、可复用性 & 组合
**1. 混入**
混入 (mixin) 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。一个混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项。

**选项合并**
当组件和混入对象含有同名选项时，这些选项将以恰当的方式进行“合并”。
* 数据对象在内部会进行递归合并，并在发生冲突时以组件数据优先。
* 同名钩子函数将合并为一个数组，因此都将被调用。另外，混入对象的钩子将在组件自身钩子之前调用。
* 值为对象的选项，例如 methods、components 和 directives，将被合并为同一个对象。两个对象键名冲突时，取组件对象的键值对。

**2. 自定义指令**
全局注册：
```
// 注册一个全局自定义指令 `v-focus`
Vue.directive('focus', {
  // 当被绑定的元素插入到 DOM 中时……
  inserted: function (el) {
    // 聚焦元素
    el.focus()
  }
})
```
如果想注册局部指令，组件中也接受一个 directives 的选项：
```
directives: {
  focus: {
    // 指令的定义
    inserted: function (el) {
      el.focus()
    }
  }
}
```
使用
```
<input v-focus>
```

**钩子函数：**
一个指令定义对象可以提供如下几个钩子函数 (均为可选)：
* **bind**：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
* **inserted**：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。
* **update**：所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新。
* **componentUpdated**：指令所在组件的 VNode 及其子 VNode 全部更新后调用。
* **unbind**：只调用一次，指令与元素解绑时调用。

**钩子函数的参数：**
**el**： 指令所绑定的元素，可以用来直接操作 DOM。
**binding**：一个对象，包含以下属性：
  **name**：指令名，不包括 v- 前缀。
  **value**：指令的绑定值，例如：v-my-directive="1 + 1" 中，绑定值为2。
  **oldValue**：指令绑定的前一个值，仅在 update 和 componentUpdated 钩子中可用。无论值是否改变都可用。
  **expression**：字符串形式的指令表达式。例如 v-my-directive="1 + 1" 中，表达式为 "1 + 1"。
  **arg**：传给指令的参数，可选。例如 v-my-directive:foo 中，参数为 "foo"。
  **modifiers**：一个包含修饰符的对象。例如：v-my-directive.foo.bar 中，修饰符对象为 { foo: true, bar: true }。

**动态指令参数**
指令的参数可以是动态的。例如，在 v-mydirective:[argument]="value" 中，argument 参数可以根据组件实例数据进行更新。
例子：
```
<div id="dynamicexample">
  <h3>Scroll down inside this section ↓</h3>
  <p v-pin:[direction]="200">I am pinned onto the page at 200px to the left.</p>
</div>

Vue.directive('pin', {
  bind: function (el, binding, vnode) {
    el.style.position = 'fixed'
    var s = (binding.arg == 'left' ? 'left' : 'top')
    el.style[s] = binding.value + 'px'
  }
})
```
**函数简写**
在很多时候都是在 bind 和 update 时触发相同行为，而不关心其它的钩子。可以简写为：
```
Vue.directive('color-swatch', function (el, binding) {
  ...
})
```
**对象字面量**
如果指令需要多个值，可以传入一个 JavaScript 对象字面量。
```
<div v-demo="{ color: 'white', text: 'hello!' }"></div>

Vue.directive('demo', function (el, binding) {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text)  // => "hello!"
})
```
#### 十、深入响应式原理
Vue 最独特的特性之一，是其非侵入性的响应式系统。数据模型仅仅是普通的 JavaScript 对象。而当你修改它们时，视图会进行更新。
**1. 如何追踪变化**
**当一个普通的 JavaScript 对象传入 Vue 实例作为 `data` 选项时，Vue 将遍历此对象所有的属性，并使用 `Object.defineProperty` 把这些属性全部转为 `getter/setter`。**`Object.defineProperty` 是 ES5 中一个无法 shim 的特性，这也就是 Vue 不支持 IE8 以及更低版本浏览器的原因。
这些 getter/setter 对用户来说是不可见的，但是在内部它们让 Vue 能够追踪依赖，在属性被访问和修改时通知变更。
**每个组件实例都对应一个 watcher 实例，它会在组件渲染的过程中把“接触”过的数据属性记录为依赖。之后当依赖项的 setter 触发时，会通知 watcher，从而使它关联的组件重新渲染。**
![image.png](https://upload-images.jianshu.io/upload_images/18030682-6aeaa46e960de7da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**异步更新队列**
Vue 在更新 DOM 时是异步执行的。只要侦听到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据变更。如果同一个 watcher 被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作是非常重要的。然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际 (已去重的) 工作。