防抖和节流严格算起来应该属于性能优化的知识，但实际上遇到的频率相当高，处理不当或者放任不管就容易引起浏览器卡死。

先看一个滚动条监听的例子：
很多网站会提供一个返回顶部的按钮
![image.png](https://upload-images.jianshu.io/upload_images/18030682-9884e418a1ac9ed1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这个功能的核心内容是----监听浏览器的滚动事件，返回当前滚动条与顶部之间的距离
具体的代码如下所示：
```javascript
function showTop  () {
    var scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
　　console.log('scrollTop：' + scrollTop);
}
window.onscroll  = showTop
```
在滚动条滚动时一直会打印：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-b1d5495503bc419c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这个事件的执行频率很高，非常消耗浏览器的性能，在实际的运行中我们并不需要这么高频率的执行反馈。
为了避免这种场景，常见的优化方式有：防抖和节流

### 防抖
JS防抖的基本思路是：**规定一个期限时间，在首次触发事件时，不立即执行回调函数，而是在期限时间后再执行，如果期限时间内回调函数被重复执行，则期限时间重新计时。**
在这里要用到setTimeOut()函数，判断期限值范围内该执行函数是否执行过，则需要一个变量来保存执行的结果。代码实现如下所示：

```javascript
function debounce (fn, delaytime) {
  let timer; 
  return function() {
    if (timer) {
      //进入这里说明当前存在一个执行过程，并且下一个相同事件又被触发了，故取消当前的执行过程,执行最新的
      clearTimeout(timer)
    }
    timer = setTimeout(fn, delaytime)
  }
}

function showTop () {
  var scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
　  console.log('scrollTop：' + scrollTop);
}
window.onscroll = debounce(showTop, 1000)
```
运行结果显示，当前滚动停止1s后，才会打印当前的滚动条位置，而且每次值打印当前的位置。
![image.png](https://upload-images.jianshu.io/upload_images/18030682-013b1b326173ebda.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**防抖的缺点**
防抖的缺点在于，事件执行完延时期间没有重复触发事件，才能再次执行该事件，若是期限范围内，不停的触发该事件，则回调函数不会执行。
防抖的应用场景
（1）用户在输入框中输入一串字符，只会在最后一次输入完成后再去执行查询ajax请求，这样可以有效减少请求的次数，节约请求资源。
（2）window的scroll、resize事件，不断地滚动、或调整浏览器窗口大小时触发的对应事件，可以允许其只触发一次。

### 节流
JS中节流的基本思路是：**规定一个期限时间，在该时间内，触发事件的回调函数只能执行一次，如果期限时间内回调函数被多次触发，则只有一次能生效。**

```javascript
function throttle(fn, delaytime) {
  let last_time
  let timer = null
  return function () {
    let cur_time = new Date().getTime()
    if (last_time && cur_time < last_time + delaytime) { //若为真，则表示上次执行过，且在期限值范围内
      clearTimeout(timer)
      timer = setTimeout(() => {
        fn();
        last_time = cur_time
      }, delaytime)
    } else {
      last_time = cur_time;
      fn();
    }

  }
}

function show_scrollPosition() {
  var scrollPosition = document.body.scrollTop || document.documentElement.scrollTop;
  console.log("scrollPosition：", scrollPosition);
}

window.onscroll = throttle(show_scrollPosition, 1000)
```
节流的道理其实就是让函数执行一次后，在某个时间段内暂时失效，过了这个时间段后再重新激活。

**节流的应用场景**
（1）在页面无限加载的情况下，需要用户再滚动页面时，每隔一定时间发起一次ajax请求，而不是再用户停下滚动时再去请求数据；
（2）监听滚动事件，比如是否滑倒底部自动加载更多，则用节流来判断；