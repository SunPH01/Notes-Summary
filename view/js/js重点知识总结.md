本文主要介绍

1.  **立即执行函数**：又叫自执行函数，定义即执行
2.  **变量提升**：`Hoisting`作用域内提升
3.  **闭包**：`closure` 一个可以访问私有作用域的函数及其所在的运行环境的组合
4.  **使用闭包定义私有变量**：变量私有化
5.  **模块化**：作用域独立化及私有化
6.  **柯里化**：定义多参数函数增加函数使用灵活性
7.  **构造函数**：又叫工厂函数，能产生隔离作用域，为生成具有特定功能的实例
8.  **原型 prototype**： 实例对象的共享属性，常为方法
9.  **自定义对象**：产生隔离作用域，为实现某些特定功能而定义的对象
10.  **apply, call 和 bind方法**：他们的异同及如何使用
11.  **Memoization**：优化耗时计算方案，常用作处理递归缓存
12.  **函数重载**： 允许函数有不同输入，并返回不同的结果


### 1\. 立即(自)执行函数（IIFE）

  立即执行函数（`Immediately Invoked Function Expression`）即（1）定义一个匿名函数，（2）马上调用该匿名函数。 它没有绑定任何事件也无需等待任何异步才做，即可立即执行。用过JQuery的都知道，JQuery开篇用的就是立即执行函数。

  立即执行函数的好处在于能隔离作用域，并在私有作用域中执行逻辑，避免了变量污染和命名冲突。常见的两种写法如下

```
// 括号包围函数体
(function(name) {
  let greet= 'Hello';
  let sayHi = () => console.log(`${greet} ${name}`)
  sayHi()
  // ...
})('zfs')

 // 括号包围全部内容
(function() {
  let name = 'liz'
  console.log(name)
  // ...
} ())

```

  基础谁都懂，但我想知道的更多一些。看一题经典面试题

```
for(var i = 0; i < 10; i++){
  setTimeout(function() {
    console.log(i);   // 为什么输出了十个10，而不是0-9
  }, 0)
}

```

【**原来如此**】
  为什么执行上述代码输出了十个10？其实想明白了就很容易了。
  Javascript是单线程的，执行顺序由上而下，而`setTimeout`是典型的异步方法，其中的操作会被挂起，直到主队列中的代码执行完成后才开始执行。
  又因为在`for`循环中，变量`i`是用具有变量提升效果的`var`定义的，因此`i`的作用域覆盖全局。
  案例中，每次循环结束，都有一个`console.log()`操作被挂起，当十次循环执行结束后，变量`i`已经累计到10（注意不是9），此时退出循环主线程执行结束，开始执行挂起队列中的打印逻辑，然而打印逻辑中的参数`i`已经是10，因此每次打印出了十个10

  那这个问题怎么处理？其实办法有很多，如把`var`改成ES6中没有变量提升效果块作用域定义法的`let`即可，每次循环执行时因`i`为块作用域变量因此它的值都会被保留而不会被下次循环执行覆盖。主要利用了**保护执行时环境**的思想。而这种思想使用立即执行函数也能实现

```
// 注意要在被挂起之前保存执行环境否则就无效了，因此用IIFE包住异步函数
for (var i = 0; i < 10; i++) {
  (function(ii){
    setTimeout(function(){
      console.log(ii)
    }, 0)
  })(i)
}

```

### 2\. 变量提升 [Hoisting]

  这是一个相对简单但又容易踩坑的地方。在ES6之前，所谓的变量提升即 **JS会将所有的变量和函数声明移动到它所在作用域的最前面。**这里有两个重要的信息：
（1）只将变量或函数的**声明**提前，而赋值并未被提前
（2）只提前到变量或函数**所在作用域**的最前面，而不是全局作用域的最前面

知道这两个要点，就不会再踩坑了。另外ES6中的`let`和`const`具有`TDZ(暂时死区)`的效果，不再本次讨论范围内。拿个案例来加深一下印象

