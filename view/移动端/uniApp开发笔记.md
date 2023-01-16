## 一、创建 uni-app + vue3 + ts的项目

1.创建

```
vue create -p dcloudio/uni-preset-vue#vue3 uni-app-vue3-demo
// 需要等待片刻，安装依赖
> 请选择 uni-app 模板: 默认模板(TypeScript)
// 等待依赖安装完成，项目就创建好了
```

2.删除不需要的依赖



## 二、配置

### 1.uni-app添加eslint检查

```
vue add @vue/eslint
```

回车后，开始安装包，需要一会，安装完成后会出现选择项：

```
? Pick an ESLint config: Prettier
? Pick additional lint features: Lint on save
```

选择之后继续下载依赖包，安装完成就可以了。

在项目的根目录自动添加了`.eslintrc.js`配置文件，按照自己的规则配置。

`package.json`文件里也自动添加了`"lint": "vue-cli-service lint",`命令，校验规则配置好之后就可以执行`yarn lint`检查了。



