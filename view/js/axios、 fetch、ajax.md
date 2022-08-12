### 一、ajax
传统 Ajax 指的是 XMLHttpRequest（XHR），核心使用XMLHttpRequest对象，允许客户端脚本发送HTTP请求，去请求服务器的数据来创建动态网页，可以在不重新加载整个网页的情况下，对网页的某部分进行更新。也称局部刷新（常见的例子：分页、用户名即时验证、聊天）；多个请求之间如果有先后关系的话，就会出现回调地狱。

```javascript
var xmlhttp;
if (window.XMLHttpRequest) {// code for IE7+, Firefox, Chrome, Opera, Safari
  xmlhttp=new XMLHttpRequest();
} else {// code for IE6, IE5
  xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
}

xmlhttp.onreadystatechange=function() {
  if (xmlhttp.readyState==4 && xmlhttp.status==200) {
    document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
   }
}
xmlhttp.open("GET","test1.txt",true);
xmlhttp.send();
```
![image.png](https://upload-images.jianshu.io/upload_images/18030682-a8f3f5d10154261e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**readyState：**
0: 请求未初始化
1: 服务器连接已建立
2: 请求已接收
3: 请求处理中
4: 请求已完成，且响应已就绪

**GET 还是 POST？**
与 POST 相比，GET 更简单也更快，并且在大部分情况下都能用。
然而，在以下情况中，请使用 POST 请求：
* 无法使用缓存文件（更新服务器上的文件或数据库）
* 向服务器发送大量数据（POST 没有数据量限制）
* 发送包含未知字符的用户输入时，POST 比 GET 更稳定也更可靠

一个简单 POST 请求：
```javascript
xmlhttp.open("POST","demo_post.asp",true);
xmlhttp.send();
```
如果需要像 HTML 表单那样 POST 数据，请使用 setRequestHeader() 来添加 HTTP 头。然后在 send() 方法中规定您希望发送的数据：
```javascript
xmlhttp.open("POST","ajax_test.asp",true);
xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");
xmlhttp.send("fname=Bill&lname=Gates");
```
![image.png](https://upload-images.jianshu.io/upload_images/18030682-210f5de696a4465c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



jquery封装ajax
```javascript
$.ajax({
  type: 'POST',
  url: url,
  data: data,
  dataType: dataType,
  success: function () {},
  error: function () {}
});
```


Ajax是对原生XHR的封装，为了达到我们跨越的目的，增添了对JSONP的支持。经过这么多年的更新维护，不得不承认它已经很成熟，能够满足我们的基本需求，但是随着react,vue新一代框架的兴起，以及ES规范的完善，更多API的更新，它逐渐暴露了自己的不足：

* 针对MVC的编程设计,不符合现在前端MVVM的趋势
* 基于原生的XHR开发，XHR本身的架构不够清晰
* JQuery较大，单纯使用ajax却要引入整个JQuery非常的不合理
* 配置和调用方式非常混乱，而且基于事件的异步模型不友好。

### 二、axios
Axios 是一个基于 promise 的 HTTP 库，可以用在浏览器和 node.js 中。

特性：
*   从浏览器中创建 [XMLHttpRequests](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)
*   从 node.js 创建 [http](http://nodejs.org/api/http.html) 请求
*   支持 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) API
*   拦截请求和响应
*   转换请求数据和响应数据
*   取消请求
*   自动转换 JSON 数据
*   客户端支持防御 [XSRF](http://en.wikipedia.org/wiki/Cross-site_request_forgery)

**PS:防止CSRF:就是让你的每个请求都带一个从cookie中拿到的key, 根据浏览器同源策略，假冒的网站是拿不到你cookie中得key的，这样，后台就可以轻松辨别出这个请求是否是用户在假冒网站上的误导输入，从而采取正确的策略。**

**1. axios.create([config])**

```javascript
const Axios = axios.create({
  // baseURL: HOST, // 前缀
  responseType: 'json', // 数据格式
  withCredentials: true, // 是否允许带cookie这些
  headers: {
    'Content-Type': 'application/json;charset=UTF-8' // json格式数据
  }
})
```

以下是可用的实例方法。指定的配置将与实例的配置合并。
* axios.request(config)
* axios.get(url[, config])
* axios.delete(url[, config])
* axios.head(url[, config])
* axios.options(url[, config])
* axios.post(url[, data[, config]])
* axios.put(url[, data[, config]])
* axios.patch(url[, data[, config]])

**2. 请求配置**
这些是创建请求时可以用的配置选项。只有 url 是必需的。如果没有指定 method，请求将默认使用 get 方法。
```javascript
{
   // `url` 是用于请求的服务器 URL
  url: '/user',

  // `method` 是创建请求时使用的方法
  method: 'get', // default

  // `baseURL` 将自动加在 `url` 前面，除非 `url` 是一个绝对 URL。
  // 它可以通过设置一个 `baseURL` 便于为 axios 实例的方法传递相对 URL
  baseURL: 'https://some-domain.com/api/',

  // `transformRequest` 允许在向服务器发送前，修改请求数据
  // 只能用在 'PUT', 'POST' 和 'PATCH' 这几个请求方法
  // 后面数组中的函数必须返回一个字符串，或 ArrayBuffer，或 Stream
  transformRequest: [function (data, headers) {
    // 对 data 进行任意转换处理
    return data;
  }],

  // `transformResponse` 在传递给 then/catch 前，允许修改响应数据
  transformResponse: [function (data) {
    // 对 data 进行任意转换处理
    return data;
  }],

  // `headers` 是即将被发送的自定义请求头
  headers: {'X-Requested-With': 'XMLHttpRequest'},

  // `params` 是即将与请求一起发送的 URL 参数
  // 必须是一个无格式对象(plain object)或 URLSearchParams 对象
  params: {
    ID: 12345
  },

   // `paramsSerializer` 是一个负责 `params` 序列化的函数
  // (e.g. https://www.npmjs.com/package/qs, http://api.jquery.com/jquery.param/)
  paramsSerializer: function(params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },

  // `data` 是作为请求主体被发送的数据
  // 只适用于这些请求方法 'PUT', 'POST', 和 'PATCH'
  // 在没有设置 `transformRequest` 时，必须是以下类型之一：
  // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
  // - 浏览器专属：FormData, File, Blob
  // - Node 专属： Stream
  data: {
    firstName: 'Fred'
  },

  // `timeout` 指定请求超时的毫秒数(0 表示无超时时间)
  // 如果请求话费了超过 `timeout` 的时间，请求将被中断
  timeout: 1000,

   // `withCredentials` 表示跨域请求时是否需要使用凭证
  withCredentials: false, // default

  // `adapter` 允许自定义处理请求，以使测试更轻松
  // 返回一个 promise 并应用一个有效的响应 (查阅 [response docs](#response-api)).
  adapter: function (config) {
    /* ... */
  },

 // `auth` 表示应该使用 HTTP 基础验证，并提供凭据
  // 这将设置一个 `Authorization` 头，覆写掉现有的任意使用 `headers` 设置的自定义 `Authorization`头
  auth: {
    username: 'janedoe',
    password: 's00pers3cret'
  },

   // `responseType` 表示服务器响应的数据类型，可以是 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'
  responseType: 'json', // default

  // `responseEncoding` indicates encoding to use for decoding responses
  // Note: Ignored for `responseType` of 'stream' or client-side requests
  responseEncoding: 'utf8', // default

   // `xsrfCookieName` 是用作 xsrf token 的值的cookie的名称
  xsrfCookieName: 'XSRF-TOKEN', // default

  // `xsrfHeaderName` is the name of the http header that carries the xsrf token value
  xsrfHeaderName: 'X-XSRF-TOKEN', // default

   // `onUploadProgress` 允许为上传处理进度事件
  onUploadProgress: function (progressEvent) {
    // Do whatever you want with the native progress event
  },

  // `onDownloadProgress` 允许为下载处理进度事件
  onDownloadProgress: function (progressEvent) {
    // 对原生进度事件的处理
  },

   // `maxContentLength` 定义允许的响应内容的最大尺寸
  maxContentLength: 2000,

  // `validateStatus` 定义对于给定的HTTP 响应状态码是 resolve 或 reject  promise 。如果 `validateStatus` 返回 `true` (或者设置为 `null` 或 `undefined`)，promise 将被 resolve; 否则，promise 将被 rejecte
  validateStatus: function (status) {
    return status >= 200 && status < 300; // default
  },

  // `maxRedirects` 定义在 node.js 中 follow 的最大重定向数目
  // 如果设置为0，将不会 follow 任何重定向
  maxRedirects: 5, // default

  // `socketPath` defines a UNIX Socket to be used in node.js.
  // e.g. '/var/run/docker.sock' to send requests to the docker daemon.
  // Only either `socketPath` or `proxy` can be specified.
  // If both are specified, `socketPath` is used.
  socketPath: null, // default

  // `httpAgent` 和 `httpsAgent` 分别在 node.js 中用于定义在执行 http 和 https 时使用的自定义代理。允许像这样配置选项：
  // `keepAlive` 默认没有启用
  httpAgent: new http.Agent({ keepAlive: true }),
  httpsAgent: new https.Agent({ keepAlive: true }),

  // 'proxy' 定义代理服务器的主机名称和端口
  // `auth` 表示 HTTP 基础验证应当用于连接代理，并提供凭据
  // 这将会设置一个 `Proxy-Authorization` 头，覆写掉已有的通过使用 `header` 设置的自定义 `Proxy-Authorization` 头。
  proxy: {
    host: '127.0.0.1',
    port: 9000,
    auth: {
      username: 'mikeymike',
      password: 'rapunz3l'
    }
  },

  // `cancelToken` 指定用于取消请求的 cancel token
  // （查看后面的 Cancellation 这节了解更多）
  cancelToken: new CancelToken(function (cancel) {
  })
}j
```
**3. 相应结构**
请求的响应包含以下信息
```javascript
{
  // `data` 由服务器提供的响应
  data: {},

  // `status` 来自服务器响应的 HTTP 状态码
  status: 200,

  // `statusText` 来自服务器响应的 HTTP 状态信息
  statusText: 'OK',

  // `headers` 服务器响应的头
  headers: {},

   // `config` 是为请求提供的配置信息
  config: {},
 // 'request'
  // `request` is the request that generated this response
  // It is the last ClientRequest instance in node.js (in redirects)
  // and an XMLHttpRequest instance the browser
  request: {}
}
```

**4. 配置的优先顺序**
配置会以一个优先顺序进行合并。这个顺序是：在 lib/defaults.js 找到的库的默认值，然后是实例的 defaults 属性，最后是请求的 config 参数。后者将优先于前者。

**5. 拦截器**
在请求或响应被 then 或 catch 处理前拦截它们。

```javascript
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error);
  });
```
如果你想在稍后移除拦截器，可以这样：
```
const myInterceptor = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(myInterceptor);
```

可以为自定义 axios 实例添加拦截器
```
const instance = axios.create({/*...*/});
instance.interceptors.request.use(function () {/*...*/});
```

### 三、[fetch](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch)

fetch号称是AJAX的替代品，是在ES6出现的，使用了ES6中的promise对象。Fetch是基于promise设计的。Fetch的代码结构比起ajax简单多了，参数有点像jQuery ajax。但是，一定记住fetch不是ajax的进一步封装，而是原生js，没有使用XMLHttpRequest对象。
**fetch的优点：**
1.符合关注分离，没有将输入、输出和用事件来跟踪的状态混杂在一个对象里
2.更好更方便的写法

坦白说，上面的理由对我来说完全没有什么说服力，因为不管是Jquery还是Axios都已经帮我们把xhr封装的足够好，使用起来也足够方便，为什么我们还要花费大力气去学习fetch？
我认为fetch的优势主要优势就是：
1.  语法简洁，更加语义化
2.  基于标准 Promise 实现，支持 async/await
3.  同构方便，使用 isomorphic-fetch
4.  更加底层，提供的API丰富（request, response）
5.  脱离了XHR，是ES规范里新的实现方式

最近在使用fetch的时候，也遇到了不少的问题：
fetch是一个低层次的API，你可以把它考虑成原生的XHR，所以使用起来并不是那么舒服，需要进行封装。
1）fetch只对网络请求报错，对400，500都当做成功的请求，服务器返回 400，500 错误码时并不会 reject，只有网络错误这些导致请求不能完成时，fetch 才会被 reject。
2）fetch默认不会带cookie，需要添加配置项： fetch(url, {credentials: 'include'})
3）fetch不支持abort，不支持超时控制，使用setTimeout及Promise.reject的实现的超时控制并不能阻止请求过程继续在后台运行，造成了流量的浪费
4）fetch没有办法原生监测请求的进度，而XHR可以


下面来看一下知识点
**语法**：`Promise<Response> fetch(input[, init]);`
**参数：**
1. **input**：定义要获取的资源。这可能是：一个 `USVString`字符串，包含要获取资源的 URL。一些浏览器会接受 `blob:` 和 `data:` 作为 schemes.一个 `Request` 对象。
2. **init(可选)**：一个配置项对象，包括所有对请求的设置。可选的参数有：
*   `method`: 请求使用的方法，如 `GET`、`POST`。
*   `headers`: 请求的头信息，形式为 `Headers` 的对象或包含 `ByteString`值的对象字面量。
*   `body`: 请求的 body 信息：可能是一个 `Blob`、`BufferSource`、`FormData`、`URLSearchParams` 或者 `USVString`对象。注意 GET 或 HEAD 方法的请求不能包含 body 信息。
*   `mode`: 请求的模式，如 `cors、` `no-cors 或者` `same-origin。`
*   `credentials`: 请求的 credentials，如 `omit`、`same-origin` 或者 `include`。为了在当前域名内自动发送 cookie ， 必须提供这个选项， 从 Chrome 50 开始， 这个属性也可以接受` `FederatedCredential`实例或是一个 `PasswordCredential`实例。
*   `cache`:  请求的 cache 模式: `default `、 `no-store 、` `reload 、` `no-cache 、` `force-cache `或者 `only-if-cached 。`
*   `redirect`: 可用的 redirect 模式: `follow` (自动重定向), `error` (如果产生重定向将自动终止并且抛出一个错误), 或者 `manual` (手动处理重定向). 在Chrome中，Chrome 47之前的默认值是 follow，从 Chrome 47开始是 manual。
*   `referrer`: 一个 `USVString`可以是 `no-referrer`、`client`或一个 URL。默认是 `client`。
*   `referrerPolicy`: 指定了HTTP头部referer字段的值。可能为以下值之一： `no-referrer、` `no-referrer-when-downgrade、` `origin、` `origin-when-cross-origin、` `unsafe-url 。`
*   `integrity`: 包括请求的  subresource integrity。`



**请注意，fetch 规范与 jQuery.ajax() 主要有三种方式的不同：**
*   当接收到一个代表错误的 HTTP 状态码时，从 `fetch()` 返回的 Promise **不会被标记为 reject，** 即使响应的 HTTP 状态码是 404 或 500。相反，它会将 Promise 状态标记为 resolve （但是会将 resolve 的返回值的 `ok` 属性设置为 false ），仅当网络故障时或请求被阻止时，才会标记为 reject。
*   `fetch()` **不会接受跨域 cookies；**你也不能使用 `fetch()` 建立起跨域会话。其他网站的 `Set-Cookie` 头部字段将会被无视。
*   `fetch` **不会发送 cookies**。除非你使用了*credentials* 的[初始化选项](https://developer.mozilla.org/zh-CN/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。（自 2017 年 8 月 25 日以后，默认的 credentials 政策变更为 `same-origin`。Firefox 也在 61.0b13 版本中进行了修改）

一个基本的 fetch 请求设置起来很简单。看看下面的代码：
```javascript
fetch('http://example.com/movies.json')
  .then(function(response) {
    return response.json();
  })
  .then(function(myJson) {
    console.log(myJson);
  });
```

**发送带凭据的请求**
为了让浏览器发送包含凭据的请求（即使是跨域源），要将`credentials: 'include'`添加到传递给 fetch()方法的init对象。

```
fetch('https://example.com', {
  credentials: 'include'  
})
```
如果你只想在请求URL与调用脚本位于同一起源处时发送凭据，请添加 credentials: 'same-origin'。
要改为确保浏览器不在请求中包含凭据，请使用 credentials: 'omit'。

**上传 JSON 数据**
使用 `fetch()` POST JSON数据
```javascript
var url = 'https://example.com/profile';
var data = {username: 'example'};

fetch(url, {
  method: 'POST', // or 'PUT'
  body: JSON.stringify(data), // data can be `string` or {object}!
  headers: new Headers({
    'Content-Type': 'application/json'
  })
}).then(res => res.json())
.catch(error => console.error('Error:', error))
.then(response => console.log('Success:', response));
```
**上传文件**
```javascript
var formData = new FormData();
var fileField = document.querySelector("input[type='file']");

formData.append('username', 'abc123');
formData.append('avatar', fileField.files[0]);

fetch('https://example.com/profile/avatar', {
  method: 'PUT',
  body: formData
})
.then(response => response.json())
.catch(error => console.error('Error:', error))
.then(response => console.log('Success:', response));
```

**检测请求是否成功**
如果遇到网络故障，`fetch()` 对象。虽然这个情况经常是遇到了权限问题或类似问题——比如 404 不是一个网络故障。想要精确的判断 `fetch()` 是否成功，需要包含 promise resolved 的情况，此时再判断 `Response.ok`是不是为 true。类似以下代码：

```javascript
fetch('flowers.jpg').then(function(response) {
  if(response.ok) {
    return response.blob();
  }
  throw new Error('Network response was not ok.');
}).then(function(myBlob) { 
  var objectURL = URL.createObjectURL(myBlob); 
  myImage.src = objectURL; 
}).catch(function(error) {
  console.log('There has been a problem with your fetch operation: ', error.message);
});
```

**Headers**
使用 `Headers` 的接口，你可以通过 `Headers()`构造函数来创建一个你自己的 headers 对象。一个 headers 对象是一个简单的多名值对：

```javascript
var content = "Hello World";
var myHeaders = new Headers();
myHeaders.append("Content-Type", "text/plain");
myHeaders.append("Content-Length", content.length.toString());
myHeaders.append("X-Custom-Header", "ProcessThisImmediately");
```
也可以传一个多维数组或者对象字面量：
```javascript
myHeaders = new Headers({
  "Content-Type": "text/plain",
  "Content-Length": content.length.toString(),
  "X-Custom-Header": "ProcessThisImmediately",
});
```

**Response 对象**
`Response`实例是在 `fetch()` 处理完 promise 之后返回的。
你会用到的最常见的 response 属性有:
*   `Response.status` — 整数（默认值为200）为response的状态码。
*   `Response.statusText` — 字符串（默认值为"OK"），该值与 HTTP 状态码消息对应。
*   `Response.ok` — 如上所示，该属性是来检查response的状态是否在 200 - 299（包括200 和 299）这个范围内。该属性返回一个`布尔值`。