```
console.log(a)  // ReferenceError: a is not defined 

(function() {
  console.log(a);  // undefined
  say();  // NaN

  var a = 10;
  function say() {
    var b = 15;
    console.log(a + b)
  }
  console.log(a);  // 10
  say();  // 25
})()

```
我们改一下say函数的声明方式看一下：
```
console.log(a)  // ReferenceError: a is not defined 

(function() {
  console.log(a);  // undefined
  say();  // Uncaught TypeError: say is not a function

  var a = 10;
 var say = function () {
    var b = 15;
    console.log(a + b)
  }
  console.log(a); 
  say(); 
})()
```

【**原来如此**】
  因为立即执行函数具有隔离作用域的作用，而外部未定义有变量a，因此第一行执行会报错。
  在自执行函数作用域内，定义有`var a = 10`，由于变量提升效果，`a`的声明会被提前至函数作用域的最上方，但赋值不会被提升，因此第二个`a`打印`undefined`.
  函数提升效果同理，但在执行第一个`say()`时，因为此时的`a`还是没有赋值，还是`undefined`，因此输出`NaN`
  最底下两个打印正常不解释

为什么改一下say函数的声明方式就报错了呢？
其实第一段代码的执行过程
```
console.log(a)  // ReferenceError: a is not defined 

(function() {
  var a;
  function say() {
    var b = 15;
    console.log(a + b)
 }
  console.log(a);  // undefined
  say();  // NaN
  a = 10;
  console.log(a);  // 10
  say();  // 25
})()
```
第二段代码执行过程：
```
console.log(a)  // ReferenceError: a is not defined 

(function() {
  var a;
  var say;
  console.log(a);  // undefined
  say();  // Uncaught TypeError: say is not a function   say此时等于undefined

  a = 10;
  say = function () {
    var b = 15;
    console.log(a + b)
  }
  console.log(a); 
  say(); 
})()
```

### 3\. 闭包 [closure]

  所谓的闭包**是指能访问私有作用域的函数及创建该函数的词法环境的组合，** 这个环境包含了这个闭包创建时所能访问的所有局部变量。第一次看到这句话的时我的内心是拒绝的，不仅难理解，还绕口很难读。但这个东西还真的挺重要。到底什么意思？结合一个案例来理解
