**1.安装@angular/material、@angular/cdk**
`cnpm install --save @angular/material @angular/cdk`

**2.app.module.ts导入模块**
```
import { ScrollingModule } from '@angular/cdk/scrolling';
import { MatCardModule } from '@angular/material';

imports: [
  ...
  ScrollingModule,
  MatCardModule
]
```
**3.在当前组件引入**
```
import { ChangeDetectionStrategy, ViewChild , ...} from '@angular/core';
import { CdkVirtualScrollViewport } from '@angular/cdk/scrolling';

@ViewChild(CdkVirtualScrollViewport)
viewport: CdkVirtualScrollViewport;
```
html
```
<cdk-virtual-scroll-viewport class="gkz-viewport" [itemSize]="20">
<div *cdkVirtualFor="let item of items; let i = index" class="item">
Item #{{i}}: {{item}}
</div>
</cdk-virtual-scroll-viewport>
````
css
```
.gkz-viewport {
height: 200px;
width: 200px;
border: 1px solid black;
.cdk-virtual-scroll-content-wrapper {
display: flex;
flex-direction: column;
}
.item {
height: 19px;
border-bottom: 1px dashed #aaa;
}
}
```

**1.Context Variables**
使用\*cdkVirtualFor，我们有一些上下文变量:index, count, first, last, even, odd.
```
<cdk-virtual-scroll-viewport class="gkz-viewport" [itemSize]="20*6">
<div [class.item-alternate]="odd" *cdkVirtualFor="
let item of items;
let i = index;
let count = count;
let first = first;
let last = last;
let even = even;
let odd = odd;">
<div class="item">Item #{{i}}: {{item}}</div>
<div class="item">count: {{count}}</div>
<div class="item">first: {{first}}</div>
<div class="item">last: {{last}}</div>
<div class="item">even: {{even}}</div>
<div class="item">odd: {{odd}}</div>
</div>
</cdk-virtual-scroll-viewport>

.gkz-viewport {
    ...
    .item-alternate {
        background: #ddd;
    }
}
```
![image.png](https://upload-images.jianshu.io/upload_images/18030682-172256ceccf8bb2e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**itemSize**
纵向列表表示高度，横向列表表示宽度；

**2.视图回收**
为了提高渲染性能， *cdkVirtualFor 会缓存那些曾经创建过但不再需要的视图。当要创建一个新视图时，会转而复用一个已缓存的视图。可以通过 templateCacheSize 属性来调整视图缓存的大小。把这个大小设置为 0 会禁用缓存。如果你的模板在内存方面很昂贵，你可能会希望减小这个数字，以免在模板缓存上花费太多内存。
```
<cdk-virtual-scroll-viewport class="gkz-viewport" [itemSize]="30">
<div *cdkVirtualFor="let item of items; let i = index;
templateCacheSize:0" class="item">
Item #{{i}}: {{item}}
</div>
</cdk-virtual-scroll-viewport>
```

**3.固定超过滚动视图的item个数**
`minBufferPx  maxBufferPx`
当所有条目都是固定大小时，你可以使用 FixedSizeVirtualScrollStrategy。可以用 itemSize 指令轻松地将它添加到视口中。这种约束的优点是它可以提供更好的性能，因为在渲染条目时不需要进行测量。

固定大小的策略也支持设置一些缓冲区参数，用来决定渲染多少额外内容，也就是视口可见内容之外的部分。第一个参数是 minBufferPx。 minBufferPx 是视口必须渲染的最小内容缓冲区数量（以像素为单位）。如果视口检测到要缓冲的内容小于这个数量（未填满），就会立即渲染更多内容。 第二个参数是 maxBufferPx。它会告诉视口当检测到需要更多缓冲区的时候要渲染多少个备用缓冲区空间。

这两个缓冲区参数的作用可以用一个例子来说明。假设我们有以下参数：itemSize = 50、minBufferPx = 100、maxBufferPx = 250。当用户滚动浏览内容时，视口就会检测到只剩下 90px 的缓冲区。由于它小于 minBufferPx，所以视口必须渲染更多缓冲区。它必须渲染足够数量的缓冲区，直到其大于等于 maxBufferPx。在这种情况下，它渲染了4个条目（额外的 200px），使缓冲区总大小达到290px，略高于maxBufferPx 。
```
<cdk-virtual-scroll-viewport class="gkz-viewport" [itemSize]="30"
minbufferpx="400" maxbufferpx="200">
<div *cdkVirtualFor="let item of items; let i = index;" class="item">
Item #{{i}}: {{item}}
</div>
</cdk-virtual-scroll-viewport>
```
**4.视口方向**
虚拟滚动视口默认为垂直方向，也可以设置为 orientation="horizontal"。在改变方向时，要确保该条目是用 CSS 进行水平布局的。要做到这一点，你可能希望把 .cdk-virtual-scroll-content-wrapper 类作为 CSS 的目标，它是包含待渲染内容的包装元素。
```
<cdk-virtual-scroll-viewport orientation="horizontal" itemSize="50" class="gkz-horizontal-viewport">
<div *cdkVirtualFor="let item of items; let i = index" class="item">
Item #{{i}}: {{item}}
</div>
</cdk-virtual-scroll-viewport>
```
**5.数据**
*cdkVirtualFor 接受来自 Array、Observable<Array> 或 DataSource 的数据。
数据源是一个抽象类，有两个方法：
– connect(): is called by the Viewport to receive a stream that emits the data array.
– disconnect(): will be invoked when the Viewport is destroyed.

例子：
```
<button (click)="emitData()">Add item</button>
<cdk-virtual-scroll-viewport class="gkz-viewport" [itemSize]="20" maxBufferPx="20">
<div *cdkVirtualFor="let item of observableData | async; let i = index" class="item">
Item #{{i}}: {{item}}
</div>
</cdk-virtual-scroll-viewport>

