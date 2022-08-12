这里记录了简单的用法（要学习更多内置管道的知识，参见[API参考手册](https://angular.cn/api?type=pipe)，并用“pipe”为关键词对结果进行过滤。）
```
<p>The hero's birthday is {{ birthday | date }}</p>
```
在这个插值表达式中，我们让组件的birthday值通过[管道操作符](#pipe)( | )流动到 右侧的[Date管道](https://angular.cn/api/common/DatePipe)函数中。所有管道都会用这种方式工作。

对管道进行参数化： {{ birthday | date:"MM/dd/yy" }}

链式管道：   {{ birthday | date | [uppercase](https://angular.cn/api/common/UpperCasePipe)}}

**1. 自定义管道**

*.pie.ts
```
import { Pipe,PipeTransform } from '@angular/core';

@Pipe({

  name: 'myAddpipe'

})

export class MyAddpipePipe implements **PipeTransform** {

  transform(value: any, args?: any,val?:any): any {

    //args:3 val:4

    return value+args;

  }

}
```
app.module.ts
```
import {MyAddpipePipe } from './my-addpipe.pipe';

declarations: [MyAddpipePipe],
```
*.html
```
<span>未加管道过滤年龄：{{age}}；经过管道过滤年龄：{{age | myAddpipe:3:4}}</span>

<!-- 未加管道过滤年龄：20；经过管道过滤年龄：23 -->
```
在这个管道的定义中体现了几个关键点：

1.管道是一个带有“管道元数据(pipe metadata)”装饰器的类。

2.这个管道类实现了[PipeTransform](https://angular.cn/api/core/PipeTransform)接口的transform方法，该方法接受一个输入值和一些可选参数，并返回转换后的值。

3.当每个输入值被传给transform方法时，还会带上另一个参数，比如我们这个管道中的exponent(放大指数)。

4.我们通过@[Pipe](https://angular.cn/api/core/Pipe)装饰器告诉Angular：这是一个管道。该装饰器是从Angular的core库中引入的。

5.这个@[Pipe](https://angular.cn/api/core/Pipe)装饰器允许我们定义管道的名字，这个名字会被用在模板表达式中。它必须是一个有效的JavaScript标识符。

**2.纯管道和非纯管道**

有两类管道：**纯**的与**非纯**的。 默认情况下，管道都是纯的。

纯管道：
```
@[Pipe](https://angular.cn/api/core/Pipe)({ name: 'flyingHeroesImpure', pure: true })
```
非纯管道：
```
@[Pipe](https://angular.cn/api/core/Pipe)({ name: 'flyingHeroesImpure', pure: false })
```
纯管道：
Angular只有在它检测到输入值发生了纯变更时才会执行纯管道。纯变更是指对原始类型值(String、Number、Boolean、Symbol)的更改， 或者对对象引用(Date、Array、Function、Object)的更改。

Angular会忽略(复合)对象内部的更改。 如果我们更改了输入日期(Date)中的月份、往一个输入数组(Array)中添加新值或者更新了一个输入对象(Object)的属性，Angular都不会调用纯管道。

非纯管道：
Angular会在每个组件的变更检测周期中执行非纯管道。 非纯管道可能会被调用很多次，和每个按键或每次鼠标移动一样频繁。