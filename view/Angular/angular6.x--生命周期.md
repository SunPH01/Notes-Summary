按照生命周期执行的先后顺序，Angular生命周期接口如下所示
![image.png](https://upload-images.jianshu.io/upload_images/18030682-655e9403c55f4b7e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
生命周期顺序简写
在Angular通过构造函数创建组件/指令时，它调用这些生命周期钩子方法的顺序是：
* **ngOnChanges**：在ngOnInit之前，当数据绑定输入属性的值发生变化时。 
* **ngOnInit**：在第一次ngOnChanges之后。 (只调用一次)
* **ngDoCheck**：每次Angular变化检测时。 
* **ngAfterContentInit**：在外部内容放到组件内之后。  (只调用一次)
* **ngAfterContentChecked**：在放到组件内的外部内容每次检查之后。 
* **ngAfterViewInit**：在初始化组件视图和子视图之后。  (只调用一次)
* **ngAfterViewChecked**：在组件视图和子视图检查之后。 
* **ngOnDestroy**：在Angular销毁组件/指令之前。 

除此之外，一些组件还提供了自己的生命周期钩子。例如router有routerOnActivate。
![image.png](https://upload-images.jianshu.io/upload_images/18030682-8dbd06ffe7c3061a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
打印结果：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-c732c2a209978ac6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
只要视图数据改变，就会触发：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-8e203c002d42390b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)