observableData = new BehaviorSubject<number[]>([]);
emitData() {
const items = Array(3).fill(0).map(() =>
   Math.round(Math.random() * 100)
);
const data = this.observableData.value.concat(items);
this.observableData.next(data);
}
```

**6.trackBy**
像 *ngFor 的 trackBy 一样，这里也可以指定 trackBy 函数，工作方式也都一样。传给这个 trackBy 的 index 是在数据源中的索引，而不是在要渲染的这部分数据中的索引。

用函数自己写排序功能：
```
<button (click)="sortBy('id')">Sort by id</button>
<button (click)="sortBy('name')">Sort by name</button>
<cdk-virtual-scroll-viewport class="gkz-viewport" [itemSize]="20">
<div *cdkVirtualFor="let customer of customersObservable | async" class="customer-item">
{{customer.id}} - {{customer.name}}
</div>
</cdk-virtual-scroll-viewport>

customers = [
  { id: 1, name: 'John Bailey' },
  { id: 2, name: 'Amelia Kerr' },
  { id: 3, name: 'Julian Wallace' },
  { id: 4, name: 'Pippa Sutherland' },
  { id: 5, name: 'Stephanie Simpson' }
];
customersObservable = new BehaviorSubject(this.customers);
sortBy(prop: 'id' | 'name') {
  this.customersObservable.next(
  this.customers.map(s => {
    console.log(s);
    return s;
  }).sort((a, b) => {
    console.log(a, b);
    const aProp = a[prop], bProp = b[prop];
    if (aProp < bProp) {
      return -1;
    } else if (aProp > bProp) {
      return 1;
    }
      return 0;
    })
  );
}
```

**7.滚动策略**
为了确定整个内容的大小以及它在任何时刻需要实际渲染的内容，视口依赖于所提供的一个 VirtualScrollStrategy。提供它的最简单方式是在视口上使用 itemSize 指令（例如 <cdk-virtual-scroll-viewport itemSize="50">）。但是，也可以通过创建一个实现 VirtualScrollStrategy 接口的类来提供自定义策略，并在包含此视口的组件上把它提供为 VIRTUAL_SCROLL_STRATEGY。
```
import { FixedSizeVirtualScrollStrategy, VIRTUAL_SCROLL_STRATEGY } from '@angular/cdk/scrolling';
export class CustomVirtualScrollStrategy extends FixedSizeVirtualScrollStrategy {
  constructor() {
    super(20, 50, 200); // (itemSize, minBufferPx, maxBufferPx)
  }
}
@Component({
  selector: 'app-virtual-scroll',
  templateUrl: './virtual-scroll.component.html',
  styleUrls: ['./virtual-scroll.component.scss'],
  changeDetection: ChangeDetectionStrategy.OnPush,
  providers: [{ provide: VIRTUAL_SCROLL_STRATEGY, useClass: CustomVirtualScrollStrategy }]
})
export class VirtualScrollComponent implements OnInit {
  @ViewChild(CdkVirtualScrollViewport)
  viewport: CdkVirtualScrollViewport;
  items = Array(1000).fill(0).map(() => Math.round(Math.random() * 100));
  constructor() {
  }
  ngOnInit() {
  }
}
```
![image.png](https://upload-images.jianshu.io/upload_images/18030682-09a36d668d03492c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)