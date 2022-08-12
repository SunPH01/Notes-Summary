移动端页面的底部菜单栏，通常会使用fixed定位在底部。在安卓手机上经常会出现软键盘弹出时，底部定位被顶上去，下面提供vue和jQuery两种解决办法。

### vue解决方法

```
<!--html部分-->
<div class="footer" v-show="hideshow"></div>
// js 部分
data(){
  return {
    docmHeight: document.documentElement.clientHeight,  //默认屏幕高度
	showHeight: document.documentElement.clientHeight,   //实时屏幕高度
	hideshow:true,  //显示或者隐藏footer
  }
},
mounted() {
  // window.onresize监听页面高度的变化
  window.onresize = ()=>{
    return(()=>{
      this.showHeight = document.body.clientHeight;
    })()
  }
},
//监听
watch:{
  showHeight:function() {
    if(this.docmHeight > this.showHeight){
      this.hideshow=false
    }else{
      this.hideshow=true
    }
  }
}
```

### js解决方案
```
var winHeight = $(window).height();  //获取当前页面高度
$(window).resize(function () {
    var thisHeight = $(this).height();
    if ( winHeight - thisHeight > 140 ) {
        //键盘弹出
        $('.footer').css('position','static');
    } else {
        //键盘收起
        $('.footer').css({'position':'fixed','bottom':'0'});
    }
})
```