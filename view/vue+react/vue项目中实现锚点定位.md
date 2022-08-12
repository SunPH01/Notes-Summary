在vue项目中有一个需要用到a标签锚点跳转的功能，但是实现的时候发现，跳转之后路径会被改变。所以找到了以下方法来实现。
```
    <div class="subscribe" @click="goRegister">立即订阅</div>
```
想要实现点击页面底部的订阅按钮，会回到顶部的注册表单的位置，
实现方法：
```
// 跳到注册
    goRegister() {
      // 注册表单盒子的类名为 form-wrap-app
      this.$el.querySelector('.form-wrap-app').scrollIntoView();
    }
```
这样就可以啦~