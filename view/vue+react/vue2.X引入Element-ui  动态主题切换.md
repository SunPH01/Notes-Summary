#引入element-ui
1.安装 loader 模块：
```
    cnpm install style-loader -D
    cnpm install css-loader -D
    cnpm install file-loader -D
```
2.安装 Element-UI 模块
```
cnpm install element-ui --save 
```
3.安装工具
进入项目文件夹，执行以下命令：
```npm i element-theme -g```
安装白垩主题
```npm i element-theme-chalk -D ```
4.导入引用
Element 的 theme-chalk 使用 SCSS 编写，如果你的项目也使用了 SCSS，那么可以直接在项目中改变 Element 的样式变量。新建一个样式文件，例如 element-variables.scss，写入以下内容：
```
/* 改变主题色变量 */
$--color-primary: teal;

/* 改变 icon 字体路径变量，必需 */
$--font-path: '~element-ui/lib/theme-chalk/fonts';

@import "~element-ui/packages/theme-chalk/src/index";
```
之后，在main.js添加如下代码
```
import ElementUI from 'element-ui'
import './element-variables.scss'
Vue.use(ElementUI)
```
这样就引用成功了，走到这里就是成功的迈出了第一步~~

#多主题动态切换

##初始化文件变量
主题生成工具安装成功后，如果全局安装可以在命令行里通过 et 调用工具，如果安装在当前目录下，需要通过 node_modules/.bin/et 访问到命令。执行 -i 初始化变量文件。默认输出到 element-variables.scss，当然你可以传参数指定文件输出目录。
默认路径：
```
et -i
```
指定路径及文件名：
```
et -i ./src/assets/scss/theme/ele-var-light.scss
```

**编译主题**
保存文件后，到命令行里执行 et 编译主题，如果你想启用 watch 模式，实时编译主题，增加 -w 参数；如果你在初始化时指定了自定义变量文件，则需要增加 -c 参数，并带上你的变量文件名
默认路径：
```
et
```
指定路径：
```
et -c ./src/assets/scss/theme/ele-var-light.scss -o ./src/assets/scss/theme/light
```
如果不是按默认路径创建的，那么每次编译起来就要敲很多代码，为了避免麻烦，我就在package.json 里写了如下配置：
```
 "scripts": {
    "serve": "npm run ele-et && vue-cli-service serve",
    "build": "npm run ele-et && vue-cli-service build",
    "lint": "vue-cli-service lint",
    "i18n:report": "vue-cli-service i18n:report --src './src/**/*.?(js|vue)' --locales './src/locales/**/*.json'",
    "ele-light": "et -c ./src/assets/scss/theme/ele-var-light.scss -o ./src/assets/scss/theme/light",
    "ele-dark": "et -c ./src/assets/scss/theme/ele-var-dark.scss -o ./src/assets/scss/theme/dark",
    "ele-et": "npm run ele-light && npm run ele-dark"
  }
```
这样每次执行```npm run serve ```的时候就会自动的先执行主题编译。

##引入主题
编译好之后，我们就可以动态的引入index.css来实现主题切换。
我的项目是需要在url地址里获取主题参数，来实现动态的切换，在src文件夹下新建theme.ts
```
import Vue from 'vue'
import router from './router'
  ////////////////////////////加载主题
let themeArr = [];
  function loadTheme(theme: string) {
      if ( !themeArr.includes(theme)) {
        import(`@/assets/scss/theme/${theme}/index.css`).then(msg => {
          //主题文件加载完成的回调  
              themeArr.push(theme);
        })
      }
  }
  
  router.beforeEach((to, from, next) => {
      const theme = to.params.theme || to.query.theme || store.state.theme || 'light';
      loadTheme(theme);
  })
```
到此就实现了主题切换。

项目地址：
vue2.x：[https://github.com/momoSph/Vue2.x-ElementUI](https://github.com/momoSph/Vue2.x-ElementUI)
vue3.x：[https://github.com/momoSph/Vue3.x-ElementUI](https://github.com/momoSph/Vue3.x-ElementUI)