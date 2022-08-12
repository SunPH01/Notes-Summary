#vue-i18n用法
**1.引入 vue-i18n**
在vscode终端执行
```
npm install vue-i18n  --save-dev
```
**2.在main.js中引入**

```
import VueI18n from 'vue-i18n'
Vue.use(VueI18n)
   const i18n = new VueI18n({
     locale:  store.state.currentLang, // 语言标识,默认汉语,
     messages: {
       'en-US': require("./assets/lang/en"),
       'zh-CN': require("./assets/lang/zh-CN"),
       'zh-TW': require("./assets/lang/zh-TW")
     }
   });
new Vue({
  el: '#app',
  router,
  store,
  i18n,
  components: { App },
  template: '<App/>'
})
```
require不能用的话需要先根据提示执行```npm i @types/node```
**3.利用vuex管理语言**
```
// vuex配置
const NavigatorLang = navigator.language.substr(0, 2) 
const store = new Vuex.Store({
  state: {
    currentLang: localStorage.getItem('UserLang') || NavigatorLang
  },
  mutations: {
    updateLang(state, value) {
      state.currentLang = value
      localStorage.setItem('UserLang', state.currentLang)
    }
  }
})
```
**4.创建语言包文件**
如下图所示：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-50abf5416f4ca72c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/18030682-ac76ddb043797720.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**5.在组件中使用**
在组建中使用的方式可以用插值法``` {{$t(key)}} ``` 也可以用 ```v-text = "$t(key)"```
```
        <el-button @click="changeLang('zh-CN')">中文</el-button>
        <el-button @click="changeLang('zh-TW')">繁體</el-button>
        <el-button @click="changeLang('en-US')">English</el-button>
        <p>{{ $t("lang.users.lang")}}</p>

  methods:{
        changeLang(lang){
            this.$i18n.locale = lang;//语言切换
            this.$store.commit('updateLang',lang);//保存到vuex
        }
    }
```
点击按钮，就可以动态切换语言了
#模板中使用词条
**普通词条**
插值表达式中向```$t```方法传入词条的key值就可以了，是不是超级简单
```<div>普通文本：{{$t('helloworld')}}</div>```
**带参数词条**
带参数词条也有几种写法，分别有命名式，列表式，ruby on rail式。
**命名式**
在词条中指定命名参数，参数由花括号包裹，如：
```
// 定义词条
helloman: 'hello {name}'
```
模板引用词条时，$t方法可以接收传入两个参数，第一个入参为词条key值，第二个入参是词条参数的key-value键值对。
```
// 引用词条
<div>{{$t('helloman', {name: 'Tom'})}}</div>
```
**列表式**
列表式其实与数组的概念一致。词条定义时，通过数组下标鉴别接收的参数。
```
// 定义词条
helloman: 'hello {0}'
```
模板中，则将对象改由数组传入
```
// 引用词条
<div>{{$t('helloman', [‘Tom’])}}</div>
// 还可以这样，用array-like object的形式传入。
<div>{{$t('helloman', {'0': 'Tom'})}}</div>
```
**ruby on rails i18n format**
与命名式方法是类似的，定义词条略有不同
```
// 定义词条
helloman: 'hello %{name}'
```
而模板上引用词条的方式是一样的，用key-value的方式传入。
**多元化**
一个词条的key值可以对应多个value信息，此时可以通过vue-i18n的管道pipe来实现。
例如：
```
iphones: '{n} iphone5 | iphone6 | iphone7'
```
这里定义了三个value，第一个value还是带参数的。
在模板上的调用方式也有了不同，需要用$tc方法来引用词条。
```
<div>Pluralization：{{$tc('iphones', 0, {n: '3台'})}}</div>
// 输出 Pluralization：3台 iphone5
<div>Pluralization：{{$tc('iphones', 1)}}</div>
// 输出 Pluralization：iphone6
```
#日期格式设置
在main.js添加如下过滤格式
```
//////////////////////////多语言
 //日期格式設置
  const dateTimeFormats = {
    'en-US': {
      short: {
        year: 'numeric', month: 'numeric', day: 'numeric'
      },
      long: {
        year: 'numeric', month: 'numeric', day: 'numeric',
        hour: 'numeric', minute: 'numeric',second:'numeric'
      }
    },
    'zh-CN': {
      short: {
        year: 'numeric', month: 'numeric', day: 'numeric'
      },
      long: {
        year: 'numeric', month: 'numeric', day: 'numeric',
        hour: 'numeric', minute: 'numeric', hour12: true
      }
    }
  }
//然后再new的时候引入
   const i18n = new VueI18n({
    dateTimeFormats,
     locale:  store.state.currentLang, // 语言标识,默认汉语,
     messages: {
       'en-US': require("./assets/lang/en"),
       'zh-CN': require("./assets/lang/zh-CN"),
       'zh-TW': require("./assets/lang/zh-TW")
     }
   });
//////////////////////////多语言
```
日期格式設置配置在main.js里太占地方，像我一样有点强迫症的可以进行如下操作
新建一个文件，我这里命名为format.ts
可以把dateTimeFormats，numberFormats等都写在这里
```
export default {
    //日期格式
    dateFormat:{
        'en-US': {
            short: {
              year: 'numeric', month: 'numeric', day: 'numeric'
            },
            long: {
              year: 'numeric', month: 'numeric', day: 'numeric',
              hour: 'numeric', minute: 'numeric',second:'numeric'
            }
          },
          'zh-CN': {
            short: {
              year: 'numeric', month: 'numeric', day: 'numeric'
            },
            long: {
              year: 'numeric', month: 'numeric', day: 'numeric',
              hour: 'numeric', minute: 'numeric', hour12: true
            }
          }
    },
    //数字格式
    numberFormat:{
        
    }


  }
```
然后再main.js引入
```
import i18nFormats from './assets/lang/format'

   const i18n = new VueI18n({
    dateTimeFormats : i18nFormats.dateFormat,
    numberFormats:i18nFormats.numberFormat,
     locale:  store.state.currentLang // 语言标识,默认汉语
    ....
   });
```
接下来就可以在组件中应用了
```
<p v-text="$d(new Date(),'long',datelang)"></p>

export default {
    name:'User',
    data(){
        return{
            datelang:this.$store.lang
        }
    },
    methods:{
        changeLang(lang){
            this.$i18n.locale = lang;
            this.datelang = lang == 'zh-TW'?'en-US':lang;
            this.$store.commit('updateLang',lang);
        }
    }
}
</script>
```
中文显示
![image.png](https://upload-images.jianshu.io/upload_images/18030682-664e3314a2d7c3cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
英文、繁体
![image.png](https://upload-images.jianshu.io/upload_images/18030682-4c78aea8e263f1cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

api地址：[https://kazupon.github.io/vue-i18n/zh/guide](https://kazupon.github.io/vue-i18n/zh/guide)

#延迟加载
初始加载时一次加载所有翻译文件是过度和不必要的，这里我们就要用到懒加载还是先按需加载语言文件
main.js改造成这样就可以了
```
//////////////////////////多语言

   const i18n = new VueI18n({
    dateTimeFormats : i18nFormats.dateFormat,
    numberFormats:i18nFormats.numberFormat,
     locale:  store.state.currentLang // 语言标识,默认汉语,
   });

   
const loadedLanguages = [] // 我们已經加載的语言
function setI18nLanguage (lang) {
  i18n.locale = lang
  store.commit('res_lang',lang);
  return lang
}

export function loadLanguageAsync (lang) {
  if (i18n.locale !== lang) {
    if (!loadedLanguages.includes(lang)) {
      /* webpackChunkName: "lang-[request]" */ 
      return import(`@/assets/lang/${lang}`).then(msgs => {
        i18n.setLocaleMessage(lang, msgs)
        loadedLanguages.push(lang)
        return setI18nLanguage(lang)
      })
    }
    return Promise.resolve(setI18nLanguage(lang))
  }
  return Promise.resolve(lang)
}

router.beforeEach((to, from, next) => {
  const lang = to.params.lang || 'zh-TW'
  loadLanguageAsync(lang).then(() => {next()})
})

//////////////////////////
```

项目地址：
vue2.x：[https://github.com/momoSph/Vue2.x-ElementUI](https://github.com/momoSph/Vue2.x-ElementUI)
vue3.x：[https://github.com/momoSph/Vue3.x-ElementUI](https://github.com/momoSph/Vue3.x-ElementUI)