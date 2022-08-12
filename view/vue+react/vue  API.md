### 一、全局配置
**1. keyCodes** 
给 v-on 自定义键位别名。
用法：

```
Vue.config.keyCodes = {
  v: 86,
  f1: 112,
  // camelCase 不可用
  mediaPlayPause: 179,
  // 取而代之的是 kebab-case 且用双引号括起来
  "media-play-pause": 179,
  up: [38, 87]
}

<input type="text" @keyup.media-play-pause="method">
```

### 二、全局API
#### **1. Vue.extend**
使用基础 Vue 构造器，创建一个“子类”。参数是一个包含组件选项的对象。
data 选项是特例，需要注意 - 在 Vue.extend() 中它必须是函数
```
<div id="mount-point"></div>

// 创建构造器
var Profile = Vue.extend({
  template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>',
  data: function () {
    return {
      firstName: 'Walter',
      lastName: 'White',
      alias: 'Heisenberg'
    }
  }
})
// 创建 Profile 实例，并挂载到一个元素上。
new Profile().$mount('#mount-point')
```

#### **2. Vue.nextTick**
在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。
```
// 修改数据
vm.msg = 'Hello'
// DOM 还没有更新
Vue.nextTick(function () {
  // DOM 更新了
})

// 作为一个 Promise 使用 (2.1.0 起新增，详见接下来的提示)
Vue.nextTick()
  .then(function () {
    // DOM 更新了
  })
```

#### **3. Vue.set( target, propertyName/index, value )**
**参数：**
* {Object | Array} target
* {string | number} propertyName/index
* {any} value

**返回值：**设置的值。
**用法：**向响应式对象中添加一个属性，并确保这个新属性同样是响应式的，且触发视图更新。

#### **4. Vue.delete( target, propertyName/index )**
**参数：**
* {Object | Array} target
* {string | number} propertyName/index

**用法：**删除对象的属性。如果对象是响应式的，确保删除能触发更新视图。
>仅在 2.2.0+ 版本中支持 Array + index 用法。

#### **5. Vue.directive( target, propertyName/index )**
**参数：**
* {string} id
* {Function | Object} [definition]

**用法：**注册或获取全局指令。
```
// 注册
Vue.directive('my-directive', {
  bind: function () {
  // 只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
  },
  inserted: function () {
    // 被绑定元素插入父节点时调用(仅保证父节点存在，但不一定已被插入文档中)
  },
  update: function () {
  // 所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。指令的值可能发生了改变，也可能没有。
  },
  componentUpdated: function () {
  // 指令所在组件的 VNode 及其子 VNode 全部更新后调用。
  },
  unbind: function () {}
})

// 注册 (指令函数)
Vue.directive('my-directive', function () {
  // 这里将会被 `bind` 和 `update` 调用
})

// getter，返回已注册的指令
var myDirective = Vue.directive('my-directive')
```

#### **6. Vue.filter(id, [definition])**
**参数：**
* {string} id
* {Function} [definition]

**用法：**注册或获取全局过滤器。
```
// 注册
Vue.filter('my-filter', function (value) {
  // 返回处理后的值
})
```

#### **7. Vue.component(id, [definition])**
注册或获取全局组件。

#### **8. Vue.use(plugin)**
安装 Vue.js 插件。该方法需要在调用 new Vue() 之前被调用。

#### **9. Vue.mixin**
全局注册一个混入，影响注册之后所有创建的每个 Vue 实例。**不推荐在应用代码中使用全局混入。**

#### **10. Vue.compile**
将一个模板字符串编译成 render 函数。只在完整版时可用。
```
var res = Vue.compile('<div><span>{{ msg }}</span></div>')

new Vue({
  data: {
    msg: 'hello'
  },
  render: res.render,
  staticRenderFns: res.staticRenderFns
})
```

#### **11. Vue.observable**
让一个对象可响应。Vue 内部会用它来处理 data 函数返回的对象。
返回的对象可以直接用于渲染函数和计算属性内，并且会在发生改变时触发相应的更新。也可以作为最小化的跨组件状态存储器，用于简单的场景：
```
const state = Vue.observable({ count: 0 })

const Demo = {
  render(h) {
    return h('button', {
      on: { click: () => { state.count++ }}
    }, `count is: ${state.count}`)
  }
}
```

