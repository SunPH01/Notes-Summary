

# 介绍

指令我们在[Sass函数](https://www.jianshu.com/p/282fd6e6e741)的介绍中有提到过，并且在那一章提前学习了@function指令。今天我们就来看一下Sass中常用的一些指令，在学习指令这一部分我会分为两篇学习，
只要因为指令包含了基于css指令扩展的指令与sass原生指令。本篇会讲解基于css指令扩展的指令。

# 指令的形式

@指令标识符 指令内容;

# 编码指令charset

不知道各位读者有没有发现我们前面的源码中在文件开始处会有@charset,这其实是一个CSS3指令，用于设置编译器解析文本所使用的编码，如果在源码中存在中文则需要否则编译出来的是乱码，如下

```
.mian{
  font-family:"宋体";
}

```

没有设置编码编译出的css

```
.mian {
  font-family: "瀹嬩綋";
}

```

设置了编码后编译出来的

```
@charset "UTF-8";
.mian {
  font-family: "宋体";
}

```

# import样式导入指令

css中import只能导入css，在sass中此指令被加强了，也可以导入sass文件。
@import 寻找 Sass 文件并将其导入，但在以下情况下，@import 仅作为普通的 CSS 语句，不会导入任何 Sass 文件。

*   文件拓展名是 .css；

*   文件名以 http:// 开头；

*   文件名是 url()；

*   @import 包含 media queries；

*   如果只希望导入其他的sass文件但是不希望被编译成css，此文件可以在命名时以下划线_开头；

    ![image](https://upload-images.jianshu.io/upload_images/13419832-5b734c9a014d257f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/357/format/webp)

    我们可以看到_Compenet.sass并没有编译为css文件

*   import指令只可以用在文件最外层，简而言之就是和@charset一样，不能用在嵌套的上下文内如；

```
//import错误用法
.mian{
  font-family:"宋体";
  @import "Compenet";

}

```

此处import在mian中使用，并不是最外层，会报如下错误信息：

```
//error _Compenet.scss (Line 1: @charset may only be used at the root of a document.)

```

## 媒体指令media

*   其在 CSS 规则中嵌套。如果 @media 嵌套在 CSS 规则内，编译时，@media 将被编译到文件的最外层，而media则包含嵌套的父选择器。
*   media 的 queries 允许互相嵌套使用，编译时，Sass 自动添加 and
*   media 可以使用 SassScript（比如变量，函数，以及运算符）代替条件的名称或者值

# 总结

以上关键内容整理为思维导图供读者快速理解

![image](https://upload-images.jianshu.io/upload_images/13419832-498c72578366ad63.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)


接下来我们来看sass独有的一些指令以及用法，我会尽量以最精简的文字描述。

## 函数指令function

用于声明函数的指令，详情可以查看[Sass函数](https://www.jianshu.com/p/282fd6e6e741)一篇中的内容，这里就不在赘述了，希望各位读者见谅。

## 流程控制指令

在任何计算机语言中都会有流程控制，sass中也是借由指令实现这一功能，sass中的流程指令有
@if、@else、@else if、@while、@for、@each
这里的用法@for与@each需要看一下
for的用法
`@for  var from <start> through <end>`
`@for var from <start> to <end>`
两者的区别就是through是包含end的，而to是不包含end的
@each遍历指令可以是
`@each i in list`
`@each i,$j in map{}`


## 开发记录指令

@debug @warn @error
可以使用--quiet命令行选项或:quiet Sass选项关闭警告。

## 样式复用指令@extend

首先我们需要了解一些概念术语

```
.cal{
 //...
}
a:hover {
  text-decoration: underline;
}
#fake-links .link {
  @extend a;
}

```

这段代码中a:hover .cal称为选择器,#fake-links .link称为选择器列
@extend 的作用是将重复使用的样式 (.error) 延伸 (extend) 给需要包含这个样式的特殊样式，延伸是什么什么概念呢，具体看一下下面的代码

```
.error {
  border: 1px #f00;
  background-color: #fdd;
}
.error.intrusion {
  background-image: url("/image/hacked.png");
}
.seriousError {
  @extend .error;
  border-width: 3px;
}

```

```
.error, .seriousError {
  border: 1px #f00;
  background-color: #fdd;
}

.error .intrusion, .seriousError .intrusion {
  background-image: url("/image/hacked.png");
}

.seriousError {
  border-width: 3px;
}

```

我们可以发现@extend的延伸其实就是在使用error的地方也使用seriousError。
@extend

*   同一个选择器可以延伸给多个选择器，它所包含的属性将继承给所有被延伸的选择器：
*   当一个选择器延伸给第二个后，可以继续将第二个选择器延伸给第三个；
*   暂时不可以将选择器列 (Selector Sequences)，比如 .foo .bar 或 .foo + .bar，延伸给其他元素

# @mixin @include

同样的针对mixin指令再来看一段代码

```
@mixin large-text {
  font: {
    family: Arial;
  }
  color: #ff0000;
}
.page-title {
  @include large-text;
  margin-top: 10px;
}

```

```
.page-title {
  font-family: Arial;
  color: #ff0000;
  margin-top: 10px; }

```

我们发现使用了include就是讲mixin的样式直接引用进来：
@mixin还可以这样使用

```
$color: white;
@mixin colors($color: blue) {
  background-color: $color;
  @content;
  border-color: $color;
}
.colors {
  @include colors { color : $color; }
}

```

```
.colors {
  background-color: blue;
  color: white;
  border-color: blue;
}

```

这里的@content类似于占位指令的作用
上面描述了@extend与@mixin的主要区别：

*   @mixin是支持参数的，@extend是不支持参数的；
*   为便于书写，@mixin 可以用 = 表示，而 @include 可以用 + 表示

# 嵌套范围指令@at-root

还记得我们在[Sass基于CSS的嵌套扩展](https://www.jianshu.com/p/c1f437677258)一文中用到了&符号来已用外层选择器，这里的@at-root指令也有一样的含义，但是功能更为强大
看一下关于@at-root的规则

*   默认@at-root只会跳出选择器嵌套，而不能跳出@media或@support。

*   如果要跳出这两种，则需使用@at-root (without: media)，@at-root (without: support)。

*   这个语法的关键词有四个：all（表示所有），rule（表示常规css），media（表示media），support（表示support，因为@support目前还无法广泛使用，所以在此不表）。

# %  （占位符选择器）
有时，我们希望编写一个仅用于扩展的样式规则。在这种情况下，您可以使用占位符选择器，它看起来像是以%代替 **.** 开头的类选择器，CSS输出中不含任何包含占位符的选择器，但是包含扩展占位符的选择器。
```
%my-color{
    color: green;
}

.div3{
    @extend %my-color;
}

.div4{
    @extend %my-color;
}
```
生成css:
```
.div3, .div4 {
  color: green;
}
```
这个看上去好像跟@mixin的用法一样,是的,两者几乎没有很大区别,唯一不同的是当一个页面中多次调用的话,@mixin不会将样式相同的代码进行合并,而使用占位符多次调用的话则会处理这个问题.下面演示一下@mixin
```
@mixin my-color{
    color: red;
}
.div1{
    @include my-color;
}

.div2{
    @include my-color;
}
```
生成css:
```
.div1 {
  color: red;
}

.div2 {
  color: red;
}
```

# 函数

## 自定义函数

要实现函数的声明和返回内容我们需要使用function和return两个指令，类似于其他语言中的关键字。
@function 函数名(形参) {
@return;
}

使用时时直接使用 函数名即可

```scss
@function getWidth($w) {

  @return $w * 2;
}

.main{

  max-width: #{getWidth(20)}px;

}

```

编译后

```
.main {
  max-width: 40px;
}

```

## 内置函数

内置函数是指语言本身针对一些数据类型和结构的常用操作进行封装，方便使用。
[Sass函数api链接](http://sass-lang.com/documentation/Sass/Script/Functions.html)
这里只会针对列表与Map的常见操作进行整理，其他更加详细的内容需要读者自己进行查阅了，官方API已经整理的很好啦
这些是对列表和Map都可以使用的函数

```scss
length($list) // 返回列表或者Map的长度.

nth($list, $n) // 返回列表或者Map中的一个元素，$n在是元素对应的index从1开始.

set-nth($list, $n, $value) // 替换列表或者Map中的元素.

join($list1, $list2, [$separator, $bracketed]) // 将两个列表或者Maps合并.

append($list1, $val, [$separator]) // 讲一个元素添加至列表或者Map中

zip($lists…) //将多个列表或者Map合并成新的列表或者Map里面每一个元素是原来单个列表

index($list, $value) // 返回列表或Map中元素的index

list-separator($list) // 返回list的分割符号有comma 逗号 space 空格

is-bracketed($list) // 返回一个列表或者map是否被（）包裹

```

```
$arr : 1 2 3 4;
$map : ("a":1,"b":2);

```

这里面的list元素为1、2、3、4，map中的元素为 "a" 1 、 "b" 2。没有错在map中元素就是 key value。kee与value都可以为null,list里也是可以存在null，不过null是不会编译到css中的

接下来针对map还有一些常见内置函数

```scss
map-get($map, $key) //根据key返回value

map-merge($map1, $map2) //将两个map合并为一个map

map-remove($map, $keys…) //根据key移除value

map-keys($map) //返回map中所有的key

map-values($map) //返回map中所有的values

map-has-key($map, $key)//判断map中是否存在key

type-of($value) //返回一个数值的数据类型

```

# 总结

1.  指令就是告诉计算机从事某一特殊运算的代码。
2.  自定义函数需要使用到@function内置指令
3.  [Sass函数api链接](http://sass-lang.com/documentation/Sass/Script/Functions.html)


# scss定义两套主题变量实例：
```scss
///////////////////////////////////自定义全局变量
// 浅色主题
$light-theme: (
  border-color: #eceeef,
  font-color:#323232,
  font-secondary: #c2c6d0,
  background-color:#fff,
  bg-title:#d3e6fb,
  bg-oddList:#f3f3f3,
  bg-listHover:#c5dffe
);

//深色主题
$dark-theme: (
  border-color: #000,
  font-color:#c2c6d0,
  font-secondary: #aaa,
  background-color:#131925,
  bg-title:#293248,
  bg-oddList:#171e2c,
  bg-listHover:#0d3a70

  
);

//定义映射集合
$themes: (
  light: $light-theme,
  dark: $dark-theme
);

@mixin themify($themes: $themes) {
  @each $theme-name, $map in $themes {
 
    .theme-#{$theme-name} & {
      $theme-map: () !global;
      @each $key, $value in $map {
        $theme-map: map-merge($theme-map, ($key: $value)) !global;
      }
 
      @content;
 
      $theme-map: null !global;
    }
  }
}
 
@function themed($key) {
  @return map-get($theme-map, $key);
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  @include themify($themes) {
    background: themed('background-color');
    color:themed('font-color');
  }
}
```
编译为css：
```css
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
.theme-light #app {
  background: #fff;
  color: #323232;
}
.theme-dark #app {
  background: #131925;
  color: #c2c6d0;
}
```

在我们看完前面的一些指令之后，sass中的内容我们已经基本看完了，在总结之前我们在看一下sass我们需要注意的一些特殊符号
* `=` 既可以是赋值运算也可以是@mixin的缩写

* `+`既可以是加法运算也可以是@include的缩写

* `#{}`可以占位格式化输出符号

* `!optional`这个的用法有点特殊难以描述，还是直接看代码吧
```
a.important {@extend .notice;}
```
当我们写下这段代码时，因为没有notice的样式所以一定会报错，想要忽略的话可以这样
```
a.important {@extend .notice !optional;}
```
在官网中说
`It's also an error if the only selector containing .notice is h1.notice, since h1 conflicts with a and so no new selector would be generated.`
但是在我亲自试验后发现有没有!optional结果是一样的也许是版本问题吧。
总结


* `=` 既可以是赋值运算也可以是@mixin的缩写

* `+`既可以是加法运算也可以是@include的缩写

* `#{}`可以占位格式化输出符号

* `!optional`用于忽略@extend的引入错误


# 总结

最后依然在文章最后将本文的内容整理为思维导图的形式以便让读者迅速理解：

![image](https://upload-images.jianshu.io/upload_images/13419832-9a5a51516c3b4ce6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)