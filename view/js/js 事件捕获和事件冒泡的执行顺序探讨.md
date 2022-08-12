##### 事件的执行顺序
先看如下代码：
```html
    <div>
        <ul>
            <li>冒泡/捕获</li>
        </ul>
    </div>
<script>
    var html = document.documentElement;
    var body = document.body;
    var div = body.querySelector('div');
    var ul = body.querySelector('ul');
    var li = body.querySelector('li');

    ul.addEventListener('click',callback,true);
    li.addEventListener('click',callback,true);
    div.addEventListener('click',callbackdiv,true);
    body.addEventListener('click',callback,true);
    html.addEventListener('click',callback,true);

   ul.addEventListener('click',callback2);
    li.addEventListener('click',callback2);
    div.addEventListener('click',callbackdiv2);
    body.addEventListener('click',callback2);
    html.addEventListener('click',callback2);

    function callback(event){
        var target = event.currentTarget;
        console.log(target.tagName + '--事件捕获');
    }
    function callback2(event){
        var target = event.currentTarget;
        console.log(target.tagName + '--事件冒泡');
    }
    function callbackdiv(event){
        //event.stopPropagation();//阻止事件冒泡
        console.log('div callback--事件捕获');
    }
    function callbackdiv2(event){
        //event.stopPropagation();//阻止事件冒泡
        console.log('div callback--事件冒泡');
    }
</script>
```
在问问题之前先补充一个知识点：
`element.addEventListener(event, function, useCapture)`
**第三个参数 useCapture：**可选。布尔值，指定事件是否在捕获或冒泡阶段执行。可能值：true - 事件句柄在捕获阶段执行；false-  默认。事件句柄在冒泡阶段执行。

**那么问题来了，点击Li，js的执行顺序是什么？**
结果如下图：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-ebf83b7ec5242937.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

总结就是：先捕获，后冒泡，捕获从上到下，冒泡从下到上（形象点说法：捕获像石头沉入海底，冒泡则像气泡冒出水面）

**假如放开callbackdiv2的 event.stopPropagation();  结果又会输出什么？**
结果如下图：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-3ce9f6f2687a5a5f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

callbackdiv2 监听的是div的事件冒泡，所以当callbackdiv2被触发时event.stopPropagation()阻止了事件的冒泡，事件冒泡就不会继续往上传播了。

**接下来我们放开callbackdiv的 event.stopPropagation();  结果又会输出什么？**(至此，两个阻止冒泡的注释已经全部放开)
结果如下图：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-fccff8ac44ced3a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

callbackdiv监听的是div的时间捕获，我们知道事件的顺序是先捕获后冒泡，所以放开callbackdiv的 event.stopPropagation(); 后，在时间捕获传播到div的时候就会阻止继续往下传播了。

**通过以上我们可知，event.stopPropagation();可以阻止捕获和冒泡阶段中当前事件的进一步传播。**

看到这里我想大家对事件捕获和事件冒泡的执行顺序已经有了一个大概的了解，接下来我又做了几个有趣的实验，我们一起来看一下。

此为html代码：
```
<body>
	<ul class="ulclass">
        <li class="li1class">1</li>
    </ul>
</body>

```
##### 实验一：
```
<script type="text/javascript">
    var $El = document.querySelector(".ulclass");
    var $El1 = document.querySelector(".li1class");

	//冒泡
    $El.addEventListener("click",function(){
        alert("ul");
    },false);
	//捕获 
    $El1.addEventListener("click",function(){
        alert("li");
    },true);
</script>
```
实验分析：冒泡执行过程中是从最内层元素 li 开始往外冒泡，在 经过 li 一层元素后到达 ul 被触发。而捕获过程则一共经历 window-> document -> body -> ul 四层元素后到达 li ，被触发，看起来好像是应该先弹出 ul ，然后再弹出 li。

实验结果：点击 li 内的 1 的时候，第一次弹出 “li”，第二次弹出 “ul” 。

##### 实验二

```
<script type="text/javascript">
    var $El = document.querySelector(".ulclass");
    var $El1 = document.querySelector(".li1class");
	//捕获
    $El.addEventListener("click",function(){
        alert("ul");
    },true);

	//冒泡
    $El1.addEventListener("click",function(){
        alert("li");
    },false);
</script>

```
实验分析：如果上述猜想是正确的，那么这次的执行结果就应该是先弹出 “ul” 再弹出 “li”。

实验结果：先弹出 “ul” 再弹出 “li”。

##### 实验四
再来试一下两个事件都冒泡的情况：

```
//冒泡
$El.addEventListener("click",function(){
    alert("ul");
},false);

//冒泡
$El1.addEventListener("click",function(){
    alert("li");
},false);
```
实验分析：如果两个都为冒泡的情况下，点击后应从最里面的元素往外冒，也就是先弹出 li , 再弹出 ul.
实验结果：无误。
然后将两个事件都改成捕获，则先弹出 ul 再 弹出 li ，与预期完全一样。

##### 实验五
这里把本来在 ul 上绑定的事件放到 li 上，也就是 li 绑定两个事件，一个为冒泡，一个为捕获。

```
var $El = document.querySelector(".ulclass");
var $El1 = document.querySelector(".li1class");
//冒泡
$El1.addEventListener("click",function(){
    alert("冒泡");
},false);

//捕获
$El1.addEventListener("click",function(){
    alert("捕获");
},true);

```
实验分析：按照之前的猜测，应该是先弹出捕获，然后弹出冒泡。（如果是这样的话我也不会写这么多了...） 实验结果：先弹出了 冒泡，然后弹出了捕获。

为什么？？

**猜想：在同一个元素的绑定事件中，冒泡和捕获没有次序之分，遵循Javascript代码的先后顺序执行。**

##### 实验六
为了进一步证实这个猜测...我们将冒泡和捕获的位置调换一下
```
var $El = document.querySelector(".ulclass");
var $El1 = document.querySelector(".li1class");
//捕获
$El1.addEventListener("click",function(){
    alert("捕获");
},true);

//冒泡
$El1.addEventListener("click",function(){
    alert("冒泡");
},false);
```
实验结果： 先捕获，后冒泡。

看似是5的猜测是对的，但是在重复实验五 的时候，li下一级有一个元素 a ，直接点击 li 很符合上面的推测，但是在点击a元素的时候，先弹出了捕获，后冒泡。

为什么？？

**猜想：在元素上同时绑定捕获事件和冒泡事件，如果通过此元素的子级元素触发，则优先触发捕获事件，若通过该元素自身触发，那么冒泡和捕获没有次序之分，则按照Javascript代码的先后顺序执行。**

附：

冒泡经常用于需要绑定很多事件的时候，给他们父级元素绑定一个事件，可以有效的提高代码执行效率。 比如：
```
<body>
<div class="">
    <ul class="ulclass">
        <li class="li1class">1</li>
        <li class="li1class">1</li>
        <li class="li1class">1</li>
        ...
        ...
        <li class="li1class">1</li>
    </ul>
</div>
</body>
```

假如此处有100个li，每个li元素上都绑定一个事件的话就验证影响了执行效率，那么就可以在ul 上绑定一个事件：
```
<script type="text/javascript">
    var $El = document.querySelector(".ulclass");
    //冒泡
    $El.addEventListener("click",function(){
        console.log(event.target);
    },false);
</script>

```
其中 event.target 就是获取触发来源。