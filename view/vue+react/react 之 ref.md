React的ref有3种用法：

1. 字符串(已废弃)
2. 回调函数
3. React.createRef() （React16.3提供）

#### 1. 字符串

最早的ref用法。

1.dom节点上使用，通过`this.refs[refName]`来引用真实的dom节点
`<input ref="inputRef" /> //this.refs['inputRef']`来访问

2.类组件上使用，通过`this.refs[refName]`来引用组件的实例
`<CustomInput ref="comRef" /> //this.refs['comRef']`来访问

#### 2. 回调函数

回调函数就是在dom节点或组件上挂载函数，函数的入参是dom节点或组件实例，达到的效果与字符串形式是一样的，
都是获取其引用。

回调函数的触发时机：

1. 组件渲染后，即componentDidMount后
2. 组件卸载后，即componentWillUnmount后，此时，入参为null
3. ref改变后

**1.dom节点上使用回调函数**
`<input ref={(input) => {this.textInput = input;}} type="text" />`

**2.类组件上使用**
`<CustomInput ref={(input) => {this.textInput = input;}} />`

**3.可用通过props跨级传递的方式来获取子孙级dom节点或组件实例**

下面是在跨两级获取到孙级别的组件内部的dom节点
```
function CustomTextInput(props) { 
    return ( 
        <div>
            <input ref={props.inputRef} />
        </div>
   );
} 

function Parent(props) { 
  return (
     <div> My input: <CustomTextInput inputRef={props.inputRef} /></div>
 );
}

class Grandparent extends React.Component {
  render() { 
    return ( 
      <Parent inputRef={el => this.inputElement = el} />
     );
  }
}
```
#### 3.React.createRef()
在React 16.3版本后，使用此方法来创建ref。将其赋值给一个变量，通过ref挂载在dom节点或组件上，该ref的current属性
将能拿到dom节点或组件的实例
例如：
```
class Child extends React.Component{
    constructor(props){
        super(props);
        this.myRef=React.createRef();
    }
    componentDidMount(){
        console.log(this.myRef.current);
    }
    render(){
        return <input ref={this.myRef}/>
    }
}
```