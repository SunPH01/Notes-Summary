在项目开发中遇到这样一个问题，项目里用window.open打开了一个外链，但是外链界面无法显示，页面有一个报错的message提示。奇怪的是复制这个链接，直接在浏览器地址栏粘贴，然后回车就可以正常打开。。。
后来换了一种方法解决了，整体思路就是创建一个a标签，a标签的href设置为此外链，然后插入到body里，执行a标签的点击事件，代码如下：
```
const openWindow = (url) => {
      let a = document.createElement("a");
      a.setAttribute("href", url);
      a.setAttribute("target", "_blank");
      a.setAttribute("id", "ftm-link");
      a.setAttribute("rel", "noreferrer");
      document.body.appendChild(a);
      a.click();
      const aNode = document.getElementById("ftm-link");
      if (aNode) {
        document.body.removeChild(aNode);
      }
    }
```
这种方法就可以正常打开外链了。
后来又在网上看到一篇文章，是这么说的：

问题原因：
当点击访问页面中外链地址时，会产生一个http请求（用于获取外链地址内容），此时出于安全策略（一些用户信息或登录信息会通过url传递），浏览器会在请求头中添加一个referrer，用来表示当前请求是从哪个页面跳转来的，也就是访问来源。当外链网站对访问做判断时，于是会出现诸如403 Forbidden、The HTTP request is not acceptable for the requested resource.问题。

解决方法：
在项目的index.html的<head>中加入如下<meta>代码：
// index.html
<meta name="referrer" content="no-referrer" />