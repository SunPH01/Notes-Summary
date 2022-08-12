### 安装
可以使用下列任一命令安装这个新的包：

```
npm install -g @vue/cli
# OR
yarn global add @vue/cli
```
用`vue --version`命令来检查其版本是否正确。

### 创建项目
运行以下命令来创建一个新项目：

```
vue create XXX
```
你会被提示选取一个 preset。你可以选默认的包含了基本的 Babel + ESLint 设置的 preset，也可以选“手动选择特性”来选取需要的特性。
![image.png](https://upload-images.jianshu.io/upload_images/18030682-5b7d8aeb46bfbd72.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个默认的设置非常适合快速创建一个新项目的原型，而手动设置则提供了更多的选项，它们是面向生产的项目更加需要的。
![image.png](https://upload-images.jianshu.io/upload_images/18030682-f39989e48150dea3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果你决定手动选择特性，在操作提示的最后你可以选择将已选项保存为一个将来可复用的 preset。我们会在下一个章节讨论 preset 和插件。

### 插件和Preset
**插件**
Vue CLI 使用了一套基于插件的架构。如果你查阅一个新创建项目的 package.json，就会发现依赖都是以 @vue/cli-plugin- 开头的。插件可以修改 webpack 的内部配置，也可以向 vue-cli-service 注入命令。在项目创建的过程中，绝大部分列出的特性都是通过插件来实现的。

>你可以通过 vue ui 命令使用 GUI 安装和管理插件。

**在现有的项目中安装插件**
每个 CLI 插件都会包含一个 (用来创建文件的) 生成器和一个 (用来调整 webpack 核心配置和注入命令的) 运行时插件。当你使用 vue create 来创建一个新项目的时候，有些插件会根据你选择的特性被预安装好。如果你想在一个已经被创建好的项目中安装一个插件，可以使用 `vue add` 命令，例如：vue add eslint
>vue add 的设计意图是为了安装和调用 Vue CLI 插件。这不意味着替换掉普通的 npm 包。

**项目本地的插件**
如果你需要在项目里直接访问插件 API 而不需要创建一个完整的插件，你可以在 package.json 文件中使用 vuePlugins.service 选项：
```
{
  "vuePlugins": {
    "service": ["my-commands.js"]
  }
}
```
也可以通过 vuePlugins.ui 选项添加像 UI 插件一样工作的文件：
```
{
  "vuePlugins": {
    "ui": ["my-ui.js"]
  }
}
```

### CLI服务
**使用命令**
在一个 Vue CLI 项目中，@vue/cli-service 安装了一个名为 `vue-cli-service` 的命令。你可以在 `npm scripts` 中以 `vue-cli-service`、或者从终端中以 `./node_modules/.bin/vue-cli-service` 访问这个命令。

```
{
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build"
  }
}
```
你可以通过 npm 或 Yarn 调用这些 script：
```
npm run serve
# OR
yarn serve
```

**vue-cli-service serve**
```
用法：vue-cli-service serve [options] [entry]
选项：
  --open    在服务器启动时打开浏览器
  --copy    在服务器启动时将 URL 复制到剪切版
  --mode    指定环境模式 (默认值：development)
  --host    指定 host (默认值：0.0.0.0)
  --port    指定 port (默认值：8080)
  --https   使用 https (默认值：false)
```

`vue-cli-service serve` 命令会启动一个开发服务器 (基于 webpack-dev-server) 并附带开箱即用的模块热重载 (Hot-Module-Replacement)。
除了通过命令行参数，你也可以使用 `vue.config.js` 里的 **devServer** 字段配置开发服务器。
命令行参数 [entry] 将被指定为唯一入口，而非额外的追加入口。


**vue-cli-service build**
```
用法：vue-cli-service build [options] [entry|pattern]

选项：

  --mode        指定环境模式 (默认值：production)
  --dest        指定输出目录 (默认值：dist)
  --modern      面向现代浏览器带自动回退地构建应用
  --target      app | lib | wc | wc-async (默认值：app)
  --name        库或 Web Components 模式下的名字 (默认值：package.json 中的 "name" 字段或入口文件名)
  --no-clean    在构建项目之前不清除目标目录
  --report      生成 report.html 以帮助分析包内容
  --report-json 生成 report.json 以帮助分析包内容
  --watch       监听文件变化
```
vue-cli-service build 会在 dist/ 目录产生一个可用于生产环境的包，带有 JS/CSS/HTML 的压缩，和为更好的缓存而做的自动的 vendor chunk splitting。它的 chunk manifest 会内联在 HTML 里。

### 浏览器兼容性
**[browserslist](https://github.com/browserslist/browserslist)**
`package.json` 文件里的 `browserslist` 字段 (或一个单独的 .browserslistrc 文件)，指定了项目的目标浏览器的范围。

### html和静态资源
**插值**
因为 index 文件被用作模板，所以你可以使用 [lodash template](https://lodash.com/docs/4.17.10#template) 语法插入内容：

* <%= VALUE %> 用来做不转义插值；
* <%- VALUE %> 用来做 HTML 转义插值；
* <% expression %> 用来描述 JavaScript 流程控制。
除了[被 `html-webpack-plugin` 暴露的默认值](https://github.com/jantimon/html-webpack-plugin#writing-your-own-templates)之外，所有**客户端环境变量**(下文会讲到)也可以直接使用。例如，`BASE_URL` 的用法：
```
<link rel="icon" href="<%= BASE_URL %>favicon.ico">
```

**Preload**
[`<link rel="preload">`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Preloading_content) 是一种 resource hint，用来指定页面加载后很快会被用到的资源，所以在页面加载的过程中，我们希望在浏览器开始主体渲染之前尽早 preload。

**处理静态资源**
静态资源可以通过两种方式进行处理：

* 在 JavaScript 被导入或在 template/CSS 中通过相对路径被引用。这类引用会被 webpack 处理。
* 放置在 public 目录下或通过绝对路径被引用。这类资源将会直接被拷贝，而不会经过 webpack 的处理。

**public 文件夹**
任何放置在 public 文件夹的静态资源都会被简单的复制，而不经过 webpack。你需要通过绝对路径来引用它们。

注意我们推荐将资源作为你的模块依赖图的一部分导入，这样它们会通过 webpack 的处理并获得如下好处：

* 脚本和样式表会被压缩且打包在一起，从而避免额外的网络请求。
* 文件丢失会直接在编译时报错，而不是到了用户端才产生 404 错误。
* 最终生成的文件名包含了内容哈希，因此你不必担心浏览器会缓存它们的老版本。

`public` 目录提供的是一个**应急手段**，当你通过绝对路径引用它时，留意应用将会部署到哪里。如果你的应用没有部署在域名的根部，那么你需要为你的 URL 配置 [publicPath](https://cli.vuejs.org/zh/config/#publicpath) 前缀：
* 在 `public/index.html` 或其它通过 html-webpack-plugin 用作模板的 HTML 文件中，你需要通过 `<%= BASE_URL %>` 设置链接前缀：
```
<link rel="icon" href="<%= BASE_URL %>favicon.ico">
```
* 在模板中，你首先需要向你的组件传入基础 URL：
```
data () {
  return {
    publicPath: process.env.BASE_URL
  }
}
// html 
<img :src="`${publicPath}my-image.png`">
```

**何时使用 public 文件夹**
* 你需要在构建输出中指定一个文件的名字。
* 你有上千个图片，需要动态引用它们的路径。
* 有些库可能和 webpack 不兼容，这时你除了将其用一个独立的 <script> 标签引入没有别的选择。

### css相关
**预处理器**
你可以在创建项目的时候选择预处理器 (Sass/Less/Stylus)。如果当时没有选好，内置的 webpack 仍然会被预配置为可以完成所有的处理。你也可以手动安装相应的 webpack loader：

```
# Sass
npm install -D sass-loader node-sass

# Less
npm install -D less-loader less

# Stylus
npm install -D stylus-loader stylus
```

**自动化导入**
如果你想自动化导入文件 (用于颜色、变量、mixin……)，你可以使用 **style-resources-loader**。这里有一个关于 Stylus 的在每个单文件组件和 Stylus 文件中导入 `./src/styles/imports.styl` 的例子：
```
// vue.config.js
const path = require('path')

module.exports = {
  chainWebpack: config => {
    const types = ['vue-modules', 'vue', 'normal-modules', 'normal']
    types.forEach(type => addStyleResource(config.module.rule('stylus').oneOf(type)))
  },
}

function addStyleResource (rule) {
  rule.use('style-resource')
    .loader('style-resources-loader')
    .options({
      patterns: [
        path.resolve(__dirname, './src/styles/imports.styl'),
      ],
    })
}
```

**向预处理器 Loader 传递选项**
有的时候你想要向 webpack 的预处理器 loader 传递选项。你可以使用 vue.config.js 中的 css.loaderOptions 选项。比如你可以这样向所有 Sass/Less 样式传入共享的全局变量：
```
// vue.config.js
module.exports = {
  css: {
    loaderOptions: {
      // 给 sass-loader 传递选项
      sass: {
        // @/ 是 src/ 的别名
        // 所以这里假设你有 `src/variables.sass` 这个文件
        // 注意：在 sass-loader v7 中，这个选项名是 "data"
        prependData: `@import "~@/variables.sass"`
      },
      // 默认情况下 `sass` 选项会同时对 `sass` 和 `scss` 语法同时生效
      // 因为 `scss` 语法在内部也是由 sass-loader 处理的
      // 但是在配置 `data` 选项的时候
      // `scss` 语法会要求语句结尾必须有分号，`sass` 则要求必须没有分号
      // 在这种情况下，我们可以使用 `scss` 选项，对 `scss` 语法进行单独配置
      scss: {
        prependData: `@import "~@/variables.scss";`
      },
      // 给 less-loader 传递 Less.js 相关选项
      less:{
        // http://lesscss.org/usage/#less-options-strict-units `Global Variables`
        // `primary` is global variables fields name
        globalVars: {
          primary: '#fff'
        }
      }
    }
  }
}
```

### webpack相关
调整 webpack 配置最简单的方式就是在 vue.config.js 中的 configureWebpack 选项提供一个对象：

```
// vue.config.js
module.exports = {
  configureWebpack: {
    plugins: [
      new MyAwesomeWebpackPlugin()
    ]
  }
}
```

如果你需要基于环境有条件地配置行为，或者想要直接修改配置，那就换成一个函数 (该函数会在环境变量被设置之后懒执行)。该方法的第一个参数会收到已经解析好的配置。在函数内，你可以直接修改配置，或者返回一个将会被合并的对象：
```
// vue.config.js
module.exports = {
  configureWebpack: config => {
    if (process.env.NODE_ENV === 'production') {
      // 为生产环境修改配置...
    } else {
      // 为开发环境修改配置...
    }
  }
}
```
**修改Loader选项**
```
// vue.config.js
module.exports = {
  chainWebpack: config => {
    config.module
      .rule('vue')
      .use('vue-loader')
        .loader('vue-loader')
        .tap(options => {
          // 修改它的选项...
          return options
        })
  }
}
```

**添加一个新的Loader**
```
// vue.config.js
module.exports = {
  chainWebpack: config => {
    // GraphQL Loader
    config.module
      .rule('graphql')
      .test(/\.graphql$/)
      .use('graphql-tag/loader')
        .loader('graphql-tag/loader')
        .end()
      // 你还可以再添加一个 loader
      .use('other-loader')
        .loader('other-loader')
        .end()
  }
}
```

### 环境变量和模式
你可以替换你的项目根目录中的下列文件来指定环境变量：
```
.env                # 在所有的环境中被载入
.env.local          # 在所有的环境中被载入，但会被 git 忽略
.env.[mode]         # 只在指定的模式中被载入
.env.[mode].local   # 只在指定的模式中被载入，但会被 git 忽略
```
一个环境文件只包含环境变量的“键=值”对：
```
NODE_ENV=development
VUE_APP_CONSOLE='hide'
VUE_BASE_URL='xxxxx'
```
被载入的变量将会对 vue-cli-service 的所有命令、插件和依赖可用。
>**环境加载属性**
>为一个特定模式准备的环境文件 (例如 .env.production) 将会比一般的环境文件 (例如 .env) 拥有更高的优先级。
>此外，Vue CLI 启动时已经存在的环境变量拥有最高优先级，并不会被 .env 文件覆写。

**模式**是 Vue CLI 项目中一个重要的概念。默认情况下，一个 Vue CLI 项目有三个模式：
* **development** 模式用于 `vue-cli-service serve`
* **production** 模式用于 `vue-cli-service build` 和 `vue-cli-service test:e2e`
* **test** 模式用于 `vue-cli-service test:unit`

注意模式不同于 NODE_ENV，一个模式可以包含多个环境变量。也就是说，每个模式都会将 NODE_ENV 的值设置为模式的名称——比如在 development 模式下 NODE_ENV 的值会被设置为 "development"。

你可以通过为 .env 文件增加后缀来设置某个模式下特有的环境变量。比如，如果你在项目根目录创建一个名为 .env.development 的文件，那么在这个文件里声明过的变量就只会在 development 模式下被载入。

你可以通过传递 --mode 选项参数为命令行覆写默认的模式。例如，如果你想要在构建命令中使用开发环境变量，请在你的 package.json 脚本中加入：
```
"dev-build": "vue-cli-service build --mode development",
```

**在客户端侧代码中使用环境变量：**`process.env.xxx`