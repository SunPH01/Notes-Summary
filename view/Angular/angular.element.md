将DOM元素或者HTML字符串一包装成一个jQuery元素。

格式：angular.element(element);

element：包装成jquery对象的html字符串或者dom元素

jqLite提供的方法：

addClass()
after()
append()
attr()
bind() – 不支持命名空间,选择器和事件数据
children() – 不支持选择器
clone()
contents()
css()
data()
empty()
eq()
find() – 限定通过标签名称查找
hasClass()
html()
next() – 不支持选择器
on() – 不支持命名空间或选择器
off() –不支持命名空间或选择器
one() – 不支持命名空间或选择器
parent() – 不支持选择器
prepend()
prop()
ready()
remove()
removeAttr()
removeClass()
removeData()
replaceWith()
text()
toggleClass()
triggerHandler() -通过一个虚拟事件对象来处理。
unbind() – 不支持命名空间
val()
wrap()