**1. 服务**

```
ng g service my-new-service

// 创建到指定目录下面
ng g service services/storage
```

**app.module.ts 里面引入创建的服务**
`import { StorageService } from './services/storage.service';`


**NgModule 里面的providers 里面依赖注入服务**
```
@NgModule({
  declarations: [
    AppComponent,
    HeaderComponent,
    FooterComponent,
    NewsComponent,
    TodolistComponent
  ],
  imports: [
    BrowserModule,
    FormsModule
  ],
  providers: [StorageService],
  bootstrap: [AppComponent]
})
export class AppModule { }
```
**使用的页面引入服务，注册服务**
```
import { StorageService } from '../../services/storage.service';
constructor(private storage: StorageService) {
	   
}
```
使用
```
addData(){

      // alert(this.username);

      this.list.push(this.username);

      this.storage.set('todolist',this.list);
  }
  removerData(key){
    
      console.log(key);
      this.list.splice(key,1);
      this.storage.set('todolist',this.list);

  }
```
自定义指令：
在 Angular 中有三种类型的指令：
1.组件 — 拥有模板的指令
2.结构型指令 — 通过添加和移除 DOM 元素改变 DOM 布局的指令
3.属性型指令 — 改变元素、组件或其它指令的外观和行为的指令。

**2. 属性型指令**
在app文件夹中新建my-highlight.directive.ts文件，
**静态属性指令**
```
import {Directive,ElementRef,Renderer} from '@angular/core';
@Directive({
  selector:'[highlight]'
})
export class HighlightDirective{
  constructor(el:ElementRef,render:Renderer){
    render.setElementStyle(el.nativeElement,'color','blue');
//el.nativeElement.style.color = 'yellow';
  }
}
```
App.module.ts中引入、注册之后便可使用
```
//引入自定义指令
import { HighlightDirective } from './my-highlight.directive';

declarations: [       //声明 注册  组件     所有自定义的组件都要在这里声明
  AppComponent,
  HeaderComponent,
  NewsComponent,
  HighlightDirective
],
```
在html中使用：
`<h3 highlight>{{newsTitle}}</h3>`
这样指令就生效了，h3的文字就会变成指令里定义的蓝色了。

**动态属性指令**
```
// 动态属性指令
import {Directive,ElementRef,HostListener,Input}from '@angular/core';

@Directive({
  selector: '[highlight]'
})
export class HighlightDirective {
  constructor(private el: ElementRef) { }
  @HostListener('mouseenter') onMouseEnter(){
    this.highLight('blue');
  }
  @HostListener('mouseleave') onMouseLeave(){
    this.highLight(null);
  }
  private highLight(color:string){
    this.el.nativeElement.style.color = color;
  }
}
```
**使用数据绑定向指令传递值**
以上两种的高亮颜色是硬编码在指令中的，这不够灵活。 我们应该让指令的使用者可以在模板中通过绑定来设置颜色。
*.sever.ts
```
import {Directive,ElementRef,HostListener,Input}from '@angular/core';

@Directive({
  selector: '[highlight]'
})
export class HighlightDirective {
  @Input () defultColor : string ;
  @Input('highlight') highlightColor : string ;
  constructor(private el: ElementRef) { }
  @HostListener('mouseenter') onMouseEnter(){
    this.highLight(this.highlightColor || this.defultColor || 'yellow');
  }
  @HostListener('mouseleave') onMouseLeave(){
    this.highLight(null);
  }
  private highLight(color:string){
    this.el.nativeElement.style.color = color;
  }
}
```
.html
```
<h3 [highlight]='selfColor' [defultColor]='defultColor'>{{title}}</h3>
```
*.component.ts
```
public selfColor:string = 'green';
public defultColor:string = 'red';
```

**3. 结构型指令**
在命令行输入  `ng g directive my-jiegou`    创建指令文件。
在app.module.ts里引入
```
//引入自定义结构型指令
import { MyJiegouDirective } from './my-jiegou.directive';

//注入    声明 注册 组件 所有自定义的组件都要在这里声明
declarations: [MyJiegouDirective]
```
my-jiegou.directive.ts    (模拟*if功能)
```
import { Directive ,Input ,TemplateRef,ViewContainerRef} from '@angular/core';

@Directive({
  selector: '[appMyJiegou]'
})
export class MyJiegouDirective {
  constructor(
    private templateRef: TemplateRef<any>,
    private viewContainer: ViewContainerRef) { }
  @Input() set appMyJiegou(flag: boolean){
    if(flag){
      this.viewContainer.createEmbeddedView(this.templateRef);
    }else{
      this.viewContainer.clear();
    }
  }
}
```
....compontent.html
```
<h3 (click)='dirFlag=!dirFlag'>{{newsTitle}}</h3>
<span *appMyJiegou='dirFlag'>结构指令控制节点</span>
```
....compontent.ts
```
public dirFlag:boolean = false;
```
**4. [服务中变量变化后组件中的值响应更新](https://github.com/cornflourblue/angular2-communicating-between-components/tree/master/app)**
```
服务：
import { Subject} from 'rxjs';

private subject = new Subject<any>();
changeData(val: string, key: string) {
this.subject.next({val: val, key: key});
}

getData(): Observable<any> {
return this.subject.asObservable();
}

用到服务的组件：
import { Component, OnDestroy } from '@angular/core';
import { Subscription } from 'rxjs/Subscription';
import { MessageService } from './_services/index';

export class AppComponent implements OnDestroy {
    message: any;
    subscription: Subscription;
    constructor(private messageService: MessageService) {
        // subscribe to home component messages
        this.subscription = this.messageService.getData().
subscribe(message => { this.message = message; });
}
    changeStart() {
       this.messageService.changeData( 'black', 'userSetting.theme');
 }
    ngOnDestroy() {
        // unsubscribe to ensure no memory leaks
        this.subscription.unsubscribe();
    }
}
```