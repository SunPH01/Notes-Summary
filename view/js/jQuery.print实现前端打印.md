[jQuery.print](https://github.com/DoersGuild/jQuery.print)是一个用于打印页面特定部分的插件。

# 一、安装

## 1. npm安装： `npm install jQuery.print`

## 2. 到github下载

下载地址：https://github.com/DoersGuild/jQuery.print，然后再html中引用`<script type="text/JavaScript" src="path/to/jquery.print.js"></script>`

# 二、 使用

```js
$("#myElementId").print(/*options*/);
/* 或者 */
$.print("#myElementId" /*, options*/);
```

# 三、 参数配置

```javascript
$("#myElementId").print({
    globalStyles: true,
    mediaPrint: false,
    stylesheet: null,
    noPrintSelector: ".no-print",
    iframe: true,
    append: null,
    prepend: null,
    manuallyCopyFormValues: true,
    deferred: $.Deferred(),
    timeout: 750,
    title: null,
    doctype: '<!doctype html>'
});
```

**globalStyles：** 默认true；接收值：Boolean；功能：是否包含来自父文档的样式

**mediaPrint：** 默认false；接收值：Boolean；功能：是否包含media='print'的链接标签;由globalStyles选项重写

**stylesheet：** 默认null；接收值：URL-string；功能：要包含的外部样式表的URL

**noPrintSelector：** 默认值".no-print"；接收值：有效的jQuery-selector；功能：要排除在打印之外的项的选择器

**iframe：** 默认值true，如果传递了无效的iframe选择器，则创建一个隐藏的iframe；接收值：任何有效的jQuery-selector或Boolean；功能：是否从iframe而不是弹出窗口打印;可以接受jquery -选择器的一个现有的iframe的值

**append/prepend：** 默认值null；接收值：任何有效的jQuery-selector或HTML-text；功能：在选定内容之前(前置)或之后(追加)添加自定义HTML

**manuallyCopyFormValues：** 默认值true；接收值：Boolean；功能：它是否应该将用户更新的表单输入值复制到打印的标记上(这是通过手工遍历每个表单元素来完成的)

**deferred：** 默认值$.Deferred()；接收值：任何有效的jQuery.Deferred对象；功能：一个jQuery。调用print函数后解析的Deferred对象。可以用来[设置回调-参见wiki](https://github.com/DoersGuild/jQuery.print/wiki/Using-the-deferred-option-to-set-up-a-callback-after-printing)

**timeout：** 默认值750；接收值： setTimeout的时间(毫秒)；功能：在新创建的窗口/iframe中打印元素之前，改变等待内容等加载的最大时间，作为新窗口/iframe的加载事件尚未触发时的回滚

**title：** 默认值null，使用主机页面标题；接收值：任何单行字符串；功能：更改打印的标题

**doctype：** 默认值：'<!doctype html>'；接收值：任何有效的doctype字符串；功能：在打印的文档框架前添加文档类型