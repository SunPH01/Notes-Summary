### 一、父组件向子组件传值
**通过 Prop 向子组件传递数据**

父组件
```
<blog-post :title="My journey with Vue"></blog-post>
```
子组件接收
```
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
**单向数据流：**
父级 prop 的更新会向下流动到子组件中，但是反过来则不行。每次父级组件发生更新时，子组件中所有的 prop 都将会刷新为最新的值。

这里有两种常见的试图改变一个 prop 的情形：
1.这个 prop 用来传递一个初始值；这个子组件接下来希望将其作为一个本地的 prop 数据来使用。在这种情况下，最好定义一个本地的 data 属性并将这个 prop 用作其初始值：
```
props: ['initialCounter'],
data: function () {
  return {
    counter: this.initialCounter
  }
}
```
这种情况是只接受参数作为初始值，后来父组件传参 initialCounter 再次改变的话，counter不会跟着改变。如果想要跟着改变可加上watch
```
props: ['initialCounter'],
data: function () {
  return {
    counter: this.initialCounter
  }
},
watch: {
  initialCounter(newV,oldV) {
     // do something
    this.counter = newV;
     console.log(newV,oldV)
   } 
}
```

2.这个 prop 以一种原始的值传入且需要进行转换。在这种情况下，最好使用这个 prop 的值来定义一个计算属性：
```
props: ['size'],
computed: {
  normalizedSize: function () {
    return this.size.trim().toLowerCase()
  }
}
```

### 二、子组件向父组件传值

**通过事件向父级组件传值**
子组件
```
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
```
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

### 三、父组件访问子组件属性
调用子组件的时候 定义一个ref
父组件

```
<recodeList ref="recordChild"></recodeList>

this.$refs.recordChild.属性
this.$refs.recordChild.方法
```

### 四、子组件访问父组件的属性
子组件可以直接通过一下代码获取

```
this.$parent.属性
this.$parent.方法
```