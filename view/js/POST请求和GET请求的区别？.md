# post和get的区别
**1.** get是从服务器上获取数据，post是向服务器传送数据。

**2**. GET请求把参数包含在URL中，将请求信息放在URL后面，POST请求通过request body传递参数，将请求信息放置在报文体中。

**3.** get传送的数据量较小，不能大于2KB。post传送的数据量较大，一般被默认为不受限制。但理论上，IIS4中最大量为80KB，IIS5中为100KB。

**4.** get安全性非常低，get设计成传输数据，一般都在地址栏里面可以看到，post安全性较高，post传递数据比较隐私，所以在地址栏看不到， 如果没有加密，他们安全级别都是一样的，随便一个[监听器](https://www.baidu.com/s?wd=%E7%9B%91%E5%90%AC%E5%99%A8&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)都可以把所有的数据监听到。

**5.** GET请求能够被缓存，GET请求会保存在浏览器的浏览记录中，以GET请求的URL能够保存为浏览器书签，post请求不具有这些功能。

**6.** HTTP的底层是TCP/IP，GET和POST的底层也是TCP/IP，也就是说，GET/POST都是TCP链接。GET和POST能做的事情是一样一样的。你要给GET加上request body，给POST带上url参数，技术上是完全行的通的。

**7.**GET产生一个TCP数据包，对于GET方式的请求，浏览器会把http header和data一并发送出去，服务器响应200（返回数据）；POST产生两个TCP数据包，对于POST，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok（返回数据），并不是所有浏览器都会在POST中发送两次包，Firefox就只发送一次。

# URI和URL的区别？ 
URI：统一资源标识符，在世界范围内唯一标识并定位信息资源。URI有两种形式：URL和URN。 
URL：统一资源定位符。URL描述了一台特定服务器上某资源的特定位置。(几乎所有的URLI都是URL。) 
# 什么是代理? 
代理是位于客户端和服务器之间的HTTP中间实体。接收所有客户端的HTTP请求，并将这些请求转发给服务器（可能会对请求进行修改之后转发）。 