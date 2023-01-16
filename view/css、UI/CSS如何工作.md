# 一、什么是 CSS?
**CSS**是一种用于向用户指定文档如何呈现的语言 — 它们如何被指定样式、布局等。
**文档**通常是用标记语言结构化的文本文件 — HTML 是最常用的标记语言, 但你依然可以遇见一些其他的标记语言，比如 SVG 或者 XML。
**呈现文档**给用户意味着将其转换为用户可用的形式。浏览器，比如 Firefox, Chrome 或者 Internet Explorer，被设计用于可视化呈现文档，例如，在计算机屏幕，投影仪或打印机上。
# 二、CSS如何影响HTML？
Web浏览器将CSS规则应用于文档以影响它们的显示方式。一个CSS规则由以下组成：

* 一组 属性 ，属性的值更新了 HTML 的内容的显示方式。比如，我想让元素的宽度是其父元素的50％，或者元素背景变为红色。
* 一个 选择器，它选择元素，这（些）元素是你想应用这些最新的属性值于其上的元素。比如，我想将我的CSS规则应用到我HTML文档中的所有段落上。

**样式表**中的一组CSS规则确定了网页如何显示，你将在下一篇文章中了解更多关于CSS语法的内容 — CSS 语法。

# 三、CSS 实际上如何工作？
当浏览器显示文档时，它必须将文档的内容与其样式信息结合。它分两个阶段处理文档：

1. 浏览器将 HTML 和 CSS 转化成 DOM （文档对象模型 *DOM在计算机内存中表示文档*）。 DOM把文档内容和其样式结合在一起。

2. 浏览器显示 DOM 的内容。

# 四、关于DOM
DOM是一种树形结构. 标记语言中的每个元素,属性,文本片段都变为一个 DOM 节点。这些节点由它们与其它 DOM 节点的关系来定义。有的元素是某些子节点的父节点，且这些子节点有兄弟（节点）。
由于 DOM 是 CSS 与文档内容的相遇之处，理解 DOM 有助于设计，调试和维护你的 CSS 文件。
相对于漫长而无聊的解释，让我们通过一个例子来看一下 DOM 和 CSS 如何一同工作。
让我们假定下面这段HTML代码：

```
<p>
  Let's use:
  <span>Cascading</span>
  <span>Style</span>
  <span>Sheets</span>
</p>
```
在该 DOM 中，我们的 <p> 元素所对应的节点是父节点。它的子节点是一个文本节点和我们的一些 <span> 元素对应的节点。这些 SPAN 结点也是父节点，它们各自的文本节点就是它们的子节点：
```
P
├─ "Let's use:"
├─ SPAN
|  └─ "Cascading"
├─ SPAN
|  └─ "Style"
└─ SPAN
   └─ "Sheets"
```
这就是浏览器解释先前的HTML片段的过程 —它渲染上述的DOM树，之后在浏览器中像这样输出它。

html、css、javaScript的关系
*   [HTML](https://developer.mozilla.org/en-US/docs/Glossary/HTML "HTML: HTML (HyperText Markup Language) is a descriptive language that specifies webpage structure.")是一种标记语言，用来结构化我们的网页内容并赋予内容含义，例如定义段落、标题和数据表，或在页面中嵌入图片和视频。
*   [CSS](https://developer.mozilla.org/en-US/docs/Glossary/CSS "CSS: CSS (Cascading Style Sheets) is a declarative language that controls how webpages look in the browser.") 是一种样式规则语言，可将样式应用于 HTML 内容， 例如设置背景颜色和字体，在多个列中布局内容。
*   [JavaScript](https://developer.mozilla.org/en-US/docs/Glossary/JavaScript "JavaScript: JavaScript (JS) is a programming language mostly used to dynamically script webpages on the client side, but it is also often utilized on the server-side, using packages such as Node.js.") 是一种脚本语言，可以用来创建动态更新的内容，控制多媒体，制作图像动画，还有很多。（好吧，虽然它不是万能的，但可以通过简短的代码来实现神奇的功能。）
