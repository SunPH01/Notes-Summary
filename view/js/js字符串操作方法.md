**提示：**ECMAScript 中的字符串是不可变的，字符串不能更改，只能替换。所以以下操作方法均不能改变原字符串，下面的讲解中将不对源字符串做打印观察，只打印观察新字符串的值。
## 1. length
获取字符串的长度

```
var browserType = 'mozilla';
browserType.length;//7
```
## 2. str[idx]、charAt()、charCodeAt()
`str[idx]` 属性访问
`charAt() `方法返回字符串中指定下标（位置）的字符串
`charCodeAt()` 方法返回字符串中指定索引的字符 unicode 编码

```
browserType[0];  //m
browserType.charAt(0);  //m
browserType[-2];  //undefined
browserType.charAt(-2);  //""
```
**注意:**小标是从0开始，不是1！

使用属性访问有点不太靠谱：
* 不适用 Internet Explorer 7 或更早的版本
* 它让字符串看起来像是数组（其实并不是）
* 如果找不到字符，[ ] 返回 undefined，而 charAt() 返回空字符串。
* 它是只读的。str[0] = "A" 不会产生错误（但也不会工作！）

例如：
```
var str = "HELLO WORLD";
str[0] = "A";             // 不产生错误，但不会工作
str[0];                   // 返回 H
```
## 3. indexOf() 、 lastIndexOf()
```indexOf()```返回字符串中一个子串第一处出现的索引（从左到右搜索）。如果没有匹配项，返回 -1 。

```
'mozilla'.indexOf('zill');  //2
```
```lastIndexOf()```返回指定文本在字符串中最后一次出现的索引，如果没有匹配项，返回 -1 
```
var str = "abcdarf";
var pos = str.lastIndexOf("a");  //4
```
两种方法都接受作为检索起始位置的第二个参数。
```
var str = "abcdarf";
var pos = str.indexOf("a");  //0
var pos = str.indexOf("a",1);  //4
```
```lastIndexOf() ```方法向后进行检索（从尾到头），这意味着：假如第二个参数是4，则从位置4开始往左检索，直到字符串的起点。(*个人的理解：lastIndexOf() 检索的本质是从右往左检索，返回该字符串第一次出现的位置，这里提到的下标位置均为从左往右的下标位置*)
```
var str = "bcda23a";
str.lastIndexOf("a");  //6
str.lastIndexOf("a",1);  //-1
str.lastIndexOf("a",4);  //3
```
## 4. search()
```search() ```方法搜索特定值的字符串，并返回匹配的位置：

```
var str = "bcda23daef";
str.search('a');  //3
str.earch(/da/ig);  //2
```
与indexOf的区别：
```search() ```方法无法设置第二个开始位置参数。
```indexOf() ```方法无法设置更强大的搜索值（正则表达式）。
## 5. slice()
提取字符串的某个部分，并在新字符串中返回被提取的部分。
```slice(a,b) //获取范围[a,b)```
该方法设置两个参数：起始索引（开始位置 ），终止索引（结束位置）。可接受负的索引

```
var str = "Apple, Banana, Mango";
var res = str.slice(7,13);  //res "Banana"
```
如果某个参数为负，则从字符串的结尾开始计数。
```
var str = "Apple, Banana, Mango";
str.slice(-14,-7);  //打印结果： " Banana"
str.slice(5,-7);  // 打印结果：", Banana"
//str不会改变
```
如果省略第二个参数，则该方法将裁剪字符串的剩余部分：
```
var str = "Apple, Banana, Mango";
str.slice(-5);  //"Mango"
str.slice(7);  //"Banana, Mango"
```
**提示：**负值位置不适用 Internet Explorer 8 及其更早版本。
## 6. substring()
```substring(start,end); //截取字符串 范围是[start, end)```
该方法设置两个参数：起始索引（开始位置），终止索引（结束位置）。无法接受负的索引。

```
var str = "Apple, Banana, Mango";
str.substring(7,13);  //"Banana"
```
如果省略第二个参数，则该 substring() 将裁剪字符串的剩余部分。
```
var  str =  'Hello world!';
str.substring(3);   //"lo world!"
```
## 7. substr()
```substr(a,b)   //截取字符串，范围是从下标为a的字符开始，截取长度为b```
该方法设置两个参数：起始索引（开始位置 *可为负数*），截取的长度。

```
var str = "Hello world!";
str.subst(3,4);  //"lo w"
```
如果省略第二个参数，则该 substr() 将裁剪字符串的剩余部分。
```
var str = "Hello world!";
str.subst(3);  //"lo world!"
```
## 8. replace()
用另一个值替换在字符串中指定的值，不会改变调用它的字符串。它返回的是新字符串。默认地，`replace() `只替换首个匹配，并对大小写敏感：

```
var str = "Hello world! hello";
var res = str.replace('llo','*');  // "He* world! hello"
var str = "HeLlo world! hello";
var res = str.replace('llo','*');  // "HeLlo world! he*"
```
如需执行大小写不敏感的替换，请使用正则表达式 /i（大小写不敏感）,如需替换所有匹配，请使用正则表达式的 g 标志（用于全局搜索）：
```
var str = "HeLlo world! hello";
str.replace(/llo/ig,'*');  //"He* world! he*"
```
## 9. toUpperCase()、toLowerCase()
`toUpperCase()` 把字符串转换为大写;`toLowerCase()` 把字符串转换为小写。

```
var str= "Hello World!"; 
str.toUpperCase();   //"HELLO WORLD!"
str.toLowerCase();  //"hello world!"
```
## 10. concat()
`concat() `连接两个或多个字符串。

```
var str1 = "Hello";
var str2= "World";
var str3= str1.concat(str2);  //  "HelloWorld"
var str4 = str1.concat(" ",str2,"!");  //"Hello World!"
```
## 11. trim()
`trim()` 方法删除字符串两端的空白符。

```
var str = "       Hello World!        ";
str.trim();  //  "Hello World!"
```
**警告：**Internet Explorer 8 或更低版本不支持 trim() 方法。
## 12. split()
可以通过 `split()` 将字符串转换为数组。可接受正则匹配。

```
var str = "a,fr,34, 2019/06/28,t";
str.split('');//["a", ",", "f", "r", ",", "3", "4", ",", " ", "2", "0", "1", "9", "/", "0", "6", "/", "2", "8", ",", "t"]
str.split(',');//["a", "fr", "34", " 2019/06/28", "t"]
str.split('/');//["a,fr,34, 2019", "06", "28,t"]
str.split(' ');//["a,fr,34,", "2019/06/28,t"]
str.split(/,|\/|\s/g);//["a", "fr", "34", "", "2019", "06", "28", "t"]
```