# 一、原型、原型链
原型是Javascript中的继承的基础，JavaScript的继承主要依靠原型链来实现的。

### 原型
​	在JavaScript中，我们创建一个函数A(就是声明一个函数), 就会为该函数创建一个`prototype`属性。而且也会在内存中创建一个对象B，A函数的属性 prototype 指向这个对象B( 即：prototype的属性的值是这个对象 )。这个对象B就是函数A的原型对象，简称函数的原型。这个原型对象B 默认会有一个属性 constructor， constructor属性指向函数A ( 意思就是说：constructor属性的值是函数A )。

看下面的代码：
```html
<body>
    <script type="text/javascript">
    	/*
    		声明一个函数，则这个函数默认会有一个属性叫 prototype 。而且浏览器会自动按照一定的规则
    		创建一个对象，这个对象就是这个函数的原型对象，prototype属性指向这个原型对象。这个原型对象
    		有一个属性叫constructor 执行了这个函数
			
			注意：原型对象默认只有属性：constructor。其他都是从Object继承而来，暂且不用考虑。
		*/
	    function Person () {
	    	
	    }	    
    </script>
</body>
```
下面的图描述了声明一个函数之后发生的事情：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-863a15f9e4f03963.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当把一个函数作为构造函数 (理论上任何函数都可以作为构造函数) 使用new创建对象的时候，那么这个对象就会存在一个默认的不可见的属性，来指向了构造函数的原型对象。 这个不可见的属性我们一般用 [[prototype]] 来表示，只是这个属性没有办法直接访问到。

看下面的代码：
```html
<body>
    <script type="text/javascript">
	    function Person () {
	    	
	    }	
        /*
        	利用构造函数创建一个对象，则这个对象会自动添加一个不可见的属性 [[prototype]], 而且这个属性
        	指向了构造函数的原型对象。
        */
      	var p1 = new Person();
    </script>
</body>

```
观察下面的示意图：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-d49425b664445b68.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 原型链
每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，而实例都包含一个指向原型对象的内部指针。假如让一个原型对象等于另一个类型的实例，那么此时的原型对象将包含一个指向另一个原型的指针，相应的，另一个原型中也包含一个指向另一个构造函数的指针。假如另一个原型又是另一个类型的实例，那么上述关系依然成立，如此层层递进，就构成了实例与原型的链条，这就是我们说的原型链。

实现原型链有一种基本模式，代码如下：
```javascript
function SuperType() {
  this.prototype = true;
}
SuperType.prototype.getSuperValue = function () {
  return this.prototype
}

function SubType () {
  this.subprototype = false;
}
SubType.prototype = new SuperType();
SubType.prototype.getSubValue = function () {
  return this.subprototype 
}

var instance = new SubType();
alert(instance.getSuperValue()); // true
```
以上代码定义了两个类型：SuperType和SubType。每个类型分别有一个属性和方法。它们的主要区别是SubType继承了SuperType，而继承是通过创建SuperType实例。换句话说，原来存在于SuperType的实例中的所有属性和方法，现在也存在于SubType.prototype中了。在确立了继承关系之后，我们给SubType.prototype添加了一个方法，这样就在继承了SuperType的属性和方法的基础上又添加了一个新方法。这个例子中的实例及构造函数和原型之间的关系如图：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-1c8c36c40b489f47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/18030682-11c15c15d2d11c1c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 二、继承
**继承的方法：**1.原型链继承；2.构造函数继承；3.组合继承； 4.原型式继承； 5.寄生式继承 ；6.寄生组合式继承；

我们先定义一个父类

### 原型链继承

