友情提示：想要更好的理解这篇文章，建议先读一下[viewport深入理解](https://www.jianshu.com/p/7c5fdf90c0ef)
，了解一下视口。

假设我们的设计稿尺寸为750 * 1340。html5设计稿尺寸以及前端与设计之间协作流程一般分为下面两种。
## 方法一
引入：页面开头处引入下面这段代码，用于动态计算font-size

```
(function(doc, win) {
    var docEl = doc.documentElement,
        isIOS = navigator.userAgent.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/),
        dpr = isIOS ? Math.min(win.devicePixelRatio, 3) : 1,
        dpr = window.top === window.self ? dpr : 1, //被iframe引用时，禁止缩放
        scale = 1 / dpr,
        resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize';
    docEl.dataset.dpr = dpr;
    var metaEl = doc.querySelector("meta[name='viewport']");
    metaEl.content = 'width=device-width,initial-scale=' + scale + ',maximum-scale=' + scale + ', minimum-scale=' + scale;

    var recalc = function() {
        var width = docEl.clientWidth;
        if (width / dpr > 750) {//750为设计稿的宽度，此处应根据实际情况改变
            width = 750 * dpr;
        }
        // 乘以100，px : rem = 100 : 1
        docEl.style.fontSize = 100 * (width / 750) + 'px';
    };

    if (!doc.addEventListener) return;
    win.addEventListener(resizeEvt, recalc, false);
    doc.addEventListener('DOMContentLoaded', recalc, false);
})(document, window);

//或者
(function(doc, win) {
    var docEl = doc.documentElement,
        resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
        recalc = function() {
          var clientWidth = docEl.clientWidth;
          var clientHeight = docEl.clientHeight;
          if (!clientWidth || !clientHeight) return;

          if (window.orientation === 90 || window.orientation === -90) {
             docEl.style.fontSize = 200 * (clientHeight / 750) + 'px';
          } else{
             docEl.style.fontSize = 200 * (clientWidth / 750) + 'px';
          }
        };
    if (!doc.addEventListener) {
        return;
    }
    win.addEventListener(resizeEvt, recalc, false);
    doc.addEventListener('DOMContentLoaded', recalc, false);
})(document, window);
```
**css使用：**
未引入前
```
body {
    width: 750px;
    height: 640px;
}
```
引入后：除以100并将px换成rem
```
body {
    width: 7.5rem;
    height: 6.4rem;
}
```
换算的依据是
```
// 乘以100，px : rem = 100 : 1
var recalc = function() {
    var width = docEl.clientWidth;
    if (width / dpr > 750) {
        width = 750 * dpr;
    }
    // 乘以100，px : rem = 100 : 1
    docEl.style.fontSize = 100 * (width / 750) + 'px';
};
```
## 方法二 (推荐)
引入：页面开头处引入下面这段代码，用于动态计算font-size，或者单独放入一个文件，引入文件也可以

```
(function(win, lib) {
    var doc = win.document;
    var docEl = doc.documentElement;
    var metaEl = doc.querySelector('meta[name="viewport"]');
    var flexibleEl = doc.querySelector('meta[name="flexible"]');
    var dpr = 0;
    var scale = 0;
    var tid;
    var flexible = lib.flexible || (lib.flexible = {});

    if (metaEl) {
        var match = metaEl.getAttribute('content').match(/initial\-scale=([\d\.]+)/);
        if (match) {
            scale = parseFloat(match[1]);
            dpr = parseInt(1 / scale);
        }
    } else if (flexibleEl) {
        var content = flexibleEl.getAttribute('content');
        if (content) {
            var initialDpr = content.match(/initial\-dpr=([\d\.]+)/);
            var maximumDpr = content.match(/maximum\-dpr=([\d\.]+)/);
            if (initialDpr) {
                dpr = parseFloat(initialDpr[1]);
                scale = parseFloat((1 / dpr).toFixed(2));
            }
            if (maximumDpr) {
                dpr = parseFloat(maximumDpr[1]);
                scale = parseFloat((1 / dpr).toFixed(2));
            }
        }
    }

    if (!dpr && !scale) {
        var isAndroid = win.navigator.appVersion.match(/android/gi);
        var isIPhone = win.navigator.appVersion.match(/iphone/gi);
        var devicePixelRatio = win.devicePixelRatio;
        if (isIPhone) {
            // iOS下，对于2和3的屏，用2倍的方案，其余的用1倍方案
            if (devicePixelRatio >= 3 && (!dpr || dpr >= 3)) {
                dpr = 3;
            } else if (devicePixelRatio >= 2 && (!dpr || dpr >= 2)) {
                dpr = 2;
            } else {
                dpr = 1;
            }
        } else {
            // 其他设备下，仍旧使用1倍的方案
            dpr = 1;
        }
        scale = 1 / dpr;
    }

    docEl.setAttribute('data-dpr', dpr);
    if (!metaEl) {
        metaEl = doc.createElement('meta');
        metaEl.setAttribute('name', 'viewport');
        metaEl.setAttribute('content', 'initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
        if (docEl.firstElementChild) {
            docEl.firstElementChild.appendChild(metaEl);
        } else {
            var wrap = doc.createElement('div');
            wrap.appendChild(metaEl);
            doc.write(wrap.innerHTML);
        }
    }

    function refreshRem() {
        var width = docEl.getBoundingClientRect().width;
        // 适配平板
        if (width / dpr > 540) {
            width = 540 * dpr;
        }
        var rem = width / 10;
        docEl.style.fontSize = rem + 'px';
        flexible.rem = win.rem = rem;
    }

    win.addEventListener('resize', function() {
        clearTimeout(tid);
        tid = setTimeout(refreshRem, 300);
    }, false);
    win.addEventListener('pageshow', function(e) {
        if (e.persisted) {
            clearTimeout(tid);
            tid = setTimeout(refreshRem, 300);
        }
    }, false);

    if (doc.readyState === 'complete') {
        doc.body.style.fontSize = 12 * dpr + 'px';
    } else {
        doc.addEventListener('DOMContentLoaded', function(e) {
            doc.body.style.fontSize = 12 * dpr + 'px';
        }, false);
    }


    refreshRem();

    flexible.dpr = win.dpr = dpr;
    flexible.refreshRem = refreshRem;
    flexible.rem2px = function(d) {
        var val = parseFloat(d) * this.rem;
        if (typeof d === 'string' && d.match(/rem$/)) {
            val += 'px';
        }
        return val;
    }
    flexible.px2rem = function(d) {
        var val = parseFloat(d) / this.rem;
        if (typeof d === 'string' && d.match(/px$/)) {
            val += 'rem';
        }
        return val;
    }

})(window, window['lib'] || (window['lib'] = {}));
```
**css使用：**
未引入前
```
body {
    width: 750px;
    height: 640px;
}
```
引入后
```
@font-size-base: 75;
body {
    width: 750rem/@font-size-base;
    height: 640rem/@font-size-base;
}
```
换算依据：
```
function refreshRem() {
    var width = docEl.getBoundingClientRect().width;
    // 适配平板
    if (width / dpr > 540) {
        width = 540 * dpr;
    }
    var rem = width / 10;
    docEl.style.fontSize = rem + 'px';
    flexible.rem = win.rem = rem;
}
```
这边是用的less，如果您没有用less，就需要手动计算，当然也可以转化为px : rem = 100 : 1。 如果想转化为px : rem = 100 : 1，可以修改上面的refreshRem函数：
```
function refreshRem() {
    var width = docEl.getBoundingClientRect().width
    // 适配平板
    if (width / dpr > 750) {
      width = 750 * dpr
    }
    var rem = 100 * (width / 750)
    docEl.style.fontSize = rem + 'px'
    flexible.rem = win.rem = rem;
}
```
未引入前
```
body {
    width: 750px;
    height: 640px;
}
```
引入后：除以100并将px换成rem
```
body {
    width: 7.5rem;
    height: 6.4rem;
}
```
换算依据就是上面修改的代码：
```
function refreshRem() {
    var width = docEl.getBoundingClientRect().width
    // 适配平板
    if (width / dpr > 750) {
      width = 750 * dpr
    }
    var rem = 100 * (width / 750)
    docEl.style.fontSize = rem + 'px'
    flexible.rem = win.rem = rem;
}
```

## 方法三
1rem等于html根元素设定的font-size的px值

```
const BASE_SIZE = 100 // 基准大小
const DEVICE_WIDTH = 750 // 设备宽度

// 设置 rem 函数
const setRem = () => {
  const scale = document.documentElement.clientWidth / DEVICE_WIDTH
  if (scale > 1) {
    // 最大尺寸
    document.documentElement.style.fontSize = BASE_SIZE + 'px'
  } else {
    // 最小尺寸
    document.documentElement.style.fontSize = BASE_SIZE * scale + 'px'
  }
}
// 初始化
setRem()
// 改变窗口大小时重新设置 rem
window.onresize = setRem
```