**app.modules.ts导入**
```
import { DragDropModule } from '@angular/cdk/drag-drop';

imports: [
  ...
  DragDropModule
]
```

html:
```
<div cdkDrag class="drag-box"
drag me
</div>
```
## **1.排序**
html:
```
<h3>列表排序</h3>
<div class="box-list" cdkDropList (cdkDropListDropped)="drop($event)">
<div class="drag-box" *ngFor="let customer of customers" cdkDrag>
{{customer.name}}
</div>
</div>
```
ts:
```
import { ...CdkDragDrop, moveItemInArray } from '@angular/cdk/drag-drop';

customers = [
{ name: 'Adam', age: 23 },
{ name: 'Jack', age: 27 },
{ name: 'Katherin', age: 26 },
{ name: 'John', age: 30 },
{ name: 'Watson', age: 42 },
];

drop(event: CdkDragDrop<string[]>) {
console.log('列表排序：', event, this.customers);
moveItemInArray(this.customers, event.previousIndex, event.currentIndex);
}
```
![image.png](https://upload-images.jianshu.io/upload_images/18030682-1814bc2fe2e00c95.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
打印结果：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-2d289cf3756d25fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## **2.横向列表排序**
```
<h3>横向排序</h3>
<div class="box-list-horizontal" cdkDropList cdkDropListOrientation="horizontal" (cdkDropListDropped)="drop($event)">
  <div class="drag-box" *ngFor="let customer of customers" cdkDrag>
    {{customer.name}}
  </div>
</div>
```
## **3.动画效果**
为了设置动画，我们定义了一个以transform属性为目标的转换。拖放CDK支持动画：
--对列表中的元素进行拖动排序时的动画=> .cdk-drag
```
.cdk-drag {
transition: transform 100ms ease;
}
```
--从动画的位置到最终把它放在列表的位置上时的动画=> .cdk-drag-animating
```
.cdk-drag-animating {
transition: transform 300ms ease;
}
```
## **4.占位符元素 placeholder**
默认占位符：
使用.cdk-drag-placeholder来显示占位符元素，而不是实际的元素，因为它是在cdkDropList中拖动的。默认情况下，这看起来与正在排序的元素完全相同。
```
.cdk-drag-placeholder {
background: #ccc;
border: dotted 1px #999;
transition: transform 500ms ease;
}
```
![image.png](https://upload-images.jianshu.io/upload_images/18030682-7748cf0dfc450a88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**自定义占位符 Custom placeholder：**
使用*cdkDragPlaceholder指令，我们可以用一个自定义的占位符来代替默认的占位符:
```
<div class="box-list customph" cdkDropList (cdkDropListDropped)="drop($event)">
  <div class="drag-box dragList" *ngFor="let customer of customers" cdkDrag >
    <div class="box-custom-placeholder" *cdkDragPlaceholder></div>
    {{customer.name}}
  </div>
</div>
```
```
// 自定义占位符
.customph{
  .box-custom-placeholder {
    height: 28px;
    width: 100px;
    background: #fff;
    border: dotted 1px #0084ff;
    transition: transform 200ms ease;
  }
  &.box-list.cdk-drop-list-dragging .drag-box:not(.cdk-drag-placeholder) {
    transition: transform 500ms ease;
  }
}
```
## **5.预览**
当提取并拖动cdkDrag元素时，可以看到预览元素。默认情况下，预览元素看起来与被拖动的元素完全相同。
使用.cdk-drag-preview来定义预览CSS:
```
.cdk-drag-preview {
height: 28px;
width: 100px;
box-shadow: 0 3px 3px -3px #0084ff;
}
```
我们还需要使用*cdkDragPreview提供一个自定义模板:
```
<div cdkDropList class="box-list" (cdkDropListDropped)="drop($event)">
<div class="drag-box dragList" *ngFor="let customer of customers" cdkDrag>
{{customer.name}}
<p *cdkDragPreview>Age: {{customer.age}}</p>
</div>
</div>
```
## **6.在列表之间拖放**
我们可以使用cdkDropListConnectedTo属性将一个或多个cdkDropList连接到一起。然后设置cdkDropListData和cdkDragData，将数据与cdkDropList和cdkDrag关联起来。
```
<div class="box-list" cdkDropList #inactiveList="cdkDropList" id = "Inactive Customers"
  [cdkDropListData]="inactiveCustomers" [cdkDropListConnectedTo]="[activeList]"
  (cdkDropListDropped)="drop1($event)">
  <div
    class="drag-box dragList"
    [cdkDragData]="customer"
    *ngFor="let customer of inactiveCustomers" 
    cdkDrag>
      {{customer}}
  </div>
</div>
<div class="box-list" cdkDropList #activeList="cdkDropList" id = "Active Customers"
  [cdkDropListData]="activeCustomers" [cdkDropListConnectedTo]="[inactiveList]"
  (cdkDropListDropped)="drop1($event)">
   <div
     class="drag-box dragList"
     [cdkDragData]="customer"
     *ngFor="let customer of activeCustomers" 
     cdkDrag>
       {{customer}}
   </div>
</div>
```
```
import { ...transferArrayItem } from '@angular/cdk/drag-drop';
```
```
inactiveCustomers = [
'Adam',
'Jack',
'Katherin'
];

activeCustomers = [
'John',
'Watson'
];
```
```
drop1(event: CdkDragDrop<string[]>) {
  console.log('拖拽事件：event', event);
  if (event.previousContainer === event.container) {
  console.log('拖拽事件',`> 拖 '${event.item.data}' 到 '${event.container.id}'`);
  moveItemInArray(event.container.data, event.previousIndex, event.currentIndex);
  } else {
    console.log('拖拽事件',`> 拖 '${event.item.data}' 到 '${event.container.id}'`);
    transferArrayItem(event.previousContainer.data, event.container.data,event.previousIndex, event.currentIndex);
  }
}
```
![image.png](https://upload-images.jianshu.io/upload_images/18030682-bd25b405cccf7543.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/18030682-7a764e4986fa3580.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/18030682-ef3a46cf7870c615.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/18030682-5007b101debc47e5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**dkDropListGroup**
CdkDropListGroup指令的范围比CdkDropList要大一级。CdkDropListGroup可以包含多个CdkDropList。而且当CdkDropListGroup包含多个CdkDropList的时候，这些CdkDropList直接是相互connect的(CdkDropList就不用去写cdkDropListConnectedTo属性了)。
```
<div cdkDropListGroup>
  <div class="box-list" cdkDropList id = "Inactive Customers"
    [cdkDropListData]="inactiveCustomers" 
    (cdkDropListDropped)="drop1($event)">
  <div class="drag-box dragList" [cdkDragData]="customer"
    *ngFor="let customer of inactiveCustomers" cdkDrag>
    {{customer.name}}
  </div>
</div>
  <div class="box-list" cdkDropList id = "Active Customers"
    [cdkDropListData]="activeCustomers" 
    (cdkDropListDropped)="drop1($event)">
    <div
      class="drag-box dragList"
      [cdkDragData]="customer"
      *ngFor="let customer of activeCustomers" 
      cdkDrag>
        {{customer.name}}
    </div>
  </div>
</div>
```

## **7.事件监听**
`Drag Event Handlers`
– cdkDragStarted: 当用户开始拖动时发出。
– cdkDragEnded: 当用户停止拖动时发出。
– cdkDragEntered: 将item移动到新容器中时发出。
– cdkDragExited: 将item移出当前容器时发出。
```
<h3>事件监听</h3>
<div cdkDrag class="drag-box"
(cdkDragStarted)="dragStarted($event)"
(cdkDragEnded)="dragEnded($event)"
(cdkDragMoved)="dragMoved($event)"
>
drag me
</div>
<p>{{state}} {{position}}</p>
```
```
import { CdkDragEnd, CdkDragStart, CdkDragMove... } from '@angular/cdk/drag-drop';
```
```
dragStarted(event: CdkDragStart) {
this.state = '拖拽开始';
console.log('拖拽开始');
}

dragEnded(event: CdkDragEnd) {
this.state = '拖拽结束';
console.log('拖拽结束');
}

dragMoved(event: CdkDragMove) {
console.log('dragMoved');
this.position = `> Position X: ${event.pointerPosition.x} - Y: ${event.pointerPosition.y}`;
}
```
`Drop Event Handlers`
– cdkDropListDropped: emits when an item was dropped inside the container.
– cdkDropListEntered: emits when a new item is dragged into this container.
– cdkDropListExited: emits when an item is dragged from this container into another container.

```
<div class="box-list" cdkDropList #inactiveList="cdkDropList" id = "Inactive Customers"
[cdkDropListData]="inactiveCustomers" [cdkDropListConnectedTo]="[activeList]"
(cdkDropListDropped)="drop1($event)"
(cdkDropListEntered)="dropListEntered($event)" (cdkDropListExited)="dropListExited($event)"
>
<div class="drag-box dragList" [cdkDragData]="customer"
(cdkDragEntered)="dragEntered($event)" (cdkDragExited)="dragExited($event)"
*ngFor="let customer of inactiveCustomers" 
cdkDrag>
{{customer}}
</div>
</div>
<div class="box-list" cdkDropList #activeList="cdkDropList" id = "Active Customers"
[cdkDropListData]="activeCustomers" [cdkDropListConnectedTo]="[inactiveList]"
(cdkDropListDropped)="drop1($event)">
<div
class="drag-box dragList"
[cdkDragData]="customer"
*ngFor="let customer of activeCustomers" 
cdkDrag>
{{customer}}
</div>
</div>
```
```
import { ...CdkDragEnter, CdkDragExit} from '@angular/cdk/drag-drop';
```
```
dragEntered(event: CdkDragEnter) {
console.log(`drag:把 '${event.item.data}' 拖进 '${event.container.id}' `);
}

dragExited(event: CdkDragExit) {
console.log(`drag:把 '${event.item.data}' 从 '${event.container.id}' 中拖出 `);
}

dropListEntered(event: CdkDragEnter) {
console.log(`drop:'${event.container.id}' 中拖进了 '${event.item.data}'`);
}

dropListExited(event: CdkDragExit) {
console.log(`drop：从 '${event.container.id}' 中拖出 '${event.item.data}'`);
}
```
执行如下操作
![image.png](https://upload-images.jianshu.io/upload_images/18030682-c51c91b822781d46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
打印结果：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-9aa71e03a3859d4c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
再執行如下操作：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-3ec8b22607bfd8a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
打印结果：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-7363ff30738622d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**cdkDropListSorted**
拖拽过程中交换item的时候回调
```
<div class="box-list" cdkDropList #activeList="cdkDropList" id = "Active Customers"
[cdkDropListData]="activeCustomers" [cdkDropListConnectedTo]="[inactiveList]"
(cdkDropListDropped)="drop1($event)" 
(cdkDropListSorted)="dropListSorted($event)"
>
<div
class="drag-box dragList"
[cdkDragData]="customer"
*ngFor="let customer of activeCustomers" 
cdkDrag>
{{customer.name}}
</div>
</div>
```
```
dropListSorted(event: CdkDropList) {
console.log('Sorted:', event);
}
```
**cdkDropListEnterPredicate**
`enterPredicate: (drag: CdkDrag, drop: CdkDropList) => boolean`
指定哪些item是可以拖拽到当前容器
```
<div class="box-list" cdkDropList #activeList="cdkDropList" id = "Active Customers"
[cdkDropListData]="activeCustomers" [cdkDropListConnectedTo]="[inactiveList]"
(cdkDropListDropped)="drop1($event)" [cdkDropListEnterPredicate]="predicate"
>
<div
class="drag-box dragList"
[cdkDragData]="customer"
*ngFor="let customer of activeCustomers" 
cdkDrag>
{{customer.name}}
</div>
</div>
```
```
import { ...CdkDrag, CdkDropList } from '@angular/cdk/drag-drop';
```
```
predicate(drag: CdkDrag, drop: CdkDropList) {
console.log('predicate:', drag, drop);
return drag.data.age >= 18;
}
```

**CdkDrag**
![image.png](https://upload-images.jianshu.io/upload_images/18030682-a5ae520ea91a8554.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**CdkDropList**
![image.png](https://upload-images.jianshu.io/upload_images/18030682-a1e6facb5de53d47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)