###一、安装最新版本的nodejs
注意：请先在终端/控制台窗口中运行命令 node -v 和 npm -v， 来验证一下你正在运行 node 6.9.x 和 npm 3.x.x 以上的版本。 更老的版本可能会出现错误，更新的版本则没问题。

###二、全局安装 Angular CLI 脚手架工具

**1.使用 npm命令安装**
`npm install -g @angular/cli`

**2.使用 cnpm命令安装**
`cnpm install -g @angular/cli`

**3.查询版本号**
 `ng   -v`

清除安装包缓存：`npm cache clean --force`
![image.png](https://upload-images.jianshu.io/upload_images/18030682-6983a6b73563245d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**angular cli**
![image.png](https://upload-images.jianshu.io/upload_images/18030682-29759e8fdc100234.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果想通过手机访问，需要这样运行：
`ng serve proName --host  xxx.xxx.x.xxx（自己的ip地址）`

###三、创建项目
1. 打开cmd找到你要创建项目的目录
2. 创建项目，ng new 项目名称  创建一个项目
`ng new my-app`
3. 进入刚才创建的项目里面启动服务
```
cd my-app

cnpm install   //安装依赖

ng serve --open 
```

Vscode运行项目：
1. 首先下载Debugger for Chrome插件，进入扩展搜索名称即可；
2. Vscode打开项目，然后点击调试按钮，选择chrome,然后点击设置按钮，即可打开launch.json文件            
         ![image.png](https://upload-images.jianshu.io/upload_images/18030682-825b41cddc017ed6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
把本地launch.json文件里面的内容先删光，然后把以下内容贴进去：
```
{
"version": "0.2.0",
"configurations": [
{ 
"type": "chrome", 
"request": "launch", 
"name": "Chrome", 
"url": "http://localhost:4200", 
"webRoot": "${workspaceRoot}" 
}
]
}
```
3. 配置完成后，在终端输入 ng serve即可运行项目

###四、目录结构分析 
![image.png](https://upload-images.jianshu.io/upload_images/18030682-7fdba535d1fcb0a1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**1. app.module.ts、组件分析**
定义AppModule，这个根模块会告诉Angular如何组装该应用。 目前，它只声明了AppComponent。 稍后它还会声明更多组件。
```
@NgModuel({
declarations: [], // 用到的组件，指令，管道
providers: [], // 依赖注入服务 
imports: [], // 导入需要的模块
exports: [], // 导出的模块，跨模块交流
entryComponents: [] // 需提前编译好的模块
bootstrap: [] // 设置根组件
})
```

```
//Angular 模块类描述应用的部件是如何组合在一起的。 每个应用都至少有一个 Angular 模块，也就是根模块，
// 用来引导并运行应用。 你可以为它取任何名字。常规名字是AppModule。  也就是 app.module.ts文件
/*引入组件*/

import { BrowserModule } from '@angular/platform-browser';  /*BrowserModule，浏览器解析的模块*/
import { NgModule } from '@angular/core';  /*angualrjs核心模块*/
import { FormsModule } from '@angular/forms';  /*表单数据绑定 表单验证需要的模块*/
i
import { AppComponent } from './app.component'; /*根组件*/
 @NgModule接受一个元数据对象，告诉 Angular 如何编译和启动应用。*/

@NgModule({
  declarations: [  /*引入当前项目运行的的组件*/
    AppComponent
  ],
  imports: [ /*引入当前模块运行依赖的其他模块*/
    BrowserModule,
    FormsModule
  ],
  providers: [],  /*定义的服务  回头放在这个里面*/
  bootstrap: [AppComponent]  /* 指定应用的主视图（称为根组件） 通过引导根AppModule来启动应用  ，这里一般写的是根组件*/
})

export class AppModule { }
```

**2. [创建angualr组件]([<u>https://github.com/angular/angular-cli</u>](https://github.com/angular/angular-cli)
)**
创建组件：`ng g component components/header`
组件内容详解：
```
import { Component, OnInit } from '@angular/core';  /*angular核心*/

@Component({
  selector: 'app-header',  /*使用组件的名称*/
  templateUrl: './header.component.html', /*html模板*/
  styleUrls: ['./header.component.css'] /*css样式*/
})
export class HeaderComponent implements OnInit {  
  constructor() {  /*构造函数*/
  }
  ngOnInit() {  /*初始化加载的生命周期函数*/

  }
}
```

**3.angular.json的配置**
在 angular.json 的顶级，一些属性用于配置工作区，其中的 projects 区则包含其余的针对每个项目的配置项。
1. version：该配置文件的版本。
2. newProjectRoot：用来创建新工程的位置。绝对路径或相对于工作区目录的路径。
3. defaultProject：当命令中没有指定参数时，要使用的默认工程名。当你用 ng new 在新的工作区中创建新应用时，该应用就会一直作为此工作区的默认项目，除非你到这里修改它。
4. projects：对于工作区中的每个项目（库、应用、e2e 测试）都会包含一个子分区，子分区中是每个项目的配置项。

**项目配置选项**
每个项目的 projects:<project_name> 下都有以下顶级配置属性。
```
"app": {
"root": "",
"sourceRoot": "src",
"projectType": "application",
"prefix": "app",
"schematics": {},
"architect": {}
}
```
![image.png](https://upload-images.jianshu.io/upload_images/18030682-0d30f71000472c97.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**项目工具的配置选项**
建筑师（Architect）是指 CLI 用来根据所提供的配置执行复杂任务（如编译和测试运行）的工具。 architect 部分包含一组建筑目标。很多目标都对应于运行它们的 CLI 命令。使用 ng run 命令可以运行一些额外的预定义目标，并可以定义自己的目标。
每个目标对象都指定了该目标的 builder，它是建筑师所运行工具的 npm 包。此外，每个目标都有一个 options 部分，用于配置该目标的默认选项，configurations 部分可以为目标命名并指定备用配置。
```
"architect": {
"build": { },
"serve": { },
"e2e" : { },
"test": { },
"lint": { },
"extract-i18n": { },
"server": { },
"app-shell": { }
}
```
* **architect/build** 节会为 ng build 命令的选项配置默认值。
* **architect/serve** 节会覆盖构建默认值，并为 ng serve 命令提供额外的服务器默认值。除了 ng build 命令的可用选项之外，还增加了与开发服务器有关的选项。
* **architect/e2e** 节覆盖了构建选项默认值，以便用 ng e2e 命令构建端到端测试应用。
* **architect/test** 节会覆盖测试时的构建选项默认值，并为 ng test 命令提供额外的默认值以供运行测试。
* **architect/lint** 节为 ng lint 命令配置了默认值，用于对项目源文件进行代码分析。 Angular 默认的 linting 工具为 TSLint。
* **architect/extract-i18n** 节为 ng xi18n 命令所用到的 ng-xi18n 工具选项配置了默认值，该命令用于从源代码中提取带标记的消息串，并输出翻译文件。
* **architect/server** 节用于为使用 ng run <project>:server 命令创建带服务器端渲染的 Universal 应用配置默认值。
* **architect/app-shell** 部分使用 ng run <project>:app-shell 命令为渐进式 Web 应用（PWA）配置创建应用外壳的默认值。

一般来说，可以为 CLI 参考手册中列出的每个命令配置相应的默认值。注意，配置文件中的所有选项都必须使用 camelCase，而不是 dash-case。