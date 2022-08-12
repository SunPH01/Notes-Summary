## **angularJs的工具方法**

**![image](https://upload-images.jianshu.io/upload_images/18030682-9a817d0cd0ee8c3e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)** 

![image](https://upload-images.jianshu.io/upload_images/18030682-987dc5e741627518.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### **1.angular.isArray()**

 ![image](https://upload-images.jianshu.io/upload_images/18030682-d7168f22009642df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

返回true;

### **2\. angular.isElement()**

支持判断JQ获取到的对象

 ![image](https://upload-images.jianshu.io/upload_images/18030682-73db9bbeba9e8e54.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### **3.angular.equals(a,b)**

判断两个参数是否相等；
```
var a=2; var b=2;
console.log(angular.equals(a,b));//true
var c=[]; var d=[];
console.log(angular.equals(c,d));//true
var c=[1,2]; var d=[1];
console.log(angular.equals(c,d));//false
var e=NaN; var f=NaN;
console.log(angular.equals(e,f));//true
```

### **4.angular.forEach()**

遍历
```
var arr = [1,4,5];
angular.forEach(arr,function(val,index){
    console.log(val);//遍历到的值
    console.log(index);//下标
}); 
var objs={"name":"names","age":"18"}
angular.forEach(objs,function(val,key){
    console.log(val);//遍历到的属性值
    console.log(key);//遍历到的属性名称
}); 
var objs={"name1":"张三","age1":"18","name2":"李四","age2":"19"}; 
var obj2={"key":[],"val":[]};
    angular.forEach(objs,function(val,key){
        console.log(val);//遍历到的属性值
        console.log(key);//遍历到的属性名称
        this.key.push(key);//this指向obj2
        this.val.push(val);
    },obj2);
    console.log(obj2); 
// Object {key: Array[4], val: Array[4]} 
// key:Array[4] 
// 0:"name1"
// 1:"age1" 
// 2:"name2" 
// 3:"age2" 
//    
// val:Array[4] 
// 0:"张三" 
// 1:"18" 
// 2:"李四" 
// 3:"19"
```

### **5\. angular.fromJson() angular.toJson()**

 ![image](https://upload-images.jianshu.io/upload_images/18030682-98126e68722833a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### **6.angular.identity()**

该函数的作用：返回的值为传入的参数；

angular.noop();返回值为undefined;实际上是一个空函数；

这两个函数一般用做默认操作。

 ![image](https://upload-images.jianshu.io/upload_images/18030682-6e413979fbe982f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### **7.大小写转换**

angular.lowercase(); 转换成小写

 angular.uppercase();转换成大写

### 8.**angular.element()**

 ![image](https://upload-images.jianshu.io/upload_images/18030682-633da9fefbafe6cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时如果引入jquery,angular内部视为angular.element ===$；

上述代码可以写为$(“oDiv”).css(‘background’,’red’);

### **9.angular.bind();**

 ![image](https://upload-images.jianshu.io/upload_images/18030682-cb618369ac9ecdca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### **10.angular.copy();拷贝对象**

angular.copy(source, [destination]);

 如果省略了destination，一个新的对象或数组将会被创建出来；如果提供了destination，则source对象中的所有元素和属性都会被复制到destination中；**如果source不是对象或数组（例如是null或undefined）, 则返回source；******如果source和destination类型不一致，则会抛出异常。****
![image.png](https://upload-images.jianshu.io/upload_images/18030682-832585ebc5d35ddf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
var a = {name:'kitty',age:18}; var b = a;//浅拷贝
b.age = 20;
console.log(a.age,b.age);//20  20
var c = {name:'kitty',age:18}; var d = angular.copy(a);//深拷贝
d.age = 20;
console.log(c.age,d.age);//18  20
```

浅拷贝：仅仅复制对象的引用，而不是对象本身，改变任何一个的属性值，另一个都会跟着改变；
深拷贝：把复制的对象所引用的全部对象都复制一遍，两个互相不受影响；

此处补充一点js对象深拷贝方法：
```
  var newArr = []; var arr = [
                { name: "xxx", age: 22 },
                { name: "yyy", age: 21 },
                { name: "zzz", age: 23 },
                { name: "www", age: 24 }
            ];
            $.each(arr, function (i, e) { var obj = {};
                $.each(e, function (k, v) {
                    obj[k] = v;
                });
                newArr.push(obj);
            });
```

### **11.angular.extend();对象聚合**
```
var a = {'x': '123'}; var b = {'xx': '456'};
angular.extend(b, a);
console.log(b);//{'x': '123','xx': '456'}
```

### 12.**angular.bootstarp();**

初始化模块

 ![image](https://upload-images.jianshu.io/upload_images/18030682-638c4177e7e1238b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

多次初始化调用

 ![image](https://upload-images.jianshu.io/upload_images/18030682-e9c68d6e37e7b9ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)