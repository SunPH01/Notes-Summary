**>数据文本绑定** `{{}}`

```
 <h1>
      {{title}}
  </h1>
```
**>绑定属性绑定  html**
```
this.h="<h2>这是一个h2用[innerHTML]来解析</h2>"

<div [innerHTML]="h"></div>

<div [id]="id" [title]="msg">调试工具看看我的属性</div>
```
![image.png](https://upload-images.jianshu.io/upload_images/18030682-32d86abfd32d336d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**>\*ngFor    普通循环**
```
 <ul>
    <li *ngFor="let item of list">
      {{item}}
    </li>
  </ul>


<ul>
    <li *ngFor="let item of list2; let i = index">
       {{i}}----- {{item.title}}
    </li>
</ul>
```

```
// trackBy 唯一标志符
trackByHeroes(index: number, hero: Hero): number { return hero.id; }
<div *ngFor="let hero of heroes; trackBy: trackByHeroes"> ({{hero.id}}) {{hero.name}} </div>
```

你可能试过把 *[ngFor](https://angular.cn/api/common/NgForOf) 和 *[ngIf](https://angular.cn/api/common/NgIf) 放在同一个宿主元素上，但 Angular 不允许。这是因为你在一个元素上只能放一个结构型指令。 这种情况下有一个简单的解决方案：把 *[ngIf](https://angular.cn/api/common/NgIf) 放在一个"容器"元素上，再包装进 *[ngFor](https://angular.cn/api/common/NgForOf) 元素。 这个元素可以使用[ng-container](#ngcontainer)，以免引入一个新的 HTML 层级。

**局部变量：**
index: number：可迭代对象中当前条目的索引。
first: boolean：如果当前条目是可迭代对象中的第一个条目则为 true。
last: boolean：如果当前条目是可迭代对象中的最后一个条目则为 true。
even: boolean：如果当前条目在可迭代对象中的索引号为偶数则为 true。
odd: boolean：如果当前条目在可迭代对象中的索引号为奇数则为 true。

**>条件判断 \*ngIf**
```
  <p *ngIf="list.length > 3">这是ngIF判断是否显示</p>
```
**>执行事件(click)**
```
<button class="button" (click)="getData()">

      点击按钮触发事件

  </button>

  getData(){ /*自定义方法获取数据*/

    //获取

    alert(this.msg);

  }
```
**>双向数据绑定 [(ngModel)]**
```
<input  [(ngModel)]="inputValue">
```
**注意引入：FormsModule**
```
import{ FormsModule} from '@angular/forms';

@NgModule({

  declarations: [

    AppComponent,

    HeaderComponent,

    FooterComponent,

    NewsComponent

  ],

  imports: [

    BrowserModule,

    FormsModule

  ],

  providers: [],

  bootstrap: [AppComponent]

})

export class AppModule { }
```
**使用：**
```
  <input type="text"  [(ngModel)]="inputValue"/>

  {{inputValue}}
```
**>style绑定样式**
```
[style.color]="colorArr[0]"

[ngStyle]="{'color':isRed?'red':'green'}"
```
**>class绑定类名**
```
[ngClass]="{'special': isSpecial,heighLight: isHeighLight}"

[class.special]="isSpecial"

<span [ngClass]= " currentClasses (click)= change ()" >测试文本</ span >

public currentClasses  = { 'widthC': true , 'heightC': false , 'colorC': this.isRed};

change() {
  this.isRed = !this.isRed;
  this.currentClasses.colorC:this.isRed;//ngStyle也适用
}
```
**>NgSwitch指令**
```
<div [[ngSwitch](https://angular.cn/api/common/NgSwitch)]="currentHero.emotion"> 

<span  *[ngSwitchCase](https://angular.cn/api/common/NgSwitchCase)="'happy'" [hero]="currentHero"></span >

<span  *[ngSwitchCase](https://angular.cn/api/common/NgSwitchCase)="'sad'" [hero]="currentHero"></span > 

<span *[ngSwitchCase](https://angular.cn/api/common/NgSwitchCase)="'confused'" [hero]="currentHero"></span>

  <span  *[ngSwitchDefault](https://angular.cn/api/common/NgSwitchDefault) [hero]="currentHero"></span > 

</div>
```
**>模板引用变量 ( #var)**

使用井号 (#) 来声明引用变量。 我们也可以用ref-前缀代替#。
```
<input #phone placeholder="phone number">
<button (click)="callPhone(phone.value)">Call</button>

<!--phone.value就是input的value值**ref-**phone -->
```
大多数情况下，Angular会把模板引用变量的值设置为声明它的那个元素。 不过，指令也可以修改这种行为，让这个值引用到别处，比如它自身。[NgForm](https://angular.cn/api/forms/NgForm)指令就是这么做的。
```
<form (ngSubmit)="onSubmit(heroForm)" #heroForm="ngForm"> </form>
```
**>hidden 显示隐藏**
```
<div [hidden]="name.valid || name.pristine"
     class="alert alert-danger">
  Name is required
</div>
```