# 实现思路
因为要实现主题的切换，所以在开发组件中涉及到主题的地方，要提取一些公用的变量，不要直接写死样式值。但是如果只是定义一些变量的话，只是完成控制颜色等值的提取。还是无法实现用户在前端点击就可以切换到对应的主题。因此必定要用到映射表来批量替换提取的这些控制变量。

# scss部分
**1.先看一下主题文件结构**
![image.png](https://upload-images.jianshu.io/upload_images/18030682-b72d55b233c04324.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**2. base.scss文件**
base.scss是定义基本样式的文件

```
//自定义样式可以写在这里
//...
```
直接在main里引入即可
```import './assets/scss/base.scss'```

**2.在variable.scss文件里定义变量：**
```
// 浅色主题
$light-theme: (
  border-color: #eceeef,
  font-color:#323232,
  background-color:#fff
);

//深色主题
$dark-theme: (
  border-color: #000,
  font-color:#c2c6d0,
  background-color:#131925
);

//定义映射集合
$themes: (
  light: $light-theme,
  dark: $dark-theme
);
//使用@mixin封装对应的方法,这些方法都通过@include  方法名(arg) 来实现调用.定义mixin方法
@mixin themify($themes: $themes) {
  @each $theme-name, $map in $themes {
 
    .theme-#{$theme-name} & {
      $theme-map: () !global;
      @each $key, $value in $map {
        $theme-map: map-merge($theme-map, ($key: $value)) !global;
      }
 
      @content;
 
      $theme-map: null !global;
    }
  }
}
 
@function themed($key) {
  @return map-get($theme-map, $key);
}

$color-danger:red;
$color-white:#fff;
```
到这里自定义的scss文件已经好了

# 全局引入

**vue2.X引入：*
1.首先要安装 sass-resources-loader
```
npm i sass-resources-loader --save-dev
```
2.安装成功后找到build下的uils.js
把里面的```scss: generateLoaders('sass')```改为
```
scss: generateLoaders('sass').concat(
      {
        loader: 'sass-resources-loader',
        options: {
          resources: path.resolve(__dirname, '../src/assets/scss/mixin.scss') //把'../src/assets/scss/mixin.scss'  改为你的文件路径
            }
       })
```
**vue3.X引入：**
在vue.config.js里写入如下代码：
```
module.exports = {
   ...
    css: {
        loaderOptions: {
            sass: {
                data: `@import "~@/assets/scss/custom-theme-var.scss";`//添加全局sass文件  此处路径替换为你的文件路径
            }
        }
    }
...
}
```
现在已经引入了，然后就可以直接在组建中使用了，例如：
```
<template>
<!-- 美股列表 -->
    <section id="usList">
        美股列表
        <el-button v-show="theme=='light'" @click="changeTheme('dark')" type="primary">浅色（点击切换深色主题）</el-button>
        <el-button v-show="theme=='dark'" @click="changeTheme('light')" type="primary">深色（点击切换浅色主题）</el-button>
    </section>
</template>
<script lang="ts">
    export default {
        name: 'UsList',
        data() {
              return {
                    theme:'light'
              }
        },
        mounted(){
            
        },
        methods: {
            changeTheme(theme){
                this.theme = theme;
                document.getElementsByTagName('body')[0].setAttribute('class', 'theme-' + theme);
            }
        }
    }
</script>
<style  lang="scss">
#usList{
    @include themify($themes) {
      background: themed('background-color');
    }
  color:$color-danger;
  }
</style>
```
点击切换按钮，就实现主题切换了，效果如下
点击浅色按钮：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-89d3fccfc65aca87.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击深色按钮：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-7bf368a142e69b7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

项目地址：
vue2.x：[https://github.com/momoSph/Vue2.x-ElementUI](https://github.com/momoSph/Vue2.x-ElementUI)
vue3.x：[https://github.com/momoSph/Vue3.x-ElementUI](https://github.com/momoSph/Vue3.x-ElementUI)