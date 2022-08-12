### 1. Set
ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。

Set本身是一个构造函数，用来生成 Set 数据结构。
Set函数可以接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化。
```javascript
// 例一
const set = new Set([1, 2, 3, 4, 4]);
[...set]
// [1, 2, 3, 4]

// 例二
const items = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
items.size // 5

// 例三
const set = new Set(document.querySelectorAll('div'));
set.size // 56

// 类似于
const set = new Set();
document
 .querySelectorAll('div')
 .forEach(div => set.add(div));
set.size // 56
```
上面代码中，例一和例二都是Set函数接受数组作为参数，例三是接受类似数组的对象作为参数。

**去除数组重复成员的方法：**`[...new Set(array)]`
**去除字符串里面的重复字符：**`[...new Set('ababbc')].join('')`

#### Set实例的属性和方法

**属性：**
* **Set.prototype.constructor**：构造函数，默认就是Set函数。
* **Set.prototype.size**：返回Set实例的成员总数。

实例的方法分为两大类：**操作方法**（用于操作数据）和**遍历方法**（用于遍历成员）。
--**操作方法**：

* **Set.prototype.add(value)**：添加某个值，返回 Set 结构本身。
* **Set.prototype.delete(value)**：删除某个值，返回一个布尔值，表示删除是否成功。
* **Set.prototype.has(value)**：返回一个布尔值，表示该值是否为Set的成员。
* **Set.prototype.clear()**：清除所有成员，没有返回值。

```javascript
const s = new Set();
s.add(1).add(2).add(2);
// 注意2被加入了两次

s.size // 2

s.has(1) // true
s.has(2) // true
s.has(3) // false

s.delete(2);
s.has(2) // false
```

--**遍历方法：**

* **Set.prototype.keys()**：返回键名的遍历器
* **Set.prototype.values()**：返回键值的遍历器
* **Set.prototype.entries()**：返回键值对的遍历器
* **Set.prototype.forEach()**：使用回调函数遍历每个成员

```javascript
let set = new Set(['red', 'green', 'blue']);

for (let item of set.keys()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.values()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.entries()) {
  console.log(item);
}
// ["red", "red"]
// ["green", "green"]
// ["blue", "blue"]
```
### 2. Map
  JavaScript 的对象（Object），本质上是键值对的集合（Hash 结构），但是传统上只能用字符串当作键。这给它的使用带来了很大的限制。
  为了解决这个问题，ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。也就是说，Object 结构提供了“字符串—值”的对应，Map 结构提供了“值—值”的对应，是一种更完善的 Hash 结构实现。如果你需要“键值对”的数据结构，Map 比 Object 更合适。

```javascript
const m = new Map();
const o = {p: 'Hello World'};

m.set(o, 'content')
m.get(o) // "content"

m.has(o) // true
m.delete(o) // true
m.has(o) // false
```
上面代码使用 Map 结构的set方法，将对象o当作m的一个键，然后又使用get方法读取这个键，接着使用delete方法删除了这个键。

上面的例子展示了如何向 Map 添加成员。作为构造函数，Map 也可以接受一个数组作为参数。该数组的成员是一个个表示键值对的数组。
```javascript
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);

map.size // 2
map.has('name') // true
map.get('name') // "张三"
map.has('title') // true
map.get('title') // "Author"
```
#### Map实例的属性和方法
**属性：**

* **size**：返回 Map 结构的成员总数。

**操作方法：**
* **Map.prototype.set(key, value)**：set方法设置键名key对应的键值为value，然后返回整个 Map 结构。如果key已经有值，则键值会被更新，否则就新生成该键。
* **Map.prototype.get(key)**：get方法读取key对应的键值，如果找不到key，返回undefined。
* **Map.prototype.has(key)**：has方法返回一个布尔值，表示某个键是否在当前 Map 对象之中。
* **Map.prototype.delete(key)**：delete方法删除某个键，返回true。如果删除失败，返回false。
* **Map.prototype.clear()**：clear方法清除所有成员，没有返回值。

**遍历方法**：

* **Map.prototype.keys()**：返回键名的遍历器。
* **Map.prototype.values()**：返回键值的遍历器。
* **Map.prototype.entries()**：返回所有成员的遍历器。
* **Map.prototype.forEach()**：遍历 Map 的所有成员。

需要特别注意的是，Map 的遍历顺序就是插入顺序。

```javascript
const map = new Map([
  ['F', 'no'],
  ['T',  'yes'],
]);

for (let key of map.keys()) {
  console.log(key);
}
// "F"
// "T"

for (let value of map.values()) {
  console.log(value);
}
// "no"
// "yes"

for (let item of map.entries()) {
  console.log(item[0], item[1]);
}
// "F" "no"
// "T" "yes"

// 或者
for (let [key, value] of map.entries()) {
  console.log(key, value);
}
// "F" "no"
// "T" "yes"

// 等同于使用map.entries()
for (let [key, value] of map) {
  console.log(key, value);
}
// "F" "no"
// "T" "yes"

map.forEach((value, key, map) => {
  console.log(value, key, map);
})
// no F  {"F" => "no", "T" => "yes"}
// yes T {"F" => "no", "T" => "yes"}
```

### 3. 扩展应用

**数组去重：**

```javascript
const arr = [1,2,3,3,5,6,2];
const arr1 = [...new Set(arr)]; // 方法一
const arr2 = Array.from(new Set(arr)); // 方法二
console.log(arr1); // [1, 2, 3, 5, 6]
console.log(arr2); // [1, 2, 3, 5, 6]
```

**字符串去重：**
```javascript
const str = 'abbfha';
const str1 = [...new Set(str)].join('');  // 方法一
const str2 = Array.from(new Set(str)).join(''); // 方法二
console.log(str1); // abfh
console.log(str2); // abfh
```

