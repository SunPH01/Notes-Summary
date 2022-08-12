1.获取repeat循环结束：

自定义指令：
```
.directive('repeatFinish', function () {
        return {
            link: function (scope, element, attrs) {
                if (scope.$last) {                   // 这个判断意味着最后一个 OK
                    scope.$eval(attrs.repeatFinish);    // 执行绑定的表达式
                }
            }
        }
})
```
html:
```
<li  ng-repeat="item in shortCutMenu.list" repeat-finish="repeatEnd($index)"></li>
```
controller:
```
 $scope.repeatEnd = function (idx) {
            alert(idx);
};
```

2.limitTo过滤，指定循环开始位置，循环长度
```
ng-repeat="bMsg in buyMsg|limitTo:20"  <!--限制循环长度，20条-->
ng-repeat="sMsg in sellMsg|limitTo:20:5"  <!--20是长度限制，5是指定循环开始下标（第一条下标是0）-->
```