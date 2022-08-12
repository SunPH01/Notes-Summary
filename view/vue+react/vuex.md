### 一、State
Vuex 使用**单一状态树**，用一个对象就包含了全部的应用层级状态。至此它便作为一个“唯一数据源 ([SSOT](https://en.wikipedia.org/wiki/Single_source_of_truth))”而存在。这也意味着，每个应用将仅仅包含一个 store 实例。单一状态树让我们能够直接地定位任一特定的状态片段，在调试的过程中也能轻易地取得整个当前应用状态的快照。
Vuex 通过 store 选项，提供了一种机制将状态从根组件“注入”到每一个子组件中（需调用 Vue.use(Vuex)），通过在根实例中注册 store 选项，该 store 实例会注入到根组件下的所有子组件中，且子组件能通过 this.$store 访问到。

**mapState辅助函数**
当一个组件需要获取多个状态的时候，将这些状态都声明为计算属性会有些重复和冗余。为了解决这个问题，我们可以使用 mapState 辅助函数帮助我们生成计算属性，
```
import { mapState } from 'vuex'

computed: {
  ...mapState({
    // 箭头函数可使代码更简练
    count: state => state.count,

    // 传字符串参数 'count' 等同于 `state => state.count`
    countAlias: 'count',

    // 为了能够使用 `this` 获取局部状态，必须使用常规函数
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
})
}
```
当映射的计算属性的名称与 state 的子节点名称相同时，我们也可以给 mapState 传一个字符串数组。
```
computed: {
  ...mapState([
    'count'
  ])
}
```
### 二、Getter
有时候我们需要从 store 中的 state 中派生出一些状态，例如对列表进行过滤并计数：

```
computed: {
  doneTodosCount () {
    return this.$store.state.todos.filter(todo => todo.done).length
  }
}
```
如果有多个组件需要用到此属性，我们要么复制这个函数，或者抽取到一个共享函数然后在多处导入它——无论哪种方式都不是很理想。
Vuex 允许我们在 store 中定义“getter”（可以认为是 store 的计算属性）。就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。
**Getter 接受 `state` 作为其第一个参数，也可以接受 `getters` 作为第二个参数：**
```
getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
    doneTodosCount: (state, getters) => {
      return getters.doneTodos.length
    }
  }
```

**通过属性访问**
Getter 会暴露为 store.getters 对象，你可以以属性的形式访问这些值：
```
store.getters.doneTodos // -> [{ id: 1, text: '...', done: true }]
```

在组件中使用它：
```
computed: {
  doneTodosCount () {
    return this.$store.getters.doneTodosCount
  }
}
```
注意，getter 在通过属性访问时是作为 Vue 的响应式系统的一部分缓存其中的。

**通过方法访问**
```
getters: {
  // ...
  getTodoById: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)
  }
}

store.getters.getTodoById(2)
```

**mapGetters辅助函数**
`mapGetters` 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性：

```
import { mapGetters } from 'vuex'

  computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter'
    ])
  }
```
如果你想将一个 getter 属性另取一个名字，使用对象形式：
```
...mapGetters({
  // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})
```

### 三、Mutation
**更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。**Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的事件类型 (type) 和 一个回调函数 (handler)。这个回调函数就是我们实际进行状态更改的地方，并且它会**接受 state 作为第一个参数**：

```
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 变更状态
      state.count++
    }
  }
})
```
你不能直接调用一个 mutation handler。可以以相应的 type 调用 store.commit 方法：
`store.commit('increment')`

**提交载荷（Payload）**
你可以向 store.commit 传入**额外的参数，即 mutation 的 载荷（payload）**：
```
mutations: {
  increment (state, n) {
    state.count += n
  }
}

store.commit('increment', 10)
```
在大多数情况下，载荷应该是一个对象，这样可以包含多个字段并且记录的 mutation 会更易读。

**对象风格的提交方式**
```
store.commit({
  type: 'increment',
  amount: 10
})
```

**Mutation 需遵守 Vue 的响应规则**
既然 Vuex 的 store 中的状态是响应式的，那么当我们变更状态时，监视状态的 Vue 组件也会自动更新。这也意味着 Vuex 中的 mutation 也需要与使用 Vue 一样遵守一些注意事项：
1.  最好提前在你的 store 中初始化好所有所需属性。
2.  当需要在对象上添加新属性时，你应该 使用 `Vue.set(obj, 'newProp', 123)` 。或者以新对象替换老对象，例如，利用[对象展开运算符](https://github.com/tc39/proposal-object-rest-spread)：`state.obj = { ...state.obj, newProp: 123 }`

**使用常量替代 Mutation 事件类型**
使用常量替代 mutation 事件类型，同时把这些常量放在单独的文件中可以让你的代码合作者对整个 app 包含的 mutation 一目了然：
```
// mutation-types.js
export const SOME_MUTATION = 'SOME_MUTATION'

// store.js
import Vuex from 'vuex'
import { SOME_MUTATION } from './mutation-types'

const store = new Vuex.Store({
  state: { ... },
  mutations: {
    // 我们可以使用 ES2015 风格的计算属性命名功能来使用一个常量作为函数名
    [SOME_MUTATION] (state) {
      // mutate state
    }
  }
})
```

**Mutation必须是同步函数**
一条重要的原则就是要记住` mutation 必须是同步函数`。
因为当 mutation 触发的时候，回调函数还没有被调用，不知道什么时候回调函数实际上被调用——实质上任何在回调函数中进行的状态的改变都是不可追踪的。所以在 mutation 中混合异步调用会导致你的程序很难调试。例如，当你调用了两个包含异步回调的 mutation 来改变状态，你怎么知道什么时候回调和哪个先回调呢？这就是为什么我们要区分mutation、action这两个概念。

**在组件中提交 Mutation**
可以在组件中使用 this.$store.commit('xxx') 提交 mutation，或者使用 `mapMutations` 辅助函数将组件中的 methods 映射为 store.commit 调用（需要在根节点注入 store）。
```
methods: {
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
  }
```

### 四、Action
Action 类似于 mutation，不同在于：

* Action 提交的是 mutation，而不是直接变更状态。
* Action 可以包含任意异步操作。
例如：
```
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
// 可以用参数结构来简化代码
actions: {
  increment ({ commit }) {
    commit('increment')
  }
}
```
**Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象**，因此你可以调用 context.commit 提交一个 mutation，或者通过 context.state 和 context.getters 来获取 state 和 getters。

**分发Action**
Action 通过 store.dispatch 方法触发：`store.dispatch('increment')`。
在 action 内部执行异步操作：
```
actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}
```
Actions 支持同样的载荷方式和对象方式进行分发：
```
// 以载荷形式分发
store.dispatch('incrementAsync', {
  amount: 10
})

// 以对象形式分发
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})
```

**在组件中分发 Action**
在组件中使用 `this.$store.dispatch('xxx')` 分发 action，或者使用 `mapActions` 辅助函数将组件的 methods 映射为 store.dispatch 调用（需要先在根节点注入 store）。(映射方法同mutation)

**组合Action**
Action 通常是异步的，那么如何知道 action 什么时候结束呢？更重要的是，我们如何才能组合多个 action，以处理更加复杂的异步流程？
首先，你需要明白 store.dispatch 可以处理被触发的 action 的处理函数返回的 Promise，并且 store.dispatch 仍旧返回 Promise：
```
actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation')
        resolve()
      }, 1000)
    })
  }
}

store.dispatch('actionA').then(() => {
  // ...
})
```
在另外一个 action 中也可以：
```
actions: {
  // ...
  actionB ({ dispatch, commit }) {
    return dispatch('actionA').then(() => {
      commit('someOtherMutation')
    })
  }
}
```
如果我们利用 [async / await](https://tc39.github.io/ecmascript-asyncawait/)，我们可以如下组合 action：
```
// 假设 getData() 和 getOtherData() 返回的是 Promise

actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // 等待 actionA 完成
    commit('gotOtherData', await getOtherData())
  }
}
```
>一个 store.dispatch 在不同模块中可以触发多个 action 函数。在这种情况下，只有当所有触发函数完成后，返回的 Promise 才会执行。

### 五、Module
由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。为了解决这个问题，Vuex 允许我们将 store 分割成模块（module）。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割：
```
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

**模块的局部状态**
对于模块内部的 mutation 和 getter，接收的第一个参数是**模块的局部状态对象state**。同样，对于模块内部的 action，局部状态通过 `context.state` 暴露出来，根节点状态则为 `context.rootState`：
```
const moduleA = {
  // ...
  actions: {
    incrementIfOddOnRootSum ({ state, commit, rootState }) {
      if ((state.count + rootState.count) % 2 === 1) {
        commit('increment')
      }
    }
  }
}
```
对于模块内部的 getter，**根节点状态会作为第三个参数**暴露出来：
```
const moduleA = {
  // ...
  getters: {
    sumWithRootCount (state, getters, rootState) {
      return state.count + rootState.count
    }
  }
}
```

**命名空间**
默认情况下，模块内部的 action、mutation 和 getter 是注册在全局命名空间的——这样使得多个模块能够对同一 mutation 或 action 作出响应。
如果希望你的模块具有更高的封装度和复用性，你可以通过添加 namespaced: true 的方式使其成为带命名空间的模块。当模块被注册后，它的所有 getter、action 及 mutation 都会自动根据模块注册的路径调整命名。例如：
```
const store = new Vuex.Store({
  modules: {
    account: {
      namespaced: true,

      // 模块内容（module assets）
      state: { ... }, // 模块内的状态已经是嵌套的了，使用 `namespaced` 属性不会对其产生影响
      getters: {
        isAdmin () { ... } // -> getters['account/isAdmin']
      },
      actions: {
        login () { ... } // -> dispatch('account/login')
      },
      mutations: {
        login () { ... } // -> commit('account/login')
      },

      // 嵌套模块
      modules: {
        // 继承父模块的命名空间
        myPage: {
          state: { ... },
          getters: {
            profile () { ... } // -> getters['account/profile']
          }
        },

        // 进一步嵌套命名空间
        posts: {
          namespaced: true,

          state: { ... },
          getters: {
            popular () { ... } // -> getters['account/posts/popular']
          }
        }
      }
    }
  }
})
```

**在带命名空间的模块内访问全局内容（Global Assets）**
如果你希望使用全局 state 和 getter，rootState 和 rootGetters 会作为第三和第四参数传入 getter，也会通过 context 对象的属性传入 action。

**在带命名空间的模块注册全局 action**
若需要在带命名空间的模块注册全局 action，你可添加 root: true，并将这个 action 的定义放在函数 handler 中。
```
actions: {
  someAction: {
    root: true,
    handler (namespacedContext, payload) { ... } // -> 'someAction'
  }
}
```

**带命名空间的绑定函数**
当使用 mapState, mapGetters, mapActions 和 mapMutations 这些函数来绑定带命名空间的模块时，写起来可能比较繁琐：
```
computed: {
  ...mapState({
    a: state => state.some.nested.module.a,
    b: state => state.some.nested.module.b
  })
},
methods: {
  ...mapActions([
    'some/nested/module/foo', // -> this['some/nested/module/foo']()
    'some/nested/module/bar' // -> this['some/nested/module/bar']()
  ])
}
// 可简化为
computed: {
  ...mapState('some/nested/module', {
    a: state => state.a,
    b: state => state.b
  })
},
methods: {
  ...mapActions('some/nested/module', [
    'foo', // -> this.foo()
    'bar' // -> this.bar()
  ])
}
```
或者，你可以通过使用 `createNamespacedHelpers` 创建基于某个命名空间辅助函数。它返回一个对象，对象里有新的绑定在给定命名空间值上的组件绑定辅助函数：
```
import { createNamespacedHelpers } from 'vuex'

const { mapState, mapActions } = createNamespacedHelpers('some/nested/module')

export default {
  computed: {
    // 在 `some/nested/module` 中查找
    ...mapState({
      a: state => state.a,
      b: state => state.b
    })
  },
  methods: {
    // 在 `some/nested/module` 中查找
    ...mapActions([
      'foo',
      'bar'
    ])
  }
}
```

**模块动态注册**
在 store 创建之后，你可以使用 store.registerModule 方法注册模块：
```
// 注册模块 `myModule`
store.registerModule('myModule', {
  // ...
})
// 注册嵌套模块 `nested/myModule`
store.registerModule(['nested', 'myModule'], {
  // ...
})
```
### 六、项目结构
Vuex 并不限制你的代码结构。但是，它规定了一些需要遵守的规则：
1. 应用层级的状态应该集中到单个 store 对象中。
2. 提交 mutation 是更改状态的唯一方法，并且这个过程是同步的。
3. 异步逻辑都应该封装到 action 里面。

只要你遵守以上规则，如何组织代码随你便。如果你的 store 文件太大，只需将 action、mutation 和 getter 分割到单独的文件。
对于大型应用，我们会希望把 Vuex 相关代码分割到模块中。项目结构示例：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-5bb6fbcb67bb0fec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)