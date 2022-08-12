1. 安装
在命令行执行：
```
npm install --save-dev vue-i18n-extract
```
2. 设置package.json
在package.json里添加如下设置：
```
{
  "scripts": {
    "i18n_ext":"vue-i18n-extract report  -v './src/pages/**/*.vue' './src/components/*.vue' -l './src/assets/lang/*.json'",
    "vue-i18n-extract": "npm run i18n_ext"
  }
}
```
然后 运行
```
npm run vue-i18n-extract
```
就会以表格的形式打印出语言文件中缺少的翻译字段，如下图
![image.png](https://upload-images.jianshu.io/upload_images/18030682-67ae283ddc11fdf0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

目前还不知道怎么把缺少的字段自动提取到json文件中、、、有没有知道怎么自动提取翻译字段的大神指教一下？
参考地址：
[https://github.com/pixari/vue-i18n-extract](https://github.com/pixari/vue-i18n-extract)