title: angular项目中使用iframe相关问题
date: 2016-09-22 17:30:34
tags: Angular
---

# iframe高度自适应
问题： 在angular项目中需要嵌入第三方页面，通过iframe来实现，但是嵌入的页面高度未知。

解决方案：自定义一个angular指令，监听iframe的`onload`事件来获取iframe中内容的高度。

<!-- more -->

源码：

angular 代码
```
// 定义指令
App.directive('iframeOnload', [function(){
    return {
        scope: {
            callBack: '&iframeOnload'
        },
        link: function(scope, element, attrs){
            element.on('load', function(){
                // 关键是这个
                element.height(element.contents().outerHeight());
                return scope.callBack();
            });
        }
    }}]);


App.controller('MyController', ['$scope', function($scope){

    $scope.iframeLoadedCallBack = function(){
        // do stuff
    }
}]);
```
在DOM中使用
```
<div ng-controller="MyController">
    <iframe iframe-onload="iframeLoadedCallBack()" src="..."></iframe>
</div>
```

[解决方案来源：Angular, onLoad function on an iFrame](http://stackoverflow.com/questions/15882326/angular-onload-function-on-an-iframe)
