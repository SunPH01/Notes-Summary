### 1. Object.assign()
该方法用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。它将返回目标对象。

>**语法： `Object.assign(target, ...sources)`**

**参数：**
* **target** -- 目标对象。
* **sources** -- 源对象。

**返回值：**目标对象。

**示例：**
```
const target = { a: 1, b: 2 };
const source = { b: 4, c: 5 };

const returnedTarget = Object.assign(target, source);

console.log(target);
// expected output: Object { a: 1, b: 4, c: 5 }

console.log(returnedTarget);
// expected output: Object { a: 1, b: 4, c: 5 }
```

### 2. Object.getPrototypeOf()
该方法返回指定对象的原型（内部[[Prototype]]属性的值）。

>**语法：`Object.getPrototypeOf(object)`**

**参数:**
* **obj** -- 要返回其原型的对象。

**返回值** -- 给定对象的原型。如果没有继承属性，则返回 null 。
```
const prototype1 = {};
const object1 = Object.create(prototype1);
console.log(Object.getPrototypeOf(object1) === prototype1); // true
```

### 3. Object.create()
该方法创建一个新对象，使用现有的对象来提供新创建的对象的\__proto__。 

>**语法：`Object.create(proto[, propertiesObject])`**

**参数:**
* **proto* -- 新创建对象的原型对象。
* **propertiesObject** -- 可选。可选。如果没有指定为 [`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined "undefined是全局对象的一个属性。也就是说，它是全局作用域的一个变量。undefined的最初值就是原始数据类型undefined。")，则是要添加到新创建对象的可枚举属性（即其自身定义的属性，而不是其原型链上的枚举属性）对象的属性描述符以及相应的属性名称。这些属性对应[`Object.defineProperties()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperties "Object.defineProperties() 方法直接在一个对象上定义新的属性或修改现有属性，并返回该对象。")的第二个参数。

**返回值：**一个新对象，带着指定的原型对象和属性。

