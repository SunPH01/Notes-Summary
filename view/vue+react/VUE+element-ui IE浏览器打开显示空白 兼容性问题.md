因为IE浏览器版本的问题，IE浏览器不支持ES6，所以就出现了promise未定义的问题

需要下载babel-polyfill  在main.js中引用 babel-polyfill
1.安装
 ```npm install --save babel-polyfill```
2.导入
```import  'babel-polyfill'```
3.修改配置
打开build文件夹下的webpack.base.conf.js
找到以下代码:
```
module.exports = {
  context: path.resolve(__dirname, '../'),
  entry: {
    app: './src/main.ts'
  },
...
}
```
修改为
```
module.exports = {
  context: path.resolve(__dirname, '../'),
  entry: {
    app: ["babel-polyfill",'./src/main.ts']
  },
...
}
```
就可以啦~

项目地址：
vue2.x：[https://github.com/momoSph/Vue2.x-ElementUI](https://github.com/momoSph/Vue2.x-ElementUI)
vue3.x：[https://github.com/momoSph/Vue3.x-ElementUI](https://github.com/momoSph/Vue3.x-ElementUI)