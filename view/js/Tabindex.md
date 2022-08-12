1、tabindex 属性可以设置键盘中的TAB键在控件中的移动顺序，及焦点的顺序

2、 tabindex 属性值范围在1到32767之间

3、 默认的 tabindex 的值为 0 ，将排在所有指定tableIndex的控件之后

4、 如果把 tabindex 的属性设置为负值，那么这个控件 将会被排除在 TAB键的序列之外

5、 tabindex 设置任意值 esc 都会起作用

6、tabindex 属性显性的定义了一个页面中foucusable（可定焦）元素的导航顺序

 

实例：指定的元素在聚焦时添加样式
```javascript
$(document).on('keyup', function (evt) {
    var oEvent = evt || event;
    if (oEvent.keyCode == 9) {
        var node = angular.element('.tabindex');
        node.css('color', 'black');
        var $active = document.activeElement;//当前聚焦的元素节点
if ($active && $active.className &&$active.className.indexOf('tabindex') > -1)         {
            $active.style.color = 'red';
        }
                
    }
})
```