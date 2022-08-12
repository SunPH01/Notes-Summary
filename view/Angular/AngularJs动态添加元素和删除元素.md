动态添加元素和删除元素

添加：
```
//通过$compile动态编译html

var html="<div ng-click='test()'>我是后添加的</div>";

var template = angular.element(html);

var mobileDialogElement = $compile(template)($scope);

angular.element("#"+id).append(mobileDialogElement);
```

删除：
```
// remove移除创建的元素

var closeMobileDialog = function () {

if (mobileDialogElement) {

  mobileDialogElement.remove();

}
```