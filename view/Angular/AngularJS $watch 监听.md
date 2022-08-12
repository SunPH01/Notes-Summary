监听$watch
监听数据变化，有三个参数

`$scope.$watch(“监听的属性”,function(new，old){},true);`

写true的时候可以监听一个对象里的多个数据变化，不写true的时候只能监听单个对象变化。new是新值，old是旧值。
```javascript
$scope.data={
    price:30,
    num:1,
    free:10,
    resault:this.num*this.price+this.free
}
$scope.$watch("data",function(newVal,oldVal){
    $scope.data.resault=$scope.data.num*$scope.data.price+$scope.data.free;
    if($scope.data.num*$scope.data.price >=100){
        $scope.data.free=0;
    }else{
        $scope.data.free=10;
    }
},true);
```
