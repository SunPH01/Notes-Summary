## 全局API

**2.x语法**

```javascript
import Vue from 'vue'

Vue.nextTick(() => {
  // 一些和DOM有关的东西
})
```

**3.x语法**

```javascript
import { nextTick } from 'vue'

nextTick(() => {
  // 一些和DOM有关的东西
})
```

Vue 2.x 中的这些全局 API 受此更改的影响：

- `Vue.nextTick`
- `Vue.observable` (用 `Vue.reactive` 替换)
- `Vue.version`
- `Vue.compile` (仅完整构建版本)
- `Vue.set` (仅兼容构建版本)
- `Vue.delete` (仅兼容构建版本)

## 模板指令

- [组件上 `v-model` 用法已更改，替换 `v-bind.sync`](https://v3.cn.vuejs.org/guide/migration/v-model.html)
- [`<template v-for>`和非`v-for`节点上`key`用法已更改](https://v3.cn.vuejs.org/guide/migration/key-attribute.html)
- [在同一元素上使用的 `v-if` 和 `v-for` 优先级已更改](https://v3.cn.vuejs.org/guide/migration/v-if-v-for.html)
- [`v-bind="object"` 现在排序敏感](https://v3.cn.vuejs.org/guide/migration/v-bind.html)
- [`v-on:event.native` 修饰符已移除](https://v3.cn.vuejs.org/guide/migration/v-on-native-modifier-removed.html)
- [`v-for` 中的 `ref` 不再注册 ref 数组](https://v3.cn.vuejs.org/guide/migration/array-refs.html)

**key 属性**

- 新增：对于`v-if`/`v-else`/`v-else-if`的各分支项 `key`将不再是必须的，因为现在 Vue 会自动生成唯一的，因为现在 Vue会自动生成唯一的 `key`。
  - **非兼容**：如果你手动提供 `key`，那么每个分支必须使用唯一的 `key`。你不能通过故意使用相同的 `key` 来强制重用分支。
- **非兼容**：`<template v-for>` 的 `key` 应该设置在 `<template>` 标签上 (而不是设置在它的子节点上)。

## 组件

- [只能使用普通函数创建功能组件](https://v3.cn.vuejs.org/guide/migration/functional-components.html)
- [`functional` 属性在单文件组件 (SFC) `
- [异步组件现在需要 `defineAsyncComponent` 方法来创建](https://v3.cn.vuejs.org/guide/migration/async-components.html)
- [组件事件现在需要在 `emits` 选项中声明](https://v3.cn.vuejs.org/guide/migration/emits-option.html)

## 渲染函数

- [渲染函数 API 改变](https://v3.cn.vuejs.org/guide/migration/render-function-api.html)
- [`$scopedSlots` property 已删除，所有插槽都通过 `$slots` 作为函数暴露](https://v3.cn.vuejs.org/guide/migration/slots-unification.html)
- [`$listeners` 被移除或整合到 `$attrs`](https://v3.cn.vuejs.org/guide/migration/listeners-removed)
- [`$attrs` 现在包含 `class` and `style` attribute](https://v3.cn.vuejs.org/guide/migration/attrs-includes-class-style.html)