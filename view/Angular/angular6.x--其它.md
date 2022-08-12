## EventEmitter实现自定义事件

通常，指令使用 Angular [EventEmitter](https://angular.cn/api/core/EventEmitter) 来触发自定义事件。 指令创建一个[EventEmitter](https://angular.cn/api/core/EventEmitter)实例，并且把它作为属性暴露出来。 指令调用[EventEmitter](https://angular.cn/api/core/EventEmitter).emit(payload)来触发事件
```javascript
deleteRequest = new [EventEmitter](https://angular.cn/api/core/EventEmitter)<Hero>(); 
delete() { 
this.deleteRequest.emit(this.hero); 
}
```
## 模板表达式操作符

**管道操作符 ( | )**
`

<div>Title through uppercase pipe: {{title | uppercase}}</div>`

**安全导航操作符 ( ?. ) 和空属性路径**

Angular 的安全导航操作符 (?.) 是一种流畅而便利的方式，用来保护出现在属性路径中 null 和 undefined 值。 下例中，当currentHero为空时，保护视图渲染器，让它免于失败。
`<span>{{currentClasses?.colorC}}</span>`

加上安全操作符 ？. 后，如果currentClasses对象没有colorC属性时，span标签里没有内容，也不会报错。


## 常见问题
### **1.date**

`'Missing locale data for the locale "fr".' for pipe 'DatePipe'`
```
import{ registerLocaleData} from '@angular/common';

import localeFr from '@angular/common/locales/fr';

registerLocaleData(localeFr, 'fr');

[<u>https://angular.cn/guide/i18n</u>](https://angular.cn/guide/i18n)
```
### **2\. post请求请求头报错**

`Request header field Access-Control-Allow-Headers is not allowed by Access-Control-Allow-Headers`
```
  private headers= new Headers({'Content-Type': 'application/json'});
```
对于跨域请求，将内容类型设置为应用程序/x-www-form- urlencoding、multipart/form-data或text/plain之外的任何内容类型，都会触发浏览器向服务器发送飞行前选项请求。

因此，尽管服务器允许跨源请求，但不允许访问控制允许头文件，它将抛出错误。默认情况下，angular的内容类型是application/json，它试图发送一个选项请求。尝试在服务器端覆盖angular默认的标头或允许访问控制允许标头。下面是一个angular示例:
```
private headers= new Headers({'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'});
```
### 3. **ngFor报错**
`Cannot find a differ supporting object '[object Object]' of type 'object'. NgFor only supports binding to Iterables such as Arrays`
```
this.myStock= this.quoSer.loadStockBaseMsg(list, 1, 'zd,zdf')

.subscribe(res => {

console.log(res);

this.myStock= res;

});
```
去掉 this.nyStock =就可以了
```
this.quoSer.loadStockBaseMsg(list, 1, 'zd,zdf')

.subscribe(res => {

console.log(res);

this.myStock= res;

});
```

### 4. **运行项目不能自动打开浏览器**

运行命令：`ng serve --open`即可

### 5. **新建项目报错**
`Schematic input does not validate against the Schema: {"name":"angular_ng","version":"6.1.3","newProjectRoot":"projects","skipInstall":false,"linkCli":false,"skipGit":false,"commit":null}
Errors:Data path ".name" should match format "html-selector".`

项目名称格式不对，改一下项目名称就解决了。

**6.语言切换主菜单不改变问题**

app.component.html

在引用的地方加上打开关闭主菜单的监听函数`(ionWillOpen)="menuOpen()" (ionDidClose)="menuClose()"`
```
<ion-menu type="push" (ionWillOpen)="menuOpen()" (ionDidClose)="menuClose()">

<ion-header>

<ion-toolbar>

<ion-title i18n="菜单|app">菜单</ion-title>

</ion-toolbar>

</ion-header>

<ion-content>

<app-main-menu [itemActive]="currLoc" [menuStatus]="isOpen"></app-main-menu>

</ion-content>

</ion-menu>

menuOpen() {

this.isOpen= true;

}

menuClose() {

this.isOpen= false;

}
```
打开菜单，关闭菜单时改变变量isOpen，用来标记当前菜单状态；

把变量isOpen传递给主菜单组件
```
<app-main-menu [menuStatus]="isOpen"></app-main-menu>
```
在子组件接收菜单状态
```
@Input()

set menuStatus(val:boolean) {

this.isOpen= val;

if(val) {

this.translate.setDefaultLang(this.setting.getSettings().lang);

}

}

get menuStatus() {return this.isOpen;}
```
当收到菜单为打开时，就去设置语言，这样就解决了。
![image.png](https://upload-images.jianshu.io/upload_images/18030682-47565c6d765c71b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**7.页面销毁事件不执行问题**
html跳转的路由‘routerDirection’赋值为‘root’可以解决；
`[routerDirection]="'root'"`

js跳转：
```
import{ AlertController, ToastController, ModalController, NavController} from '@ionic/angular';

nav: NavController

this.nav.navigateRoot('/users/firstSet');
```
## 测试报错

### **1. location**

`angular NullInjectorError: No provider for Location`

报着个错是因为spec.ts文件里没有导入Location，需要导入：
```
import {Location } from '@angular/common';

providers注入

providers: [

Location,

...

]
```
之后在测试，如果又出现新的报错 `angular NullInjectorError: No provider for LocationStrategy`

则需要继续导入LocationStrategy:
```
import {Location ,LocationStrategy} from '@angular/common';

providers注入

providers: [

Location,

{ provide: LocationStrategy, useClass:PathLocationStrategy },

...

]
```
继续运行测试可能还会出现以下报错
`TypeError: this._platformStrategy.getBaseHref is not a function`

还需继续导入PathLocationStrategy, APP_BASE_HREF
```
import {Location ,LocationStrategy, PathLocationStrategy, APP_BASE_HREF} from '@angular/common';

providers: [

Location,

{ provide: LocationStrategy, useClass:PathLocationStrategy },

{ provide: APP_BASE_HREF, useValue: '/my/app'}

...

]
```
### **2\.** **NavParams**

报错： `Can't resolve all parameters for NavParams: (?)   （ionic）`

解决：

在测试文件里添加
```
providers: [

{ provide: NavParams, useClass: class{ NavParams= jasmine.createSpy('NavParams'); }},

{ provide: NavParams, useValue:{ data:{ 'MYVALUE': 'XYZ'}}}

]
```
### **3\. post\get请求JSON报错问题**
`Unexpected end of JSON input in ...`
```
this.http.post(loc, JSON.stringify(pm), {headers: this.headers}).pipe(

map(res => {

res.json();

})

)

.subscribe(res => {

this.quotesContext.session= res.data.session;

}, err => {

console.error('登录jybData失败:', err);

});
```
如果res为空/未定义/空字符串，您将得到错误。在使用res.json()之前，只需检查res的值，就不会得到错误。为了确保这一点，在尝试解析它之前先创建一个console.log(res)。

参照如下修改就不会报错：
```
map(res => {

if (res) {

return res.json();

}

})
```
### **4\. 表单报错**

`1.No value accessor for form control with name: 'name'`
```
<ion-range [(ngModel)]="buyFund" (ionBlur)="fundSelected()" pin="true" min="0" [max]="maxbuy" name="name" [ngModelOptions]="{standalone: true}" color="danger"></ion-range>
```
只需要添加上ngDefaultControl属性即可
```
<ion-range [(ngModel)]="buyFund" (ionBlur)="fundSelected()" pin="true" min="0" [max]="maxbuy" name="name" [ngModelOptions]="{standalone: true}" color="danger" ngDefaultControl></ion-range>
```
`2.Can't bind to 'formGroup' since it isn't a known property of 'form'.`

在使用form表单时，如果用到了form-group与formControlName，需要在app.module.ts中的import引入的不仅仅有FormsModule，还要引入ReactiveFormsModule。
```
import { ReactiveFormsModule, FormsModule } from '@angular/forms';

@NgModule({

    imports: [

        FormsModule,

        ReactiveFormsModule

           ]

})
```

### **5. platform**

`this.platform.is is not a function`

测试文件：
```
import{ Platform, AlertController, ToastController} from '@ionic/angular';

beforeEach(async() => {

platformReadySpy= Promise.resolve();

platformSpy= jasmine.createSpyObj('Platform', { ready: platformReadySpy,is:()=> {} });

TestBed.configureTestingModule({

providers: [

{ provide: Platform, useValue: platformSpy },

],

}).compileComponents();

});
```
### **6.The pipe 'translate' could not be found**
```
import{ TranslateModule} from '@ngx-translate/core';

imports: [TranslateModule.forRoot()]
```
### **7.ERROR in node_modules/@biesbjerg/ngx-translate-extract/dist/cli/cli.d.ts(1,23): error TS2688:** **Cannot find type definition file for 'yargs'**

多语言翻译文件引用地址错误
```
import{ _} from '@biesbjerg/ngx-translate-extract';
```
改为一下引用地址即可：
```
import{ _} from '@biesbjerg/ngx-translate-extract/dist/utils/utils';
```
### **8. ERROR Error: ExpressionChangedAfterItHasBeenCheckedError:**

`Expression has changed after it was checked. Previous value: 'model: undefined'. Current value: 'model: false'.`

原因：Angular通过修补一些异步api(如addEventHandler、setTimeout等)来知道事件何时被完全处理，然后在每个事件之后运行变更检测。

在开发模式下，Angular会在第一个变更检测之后执行一个额外的变更检测。因为中间没有事件，所以不应该发生任何变化。如果模型仍然改变，Angular认为这是一个可能的bug。

在开发模式中，更改检测将第二次运行，并将当前值与第一次运行进行比较。  如果值不同，那么它“认为”更改是由它自己引起的。这就是它抛出的原因。但是，在enableProdMode()的生产模式中不会发生这种情况。

解决方法：

在main.ts里添加
```
import {  enableProdMode } from  '@angular/core';

enableProdMode();
```


## 其它

### 1. **cookie**

储存cookie
```
this.cookieService.set("userId",this.userId,new Date(new Date().getTime() + this.time));

this.cookieService.set("userName",response.data.name,new Date(new Date().getTime() + this.time));
```
读取cookie
```
this.cookieService.get("userId");

this.cookieService.get("userName");
```
重新存储cookie
```
this.cookieService.set("userId",this.cookieService.get("userId"),new Date(new Date().getTime() + this.time));

this.cookieService.set("userName",this.cookieService.get("userName"),new Date(new Date().getTime() + this.time));
```
删除cookie
```
this.cookieService.delete("userId");
```
check( name: string ): boolean;
get( name: string ): string;
set( name: string, value: string, expires?: number | Date, path?: string, domain?: string, secure?: boolean, sameSite?: 'Lax' | 'Strict' ): void;
delete( name: string, path?: string, domain?: string ): void;
deleteAll( path?: string, domain?: string ): void;

### 2. 共享模块
项目中分有多个模块（Module），当两个或多个模块都引用同一个服务、管道等组件时，angular编译会报错，这种情况需要新创建一个共享模块，来承载共用组件；
![image.png](https://upload-images.jianshu.io/upload_images/18030682-164e8b3fe33f8ec5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/18030682-53ea56280acfecea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如上图所示，项目quotes模块和trade模块都引入了xpipes.ts里的adata管道，如果在quotes.Moudle和tradeModule里直接引用AdataPipe就会报错，所以新建一个共享模块(ng g module share),在share.module.ts里引入共用的管道

share.module.ts：
```
import{ NgModule} from '@angular/core';

import{ CommonModule} from '@angular/common';

import {  AdataPipe, LimitToPipe} from '../components/pipes/xpipes';

@NgModule({

imports: [

CommonModule

],

declarations: [

AdataPipe,

LimitToPipe

],

exports: [

AdataPipe,

LimitToPipe

]

})

export class ShareModule { }
```
然后再把shareModule引入到两个用到管道的模块里：

![image.png](https://upload-images.jianshu.io/upload_images/18030682-c02b3607f9305ac6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/18030682-0e380c0c7c6ac120.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这样就可以在quotes.Moudle和tradeModule这两个模块下的组件里用shareModule导出的管道了；（这里举例是管道，服务等其他共用都参照这种做法）；

### 3. 移动端点击事件监听
`(touchstart)="addColor()" (touchend)="removeColor()"`
相当于电脑网页的keydown、keyup事件