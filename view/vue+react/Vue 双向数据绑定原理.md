###  前言

  MVVM 是与 MVC 进化出来的，区别在与将view层的数据变动直接响应到viewModel层上而不是响应给model，其表现上最大的区别就在于双向数据绑定功能

### 双向数据绑定原理简介

> **发布者-订阅者模式（Backbone.js）** 一般通过sub, pub的方式实现数据和视图的绑定监听，更新数据方式通常做法是 vm.set('property', value)
> **脏值检查（Angular.js）** 通过一定的事件触发检测数据是否有变更来决定是否需要更新视图。如用户操作ng-click，XHR事件，浏览器Location变更事件 ( [图片上传失败...(image-aed435-1561973477623)]
>
> timeout , [图片上传失败...(image-9ee74c-1561973477623)]
>
> digest() 或 $apply()等
> **数据劫持（vue.js）**

  Vue 的双向数据绑定的原理关心两个要点

1.  **数据劫持：** 通过`Object.definedProperty()`劫持并监听数据变动
2.  **观察者模式：** 通过 `发布者-订阅者` 模式实现数据更新

### [Object.definedProperty()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)

  这是一个原生JavaScript标准库中的一个方法，被称作是**对象属性的精确添加和修改**。用于直接在一个对象上定义一个新属性，或者修改一个对象的现有属性， 并返回这个对象。

> **`Object.defineProperty(obj, prop, descriptor)`**

![image](//upload-images.jianshu.io/upload_images/8646214-304b3d8f54cbc664.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

  从图上可以看得出来，这个“精确添加和修改”的方法最重要的是属性操作符的配置。需要提到的一点是，属性操作符分类：`存取操作符` 和 `数据操作符`。上图也有表示，指定时两中操作符只能选择其一，不能混用，否者报错。

  **被操作的属性如果不存在，则会创建这个属性；如果已存在，则执行更新。**

##### 添加属性

> 常用的 `var obj = {};` `obj.a = 1;` 这种添加属性的办法实质上就是执行的如下操作。需要强调的是：直接赋值的操作符设置的都是true，而这些操作符他们本身的默认值都是false，注意区分。

```
var obj = {};
// 在对象中添加一个属性与数据描述符的示例
Object.defineProperty(o, "attr", {
  value : 37,
  writable : true,
  enumerable : true,
  configurable : true
});

```

【**解读**】本例的意思是 给对象`obj`添加属性`attr`，它的值为37，并且设置属性`attr`的值可以被修改（writable），加入到可枚举队列中以便`for-in / for-of`等操作可以遍历到（enumerable），设置配置属性可以修改，且该属性可以被`delete obj.attr` 删除（configurable），所以有以下操作

```
obj.attr = 20;
console.log( obj.attr );  // 20
delete obj.attr;
console.log(obj.attr); // undefined

```

##### 修改属性（writable）

> 正常情况下属性都需要修改功能，需要设置操作符writable值为true。当设置为false时，并不会抛出错误，但是值不会被修改。在严格模式下，会抛出错误`throw Error：xx is read-only`

```
var o = {}; // Creates a new object

Object.defineProperty(o, 'a', {
  value: 37,
  writable: false
});

console.log(o.a); // logs 37
o.a = 25; // No error thrown
console.log(o.a); // logs 37\. 

```

##### 是否可被for...in和Object.keys()获取（enumerable）

```
var o = {};
Object.defineProperty(o, "a", { value : 1, enumerable: true });
Object.defineProperty(o, "b", { value : 2, enumerable: false });
Object.defineProperty(o, "c", { value : 3 });  // enumerable 默认是false
o.d = 4; // 采用默认赋值的方式，属性操作符enumerable为true（configurable也是true）

for (var i of o) {    
  console.log(i);  // 打印 'a' 和 'd'
}
Object.keys(o); // ["a", "d"]

o.propertyIsEnumerable('a'); // true
o.propertyIsEnumerable('b'); // false

```

##### 设置属性不可被删除 （configurable）

  `configurable`特性表示对象的属性是否可以被删除，以及除`writable`特性外的其他特性是否可以被修改。【这里并不是说设置`configurable`之后就不能设置其他操作符，而是不能修改，如下`enumerable`属性】

```
var o = {};
Object.defineProperty(o, "a", { 
  get : function(){return 1;}, 
  enumerable: true,
  configurable: false 
});
// 以下例子会抛出异常，因为属性a的值不可被修改了
Object.defineProperty(o, "a", {value : 12});

console.log(o.a);   // logs 1
delete o.a;   // 不会抛出错误，但执行不成功
console.log(o.a);   // logs 1

```

当然，如果把`configurable`设置为`true`，`delete o.a` 就能将`a`属性正确删除

#### *能实现 `数据劫持`的存取操作符 get/set*

  `get()` 和`set()`是`Object.definedProperty()`中非常重要的两个操作符，当属性被访问时，`get`方法被执行，传入的参数列表为空。当属性被修改时，只有一个参数，即新值。注意参数列表虽然没体现但默认都带有`this`对象，并且需要注意`this`有可能是本身属性，也有可能是继承属性。另外，访问器属性的会"覆盖"同名的普通属性，因为访问器属性会被优先访问，与其同名的普通属性则会被忽略。

**| `obj.attr = 10`这种直接赋值操作对应的 getter 和 setter 逻辑如下**

```
var obj = { attr: 10 }

Object.definedProperty(o, 'attr', {
  enumerable : true,
  configurable : true,
  get: function() {
    console.log('get方法被调用了');
    return this.attr
  },
  set: function(newValue) {
    console.log('set方法被调用了，参数是: ' + newVal);
    this.attr = newValue
  }
})

obj.attr;  // get方法被调用了
obj.attr = 3;   // set方法被调用了，参数是: 3

```

**| 数据劫持的实现**
  正因为`get`和`set`的存在，我们可以在其中自行添加一些处理逻辑

```
// 属性操作符配置
var pattern = {
    enumerable : true,
    configurable : true,
    get: function () {
        return 'I alway return this string,whatever you have assigned';
    },
    set: function (newVal) {
        this.myname = 'this is my name string';
    }
};

function TestDefineSetAndGet() {
  Object.defineProperty(this, 'attr', pattern);
};

var instance = new TestDefineSetAndGet();
instance.attr = 'baby';

console.log(instance.attr); // I alway return this string,whatever you have assigned
console.log(instance.myname);  // this is my name string

```

###  极简的双向数据绑定

![image](//upload-images.jianshu.io/upload_images/8646214-f0d39faf070ab521.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/960/format/webp)

  此例实现的效果是：随文本框输入文字的变化，`span` 中会同步显示相同的文字内容；在js或控制台显式的修改 `obj.hello` 的值，视图会相应更新。这样就实现了 `model => view` 以及 `view => model` 的双向绑定。

###  Vue 实现双向数据绑定

 整理了一下，要实现mvvm的双向绑定，就必须要实现以下几点：
1、实现一个数据监听器Observer，能够对数据对象的所有属性进行监听，如有变动可拿到最新值并通知订阅者
2、实现一个指令解析器Compile，对每个元素节点的指令进行扫描和解析，根据指令模板替换数据，以及绑定相应的更新函数
3、实现一个Watcher，作为连接Observer和Compile的桥梁，能够订阅并收到每个属性变动的通知，执行指令绑定的相应回调函数，从而更新视图
4、mvvm入口函数，整合以上三者

![image](//upload-images.jianshu.io/upload_images/8646214-07a3615012b7e246.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/730/format/webp)

##### ｜实现Observer - 监听每个数据的变化

  现在我们知道可以利用`Obeject.defineProperty()`来监听属性变动， 那么将需要observe的数据对象进行递归遍历，包括子属性对象的属性，都加上 setter和getter。这样的话，给这个对象的某个值赋值，就会触发setter，那么就能监听到了数据变化。相关的代码可以是这样的

```
var data = {name: 'Crain'}
observe(data);
data.name = "Ocaka";  // 监听到数据变化  Crain => Ocaka (set方法中打印)

function observe() {
  if (!data || typeof data !== 'object')  return;

  Object.keys(data).forEach(function(key) {
    defineReactive(data, key, data[key]);
  })
}

function defineReactive(data, key, val) {
  observe(val);  // 深度递归
  Object.defineProperty(data, key, {
    enumerable: true,  // 可以被枚举
    configurable: false,  // 不能再被defined
    get: function() {
      return val;
    },
    set: function(newVal) {
      console.log('监听到变化 ', val, '=>', newVal)
      val = newVal
    }

  })
}

```

##### | 将数据变化通知订阅者

  我们已经可以监听每个数据的变化了，那么监听到变化之后就是怎么通知订阅者了，所以接下来我们需要实现一个消息订阅器，很简单，维护一个数组，用来收集订阅者，数据变动触发`notify`，再调用订阅者的`update`方法，代码改善之后是这样：

```
// ..省略
function definedReactive(data, key, val) {
  var dep = new Dep(); // 实例化一个订阅器
  observe(val);  // 深度递归
  Object.defineProperty(data, key, {
        // ... 省略
        set: function(newVal) {
            if (val === newVal) return;
            console.log('监听到变化 ', val, '=>', newVal);
            val = newVal;
            dep.notify(); // 通知所有订阅者
        }
    });
}

// Dep订阅器构造函数
function Dep() {
  this.subs = [];  // 收集订阅者
}
Dep.prototype = {
  addSub: function(sub) {
    this.subs.push(sub);
  },
  notify: function() {
    this.subs.forEach(function(sub) {
      sub.update();
    })
  }
}

```

那么问题来了，谁是订阅者？怎么往订阅器添加订阅者？
没错，上面的思路整理中我们已经明确订阅者应该是`Watcher`, 而且`var dep = new Dep();`是在 `defineReactive`方法内部定义的，所以想通过`dep`添加订阅者，就必须要在闭包内操作，所以我们可以在 `getter`里面动手脚：

```
// Observer.js
// ...省略
Object.defineProperty(data, key, {
    get: function() {
        // 由于需要在闭包内添加watcher，所以通过Dep定义一个全局target属性，暂存watcher, 添加完移除
        Dep.target && dep.addDep(Dep.target);
        return val;
    }
    // ... 省略
});

// Watcher.js
Watcher.prototype = {
    get: function(key) {
        Dep.target = this;
        this.value = data[key];    // 这里会触发属性的getter，从而添加订阅者
        Dep.target = null;
    }
}

```

  这里已经实现了一个Observer了，已经具备了监听数据和数据变化通知订阅者的功能。那么接下来就是实现Compile了

##### | 实现 Compile

  `compile`主要做的事情是解析模板指令，将模板中的变量替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，更新视图，如图所示：

![image](//upload-images.jianshu.io/upload_images/8646214-755cd4925463c7ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/625/format/webp)

因为遍历解析的过程有多次操作dom节点，为提高性能和效率，会先将跟节点el转换成文档碎片fragment进行解析编译操作，解析完成，再将fragment添加回原来的真实dom节点中

```
function Compile(el) {
    this.$el = this.isElementNode(el) ? el : document.querySelector(el);
    if (this.$el) {
        this.$fragment = this.node2Fragment(this.$el);
        this.init();
        this.$el.appendChild(this.$fragment);
    }
}
Compile.prototype = {
    init: function() { this.compileElement(this.$fragment); },
    node2Fragment: function(el) {
        var fragment = document.createDocumentFragment(), child;
        // 将原生节点拷贝到fragment
        while (child = el.firstChild) {
            fragment.appendChild(child);
        }
        return fragment;
    }
};

```

compileElement方法将遍历所有节点及其子节点，进行扫描解析编译，调用对应的指令渲染函数进行数据渲染，并调用对应的指令更新函数进行绑定，详看代码及注释说明：

```
Compile.prototype = {
    // ... 省略
    compileElement: function(el) {
        var childNodes = el.childNodes, me = this;
        [].slice.call(childNodes).forEach(function(node) {
            var text = node.textContent;
            var reg = /\{\{(.*)\}\}/;    // 表达式文本
            // 按元素节点方式编译
            if (me.isElementNode(node)) {
                me.compile(node);
            } else if (me.isTextNode(node) && reg.test(text)) {
                me.compileText(node, RegExp.$1);
            }
            // 遍历编译子节点
            if (node.childNodes && node.childNodes.length) {
                me.compileElement(node);
            }
        });
    },

    compile: function(node) {
        var nodeAttrs = node.attributes, me = this;
        [].slice.call(nodeAttrs).forEach(function(attr) {
            // 规定：指令以 v-xxx 命名
            // 如 <span v-text="content"></span> 中指令为 v-text
            var attrName = attr.name;    // v-text
            if (me.isDirective(attrName)) {
                var exp = attr.value; // content
                var dir = attrName.substring(2);    // text
                if (me.isEventDirective(dir)) {
                    // 事件指令, 如 v-on:click
                    compileUtil.eventHandler(node, me.$vm, exp, dir);
                } else {
                    // 普通指令
                    compileUtil[dir] && compileUtil[dir](node, me.$vm, exp);
                }
            }
        });
    }
};

// 指令处理集合
var compileUtil = {
    text: function(node, vm, exp) {
        this.bind(node, vm, exp, 'text');
    },
    // ...省略
    bind: function(node, vm, exp, dir) {
        var updaterFn = updater[dir + 'Updater'];
        // 第一次初始化视图
        updaterFn && updaterFn(node, vm[exp]);
        // 实例化订阅者，此操作会在对应的属性消息订阅器中添加了该订阅者watcher
        new Watcher(vm, exp, function(value, oldValue) {
            // 一旦属性值有变化，会收到通知执行此更新函数，更新视图
            updaterFn && updaterFn(node, value, oldValue);
        });
    }
};

// 更新函数
var updater = {
    textUpdater: function(node, value) {
        node.textContent = typeof value == 'undefined' ? '' : value;
    }
    // ...省略
};

```

这里通过递归遍历保证了每个节点及子节点都会解析编译到，包括了`{{}}`表达式声明的文本节点。指令的声明规定是通过特定前缀的节点属性来标记，如`<span v-text="content" other-attr`中`v-text`便是指令，而`other-attr`不是指令，只是普通的属性。
监听数据、绑定更新函数的处理是在`compileUtil.bind()`这个方法中，通过`new Watcher()`添加回调来接收数据变化的通知

至此，一个简单的Compile就完成了。接下来要看看Watcher这个订阅者的具体实现了

##### | 实现Watcher

Watcher订阅者作为Observer和Compile之间通信的桥梁，主要做的事情是:
1、在自身实例化时往属性订阅器(`dep`)里面添加自己
2、自身必须有一个`update()`方法
3、待属性变动`dep.notice()`通知时，能调用自身的`update()`方法，并触发`Compile`中绑定的回调，则功成身退。
如果有点乱，可以回顾下前面的思路整理

```
function Watcher(vm, exp, cb) {
    this.cb = cb;
    this.vm = vm;
    this.exp = exp;
    // 此处为了触发属性的getter，从而在dep添加自己，结合Observer更易理解
    this.value = this.get(); 
}
Watcher.prototype = {
    update: function() {
        this.run();    // 属性值变化收到通知
    },
    run: function() {
        var value = this.get(); // 取到最新值
        var oldVal = this.value;
        if (value !== oldVal) {
            this.value = value;
            this.cb.call(this.vm, value, oldVal); // 执行Compile中绑定的回调，更新视图
        }
    },
    get: function() {
        Dep.target = this;    // 将当前订阅者指向自己
        var value = this.vm[exp];    // 触发getter，添加自己到属性订阅器中
        Dep.target = null;    // 添加完毕，重置
        return value;
    }
};
// 这里再次列出Observer和Dep，方便理解
Object.defineProperty(data, key, {
    get: function() {
        // 由于需要在闭包内添加watcher，所以可以在Dep定义一个全局target属性，暂存watcher, 添加完移除
        Dep.target && dep.addDep(Dep.target);
        return val;
    }
    // ... 省略
});
Dep.prototype = {
    notify: function() {
        this.subs.forEach(function(sub) {
            sub.update(); // 调用订阅者的update方法，通知变化
        });
    }
};

```

实例化`Watcher`的时候，调用`get()`方法，通过`Dep.target = watcherInstance`标记订阅者是当前watcher实例，强行触发属性定义的`getter`方法，`getter`方法执行的时候，就会在属性的订阅器`dep`添加当前watcher实例，从而在属性值有变化的时候，watcherInstance就能收到更新通知。

ok, Watcher也已经实现了。

最后来讲讲MVVM入口文件的相关逻辑和实现吧，相对就比较简单了~

##### | 实现 MVVM

MVVM作为数据绑定的入口，整合Observer、Compile和Watcher三者，通过Observer来监听自己的model数据变化，通过Compile来解析编译模板指令，最终利用Watcher搭起Observer和Compile之间的通信桥梁，达到数据变化 -> 视图更新；视图交互变化(input) -> 数据model变更的双向绑定效果。

一个简单的MVVM构造器是这样子：

```
function MVVM(options) {
    this.$options = options;
    var data = this._data = this.$options.data;
    observe(data, this);
    this.$compile = new Compile(options.el || document.body, this)
}

```

但是这里有个问题，从代码中可看出监听的数据对象是`options.data`，每次需要更新视图，则必须通过`var vm = new MVVM({data:{name: 'kindeng'}}); vm._data.name = 'dmq';` 这样的方式来改变数据。

显然不符合我们一开始的期望，我们所期望的调用方式应该是这样的：
`var vm = new MVVM({data: {name: 'kindeng'}}); vm.name = 'dmq';`

所以这里需要给MVVM实例添加一个属性代理的方法，使访问vm的属性代理为访问vm._data的属性，改造后的代码如下：

```
function MVVM(options) {
    this.$options = options;
    var data = this._data = this.$options.data, me = this;
    // 属性代理，实现 vm.xxx -> vm._data.xxx
    Object.keys(data).forEach(function(key) {
        me._proxy(key);
    });
    observe(data, this);
    this.$compile = new Compile(options.el || document.body, this)
}

MVVM.prototype = {
    _proxy: function(key) {
        var me = this;
        Object.defineProperty(me, key, {
            configurable: false,
            enumerable: true,
            get: function proxyGetter() {
                return me._data[key];
            },
            set: function proxySetter(newVal) {
                me._data[key] = newVal;
            }
        });
    }
};

```

这里主要还是利用了`Object.defineProperty()`这个方法来劫持了vm实例对象的属性的读写权，使读写vm实例的属性转成读写了`vm._data`的属性值，达到鱼目混珠的效果

### 参考文献

[MDN-Object.definedProperty()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)
[#](https://segmentfault.com/a/1190000006599500#articleHeader4) [剖析Vue原理&实现双向绑定MVVM](https://segmentfault.com/a/1190000006599500)