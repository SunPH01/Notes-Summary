### 一、class绑定
**1. 对象语法**

```
<div
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }"
></div>
```
**2. 数组语法**
```
<!-- html -->
<div :class="[activeClass, errorClass]"></div>

// 三元表达式
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>

// 数组语法中也可以使用对象语法
<div v-bind:class="[{ active: isActive }, errorClass]"></div>

// js
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```
**3. 用在组件上**
```
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})

<!-- 引用组件 -->
<my-component class="baz boo"></my-component>
<!-- 渲染结果 -->
<p class="foo bar baz boo">Hi</p>


<!-- 引用组件 -->
<my-component v-bind:class="{ active: isActive }"></my-component>
<!-- 渲染结果 -->
<p class="foo bar active">Hi</p>
```

### 二、style绑定
**1. 对象语法**
```
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```
**2. 数组语法**
数组语法可以将多个样式对象应用到同一个元素上：
```
<div :style="[baseStyles, overridingStyles]"></div>
```

**3. 自动添加前缀**
当 `v-bind:style` 使用需要添加[浏览器引擎前缀](https://developer.mozilla.org/zh-CN/docs/Glossary/Vendor_Prefix)的 CSS 属性时，如 `transform`，Vue.js 会自动侦测并添加相应的前缀。

**4. 多重值**
从 2.3.0 起你可以为 style 绑定中的属性提供一个包含多个值的数组，常用于提供多个带前缀的值，例如：
```
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```
这样写只会渲染数组中最后一个被浏览器支持的值。在本例中，如果浏览器支持不带浏览器前缀的 flexbox，那么就只会渲染 display: flex。