# 一、安装配置

在应用商店搜索Path Intellisense然后安装，安装之后需要配置
![image.png](https://upload-images.jianshu.io/upload_images/18030682-9951997e1ae23f84.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/18030682-b003408b0f3f855f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


点击设置按钮，在下拉列表中点击--扩展设置  选项，然后打开setting.json文件
![image.png](https://upload-images.jianshu.io/upload_images/18030682-86aa9f077237f59b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在文件中添加配置
```
"path-intellisense.mappings": {
    "@": "${workspaceRoot}/src",
    "/": "${workspaceRoot}/"
  }
```
到这里还没有结束，还要在package.json的同级文件添加jsconfig.json文件，加上以下配置：
```
{
  "compilerOptions": {
    "target": "ES6",
    "module": "commonjs",
    "allowSyntheticDefaultImports": true,
    "baseUrl": "./",
    "paths": {
      "@/*": [
        "src/*"
      ]
    }
  },
  "exclude": [
    "node_modules"
  ]
}
```
全都配置完会后发现在vue文件里还不会出现路径提示，查了资料有的说是插件版本的问题
# 二、降低版本
同样在插件里点击设置按钮
![image.png](https://upload-images.jianshu.io/upload_images/18030682-84be05d8ba99d2d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
下拉列表里选择--‘安装另一个版本...’,然后选择1.4.2，重启vscode就可以了
![image.png](https://upload-images.jianshu.io/upload_images/18030682-c2d4992bb03fd1c5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)