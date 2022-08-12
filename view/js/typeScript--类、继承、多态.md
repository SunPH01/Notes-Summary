ts中定义类
```typescript
class Person01{
name:string; 
constructor(name:string){ //构造函数 实例化类的时候触发的方法
this.name=name;
}
getName():string{
return this.name;
}
setName(name:string):void{
this.name=name;
}
}
var p01=new Person01('张三01');
console.log(p01.getName()); //张三01
p01.setName('李四01');
console.log(p01.getName()); //李四01

ts中类的继承
//2、ts中实现继承 extends、 super
class Person02{
name:string;
constructor(name:string){
this.name=name;
}

run():string{
return `${this.name}在运动`
}
}
class Web extends Person02{
constructor(name:string){
super(name); /*初始化父类的构造函数*/
}
}
var w02=new Web('李四');
console.log('w02:',w02.run()); //w02: 李四在运动
```
ts中继承----父类方法与子类方法
```typescript
// ts中继承的探讨 父类的方法和子类的方法一致
class Person03{
name:string;
constructor(name:string){
this.name=name;
}
run():string{
return `${this.name}在运动`
}
}
class Web03 extends Person03{
constructor(name:string){
super(name); /*初始化父类的构造函数*/
}
run():string{
return `${this.name}在运动-子类`
}
work(){
console.log(`w03:${this.name}在工作`)
}
}
var w03=new Web03('李四');
console.log('w03:',w03.run());//w03: 李四在运动-子类
w03.work();//w03:李四在工作
```
### 类里面的修饰符
**public :**公有 在当前类里面、 子类 、类外面都可以访问
**protected：**保护类型 在当前类里面、子类里面可以访问 ，在类外部没法访问
**private ：**私有 在当前类里面可以访问，子类、类外部都没法访问
属性如果不加修饰符 默认就是 公有 （public）

#### public 公有属性
public :公有 在类里面、 子类 、类外面都可以访问

```typescript
class Person04{
public name:string; /*公有属性*/
constructor(name:string){
this.name=name;
}
run():string{
return `${this.name}在运动`
}
}
class Web04 extends Person04{
constructor(name:string){
super(name); /*初始化父类的构造函数*/
}
run():string{
return `${this.name}在运动-子类`
}
work(){
console.log(`04:${this.name}在工作`)
}
}
var w04=new Web04('李四');//子类访问name
w04.work();//04:李四在工作
//类外部访问公有属性
var p04=new Person04('哈哈哈');
console.log('04:',p04.name); //类外部访问name //04: 哈哈哈
```
#### protected 保护类型
protected：保护类型 在类里面、子类里面可以访问 ，在类外部

```typescript
没法访问
class Person05{
protected name:string; /*公有属性*/
constructor(name:string){
this.name=name;
}
run():string{
return `05:${this.name}在运动`
}
}
var p05=new Person05('王五');
console.log(p05.run());//在类里面访问 05:王五在运动
class Web05 extends Person05{
constructor(name:string){
super(name); /*初始化父类的构造函数*/
} 
work(){

console.log(`05:${this.name}在工作`)
}
}
var w05=new Web05('李四11');
w05.work();//子类访问 05:李四11在工作
console.log( '05:',w05.run()); //子类访问 05: 05:李四11在运动
//类外外部没法访问保护类型的属性
var ps05=new Person05('哈哈哈');
console.log('05:',ps05.name);//浏览器还能打印 但是编辑器语法检查报错 name受保护，只能在类'Person05'及其子类中访问
```
#### private 私有属性
private ：私有 在类里面可以访问，子类、类外部都没法访问

```typescript
class Person06{
private name:string; /*私有*/
constructor(name:string){
this.name=name;
}
run():string{
return `${this.name}在运动`
}
}
class Web06 extends Person06{
constructor(name:string){
super(name)
}
work(){
console.log(`06:${this.name}在工作`); //语法检查报错 属性name为私有属性，只能在类'Person06'中访问
}
}
var p06=new Person06('哈哈哈');
console.log('06:',p06.run());//类里面访问 06: 哈哈哈在运动
console.log('06:',p06.name);//类外面访问 语法检查报错 属性name为私有属性，只能在类'Person06'中访问
var w06 = new Web06('小佳');
console.log('06:',w06.run());//类里面访问 06: 小佳在运动
w06.work();//子类访问 子类Web06 work函数里，语法检查报错 06: 小佳在工作
```
### 静态类   静态方法
静态方法与非静态方法最明显的区别就是如果某个方法是公共静态的，那么可以直接通过 ‘类名.方法名’ 的方法来调用，而公共实例方法则需要事先实例化对象，然后才能调用。

```typescript
/****************静态属性 静态方法**********************/
class Per07{
public name:string;
public age:number=20;
//静态属性
static sex="男";
constructor(name:string) {
this.name=name;
}
run(){ /*实例方法*/
console.log(`07：${this.name}在运动`)
}
work(){
console.log(`07：${this.name}在工作`)
}
static print(){ /*静态方法 里面没法直接调用类里面的属性 只能调用静态属性*/
console.log('07:print方法'+Per07.sex + this.age); 
}
}
Per07.print();//07:print方法男undefined
console.log('07:',Per07.sex);//07: 男
```
### 继承多态
多态:父类定义一个方法不去实现，让继承它的子类去实现 每一个子类有不同的表现 
多态属于继承

```typescript
class Animal02 {
name:string;
constructor(name:string) {
this.name=name;
}
eat(){ //具体吃什么 不知道 ， 具体吃什么由继承它的子类去实现 ，每一个子类的表现不一样
console.log('Animal02：吃的方法')
}
}
class Dog extends Animal02{
constructor(name:string){
super(name)
}
eat(){
return this.name+'吃粮食'
}
}
class Cat extends Animal02{
constructor(name:string){
super(name)
}
eat(){
return this.name+'吃老鼠'
}
}
```
### 抽象类 

* typescript中的抽象类：它是提供其他类继承的基类，不能直接被实例化。
* 用abstract关键字定义抽象类和抽象方法，抽象类中的抽象方法不包含具体实现并且必须在派生类中实现。
* abstract抽象方法只能放在抽象类里面
* 抽象类和抽象方法用来定义标准 。 标准：Animal 这个类要求它的子类必须包含eat方法
```typescript
//标准:
abstract class Animal1{
public name:string;
constructor(name:string){
this.name=name;
}
abstract eat():any; //抽象方法不包含具体实现并且必须在派生类中实现。
run(){
console.log('其他方法可以不实现')
}
}
// var a=new Animal() /*错误的写法*/
class Dog1 extends Animal1{
//抽象类的子类必须实现抽象类里面的抽象方法
constructor(name:any){
super(name)
}
eat(){
console.log(this.name+'吃粮食')
}
}
var d=new Dog1('小花花');
d.eat();
class Cat1 extends Animal1{
//抽象类的子类必须实现抽象类里面的抽象方法
constructor(name:any){
super(name)
}
run(){
}
eat(){
console.log(this.name+'吃老鼠')
}
}
var c=new Cat1('小花猫');
c.eat();
```