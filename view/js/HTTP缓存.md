缓存是一种保存资源副本并在下次请求时直接使用该副本的技术。当 web 缓存发现请求的资源已经被存储，它会拦截请求，返回该资源的拷贝，而不会去源服务器重新下载。
**优点：**缓解服务器端压力，提升性能。

缓存的种类有很多,其大致可归为两类：**私有与共享缓存**。
共享缓存存储的响应能够被多个用户使用。私有缓存只能用于单独用户。
本文将主要介绍浏览器与代理缓存，除此之外还有网关缓存、CDN、反向代理缓存和负载均衡器等部署在服务器上的缓存方式，为站点和 web 应用提供更好的稳定性、性能和扩展性。
![image.png](https://upload-images.jianshu.io/upload_images/18030682-e4d7c9fd8b0f6cba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### (私有)浏览器缓存
  私有缓存只能用于单独用户。你可能已经见过浏览器设置中的“缓存”选项。浏览器缓存拥有用户通过 [HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP "en/HTTP") 下载的所有文档。这些缓存为浏览过的文档提供向后/向前导航，保存网页，查看源码等功能，可以避免再次向服务器发起多余的请求。它同样可以提供缓存内容的离线浏览。

### (共享)代理缓存
共享缓存可以被多个用户使用。例如，ISP 或你所在的公司可能会架设一个 web 代理来作为本地网络基础的一部分提供给用户。这样热门的资源就会被重复使用，减少网络拥堵与延迟。

### 缓存操作的目标
虽然 HTTP 缓存不是必须的，但重用缓存的资源通常是必要的。然而常见的 HTTP 缓存只能存储 `GET`响应，对于其他类型的响应则无能为力。缓存的关键主要包括request method和目标URI（一般只有GET请求才会被缓存）。 普遍的缓存案例:

*   一个检索请求的成功响应: 对于 [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET "HTTP GET 方法请求指定的资源。使用 GET 的请求应该只用于获取数据。")请求，响应状态码为：[`200`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/200 "状态码 200 OK 表明请求已经成功. 默认情况下状态码为200的响应可以被缓存。")，则表示为成功。一个包含例如HTML文档，图片，或者文件的响应。
*   永久重定向: 响应状态码：[`301`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/301 "HTTP 301 永久重定向 说明请求的资源已经被移动到了由 Location 头部指定的url上，是固定的不会再改变。搜索引擎会根据该响应修正。")。
*   错误响应: 响应状态码：[`404`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/404 "状态码 404 Not Found 代表客户端错误，指的是服务器端无法找到所请求的资源。返回该响应的链接通常称为坏链（broken link）或死链（dead link），它们会导向链接出错处理(link rot)页面。") 的一个页面。
*   不完全的响应: 响应状态码 [`206`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/206 "HTTP 206 Partial Content 成功状态响应代码表示请求已成功，并且主体包含所请求的数据区间，该数据区间是在请求的 Range 首部指定的。")，只返回局部的信息。
*   除了 [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET "HTTP GET 方法请求指定的资源。使用 GET 的请求应该只用于获取数据。") 请求外，如果匹配到作为一个已被定义的cache键名的响应。

### 缓存控制
**Cache-control 头**
HTTP/1.1定义的 `Cache-Control`头用来区分对缓存机制的支持情况， 请求头和响应头都支持这个属性。通过它提供的不同的值来定义缓存策略。

* **`Cache-Control: no-store`**：**没有缓存**。缓存中不得存储任何关于客户端请求和服务端响应的内容。每次由客户端发起的请求都会下载完整的响应内容。
* **`Cache-Control: no-cache`**：**缓存但重新验证**。每次有请求发出时，缓存会将此请求发到服务器，服务器端会验证请求中所描述的缓存是否过期，若未过期（注：实际就是返回304），则使用本地缓存副本。
* **`Cache-Control: private` `Cache-Control: public`**：**私有和公共缓存**。`public` 指令表示该响应可以被任何中间人缓存。若指定了"public"，则一些通常不被中间人缓存的页面（比如 带有HTTP验证信息（帐号密码）的页面 或 某些特定状态码的页面）将会被其缓存。
而 `private`则表示该响应是专用于某单个用户的，中间人不能缓存此响应，该响应只能应用于浏览器私有缓存中。
* **`Cache-Control: max-age=31536000`**：**过期**。过期机制中，最重要的指令是 "max-age=<seconds>"，表示资源能够被缓存（保持新鲜）的最大时间。max-age是距离请求发起的时间的秒数。
* **`Cache-Control: must-revalidate`**：验证方式。当使用了 "must-revalidate" 指令，那就意味着缓存在考虑使用一个陈旧的资源时，必须先验证它的状态，已过期的缓存将不被使用。

**Pragma 头**
`Pragma`是**HTTP/1.0**标准中定义的一个header属性，请求中包含Pragma的效果跟在头信息中定义Cache-Control: no-cache相同，但是HTTP的响应头没有明确定义这个属性，所以它不能拿来完全替代HTTP/1.1中定义的Cache-control头。通常定义Pragma以向后兼容基于HTTP/1.0的客户端。

### 新鲜度
理论上来讲，当一个资源被缓存存储后，该资源应该可以被永久存储在缓存中。由于缓存只有有限的空间用于存储资源副本，所以缓存会定期地将一些副本删除，这个过程叫做**缓存驱逐**。另一方面，当服务器上面的资源进行了更新，那么缓存中的对应资源也应该被更新，由于HTTP是C/S模式的协议，服务器更新一个资源时，不可能直接通知客户端更新缓存，所以双方必须为该资源约定一个过期时间，在该过期时间之前，该资源（缓存副本）就是新鲜的，当过了过期时间后，该资源（缓存副本）则变为陈旧的*。*驱逐算法用于将陈旧的资源（缓存副本）替换为新鲜的，注意，一个陈旧的资源（缓存副本）是不会直接被清除或忽略的，当客户端发起一个请求时，缓存检索到已有一个对应的陈旧资源（缓存副本），则缓存会先将此请求附加一个`If-None-Match`头，然后发给目标服务器，以此来检查该资源副本是否是依然还是算新鲜的，若服务器返回了 `304`(Not Modified)（该响应不会有带有实体信息），则表示此资源副本是新鲜的，这样一来，可以节省一些带宽。若服务器通过 If-None-Match 或 If-Modified-Since判断后发现已过期，那么会带有该资源的实体内容返回。
下面是上述缓存处理过程的一个图示：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-cf11c5bee392e518.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

缓存失效时间计算公式如下：
`expirationTime = responseTime + freshnessLifetime - currentAge`
上式中，responseTime 表示浏览器接收到此响应的那个时间点。

### 缓存验证
用户点击刷新按钮时会开始缓存验证。如果缓存的响应头信息里含有"Cache-control: must-revalidate”的定义，在浏览的过程中也会触发缓存验证。另外，在浏览器偏好设置里设置Advanced->Cache为强制验证缓存也能达到相同的效果。

当缓存的文档过期后，需要进行缓存验证或者重新获取资源。只有在服务器返回强校验器或者弱校验器时才会进行验证。