**$ 闭包有什么特性？**
* 在函数内再嵌套函数，这也是函数最直接的展示
* 内部函数可以引用外层函数作用域内的参数和变量
* 被引用的参数和变量不会被垃圾回收机制回收
```
function func(){
  var n = 0;  // n是func函数的局部变量
  console.log(n,'func');//
  function closure() {  // closure是func函数的内部函数，是闭包
     n += 1;  // 内部使用了外部函数中的变量n
    console.log(n,'closure');
  }
  return closure;
}

var counter= func();//这里会调用一次func函数，打印  0,'func'

counter();  //调closure 打印 1,'closure'
counter();  //调closure 打印 2,'closure'
counter();  //调closure 打印 3,'closure'
```
`func`只有在`var counter= func()`时调用了一次，后续`counter()`就只会调用`closure`函数，`func`不会再执行了。
  这里利用闭包实现了一个计数器功能，它有什么好处？相比于普通变量定义的计数器，这个计数器只能通过调用`counter`来实现数量`n`的累加，再没有别的方法可以改变`n`的值，这样就保证了计数器正确稳定的计数杜绝外部干扰和破坏。这是闭包的一大优势。为什么有这效果？听说会造成内存泄漏是怎么回事？

  我们得先理解闭包定义的这句话。分析一下案例，`func`是一个普通函数，在它的私有作用域中，定义了一个变量`n`，和一个`closure`函数，最后将这个函数返回。

  单从`func`出发，在其私有作用域中有两种性质的变量或方法：（1）私有的即外部不可访问的；（2）被暴露可访问的即被`return`的。关于这点一会模块化里还会再提到。

  而**闭包要做的事就是将函数私有作用域内外部不可访问的变量或方法让外界可以访问，实现这个思想的主要手段就是通过被暴露的方法来导出私有变量**，这样做是**为了避免变量污染全局同时也避免被污染，同时又可以在全局中被引用和改变更新**，通过这种方式定义的变量来做以上计数器的功能，是很被放心的。

  然而闭包虽好用，却容易造成内存泄漏问题。浏览器自身有垃圾回收机制**（GC）**，即引用数为0的变量或方法所占用的内存空间会被释放回收。通常情况下，一个普通函数执行结束后，因为其私有作用域效果，其内部的所有变量和函数的引用数立刻下降为0，GC会立刻回收这部分内容。 而闭包因为其特性，即使本函数执行结束，也可能有某个外部方法仍然调用着内部的变量导致引用数不为0，造成GC无法回收内存也就是所说的**内存泄漏**

  回到案例，当执行完第一个`counter()`打印`1`后，我们理解函数执行完毕，变量`n`应该被GC回收而不存在，但当我们继续执行第二个`counter()`后发现，打印值是`2`不是`1`，也就是说，这个`func`的私有变量`n`一直被`counter`引用着，因此实现了计数累加。这也就是产生内存泄漏的根源所在。

  关于闭包还可查阅[闭包攻略](https://www.jianshu.com/p/465e5155caec)一文

### 4\. 使用闭包定义私有变量

  通常开发者们会用下划线定义私有变量，但从严格意义上来说这并不准确。闭包能真正做到定义私有变量

```
function Product () {
  var name;   // 函数内的私有变量

  this.setName = function (v) {
    name = v;
  };

  this.getName = function () {
    return name;
  };
}

var person = new Product();

person.setName('zfs');
console.log(person.name);   // undefined
console.log(person.getName());  // ‘zfs’

```

【**原来如此**】
  `name`是函数内的私有变量，外界不可访问，因此`person.name()`为`undefined`，而`getName()`在函数内部能访问到`name`值，因此得到`zfs`。
  这很像一个工厂函数，只是工厂函数通常都会将所有的变量和函数都暴露出来，当然这也不是绝对的，我们同样可以认为这就是一个工厂函数

### 5\. 模块化

  在ES6之前，JavaSrcipt并不是模块化语言，后来有了AMD（`Asynchronous Module Definition`异步模块定义）规范和RequireJs规范，使得JS也能实现模块化编程。直到2015年ES6的出现，JS的模块化变得更加重要和流行。想了解更多模块化知识，可阅读[模块化编程](https://www.jianshu.com/p/ce077877a88c) 一文
  定义一个模块的方式有很多种，既然要成为一个模块，它必就必须要有私有作用域。可以实例化一个对象，用一个独立的文件，或者一个立即执行函数等方法来实现。借鉴大家所熟知的JQuery库，有如下案例

```
var module = (function() {
  let _count = 3;

  function print () {
    console.log(`now count is ${_count }`)
  }

  function plus (x = 1) {
    _count = x + _count 
    print(x) 
  }

  return {
    desc: 'this is a module sample',
    plus: plus
  }
})();

console.log(module.desc);  // "this is a module sample"
module.plus(2);  // now count is 5

```

【**原来如此**】
  稍有用心你会发现，一个模块通常会有几个特性:（1）有个独立的作用域空间，形成模块；（2）有一些私有的方法或变量，用于处理模块内的逻辑，而这些逻辑对外界透明外界也不需要关心；（3）会暴露出一些方法或变量，用于提供外界访问的接口。

  模块化的最优实现方案是控制好自己的私有作用域，隐藏外界不需要关心的变量和处理逻辑，同时不被污染和意外的修改，这与闭包思想一致。如下案例便不是一个好的办法

```
var module = new Object {
  _count: 3;
  f1: function(){
    console.log('I am f1');
  }
}
console.log(module._count);  // 3
module.f1();  // I am f1

```

  对象定义法实现简单，但暴露了所有成员变量，外界可以很随意的通过module本身调用和修改更新内部的成员值。导致模块内部的变量不安全，不是最佳方案

### 6\. 柯里化

  柯里化，即 **Currying**，目的是为了提高函数的灵活性。常规的函数只有一个参数，在学习了闭包的思想后我们得知可以在函数内部返回一个函数，但我们可以一次性传入多个参数，方案如下

```
var plus = function (a) {

  return function (b) {
    console.log(a + b);
  }
}

var add5 = plus(5);
var add8 = plus(8);

plus(20)(15);  // 35
add5(10); // 15
add8(10);  // 18

```

  看完案例是不是觉得很简单？定义一个类似`plus`函数我们可以先传入一个参数，处理一些公用的基础的逻辑，得到一个带有功能的函数体，在用第二个参数来处理各个业务流程不同的需求。避免了重复实现一些基础逻辑的部分。

### 7\. 构造函数

  JS中不存在类，至少在ES6之前是不存在的。此之前，实现这个相应的功能则是通过构造函数和原型链来实现的。

  通常实现一个构造函数有一下几个特点：
（1）构造函数函数名首字母建议(必须)大写，用来区分与普通函数的区别。
（2）内部属性使用`this`来指向即将要生成的属性；
（3）使用`new`关键字来生成实例对象

```
var Person = function () {
  this.name = 'zfs';
  this.age = 25
  this.intro = function () {
    console.log(this.name + ' age '  +  this.age)
  }
}

var p1 = new Person()
console.log(p1.name)  // zfs
p1.intro ()  // zfs age 25

```

  所有的实例对象都可以继承构造器函数中的属性和方法，但是，不同实例对象之间的属性和方法相对独立，无法共享数据。要解决这个问题，需要使用到构造函数的`prototype`原型属性

### 8\. 构造函数的原型 prototype

  先思考一个问题： `let obj = Product()` 和 `let obj = new Product()` 这两者怎么理解？有什么区别？
  前者是将函数`Product`的运行返回值赋给变量`obj`，后者做的是 调用构造函数，创建一个包含`prototype`内部指针的新对象`obj`。

  实际上：每个JavaScript构造函数都有一个`prototype`属性，用于设置所有实例对象需要的共享属性和方法。被声明在`prototype`中的方法和属性不能被枚举，也不能通过 `hasOwnProperty()` 判断，判断对象是否含有某个原型属性需要使用`in`关键字

  关于`prototype`个人认为W3C的说法并不太容易理解反而有点容易混淆，如下

> prototype 属性使您有能力向对象添加属性和方法。
> 语法： `Object.prototype.name = value`

相信很多新手朋友会有如下误解

```
var obj = {
  name: 'zfs',
  age: 25
}
obj.prototype.hobby = 'basketball' 
// TypeError cannot set property `hobby` of undefined

```

  没错啊，根据W3C的讲解，我给对象obj设置了原型属性`hobby`，为什么报错了？

  会这么做的朋友，大都是没有好好理解W3C语法那半句。没有注意到语法中所谓的对象，其实是“`Object`”，它是什么？原生能力较好的朋友应该都清楚，这其实是对象的构造器，我们可以通过它来实例化出实例对象，即

```
let obj = new Object()

```

  `prototype`是构造函数上用来设置共享属性和方法的属性，上述错误主要是错在将`prototype`设置在了实例对象上了，因此编译器抛出异常。正确的用法应该是如下所示：

```
var someone = {}
// 需要在构造函数上设置原型属性
Object.prototype.hobby = 'basketball' 
// in 能遍历出原型属性，而hasOwnProperty() 则不能
for (let s in someone) {
  console.log(s)  // hobby
}

```

`prototype`在构造函数上的应用，更加丰富和值得大家学习掌握

```
// 创建构造函数
function Person (name = 'sg') {
  this.name = name;  // 实例属性 name
  this.visited= [];  // 实例属性 travel
  this.sayHi = function () {  // 实例方法 sayHi()
    console.log(`Hello, ${name}`)
  }
}

Person.prototype.city = 'beijing'  // 原型属性 city
Person.prototype.travel = function (place) {
  this.visited.push(place)
  console.log(this.visited)
}

let zfs = new Person('zfs')
let borui = new Person('borui')

console.log(zfs.name)  // 'zfs'
zfs.sayHi()  // Hello, zfs
console.log(zfs.city);  // beijing
zfs.travel('shanghai'); // ['shanghai']

console.log(borui.name)  // borui  实例属性值各自私有
console.log(borui.city)  // beijing 实例属性被共享
zfs.travel('chongqin')  // ['shagnhai', 'chongqin'] 
// 原型共享属性中，当其中一个实例对象的属性值被修改，不会影响其他实例对象。
zfs.city = 'fujian'; 
console.log(zfs.city);  // fujian
console.log(borui.city);  // beijing

```

  总结一下： `prototype`是设置在构造函数中的用来创建共享属性和方法的特殊属性，用它设置的属性和方法可以被所有的实例对象继承，不同实例对象中的属性修改不会相互影响。
【**扩展**】
  另外，JS属性大家还应该知道三个：（1）静态属性；（2）实例属性；（3）原型属性

```
// 静态属性
function Person () { }
let zfs = new Person()
Person.age = '25'
// 静态属性只能通过 `类名.属性` 形式来访问，无法通过实例访问
console.log(Person.age)  // 25
console.log(zfs.age)  // undefined

```

```
// 实例属性
funciton Person () {
  this.name = 'zfs';  // 用this 引用，能够被实例对象直接继承的属性
}

```

```
function Person() { } 
Person.prototype.name = 'zfs';  // 使用原型定义的属性称为共享属性

```

  想了解更多，请戳 [JS原型与原型链](https://www.jianshu.com/p/d427ce9da34d)

### 9\. 自定义对象

  曾有次面试被问到，了解“自定义对象”吗？ 答： 了解，`var obj = new Object()`.... 然后很自然就被“回去等消息”了。

  其实回答的也没错，只是自定义对象的内容远不止这些。方法很多如（1）字面量法；（2）new 构造函数；（3）ES5中 Object.create(prototype, propertyDescriptor) .....
  这里我们只讲解前两种，第三种并不太好用

  字面量法很简单，也是初学程序员最常用的

```
var obj = {
  name: 'zfs',
  age: 25
}
Object.prototype.sex = 'male'

console.log(obj)  // { name:'zfs', age: 25, sex:'male' }

```

而真正的自定义变量精髓就在于使用构造函数，定制化的创造出我们需要的“类”。利用构造函数生成实例对象。本人回答的`var obj = new Object()`也正是这种办法中的一种，要达到定制化效果，还是需要自己创建构造函数

```
// 根据自己需求创建构造函数
function People(name) {
  this.name = name;
}

let zfs = new Person('zfs')
console.log(zfs.name)  // zfs
cosole.log(zfs.constructor.prototype == Person.prototype)  // true

```

  自定义对象实现多层继承。此时`constructor`返回最先调用的构造函数

```
function People (name) {
  this.name = name
}

function Student(sex) {
  this.sex = sex;
}
// 设置Student的原型为People对象
Student.prototype = new People()

var s = new Student(25)  // 对象初始化时，先调用People(), 再调用Student()
console.log(s.constructor)  // function People 对象s的构造函数是Peopel()
console.log(s.constructor.prototype)  // People()
console.log(s.constructor.prototype == People.prototype)  // true

```

  用法大致如上，至于如何实现自己的需求，关键在于创建好自己的构造函数

### 10\. apply, call 和 bind方法 - 实现多重继承

  每个函数都包含两个非继承过来的方法，`apply()` 和 `call()`，他们都是`Function.prototype` 的方法。

  **`call` 和 `apply` 被用来调用函数，它们都能改变`this`的指向，指定对象（第一个参数）替换函数的`this`值。他们的作用是实现多重继承。**他们也可以不指定参数，此时只是单纯的调用函数如`func.call()`。他们两功能相同，区别在于`call()`的参数需要一一列出；`apply`除第一个`this对象`参数外，其余参数要封装在一个`arguments`数组中

1.  **B.call(A，arg1, arg2):** A对象调用B对象的方法，或者说B对象中的方法在A的`this`环境下运行。参数需要一一列出

```
// 定义一个自定义对象，包含可被继承的方法
var person = {
  name: 'name',
  sayHi: function (args1, args2) {
    console.log(`Hi ${this.name}`);
    console.log(`args1：${args1}， args2：${args2}`)
  }
}
// 欲实现继承的对象本身
var zfs = {
  name: 'zfs'
}

person.sayHi.call(zfs, 'p1', 'p2')
// ---------output-----------
// Hi zfs
// args1: p1，args2: p2

```

【**原来如此**】
`person`对象的方法调用`call()`后执行时的`this`对象被改变成了`zfs`，因此输出`Hi zfs`而不是`Hi name`，而`zfs`对象本身没有`sayHi()`方法，也通过`call()`成功继承了该方法，这就是`call()`方法的作用。

* * *

2.  **B.apply(A, arguments):** A对象调用B对象的方法，参数使用数组形式传递，也就是说除第一个参数外，其他参数封装在一个数组内。

```
var Greeting= {
  greet: 'Good morning!',
  say: function (a, b, c) { 
    console.log(`${this.greet} ${a}, ${b}, ${c}`)
  }
}

var afternoon= {
  greet: 'Good afternoon! '
}

Greeting.say.apply(afternoon, ['zfs', 'borui', 'laic'])
// -----------output-------------
// Good morning! zfs, borui, laic

```

【**注释**】虽然`apply()`的参数是数组形式传递的，但原型函数依旧是多个参数接收。要注意自定义对象的定义并不受调用函数影响，只有调用函数去适应自定义函数本身。

* * *

3.  **B.bind(A, arg1, agr2):** 与前两者不同，它可以为函数绑定`this`值，然后作为一个新的函数返回，并且不会立即执行该函数。其余与`call()`类似

```
var greet = {
  name: 'name',
  say: function (greet) {
    console.log(`${greet} ${this.name}`)
  }
}

const zfs = {
  name: 'zfs'
}

const borui = {
  name: 'borui'
}
// 定义时并不会立即执行，而是以函数形式返回
let greetZfs = greet.say.bind(zfs, 'Good morning!')
let greetBorui = greet.say.bind(borui)

greetZfs()   // Good morning! zfs
greetBorui('Hello')  // Hello borui

greetZfs('Hi')  // Good morning zfs

```

【**原来如此**】
  案例中，如`greetZfs`得到的其实是`greet`对象中被改变了`this`指向的`say()`函数本身，而不是`say()`的执行结果。对比一下第一个和第三个打印会发现输出都是`Good morning zfs`而不是`Hi zfs`，即`bind()`方法本身参数优先级高于结果函数传入参数的优先级

### 11\. Memoization - 解决递归卡死

  Memoize 用于优化比较耗时的计算，通过计算将结果缓存到内存中，这样对于同样的输入值，下次只需要从内存中获取即可。数学上有个经典的斐波那契数列，如下

【**注**】 `arguments`用于保存函数参数，`callee`是`arguments`的一个属性，返回正在被调用的函数对象。这有利于函数的递归和保证函数的封装性

```
var fibonacci = function (n) {
  return n < 2 ? n : arguments.callee(n - 1) + arguments.callee(n - 2);
}
console.log(fibonacci(8))

```

  随着系数`n`的不断加大，运行时间也随之增加，页面性能急速下降。当增加到40时，ff和 ie开始进入僵死状态，UI线程被阻塞！页面卡死。著名的underscore 的 `Memoize`方法，成功解决了该问题

```
var fibonacci = _.memoize(function (n) {
  return n < 2 ? n : arguments.callee(n - 1) + arguments.callee(n - 2);
})
console.log(fibonacci(40))
// 源码：已参数作为键进行缓存，利用空间换cpu运行时间
_.memoize = function(func, hasher) {
  var memoize = function (key) {
    var cache = memoize.cache;
    var address = hasher ? hasher.apply(this, arguments) : key
    if (!_.has(cache, address)) {
        cache[address] = func.apply(this, arguments) 
    }
    return cache(address)
  }
  memoize.cache = {};
  return memoize;
}

```

  换个比较容易理解的写法如下

```
function memoizeFunc (func) {
  var cache = {};
  return function () {
    var key = arguments[0];
    if (!cache[key]) {
      cache[key] = func.apply(this, arguments)
    }
    return cache[key]
  }
}

```

  这样，我们就不担心页面卡死问题，该类思想适合处理递归问题。也玩玩递归现象比较容易出现大量的计算量

### 12\. 函数重载

  所谓**函数重载（method overloading）** 即函数名称一样，但输入输出不一样。也就是说允许函数有各种不同的输入，根据不同的输入，返回的结果不同。jQuery之父John Resig提出了一个非常巧(bian)妙(tai)的方法，`利用了闭包`。

  假设有如下一个需求，有一个`people`对象，里面存着一些人名，如下:

```
var people = {
  value: ["Dean Edwards", "Sam Stephenson", "Alex Russell", "Dean Tom"]
}

```

  我们希望`people`对象中拥有一个`find`方法，当不传任何参数时，就会把`people.value`里面的所有元素返回来，当传入一个参数时，就把`first-name`跟这个参数匹配的元素返回来，当传两个参数时，则把`first-name`和`last-name`都匹配的值才返回来。这个`find`方法是根据参数的个数不同而执行不同的操作，所以我们希望有一个`addMethod`方法，能够如下的为`people`添加`find`重载

```
addMethod(people, "find", function() {}); /*不传参*/
addMethod(people, "find", function(a) {}); /*传一个*/
addMethod(people, "find", function(a, b) {}); /*传两个*/

```

  难点在于如何实现这个`addMethod()`方法，John Resig的实现方法如下：思路是当绑定find_2(两个参数，下同)时，`old`为find_1，当绑定find_1时，`old`为find_0；利用这种闭包调用的思想实现了不同处理函数的链接。

```
function addMethod (object, name, fn) {
  // 把前一次添加的方法存在一个临时变量old里面
  var old = object[name];
  // 重写object[name]的方法
  object[name] = function () {
    // 如果调用object[name]方法时，传入的参数个数跟预期的一致，则直接调用
    if (fn.length === arguments.length ) {
      return fn.apply(this, arguments)
    } else if (typeof old === "function") { // old如果是函数，调用
      return 
    }
  }
}

```

> `fn.length`表示函数形参的个数，即函数定义时参数的个数。见如下【**拓展**】

  实现了`addMethod`方法，接下来开始实现`people.find`方法的重载

```
// 当不传递参数时，返回`people.values`里面的所有元素
addMethod(people, 'find', function() {
  return this.values
})
// 传入一个参数时，按first-name的匹配进行返回
addMethod(people, "find", function() {
  var ret = [];
  for (let i in this.values) {
    this.values[i].indexOf(firstName) === 0 && ret.push(this.value[i]);
  }
  return ret;
})
// 传入两个参数时，返回first-name和last-name都匹配的元素
addMethod(people, "find", function(firstName, lastName) {
  var ret = []
  for (let i in this.values) {
    this.values[i] === (`${firstName} ${lastName}`) && ret.push(this.values[i])
  }
  return ret;
})

```

  这样我们就实现了find的函数根据不同输入返回不同结果的功能，测试验证一下

```
// 分别传入0， 1， 2个参数测试
console.log(people.find());  // ["Dean Edwards", "Alex Russell", "Dean Tom"]
console.log(people.find("Dean"));  // ["Dean Edwards", "Dean Tom"]
console.log(people.find("Dean Edwards"));  // ["Dean Edwards"]

```

* * *

##### 【**拓展**】`fn.length`与`arguments.length`的区别

  这两者均表示函数参数的个数，但指代不同。`fn.length`表示函数形参的个数，即函数定义是参数的个数。`arguments.length`表示函数实参的个数，表示函数调用时传递进来的参数个数。如下案例

```
function find (a,b,c,d) {
  console.log(arguments.length)  // 9
}

find(1,2,3,4,5,6,7,8,9)

console.log(find.length)  // 4

```

### 参考文献

*   [浅谈函数重载](http://www.cnblogs.com/yugege/p/5539020.html)