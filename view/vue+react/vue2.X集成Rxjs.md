想要引用Rxjs编程，只需在已有项目中执行以下操作即可：
1.下载安装
```
npm install vue-rx rxjs --save-dev
```

2.main.ts/main.js中导入
```
import Rx from 'rxjs/Rx'
// 如果你想在vue中使用RxJS的体验更好，这里推荐使用vue-rx这个官方维护的库，使用如下：
import Vue from 'vue'
import VueRx from 'vue-rx'
import Rx from 'rxjs/Rx'

Vue.use(VueRx, Rx)
```
如果安装的rxjs是6.X 的版本，运行可能会出现如下报错
![image.png](https://upload-images.jianshu.io/upload_images/18030682-dae786307e51f8a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
报错是因为为了让用户能便捷地从RxJS 5迁移到RxJS 6，RxJS团队发布了一个名为rxjs-compat的兄弟软件包。该软件包在v6和v5的API之间创建了一个兼容层。RxJs团队建议开发人员通过安装^6.0.0版本的rxjs和rxjs-compat包来升级现有应用,此时只需按照提示安装包即可：
```
cnpm install rxjs@6 rxjs-compat@6 --save-dev
```
3.组件中使用
```
<script lang="ts">
import {  fromEvent } from 'rxjs'
import {take} from 'rxjs/operators'
export default {
  name: 'HelloWorld',
  mounted(){
    fromEvent(document,'click').pipe(
      take(3)//Observable 只发出源 Observable 最初发出的的3个值
    ).subscribe(res =>{
        console.log(res);
    })
  }
}
</script>
```
到此，就可以使用rxjs编程了~

项目地址：
vue2.x：[https://github.com/momoSph/Vue2.x-ElementUI](https://github.com/momoSph/Vue2.x-ElementUI)
vue3.x：[https://github.com/momoSph/Vue3.x-ElementUI](https://github.com/momoSph/Vue3.x-ElementUI)