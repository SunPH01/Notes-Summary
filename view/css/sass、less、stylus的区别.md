### 书写
stylus：使用缩进和突出来代替 `{ }` 空格间隔替代`: `和`;`

### 声明变量：
stylus： `color-fff=fff`
sass：`$`开头， `$color-fff:fff;`
less：`@`开头， `@color-fff:fff;`

### 混入(mixin)
stylus：

```
bordered($color, $width)
  border $width solid $color

body
  bordered(blue, 1px)
```

sass：
```
@mixin bordered($color, $width) {
  border: $width solid $color;
}
.myArticle {
  @include bordered(blue, 1px);  // 调用混入，并传递两个参数
}
```

less：
```
.error(@borderWidth: 2px) {
  border: @borderWidth solid #F00;
  color: #F00;
}
.generic-error {
  padding: 20px;
  margin: 4px;
  .error(); //这里调用默认 border: 2px solid #F00;
}
```

### 继承
stylus和sass一样：

```
.message {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}
.success {
  @extend .message;
  border-color: green;
}
```
less：
```
.message {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}
.success {
  .message;
  border-color: green;
}
```

### 高级语法
1. 在sass中，还支持条件语句：
   **@if可一个条件单独使用，也可以和@else结合多条件使用**
```
$lte7: true;
$type: monster;
.ib{
    display:inline-block;
    @if $lte7 {
        *display:inline;
        *zoom:1;
    }
}
p {
  @if $type == ocean {
    color: blue;
  } @else if $type == matador {
    color: red;
  } @else if $type == monster {
    color: green;
  } @else {
    color: black;
  }
}
```
其最终的css代码如下：
```
.ib{
    display:inline-block;
    *display:inline;
    *zoom:1;
}
p {
  color: green; 
}
```
2. 除却条件语句，sass还支持for循环：
for循环有两种形式，分别为：
* @for $var from <start> through <end>
* @for $var from <start> to <end>。

其中$i表示变量，start表示起始值，end表示结束值，这两个的区别是关键字through表示包括end这个数，而to则不包括end这个数。
```
@for $i from 1 to 10 {
  .border-#{$i} {
    border: #{$i}px solid blue;
  }
}
```
同时也支持while循环：
```
$i: 6;
@while $i > 0 {
  .item-#{$i} {
    width: 2em * $i;
  }
  $i: $i - 2;
}
```
最后，同时支持each命令，作用与for类似：
```
$animal-list: puma, sea-slug, egret, salamander;
@each $animal in $animal-list {
  .#{$animal}-icon {
    background-image: url('/images/#{$animal}.png');
  }
}
```
其css最终效果如下：
```
.puma-icon {
  background-image: url('/images/puma.png'); 
}
.sea-slug-icon {
  background-image: url('/images/sea-slug.png'); 
}
.egret-icon {
  background-image: url('/images/egret.png'); 
}
.salamander-icon {
  background-image: url('/images/salamander.png'); 
}
```