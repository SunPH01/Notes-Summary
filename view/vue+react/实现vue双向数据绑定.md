Vue的双向数据绑定的原理主要是通过Object对象的defineProperty属性，重写data的set和get函数来实现的。

下面我们官网给出的追踪变化的解释：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-e9fa84de84e923b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
当你把一个普通的 JavaScript 对象传入 Vue 实例作为 `data` 选项，Vue 将遍历此对象所有的属性，并使用 [`Object.defineProperty`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 把这些属性全部转为 [getter/setter](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Working_with_Objects#%E5%AE%9A%E4%B9%89_getters_%E4%B8%8E_setters)。`Object.defineProperty` 是 ES5 中一个无法 shim 的特性，这也就是 Vue 不支持 IE8 以及更低版本浏览器的原因。

这些 getter/setter 对用户来说是不可见的，但是在内部它们让 Vue 能够追踪依赖，在属性被访问和修改时通知变更。这里需要注意的是不同浏览器在控制台打印数据对象时对 getter/setter 的格式化并不同，所以建议安装 [vue-devtools](https://github.com/vuejs/vue-devtools) 来获取对检查数据更加友好的用户界面。

每个组件实例都对应一个 **watcher** 实例，它会在组件渲染的过程中把“接触”过的数据属性记录为依赖。之后当依赖项的 setter 触发时，会通知 watcher，从而使它关联的组件重新渲染。

知道了如何追踪变化，那么下面看代码：
```
<!DOCTYPE html>
<head>
  <title>myVue</title>
</head>
<style>
  #app {
    text-align: center;
  }
</style>
<body>
  <div id="app">
    <form>
      <input type="text"  v-model="number">
      <button type="button" v-click="increment">增加</button>
    </form>
    <h3 v-bind="number"></h3>
  </div>
</body>

<script>
  function myVue(options) {
    this._init(options);
  }

  myVue.prototype._init = function (options) {
    this.$options = options;
    this.$el = document.querySelector(options.el);
    this.$data = options.data;
    this.$methods = options.methods;

    this._binding = {};
    this._obverse(this.$data);
    this._complie(this.$el);
  }
 
  myVue.prototype._obverse = function (obj) {
    var value;
    for (key in obj) {
      if (obj.hasOwnProperty(key)) {
        this._binding[key] = {                                                                                                                                                          
          _directives: []
        };
        value = obj[key];
        if (typeof value === 'object') {
          this._obverse(value);
        }
        var binding = this._binding[key];
        Object.defineProperty(this.$data, key, {
          enumerable: true,
          configurable: true,
          get: function () {
            console.log(`获取${value}`);
            return value;
          },
          set: function (newVal) {
            console.log(`更新${newVal}`);
            if (value !== newVal) {
              value = newVal;
              binding._directives.forEach(function (item) {
                item.update();
              })
            }
          }
        })
      }
    }
  }

  myVue.prototype._complie = function (root) {
    var _this = this;
    var nodes = root.children;
    for (var i = 0; i < nodes.length; i++) {
      var node = nodes[i];
      if (node.children.length) {
        this._complie(node);
      }

      if (node.hasAttribute('v-click')) {
        node.onclick = (function () {
          var attrVal = nodes[i].getAttribute('v-click');
          return _this.$methods[attrVal].bind(_this.$data);
        })();
      }

      if (node.hasAttribute('v-model') && (node.tagName == 'INPUT' || node.tagName == 'TEXTAREA')) {
        node.addEventListener('input', (function(key) {
          var attrVal = node.getAttribute('v-model');
          _this._binding[attrVal]._directives.push(new Watcher(
            'input',
            node,
            _this,
            attrVal,
            'value'
          ))

          return function() {
            _this.$data[attrVal] =  nodes[key].value;
          }
        })(i));
      } 

      if (node.hasAttribute('v-bind')) {
        var attrVal = node.getAttribute('v-bind');
        _this._binding[attrVal]._directives.push(new Watcher(
          'text',
          node,
          _this,
          attrVal,
          'innerHTML'
        ))
      }
    }
  }

  function Watcher(name, el, vm, exp, attr) {
    this.name = name;         //指令名称，例如文本节点，该值设为"text"
    this.el = el;             //指令对应的DOM元素
    this.vm = vm;             //指令所属myVue实例
    this.exp = exp;           //指令对应的值，本例如"number"
    this.attr = attr;         //绑定的属性值，本例为"innerHTML"

    this.update();
  }

  Watcher.prototype.update = function () {
    this.el[this.attr] = this.vm.$data[this.exp];
  }

  window.onload = function() {
    var app = new myVue({
      el:'#app',
      data: {
        number: 0
      },
      methods: {
        increment: function() {
          this.number ++;
        },
      }
    })
  }
</script>
```
效果：
![image](https://upload-images.jianshu.io/upload_images/10153386-bbcab3412e76242e.gif?imageMogr2/auto-orient/strip|imageView2/2/w/600/format/webp)