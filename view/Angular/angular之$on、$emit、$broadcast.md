### 1.`$scope.$on`
view事件

```
//View被加载但是DOM树构建之前时： 

$scope.$on('$viewContentLoading', function(event, viewConfig){ ... }); 

//View被加载而且DOM树构建完成时： 

$scope.$on('$viewContentLoaded', function(event){ ... }); 
```

路由路径发生改变时 current:要到达的路径  previous：上一个路径
```
$scope.$on('$locationChangeStart', function (event, current, previous) {  }）；
```

销毁事件
```
$scope.$on('$destroy', function () {    });
```

**ng-route路由有几个常用的事件：**

$routeChangeStart：这个事件会在路由跳转前触发

$routeChangeSuccess：这个事件在路由跳转成功后触发

$routeChangeError：这个事件在路由跳转失败后触发

` $scope.$on("$routeChangeStart",function(event, current, previous){  });`

页面刷新：`$route.reload();`

 

**ui-router**

使用angular来做项目时，习惯性的使用第三方路由插件ui-router配置路由。每一个状态都对应着一个页面，因此对路由状态改变的监听也变的十分重要。可以使用：`$rootScope.$on（…….）`监听

$stateChangeStart: 表示状态切换开始

$stateNoFound：没有发现

$stateChangeSuccess:切换成功

$stateChangeError:切换失败

 

`$rootScope.$on('$stateChangeStart', function(event, toState, toParams, fromState, fromParams){ ... })`
回调函数的参数解释：event:当前事件的信息，toState:目的路由状态，toParams:传到目的路由的参数，fromState:起始路由状态，toParams:起始路由的参数;

2.父子controller传值

子级传递数据给父级  
 ```
// 子级传递  
$scope.checkLoggedIn = function(type) {  
          $scope.transferType = type;  
         $scope.$emit('transfer.type', type);  
}  
 
// 父级接收  
$scope.$on('transfer.type', function(event, data) {  
         $scope.transferType = data;  
        });  
       $scope.checkLoggedIn = function() {  
         var type = $scope.transferType;  
        }
 ```

父级传递数据给子级  
```
// 父级传递  
$scope.transferType = '';  
$scope.checkLoggedIn = function(type) {  
          $scope.transferType = type;  
          $scope.$broadcast('transfer.type', type);  
}  
  
// 子级接收  
$scope.transferType = '';  
$scope.$on('transfer.type', function(event, data) {  
         $scope.transferType = data;  
        });  
        $scope.checkLoggedIn = function() {  
          var type = $scope.transferType;  
}  
```