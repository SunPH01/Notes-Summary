先看一个题目
```
var foo = 1;
function bar() {
    if (!foo) {
        var foo = 10;
    }
    alert(foo);
}
bar();
```
alert的结果是 10

再看下一个：
```
var a = 1;
function b() {
    a = 10;
    return;
    function a() {}
}
b();
alert(a);
```
alert结果是1

```
console.log(v1);
var v1 = 100;
function foo() {
    console.log(v1);
    var v1 = 200;
    console.log(v1);
}
foo();
console.log(v1);

//undefined
//undefined
//200
//100
```


如果以上题目都答对了，那可能接下来的内容你就不需要去看了，如果你现在有很多问号，那就继续看下去

### 一、作用域(scope)
C语言的一个例子：

```
#include <stdio.h>
int main() {
    int x = 1;
    printf("%d, ", x); // 1
    if (1) {
        int x = 2;
        printf("%d, ", x); // 2
    }
    printf("%d\n", x); // 1
}
```
程序依次输出了1，2，1.
C语言中，我们有块级作用域（block-level scope）。在一个代码块(一对{}花括号括起来的部分)的中变量并不会覆盖掉代码块外面的变量。

JavaScript中的表现：
```
var x = 1;
console.log(x); // 1
if (true) {
    var x = 2;
    console.log(x); // 2
}
console.log(x); // 2
```
JavaScript有函数级作用域（function-level scope）。这一点和C家族完全不同。语句块，如if语言，不创建新的作用域。仅仅函数能创建新作用域。

在JavaScript中，如果我们需要实现block-level scope，我们也有一种变通的方式，那就是通过自执行函数创建临时作用域:(闭包)
```
function foo() {
    var x = 1;
    if (x) {
        (function () {
            var x = 2;
            // some other code
        }());
    }
    // x is still 1.
}
```
上面代码在if条件块中创建了一个闭包，它是一个立即执行函数，所以相当于我们又创建了一个函数作用域，所以内部的x并不会对外部产生影响。

**变量作用域**
一个变量的作用域表示这个变量存在的上下文。它指定了你可以访问哪些变量以及你是否有权限访问某个变量。
**变量作用域分为局部作用域和全局作用域。**

**全局作用域**：最外层函数定义的变量拥有全局作用域，即对任何内部函数来说，都是可以访问的
```
var outerVar = "outer";
function fn(){
  console.log(outerVar);
}
fn();//result:outer
```
**局部作用域**：和全局作用域相反，局部作用域一般只在固定的代码片段内可访问到，而对于函数外部是无法访问的
```
function fn(){
  var innerVar = "inner";
}
fn();
console.log(innerVar);// ReferenceError: innerVar is not defined
```
需要注意的是，函数内部声明变量的时候，一定要使用var命令。如果不用的话，你实际上声明了一个全局变量！
```
 function fn(){
  innerVar = "inner";
}
fn();
console.log(innerVar);// result:inner
```
再来看一个代码：
```
var scope = "global";
function fn(){
  console.log(scope);//result:undefined
  var scope = "local";
  console.log(scope);//result:local;
}
fn();
```
第一个输出居然是undefined，原本以为它会访问外部的全局变量(scope=”global”)，但是并没有。这可以算是javascript的一个特点，**只要函数内定义了一个局部变量，函数在解析的时候都会将这个变量“提前声明”：**
```
var scope = "global";
function fn(){
  var scope;
  console.log(scope);//result:undefined
  scope = "local";
  console.log(scope);//result:local;
}
fn();
```
然而，也不能因此草率地将局部作用域定义为：用var声明的变量作用范围起止于花括号之间。 javascript并没有块级作用域，javascript的作用域是相对函数而言的，可以称为函数作用域。

**变量提升 函数提升**
变量提升即将变量声明提升到它所在作用域的最开始的部分。在js代码执行前引擎会先进行预编译，预编译期间会将变量声明与函数声明提升至其对应作用域的最顶端。

概念：
1. js的变量和函数在编译阶段会被提升到当前作用域最前面编译
2. 函数是一等公民，优先编译函数

下面看例子
```
console.log(a);//function a(){}
var a = 10;
function a(){

}
console.log(a);//10
```
变量的声明虽然在函数声明之前，但是函数优先被编译，实际代码是
```
function a(){

}
console.log(a);
a = 10;
console.log(a);
```
这里的实际代码中是a = 10而不是var a = 10;因为函数声明不仅优先被编译了，还覆盖掉剩余的同名变量的声明。

js中创建函数有两种方式：**函数声明式**和**函数字面量式**。**只有函数声明才存在函数提升！**
继续看这个例子：
```
console.log(a);//function a(){}
console.log(b); // undefined
function a(){

}
var b = function(){}
```
它实际的执行过程
```
function a(){

}
var b;
console.log(a);//function a(){}
console.log(b); // undefined
b = function(){}
```