```javascript
function SuperType() {
  this.property = true;
}
SuperType.prototype.getSuperValue = function() {
  return this.property;
}
function SubType() {
  this.Subproperty = false;
}
SubType.prototype = new SuperType(); // 继承了SuperType
SubType.prototype.getSubValue = function() {
  return this.Subproperty ;
}
var instance = new SubType();
alert(instance.getSuperValue()) // true
```
**继承原理：**通过让子类的原型等于父类的实例，来实现继承。
优点：继承了超类型的构造函数的所有属性和方法。
缺点：1、在创建子类实例时，无法向超类型的构造函数传参，继承单一。
　　　2、所有新实例都会共享父类实例的属性。（原型上的属性是共享的，一个实例修改了原型引用类型的属性，另一个实例的原型属性也会被修改！）

```
function SuperType() {
  this.colors = ['red','blue','green'];
}
function SubType() {
}
SubType.prototype = new SuperType(); // 继承方法

var instance1 = new SubType();
instance1.colors.push('black');
console.log(instance1.colors); // ['red','blue','green','black']

var instance2 = new SubType();
console.log(instance2.colors); // ['red','blue','green','black']
```

### 构造函数继承

```
function SuperType() {
  this.colors = ['red', 'blue', 'green'];
}
function SubType() {
  SuperType.call(this); //  继承了SuperType
}
var instance1 = new SubType();
instance1.colors.push('black');
console.log(instance1.colors); // ['red', 'blue', 'green', 'black']

var instance2 = new SubType();
console.log(instance2.colors); // ['red', 'blue', 'green']
```
在子类的内部调用父类，通过call改变父类中this的指向。等于是复制父类的实例属性给子类。
优点：可以在子类构造函数中，向超类型构造函数传递参数。
缺点：只继承了父类构造函数的属性，没有继承父类原型的属性。所有的方法都在构造函数中定义，无法实现复用，影响性能。

### 组合继承 （原型链+构造函数）

```
function SuperType(name) {
  this.name = name;
  this.colors = ['red','blue','green'];
}
SuperType.prototype.sayNAme = function() {
  alert(this.name);
}
function SubType(name,age) {
  SuperType.call(this, name); // 继承属性
  this.age = age;
}
SubType.prototype = new SuperType(); // 继承方法
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function() {
  alert(this.age);
}

var instance1 = new SubType('xxx', 15);
instance1.colors.push('black');
console.log(instance1.colors); // ['red','blue','green','black']
instance1.sayNAme(); // xxx
instance1.sayAge(); // 15

var instance2 = new SubType('yyy', 12);
console.log(instance2.colors); // ['red','blue','green']
instance2.sayNAme(); // yyy
instance2.sayAge(); // 12
```
使用原型链实现对原型属性和方法的继承，通过构造函数来实现对实例属性的继承。这样既通过在原型上定义方法实现了函数的复用，又能够保证每个实例都有它自己的属性。
缺点：调用两次父类构造函数。

# 三、与原型有关的几个属性和方法
### 2.1	prototype属性
​	prototype 存在于构造函数中 (其实任意函数中都有，只是不是构造函数的时候prototype我们不关注而已) ，他指向了这个构造函数的原型对象。

	参考前面的示意图。

### 2.2	constructor属性
​	constructor属性存在于原型对象中，他指向了构造函数

看下面的代码：
```
<script type="text/javascript">
	function Person () {
	}
	alert(Person.prototype.constructor === Person);	// true
	var p1 = new Person();
  	//使用instanceof 操作符可以判断一个对象的类型。  
  	//typeof一般用来获取简单类型和函数。而引用类型一般使用instanceof，因为引用类型用typeof 总是返回object。
	alert(p1 instanceof Person);	// true
</script>
```
>我们根据需要，可以Person.prototype 属性指定新的对象，来作为Person的原型对象。

但是这个时候有个问题，新的对象的constructor属性则不再指向Person构造函数了。

