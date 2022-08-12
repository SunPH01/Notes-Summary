# 一、MVC
MVC是Model-View- Controller的简写。即模型-视图-控制器。软件可以分成三个部分。
![image.png](https://upload-images.jianshu.io/upload_images/18030682-cfea6a26ae0a2036.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 视图（View）：用户界面。
* 控制器（Controller）：业务逻辑
* 模型（Model）：数据保存
各部分之间的通信方式如下。
![image.png](https://upload-images.jianshu.io/upload_images/18030682-287854c0b10e9e98.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* View 传送指令到 Controller
* Controller 完成业务逻辑后，要求 Model 改变状态
* Model 将新的数据发送到 View，用户得到反馈
使用MVC的目的就是将M和V的代码分离。所有通信都是单向的。

# 二、MVVM
MVVM是Model-View-ViewModel的简写。即模型-视图-视图模型。【模型】指的是后端传递的数据。【视图】指的是所看到的页面。【视图模型】mvvm模式的核心，它是连接view和model的桥梁。
![image.png](https://upload-images.jianshu.io/upload_images/18030682-8d87c3b7e3d7b20e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
它采用双向绑定（data-binding）：View的变动，自动反映在 ViewModel，反之亦然。
在MVVM的框架下视图和模型是不能直接通信的。它们通过ViewModel来通信，ViewModel通常要实现一个observer观察者，当数据发生变化，ViewModel能够监听到数据的这种变化，然后通知到对应的视图做自动更新，而当用户操作视图，ViewModel也能监听到视图的变化，然后通知数据做改动，这就实现了数据的双向绑定。