**示例：**
```
const person = {
  isHuman: false,
  printIntroduction: function () {
    console.log(`My name is ${this.name}. Am I human? ${this.isHuman}`);
  }
};

const me = Object.create(person);

me.name = "Matthew"; // "name" is a property set on "me", but not on "person"
me.isHuman = true; // inherited properties can be overwritten

me.printIntroduction(); //   "My name is Matthew. Am I human? true"
person.printIntroduction(); // "My name is undefined. Am I human? false"
```
![image.png](https://upload-images.jianshu.io/upload_images/18030682-0dd7588ba8de502a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 4. Object.is()
该方法判断两个值是否是相同的值。

>**语法：`Object.is(value1, value2);`**

**参数:**
* **value1** -- 第一个需要比较的值。
* **value2** -- 第二个需要比较的值。

**返回值** -- 表示两个参数是否相同的布尔值 。

**Object.is() 判断两个值是否相同。如果下列任何一项成立，则两个值相同：**
* 两个值都是 **undefined**
* 两个值都是 **null**
* 两个值都是 **true** 或者都是 **false**
* 两个值是由**相同个数的字符**按照**相同的顺序**组成的**字符串**
* 两个值**指向同一个对象**
* 两个值都是**数字**并且都是**正零 +0 / 负零 -0 / NaN / 除零和 NaN 外的其它同一个数字**

这种相等性判断逻辑和传统的 == 运算不同，== 运算符会对它两边的操作数做隐式类型转换（如果它们类型不同），然后才进行相等性比较，（所以才会有类似 "" == false 等于 true 的现象），但 Object.is 不会做这种类型转换。
这与 === 运算符的判定方式也不一样。=== 运算符（和== 运算符）将数字值 -0 和 +0 视为相等，并认为 Number.NaN 不等于 NaN。
```
const obj1 = {
  a: 33,
  b: 'ed'
};
const obj2 = {
  a: 33,
  b: 'ed'
}
const obj3 = obj1;
console.log(Object.is(obj1, obj2)); // false
console.log(Object.is(obj1, obj3)); // true
console.log(Object.is('foo', 'foo')); // true
console.log(Object.is([], [])); // false
console.log(Object.is({}, {})); // false
```

### 5. Object.defineProperties()、Object.defineProperty()
**Object.defineProperties()** 方法直接在一个对象上定义**一个或多个**新的属性或修改现有属性，并返回该对象。
**Object.defineProperty()** 方法会直接在一个对象上定义**一个**新属性，或者修改一个对象的现有属性， 并返回这个对象。

>**语法:** `Object.defineProperties(obj, props)`

**参数**
* **obj** -- 在其上定义或修改属性的对象。
* **props** -- 要定义其可枚举属性或修改的属性描述符的对象。对象中存在的属性描述符主要有两种：数据描述符和访问器描述符（更多详情，请参阅[`Object.defineProperty()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty "Object.defineProperty() 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性， 并返回这个对象。")）。描述符具有以下键：
**configurable**
当且仅当该属性的 configurable 为 true 时，该属性描述符才能够被改变，同时该属性也能从对应的对象上被删除。默认为 false
**enumerable**
表示该属性是否可枚举，即是否通过for-in循环或Object.keys()返回属性，默认为false。如果直接使用字面量定义对象，默认值为true
**value**
与属性关联的值。可以是任何有效的JavaScript值（数字，对象，函数等）。默认为 undefined.
**writable**
能否修改属性的值。默认为 false。如果直接使用字面量定义对象，默认值为true
**get**
一个给属性提供 getter 的方法，如果没有 getter 则为 undefined。当访问该属性时，该方法会被执行，方法执行时没有参数传入，但是会传入this对象（由于继承关系，这里的this并不一定是定义该属性的对象）。默认为 undefined
**set**
一个给属性提供 setter 的方法，如果没有 setter 则为 undefined。当属性值修改时，触发执行该方法。该方法将接受唯一参数，即该属性新的参数值。默认为 undefined

描述符可同时具有的键值:
![image.png](https://upload-images.jianshu.io/upload_images/18030682-37fdf5682b2f6d9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**返回值：**传递给函数的对象。

**示例：**
```
var obj = {};
Object.defineProperties(obj, {
  'property1': {
    value: true,
    writable: true
  },
  'property2': {
    value: 'Hello',
    writable: false
  }
  // etc. etc.
});
```

### 6. Object.entries()
该方法返回一个给定对象自身可枚举属性的键值对数组，其排列与使用 [`for...in`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in "for...in语句以任意顺序遍历一个对象自有的、继承的、可枚举的、非Symbol的属性。对于每个不同的属性，语句都会被执行。") 循环遍历该对象时返回的顺序一致（区别在于 for-in 循环也枚举原型链中的属性）。

>**语法：** `Object.entries(obj)`

**参数：obj** -- 可以返回其可枚举属性的键值对的对象。
**返回值：**给定对象自身可枚举属性的键值对数组。

**示例：**
```
const obj = { foo: 'bar', baz: 42 };
console.log(Object.entries(obj)); // [ ['foo', 'bar'], ['baz', 42] ]
// 数组和对象一样，具有随机的键值对顺序
const anObj = { 100: 'a', 2: 'b', 7: 'c' };
console.log(Object.entries(anObj)); // [ ['2', 'b'], ['7', 'c'], ['100', 'a'] ]
console.log(Object.entries('foo')); // [ ['0', 'f'], ['1', 'o'], ['2', 'o'] ]
```
### 7. Object.freeze() 
该方法可以冻结一个对象。一个被冻结的对象再也不能被修改；冻结了一个对象则不能向这个对象添加新的属性，不能删除已有属性，不能修改该对象已有属性的可枚举性、可配置性、可写性，以及不能修改已有属性的值。此外，冻结一个对象后该对象的原型也不能被修改。freeze() 返回和传入的参数相同的对象。

```
const obj = {
  prop: 42
};
Object.freeze(obj);
obj.prop = 33; // Throws an error in strict mode
console.log(obj.prop); // 42
```

### 8. Object.fromEntries()
该方法把键值对列表转换为一个对象。

>**语法：** `Object.fromEntries(iterable)`

**参数：iterable** -- 可迭代对象，类似 Array 、 Map 或者其它实现了可迭代协议的对象。
**返回值：**一个由该迭代对象条目提供对应属性的新对象。

**示例：**
```
const entries = [
  ['foo', 'bar'],
  ['baz', 42]
];
const obj = Object.fromEntries(entries);
console.log(obj); // { foo: "bar", baz: 42 }
```
### 9. Object.getOwnPropertyDescriptor()、Object.getOwnPropertyDescriptors()
**Object.getOwnPropertyDescriptor()** 方法返回指定对象上**一个**自有属性对应的属性描述符。（自有属性指的是直接赋予该对象的属性，不需要从原型链上进行查找的属性）。
**Object.getOwnPropertyDescriptors()** 方法用来获取一个对象的**所有**自身属性的描述符。

>**语法：**
>`Object.getOwnPropertyDescriptor(obj, prop)`
>`Object.getOwnPropertyDescriptors(obj)`

**参数：**
**getOwnPropertyDescriptor:**
* **obj** -- 需要查找的目标对象。
* **prop** -- 目标对象内属性名称。

**getOwnPropertyDescriptors:**
* **obj** -- 任意对象。


**返回值：**
**getOwnPropertyDescriptor：**如果指定的属性存在于对象上，则返回其属性描述符对象（property descriptor），否则返回 undefined。
**getOwnPropertyDescriptors：**所指定对象的所有自身属性的描述符，如果没有任何自身属性，则返回空对象。
```
const o = { bar: 42, foo: 'ew3', sl: 23 };
const d = Object.getOwnPropertyDescriptor(o, "bar");
console.log(d); // { configurable: true,enumerable: true,value: 42,writable: true }

const ds = Object.getOwnPropertyDescriptors(o);
console.log(ds);
// {
//   bar: {value: 42, writable : true, enumerable: true, configurable: true},
//   foo: {value: "ew3", writable: true, enumerable: true, configurable: true},
//   sl: {value: 23, writable: true, enumerable: true, configurable: true},
// }
```

### 10. Object.keys()
该方法会返回一个由一个给定对象的自身可枚举属性组成的数组，数组中属性名的排列顺序和使用 for...in 循环遍历该对象时返回的顺序一致 。如果对象的键-值都不可枚举，那么将返回由键组成的数组。

>**语法：** `Object.keys(obj)`

**参数：**
* **obj** -- 要返回其枚举自身属性的对象。

**返回值：**一个表示给定对象的所有可枚举属性的字符串数组。

Object.keys 返回一个所有元素为字符串的数组，其元素来自于从给定的object上面可直接枚举的属性。这些属性的顺序与手动遍历该对象属性时的一致。
```
// simple array
var arr = ['a', 'b', 'c'];
console.log(Object.keys(arr)); // console: ['0', '1', '2']

// array like object
var obj = { 0: 'a', 1: 'b', 2: 'c' };
console.log(Object.keys(obj)); // console: ['0', '1', '2']

// array like object with random key ordering
var anObj = { 100: 'a', 2: 'b', 7: 'c' };
console.log(Object.keys(anObj)); // console: ['2', '7', '100']
```

### 11. Object.values()
返回一个给定对象自身的所有可枚举属性值的数组，值的顺序与使用for...in循环的顺序相同 ( 区别在于 for-in 循环枚举原型链中的属性 )。

>**语法：** `Object.values(obj)`

**参数：**
* **obj** -- 被返回可枚举属性值的对象。

**返回值：**一个包含对象自身的所有可枚举属性值的数组。
```
var obj = { foo: 'bar', baz: 42 };
console.log(Object.values(obj)); // ['bar', 42]

// array like object
var obj = { 0: 'a', 1: 'b', 2: 'c' };
console.log(Object.values(obj)); // ['a', 'b', 'c']

// array like object with random key ordering
// when we use numeric keys, the value returned in a numerical order according to the keys
var an_obj = { 100: 'a', 2: 'b', 7: 'c' };
console.log(Object.values(an_obj)); // ['b', 'c', 'a']
```

### 12. hasOwnProperty()
返回一个布尔值，指示对象自身属性中是否具有指定的属性（也就是是否有指定的键）。

>语法 `obj.hasOwnProperty(prop)`

**参数:**
**prop** -- 要检测的属性  字符串 名称或者 Symbol。

**返回值：**用来判断某个对象是否含有指定的属性的 Boolean 。

所有继承了 Object 的对象都会继承到 hasOwnProperty 方法。这个方法可以用来检测一个对象是否含有特定的自身属性；和 in 运算符不同，该方法会忽略掉那些从原型链上继承到的属性。
```
o = new Object();
o.prop = 'exists';
o.hasOwnProperty('prop');             // 返回 true
o.hasOwnProperty('toString');         // 返回 false
o.hasOwnProperty('hasOwnProperty');   // 返回 false
```

### 13. toLocaleString()
返回一个该对象的字符串表示。此方法被用于派生对象为了特定语言环境的目的（locale-specific purposes）而重载使用。

>语法 `obj.toLocaleString()`

**返回值：**表示对象的字符串。

**覆盖 toLocaleString 的对象：**
* Array：Array.prototype.toLocaleString()
* Number：Number.prototype.toLocaleString()
* Date：Date.prototype.toLocaleString()

```
const arr = ["George", "John", "Thomas"];
console.log(arr.toLocaleString()); // George,John,Thomas

const num = 345623;
console.log(num, num.toLocaleString(), num.toString()); // 345623 "345,623" "345623"

const dates = new Date();
console.log(dates, dates.toLocaleString()); 
// Wed Aug 21 2019 11:20:35 GMT+0800 (中国标准时间) "2019/8/21 上午11:20:35"
```

### 14. toString()
返回一个表示该对象的字符串。
>语法 `obj.toString()`

**返回值：**一个表示该对象的字符串。

每个对象都有一个 toString() 方法，当该对象被表示为一个文本值时，或者一个对象以预期的字符串方式引用时自动调用。默认情况下，toString() 方法被每个 Object 对象继承。