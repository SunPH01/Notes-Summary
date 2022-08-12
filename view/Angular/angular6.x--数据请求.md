### 1. 不使用rxjs请求数据
**引入HttpModule 、JsonpModule**
普通的 HTTP 调用并不需要用到 JsonpModule，不过稍后我们就会演示对 JSONP 的支持， 所以现在就加载它，免得再回来改浪费时间。
```
import { HttpModule, JsonpModule } from '@angular/http';
```
**HttpModule 、JsonpModule依赖注入**
```
@NgModule({
  declarations: [
    AppComponent,
    HomeComponent,
    NewsComponent,
    NewscontentComponent
  ],
  imports: [
    BrowserModule,
    FormsModule,
    HttpModule,
    JsonpModule,
    AppRoutingModule
  ],
  providers: [StorageService,NewsService],
  bootstrap: [AppComponent]
})
export class AppModule { }
```
**使用Http、Jsonp：**
1、在需要请求数据的地方引入 Http
```
import {Http,Jsonp} from "@angular/http";
```
2、构造函数内申明：
```
constructor(private http:Http,private jsonp:Jsonp) { }
```
3、对应的方法内使用http请求数据
```
   this.http.get("http://www.phonegap100.com/appapi.php?a=getPortalList&catid=20&page=1")
      .subscribe(
         function(data){
           console.log(data);
         },function(err){
          console.log('失败');
         }
      );


   this.jsonp.get("http://www.phonegap100.com/appapi.php?a=getPortalList&catid=20&page=1&callback=JSONP_CALLBACK")
      .subscribe(
         function(data){
           console.log(data);
         },function(err){
          console.log('失败');
         }
      );

//两种形式
this.http.get(`http://www.phonegap100.com/appapi.php?a=getPortalList&catid=${this.paramObj.id-10}&page=1`)
  .map(response => response.json())//需要引入
import 'rxjs/add/operator/map';
  .subscribe(response => {
        if(response.result){
          this.listArr = response.result;
        }
    },err =>{
      console.log('失败');
    }
  )
// this.http.get(`http://www.phonegap100.com/appapi.php?a=getPortalList&catid=${this.paramObj.id-10}&page=1`)
//   .toPromise()
//   .then(data => {
//     let response = data.json();
//       if(response.result){
//         this.listArr = response.result;
//       }
//     },err =>{
//       console.log('失败');
//     }
//   )
```
**取消请订阅**
```
import { Subscription } from 'rxjs/Subscription';
constructor(private messageService: MessageService) {
// subscribe to home component messages
this.subscription = this.messageService.getMessage().subscribe(message => { this.message = message; });
}
ngOnDestroy() {
// unsubscribe to ensure no memory leaks
this.subscription.unsubscribe();
}
```

**使用Post**

1.引入Headers 、Http模块  用的地方
```
import {Http,Jsonp,Headers} from "@angular/http";
```
2.实例化Headers
```
  private headers = new Headers({'Content-Type': 'application/json'});
```

3.post提交数据
```
this.http
    .post('http://localhost:8008/api/test', 
    JSON.stringify({username: 'admin'}), {headers:this.headers})
    // .toPromise()
    .subscribe(function(res){
      console.log(res.json());
    });
```

4.delete删除数据
```
const url = `${this.heroesUrl}/${id}`; 
this.http.delete(url, {headers: this.headers}) 
.toPromise() 
.then((res) => console.log(res)) .catch(this.handleError);
```

###2. 使用rxjs请求数据
RxJS是一种针对异步数据流编程工具，或者叫响应式扩展编程；可不管如何解释RxJS其目标就是异步编程，Angular引入RxJS为了就是让异步可控、更简单。

大部分RxJS操作符都不包括在Angular的Observable基本实现中，基本实现只包括Angular本身所需的功能。
如果想要更多的RxJS功能，我们必须导入其所定义的库来扩展Observable对象， 以下是这个模块所需导入的所有RxJS操作符：

1、引入Http 、Jsonp、RxJs  模块
```
import {Http,Jsonp} from "@angular/http";

import {Observable} from "rxjs";
import "rxjs/Rx";
```
你可能并不熟悉这种import 'rxjs/Rx'语法，它缺少了花括号中的导入列表：{...}。
这是因为我们并不需要操作符本身，这种情况下，我们所做的其实是导入这个库，加载并运行其中的脚本， 它会把操作符添加到Observable类中。

2、构造函数内申明：
```
constructor(private http:Http,private jsonp:Jsonp) { }
```
3、get请求
```
this.http.get("http://www.phonegap100.com/appapi.php?a=getPortalList&catid=20&page=1")
      .map(res => res.json()) .subscribe(
         function(data){
           console.log(data);
         }
      );
```
4、Jsonp请求
```
this.jsonp.get("http://www.phonegap100.com/appapi.php?a=getPortalList&catid=20&page=1&callback=JSONP_CALLBACK")
      .map(res => res.json()) .subscribe(
         function(data){
           console.log(data);
         }
      );
```

http.get 方法中返回一个Observable对象，我们之后调用RxJS的map操作符对返回的数据做处理。