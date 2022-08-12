# 1. 编程语言的不同
angular1.x是JavaScript，可以直接在浏览器中运行，而angular2是TypeScript,只有经过编译后才能在浏览器中运行。
# 2. 控制器Controller的改变
在1 中它是非常重要的，用于封装业务控制逻辑，而2中这个，开发模式改为了组件化、模块化开发，去除了它。
# 3. 作用域Scope
```$scope```是一个相当强大又相当可怕的东西，一言不合就让开发者自己```$apply```。ng2响应社区的强烈吐槽，删除（或者说隐藏） 了```$scope```这个东西，开发者不再需要感知到它的存在，另外，由于ng2引入了zone.js，所以即使在各种回调函数中修改数据模型也不需要手 动```$apply()```了。
# 4. 指令
在2中指令使用符号，例如：ngFor、*ngIf；指令不能直接嵌套使用，所以它增加了一个<ng-container></ng-container>标签来包裹指令。
# 5. 组件生命周期
1中没有明确的生命周期机制。在2中有精心设计的生命周期：

* ngOnlnit:当Angular初始化完成数据绑定的输入属性后，用来初始化指令或者组件;
* ngDoCheck:用来检测所有变化（无论是Angular本身能检测还是无法检测的），并作出相应行动。在每次执行“变更检测”时被调用;
* ngAfterContentInit:当Angular把外来内容投影进自己的视图之后调用。
* ngAfterContentChecked: 当Angular检查完那些投影到自己视图中的外来内容的数据绑定之后调用。
* ngAfterViewInit :在Angular创建完组件的视图后调用。
* ngAfterViewChecked:在Angular检查完组件视图中的绑定后调用。
* ngOnChanges :当Angular设置了一个被绑定的输入属性后触发。该回调方法会收到一个包含当前值和原值的changes对象。
* ngOnDestroy:在Angular销毁指令或组件之前做一些清理工作，比如退订可观察对象和移除事件处理器，以免导致内存泄漏。

**生命周期的顺序**
* ngOnChanges：当被绑定的输入属性的值发生变化时调用，首次调用一定会发生在ngOnInit之前。
* ngOnInit：在第一轮ngOnChanges完成之后调用。
* ngDoCheck：在每个Angular变更检测周期中调用。
* ngAfterContentInit：当把内容投影进组件之后调用。
* ngAfterContentChecked：每次完成被投影组件内容的变更检测之后调用。
* ngAfterViewInit：初始化完组件及其子视图之后调用。
* ngAfterViewChecked：每次做完组件视图和子视图的变更检测之后调用。
* ngOnDestroy：当Angular每次销毁指令/组件之前调用。

# 6. 绑定方式
ng-model---[(ngModel)]、ng-click---(click)...

# 7. 大幅度演进了脏值检测机制
ng1.x里面的脏值检测机制的运行效率是非常差的。在ng2中，大幅度演进了这一机制，不仅引入了单向绑定，还引入了各种绑定策略，例如：只检测一次、利用JIT动态生成脏值检测代码等等。毫无疑问，有了这些工具之后，数据绑定效率不再是问题。