#### **12. Vue.version**
提供字符串形式的 Vue 安装版本号。这对社区的插件和组件来说非常有用，你可以根据不同的版本号采取不同的策略。
```
var version = Number(Vue.version.split('.')[0])

if (version === 2) {
  // Vue v2.x.x
} else if (version === 1) {
  // Vue v1.x.x
} else {
  // Unsupported versions of Vue
}
```

### 三、选项/数据
#### **1. data**
**限制：**组件的定义只接受 function。
Vue 实例的数据对象。Vue 将会递归将 data 的属性转换为 getter/setter，从而让 data 的属性能够响应数据变化。对象必须是纯粹的对象 (含有零个或多个的 key/value 对)

#### **2. Props**
props 可以是数组或对象，用于接收来自父组件的数据。props 可以是简单的数组，或者使用对象作为替代，对象允许配置高级选项，如类型检测、自定义验证和设置默认值。
基于对象的语法使用以下选项：
*   `type`：可以是下列原生构造函数中的一种：`String`、`Number`、`Boolean`、`Array`、`Object`、`Date`、`Function`、`Symbol`、任何自定义构造函数、或上述内容组成的数组。会检查一个 prop 是否是给定的类型，否则抛出警告。Prop 类型的[更多信息在此](https://cn.vuejs.org/v2/guide/components-props.html#Prop-%E7%B1%BB%E5%9E%8B)。
*   `default`：`any`
    为该 prop 指定一个默认值。如果该 prop 没有被传入，则换做用这个值。对象或数组的默认值必须从一个工厂函数返回。
*   `required`：`Boolean`
    定义该 prop 是否是必填项。在非生产环境中，如果这个值为 truthy 且该 prop 没有被传入的，则一个控制台警告将会被抛出。
*   `validator`：`Function`
    自定义验证函数会将该 prop 的值作为唯一的参数代入。在非生产环境下，如果该函数返回一个 falsy 的值 (也就是验证失败)，一个控制台警告将会被抛出。你可以在[这里](https://cn.vuejs.org/v2/guide/components-props.html#Prop-%E9%AA%8C%E8%AF%81)查阅更多 prop 验证的相关信息。

```
  props: {
    // 检测类型
    height: Number,
    // 检测类型 + 其他验证
    age: {
      type: Number,
      default: 0,
      required: true,
      validator: function (value) {
        return value >= 0
      }
    }
  }
```

#### **3. propsData**
**限制：**只用于 new 创建的实例中。
创建实例时传递 props。主要作用是方便测试。
```
var Comp = Vue.extend({
  props: ['msg'],
  template: '<div>{{ msg }}</div>'
})

var vm = new Comp({
  propsData: {
    msg: 'hello'
  }
})
```

#### **4. computed**
类型：`{ [key: string]: Function | { get: Function, set: Function } }`
计算属性将被混入到 Vue 实例中。所有 getter 和 setter 的 this 上下文自动地绑定为 Vue 实例。
注意如果你为一个计算属性使用了箭头函数，则 this 不会指向这个组件的实例，不过你仍然可以将其实例作为函数的第一个参数来访问。
```
computed: {
  aDouble: vm => vm.a * 2
}
```
**计算属性的结果会被缓存，除非依赖的响应式属性变化才会重新计算。**注意，如果某个依赖 (比如非响应式属性) 在该实例范畴之外，则计算属性是不会被更新的。
```
var vm = new Vue({
  data: { a: 1 },
  computed: {
    // 仅读取
    aDouble: function () {
      return this.a * 2
    },
    // 读取和设置
    aPlus: {
      get: function () {
        return this.a + 1
      },
      set: function (v) {
        this.a = v - 1
      }
    }
  }
})
vm.aPlus   // => 2
vm.aPlus = 3
vm.a       // => 2
vm.aDouble // => 4
```

#### **5. methods**
methods 将被混入到 Vue 实例中。可以直接通过 VM 实例访问这些方法，或者在指令表达式中使用。方法中的 this 自动绑定为 Vue 实例。
>注意，不应该使用箭头函数来定义 method 函数 (例如 plus: () => this.a++)。理由是箭头函数绑定了父级作用域的上下文，所以 this 将不会按照期望指向 Vue 实例，this.a 将是 undefined。

#### **6. watch**
类型：`{ [key: string]: string | Function | Object | Array }`
```
  watch: {
    a: function (val, oldVal) {
      console.log('new: %s, old: %s', val, oldVal)
    },
    // 方法名
    b: 'someMethod',
    // 该回调会在任何被侦听的对象的 property 改变时被调用，不论其被嵌套多深
    c: {
      handler: function (val, oldVal) { /* ... */ },
      deep: true
    },
    // 该回调将会在侦听开始之后被立即调用
    d: {
      handler: 'someMethod',
      immediate: true
    },
    // 你可以传入回调数组，它们会被逐一调用
    e: [
      'handle1',
      function handle2 (val, oldVal) { /* ... */ },
      {
        handler: function handle3 (val, oldVal) { /* ... */ },
        /* ... */
      }
    ],
    // watch vm.e.f's value: {g: 5}
    'e.f': function (val, oldVal) { /* ... */ }
  }
```
### 四、 选项/生命周期
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
```
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

### 五、 选项/组合
#### **1. parents**
指定已创建的实例之父实例，在两者之间建立父子关系。子实例可以用 `this.$parent` 访问父实例，子实例被推入父实例的 $children 数组中。

#### **2. extends**
允许声明扩展另一个组件 (可以是一个简单的选项对象或构造函数)，而无需使用 Vue.extend。这主要是为了便于扩展单文件组件。
```
var CompA = { ... }

// 在没有调用 `Vue.extend` 时候继承 CompA
var CompB = {
  extends: CompA,
  ...
}
```
#### **3. provide / inject**
provide：`Object | () => Object`
inject：`Array<string> | { [key: string]: string | Symbol | Object }`
provide 和 inject 主要在开发高阶插件/组件库时使用。并不推荐用于普通应用程序代码中。

### 六、指令
**1. v-text**
更新元素的 textContent。  `<span v-text="msg"></span>`

**2. v-html**
更新元素的 innerHTML。**注意：内容按普通 HTML 插入 - 不会作为 Vue 模板进行编译。**

**3. v-show**
根据表达式之真假值，切换元素的 display CSS 属性。

**4. v-if**
根据表达式的值的 truthiness 来有条件地渲染元素。

**5. v-else**

**6. v-else-if**

**7. v-for**
基于源数据多次渲染元素或模板块。

**8. v-on**
修饰符：
**.stop** - 调用 event.stopPropagation()。
**.prevent** - 调用 event.preventDefault()。
**.capture** - 添加事件侦听器时使用 capture 模式。
**.self** - 只当事件是从侦听器绑定的元素本身触发时才触发回调。
**.{keyCode | keyAlias}** - 只当事件是从特定键触发时才触发回调。
**.native** - 监听组件根元素的原生事件。
**.once** - 只触发一次回调。
**.left** - (2.2.0) 只当点击鼠标左键时触发。
**.right** - (2.2.0) 只当点击鼠标右键时触发。
**.middle** - (2.2.0) 只当点击鼠标中键时触发。
**.passive** - (2.3.0) 以 { passive: true } 模式添加侦听器
```
<!-- 动态事件 (2.6.0+) -->
<button v-on:[event]="doThis"></button>
```
**9. v-bind**

**10. v-model**
在表单控件或者组件上创建双向绑定。

**11. v-slot**
缩写：#

**12. v-pre**
跳过这个元素和它的子元素的编译过程。可以用来显示原始 Mustache 标签。跳过大量没有指令的节点会加快编译。
```
<span v-pre>{{ this will not be compiled }}</span>
```

**13. v-cloak**
这个指令保持在元素上直到关联实例结束编译。和 CSS 规则如 [v-cloak] { display: none } 一起用时，这个指令可以隐藏未编译的 Mustache 标签直到实例准备完毕。不会显示，直到编译结束。
```
[v-cloak] {
  display: none;
}
<div v-cloak> {{ message }}</div>
```

**14. v-once**
只渲染元素和组件一次。