看下面的代码：
```
<script type="text/javascript">
	function Person () {
		
	}
	//直接给Person的原型指定对象字面量。则这个对象的constructor属性不再指向Person函数
	Person.prototype = {
		name:"志玲",
		age:20
	};
	var p1 = new Person();
	alert(p1.name);  // 志玲

	alert(p1 instanceof Person); // true
	alert(Person.prototype.constructor === Person); //false
  	//如果constructor对你很重要，你应该在Person.prototype中添加一行这样的代码：
  	/*
  	Person.prototype.constructor = Person;	//让constructor重新指向Person函数

  	*/
</script>
```
### 2.3	_ _ proto _ _属性(注意：左右各是2个下划线)
​	用构造方法创建一个新的对象之后，这个对象中默认会有一个不可访问的属性 [[prototype]] , 这个属性就指向了构造方法的原型对象。

	但是在个别浏览器中，也提供了对这个属性[[prototype]]的访问(chrome浏览器和火狐浏览器。ie浏览器不支持)。访问方式：p1.__proto__
	
	但是开发者尽量不要用这种方式去访问，因为操作不慎会改变这个对象的继承原型链。
```
<script type="text/javascript">
	function Person () {
		
	}
	//直接给Person的原型指定对象字面量。则这个对象的constructor属性不再指向Person函数
	Person.prototype = {
		constructor : Person,
		name:"志玲",
		age:20
	};
	var p1 = new Person();

	alert(p1.__proto__ === Person.prototype);	//true
	
</script>
```
### 2.4	hasOwnProperty() 方法
​	大家知道，我们用去访问一个对象的属性的时候，这个属性既有可能来自对象本身，也有可能来自这个对象的[[prototype]]属性指向的原型。

	那么如何判断这个对象的来源呢？
	
	hasOwnProperty方法，可以判断一个属性是否来自对象本身。
```
<script type="text/javascript">
	function Person () {
		
	}
	Person.prototype.name = "志玲";
	var p1 = new Person();
	p1.sex = "女";
  	//sex属性是直接在p1属性中添加，所以是true
	alert("sex属性是对象本身的：" + p1.hasOwnProperty("sex"));
  	// name属性是在原型中添加的，所以是false
	alert("name属性是对象本身的：" + p1.hasOwnProperty("name"));
  	//  age 属性不存在，所以也是false
	alert("age属性是存在于对象本身：" + p1.hasOwnProperty("age"));
	
</script>
```
所以，通过hasOwnProperty这个方法可以判断一个对象是否在对象本身添加的，但是不能判断是否存在于原型中，因为有可能这个属性不存在。

也即是说，在原型中的属性和不存在的属性都会返回fasle。

如何判断一个属性是否存在于原型中呢？

### 2.5	in 操作符
	in操作符用来判断一个属性是否存在于这个对象中。但是在查找这个属性时候，现在对象本身中找，如果对象找不到再去原型中找。换句话说，只要对象和原型中有一个地方存在这个属性，就返回true
```
<script type="text/javascript">
	function Person () {
		
	}
	Person.prototype.name = "志玲";
	var p1 = new Person();
	p1.sex = "女";
	alert("sex" in p1);		// 对象本身添加的，所以true
	alert("name" in p1);	//原型中存在，所以true
	alert("age" in p1); 	//对象和原型中都不存在，所以false
	
</script>
```

回到前面的问题，如果判断一个属性是否存在于原型中：

如果一个属性存在，但是没有在对象本身中，则一定存在于原型中。
```
<script type="text/javascript">
	function Person () {
	}
	Person.prototype.name = "志玲";
	var p1 = new Person();
	p1.sex = "女";
	
	//定义一个函数去判断原型所在的位置
	function propertyLocation(obj, prop){
		if(!(prop in obj)){
			alert(prop + "属性不存在");
		}else if(obj.hasOwnProperty(prop)){
			alert(prop + "属性存在于对象中");
		}else {
			alert(prop + "对象存在于原型中");
		}
	}
	propertyLocation(p1, "age");
	propertyLocation(p1, "name");
	propertyLocation(p1, "sex");
</script>
```