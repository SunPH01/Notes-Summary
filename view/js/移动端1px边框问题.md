### 为什么1px变粗了？

  为什么移动端CSS里面写了1px，实际上看起来比1px粗；了解设备物理像素和逻辑像素的同学应该很容易理解，其实这两个`px`的含义其实是不一样的，UI设计师要求的1px是指设备的物理像素1px，而CSS里记录的像素是逻辑像素，它们之间存在一个比例关系，可以用javascript中的`window.devicePixelRatio`来获取，也可以用媒体查询的`-webkit-min-device-pixel-ratio`来获取。当然，比例多少与设备相关。
在手机上border无法达到我们想要的效果。这是因为devicePixelRatio特性导致，iPhone的devicePixelRatio==2，而border-width: 1px描述的是设备独立像素，所以，border被放大到物理像素2px显示，在iPhone上就显得较粗。

  移动端开发常需要在html的header里添加如下一句：
  `<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">`
  这句话定义了本页面的**viewport**的宽度为设备宽度，初始缩放值和最大缩放值都为1，并禁止了用户缩放。

  你的疑问可能来了，老是看到**viewport**，除了知道中文名叫[**视口**](https://www.jianshu.com/p/7c5fdf90c0ef)
，到底是啥意思？其实，它就是设备屏幕上能用来显示我们网页内容的那一块区域，具体点就是浏览器或app中的webview用来显示网页的那部分区域，但viewport又不局限于浏览器可视区域的大小，可能大也可能小。体现在用户是否缩放了屏幕。

  心细的同学应该都有感觉，meta标签中常设置`user-scalable=no`也就是禁止用户缩放，那用户缩放到底会造成什么影响呢？ 其实也就是显示上的变化。缩放一倍，CSS像素(逻辑像素)所代表的物理像素也就缩放了一倍，即设备物理像素和设备独立像素的比例增大(减小)了一倍。

  或许你已经明白1px变粗的原因是啥了， **viewport的设置和屏幕物理分辨率是按比例而不是相同的. 移动端window对象有个devicePixelRatio属性, 它表示设备物理像素和css像素的比例, 在retina屏的iphone手机上, 这个值为2或3, css里写的1px长度映射到物理像素上就有2px或3px那么长。**

# 解决方案

##### 1\. viewport + rem 方案

  该方案是对上述方案的优化，整体思路就是利用`viewport + rem + js` 动态的修改页面的缩放比例，实现小于1像素的显示。在页面初始化时，在头部引入原始默认状态如下：

```
<meta http-equiv="Content-Type" content="text/html;charset=UTF-8">  
<meta name="viewport" id="WebViewport" content="initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no"> 

```

接下来的任务就是js的动态修改缩放比 以及 实现rem根元素字体大小的设置。

```
var viewport = document.querySelector("meta[name=viewport]");
var scale = 1 / window.devicePixelRatio;
viewport .content = 'initial-scale=' + scale + ',maximum-scale=' + scale + ', minimum-scale=' + scale;

var docEl = document.documentElement;
//px:rem = 1: 100      1rem = 100px
var fontsize = 100 * (docEl.clientWidth / 640) + 'px';//640为设计稿的宽度，可根据情况修改
docEl.style.fontSize = fontsize;
```

##### 2.  @media + transfrom 

```css
.border{
  position: relative;
}
.border::after {
            content: '';
            width: 200%;
            height: 200%;
            position: absolute;
            top: 0;
            left: 0;
            border: .01rem solid #bfbfbf;
            border-radius: .04rem;
            -webkit-transform: scale(0.5, 0.5);
            transform: scale(0.5, 0.5);
            -webkit-transform-origin: top left;
}
/* 2倍屏 */
@media only screen and (-webkit-min-device-pixel-ratio: 2.0) {
            .border::after {
                -webkit-transform: scale(0.5, 0.5);
                transform: scale(0.5, 0.5);
            }
        }

/* 3倍屏 */
@media only screen and (-webkit-min-device-pixel-ratio: 3.0) {
            .border::after {
                -webkit-transform: scale(0.33, 0.33);
                transform: scale(0.33, 0.33);
            }
}
```