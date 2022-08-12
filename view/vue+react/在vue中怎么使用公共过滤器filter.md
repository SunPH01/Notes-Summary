平时我们在vue中使用过滤器时，在组件中定义的过滤器不能在其他组件中使用，所以要在每个组件中定义自己的filter，这样就会出现很多重复的代码，那有没有办法定义一个公用的filter，减少代码的重复呢？下面就给大家展示下使用最多且有效的方法。
1.首先在公用js中定义一个通用的filter.js，如下图所示，记得一定要把代码整个export出来
```
const Filters = {
    stockCode(code){
        if(code){
            return code.substr(1,code.length-1);
        } else {
            return code;
        }
    }
}
export default Filters;
```
2.然后在main.js中引入
```
//引入过滤函数
import vueFilter from './sys/filters';

for (let key in vueFilter){
  Vue.filter(key,vueFilter[key])
}
```
3.这样我们就可以在组件中使用啦
```
<el-col :span="3"><div>{{item[0]|stockCode}}</div></el-col>
```

项目地址：
vue2.x：[https://github.com/momoSph/Vue2.x-ElementUI](https://github.com/momoSph/Vue2.x-ElementUI)
vue3.x：[https://github.com/momoSph/Vue3.x-ElementUI](https://github.com/momoSph/Vue3.x-ElementUI)