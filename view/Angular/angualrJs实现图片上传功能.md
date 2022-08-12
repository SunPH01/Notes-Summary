整体逻辑：service提供FileReader函数，directive提供点击事件的绑定和监听，controller用来修改html上的ng-src属性值

1.HTML
```
<input type="file" file-model="myFile">
/*AngularJS定义的file-Model属性用于对文件进行操作*/
    
<img alt="配图预览" ng-src="{{imageSrc}}">
/*这里用来放置上传的图片进行预览，ng-src是AngularJS定义替代<img>标签的src属性，其值将在后台逻辑获得*/
```
2.AngularJS

1)Controller
 ```
  .controller('AddarticleCtrl', function ($scope,fileReader) {
/*Controller是实际操作html元素的部分*/
    $scope.getFile= function () {
      fileReader.readAsDataUrl($scope.myFile,$scope)
/*注意这里$scope.myFile，要看实际情况，调试发现这里用该调用入参数的myFile属性*/
        .then(function (result) {
          $scope.imageSrc=result;
        });
      };
  });
 ```
2）Directive
```
 .directive('fileModel', function ($parse) {/*$parse是AngularJS的内置directive*/
    return {
      restrict: 'A',/*限制该directive的声明方式 为Attribute*/
      link: function (scope, element, attrs) {
        var model=$parse(attrs.fileModel);
        var modelSetter=model.assign;
        element.bind('change',function (event) {/*页面加载时执行*/
          scope.$apply(function () {/*当用户点击html上的input标签，选中需要上传的图片 然后点击确定后执行*/
            modelSetter(scope,element[0].files[0]);
          });
          scope.getFile();
        });
      }
    };
  });
```
3)Service
```
 .service('fileReader', function ($q) {
    // AngularJS will instantiate a singleton by calling "new" on this function
    var onLoad=function (reader,deferred,scope) {
      return function () {
        scope.$apply(function () {
          deferred.resolve(reader.result);
        });
      };
    };
    var onError=function (reader,deferred,scope) {
      return function () {
        scope.$apply(function () {
          deferred.reject(reader.result);
        });
      };
    };

    var getReader=function (deferred,scope) {
      var reader=new FileReader();
      reader.onload=onLoad(reader,deferred,scope);
      reader.onerror=onError(reader,deferred,scope);
      return reader;
    }

    var readAsDataURL=function (file,scope) {/*上传图片的主函数*/
      var deferred=$q.defer();
      var reader=getReader(deferred,scope);
      reader.readAsDataURL(file);
      return deferred.promise;
    };

    return{
      readAsDataUrl:readAsDataURL
    };
  });
```