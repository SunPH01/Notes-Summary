>前言

根据环境的需要，我们提供了创建历史对象的3种不同方法：

**createBrowserHistory:** 用于支持HTML5历史API的现代Web浏览器（参见跨浏览器兼容性）

**createHashHistory:** 用于希望将位置存储在当前URL的哈希中以避免在页面重新加载时将其发送到服务器的情况

**createMemoryHistory:** 用作引用实现，也可用于非DOM环境，如对本地或测试的反应。

根据要用于跟踪历史记录的方法，您将 import （or require,如果使用commonjs）其中一个方法。

### 1.基本用法

```
import { createBrowserHistory } from 'history';

const history = createBrowserHistory();

// 获取当前 location
const location = history.location;

// 监听当前 location改变
const unlisten = history.listen((location, action) => {
  // location is an object like window.location
  console.log(action, location.pathname, location.state);
});

//使用push、replace和go导航
history.push('/home', { some: 'state' });

//要停止侦听，请调用从listen（）返回的函数
unlisten();
```
每个创建方法所采用的选项及其默认值包括：
```
createBrowserHistory({
  basename: '', // The base URL of the app (see below)
  forceRefresh: false, // Set true to force full page refreshes
  keyLength: 6, // The length of location.key
  // A function to use to confirm navigation with the user (see below)
  getUserConfirmation: (message, callback) => callback(window.confirm(message))
});

createHashHistory({
  basename: '', // The base URL of the app (see below)
  hashType: 'slash', // The hash type to use (see below)
  // A function to use to confirm navigation with the user (see below)
  getUserConfirmation: (message, callback) => callback(window.confirm(message))
});

createMemoryHistory({
  initialEntries: ['/'], // The initial URLs in the history stack
  initialIndex: 0, // The starting index in the history stack
  keyLength: 6, // The length of location.key
  // A function to use to confirm navigation with the user. Required
  // if you return string prompts from transition hooks (see below)
  getUserConfirmation: null
});
```

### 2.属性
每个history对象都有以下属性：

- history.length - 历史堆栈中的条目数
- history.location - 当前的 location (见下文)
- history.action - 当前导航操作（见下文）

此外，createMemoryHistory 提供了`history.index` 和 `history.entries`属性，这些属性允许您检查历史堆栈。

### 3.监听
可以使用`history.listen`监听当前位置的更改：

```
history.listen((location, action) => {
  console.log(
    `The current URL is ${location.pathname}${location.search}${location.hash}`
  );
  console.log(`The last navigation action was ${action}`);
});
```
**location对象实现 window.location 接口的子集，包括：**
- **location.pathname** - The path of the URL
- **location.search** - The URL query string
- **location.hash** - The URL hash fragment

Location还可以具有以下属性：
- **location.state** - 当前location不存在于URL中的一些额外状态 (createBrowserHistory、createMemoryHistory支持该属性)
- **location.key** - 表示当前loaction的唯一字符串 (createBrowserHistory、createMemoryHistory支持该属性)

回调函数返回的参数action是push、replace或pop，具体取决于用户如何到达当前url。

**取消监听：**
使用`history.listen`附加侦听器时，它返回一个可用于删除侦听器的函数，然后可以在清理逻辑中调用该函数：
```
const unlisten = history.listen(myListener);
// ...
unlisten();
```
### 4.导航
history对象可以使用以下方法以编程方式更改当前位置：

- history.push(path, [state])
- history.replace(path, [state])
- history.go(n)
- history.goBack()
- history.goForward()
- history.canGo(n) (only in createMemoryHistory)

使用push或replace时，可以将url路径和状态指定为单独的参数，也可以将object等单个位置中的所有内容作为第一个参数：
* 一个url路径
* 一个路径对象 { pathname, search, hash, state }
```
// Push a new entry onto the history stack.
history.push('/home');

// Push a new entry onto the history stack with a query string
// and some state. Location state does not appear in the URL.
history.push('/home?the=query', { some: 'state' });

// If you prefer, use a single location-like object to specify both
// the URL and state. This is equivalent to the example above.
history.push({
  pathname: '/home',
  search: '?the=query',
  state: { some: 'state' }
});

// Go back to the previous history entry. The following
// two lines are synonymous.
history.go(-1);
history.goBack();
```
**注意**：state仅在createBrowserHistory和createMemoryHistory中得到支持。

### 5.blocking transitions 阻塞过渡
history允许您在location监听触发之前注册一个提示信息呈现给用户。

```
// 注册一个简单的提示消息，在用户离开当前页面之前，该消息将显示给用户。
const unblock = history.block('Are you sure you want to leave this page?');

// 或者在需要时使用返回消息的函数。
history.block((location, action) => {
  //location和action参数指示我们要转换到的位置以及如何到达那里。

  //一个常见的用例是防止用户在有表单尚未提交时离开页面。
  if (input.value !== '') return 'Are you sure you want to leave this page?';
});

// 要停止blocking transitions，请调用block（）返回的函数。
unblock();
```
注意：如果使用createMemoryHistory的这个特性的话，您需要提供一个getUserConfirmation函数（请参阅下文）。

**自定义确认对话框**
默认情况下，**window.confirm**用于向用户显示提示消息。如果需要重写此行为（或者如果您使用的是createMemoryHistory，它不假定DOM环境），则在创建历史对象时提供getUserConfirmation。
```
const history = createHistory({
  getUserConfirmation(message, callback) {
    // 向用户显示一些自定义对话框并调用callback（true）继续传输，或调用callback（false）中止传输。
  }
});
```
### 6.其它
**1. 使用basename**
如果应用程序中的所有URL都与其他“base”URL相关，请使用 basename 选项。此选项将给定字符串添加到您使用的所有URL的前面。
```
const history = createHistory({
  basename: '/the/base'
});

history.listen(location => {
  console.log(location.pathname); // /home
});

history.push('/home'); // URL is now /the/base/home
```
**注意**：在createMemoryHistory中不支持basename属性。

**2. 在CreateBrowserHistory中强制刷新整页**
默认情况下，createBrowserHistory使用HTML5 `pushState`和`replaceState`来防止在导航时从服务器重新加载整个页面。如果希望在url更改时重新加载，请使用forcerefresh选项。
```
const history = createBrowserHistory({
  forceRefresh: true
});
```
**3.修改createHashHistory中的`Hash`类型**
默认情况下，createHashHistory在基于hash的URL中使用'/'。可以使用hashtype选项使用不同的hash格式。
```
const history = createHashHistory({
  hashType: 'slash' // the default
});

history.push('/home'); // window.location.hash is #/home

const history = createHashHistory({
  hashType: 'noslash' // Omit the leading slash
});

history.push('/home'); // window.location.hash is #home

const history = createHashHistory({
  hashType: 'hashbang' // Google's legacy AJAX URL format
});

history.push('/home'); // window.location.hash is #!/home
```