##Angular JS - Controller 通信两种机制实践和思考
#####2015年6月4日 星期四 上午8:22
----

	Angularjs为在scope中为我们提供了冒泡和隧道机制，$broadcast会把事件广播给所有子controller，而$emit则会将事件冒泡传递给父controller，$on则是angularjs的事件注册函数，有了这一些我们就能很快的以angularjs的方式去解决angularjs controller之间的通信。

[tris_web](https://github.com/zhoufan2013/TRIS_Platform/tree/master/web) 为了解决 LoginController  和 MenuController 间的通信问题，尝试了两种解决思路。

	其一，采用事件监听注册机制。登录模块获取到的登录标识冒泡传递给父 Controller，后者广播给所有子 Controller，注册了监听函数的子 Controller 获得登录标识后再做关联动作。

	其二，采用 $rootScope 。Scope 是 AngularJS 中各个 View 和 Controller 之间的桥梁，而 $rootScope 即各个Controller 之间 scope 的桥梁。定义全局的 scope 变量可以帮助我们区分不同的登录标识触发的不同动作。

	两种代码实践，最后择其二。当然还要考虑全局变量被滥用的问题，其一如果定义不好，也会造成循环冒泡页面假死的状态。

----
#####事件监听机制代码实现：

*冒泡*

	$scope.$emit("upc_menu_control", true);

*父 Controller 广播*

	$scope.$on("upc_menu_control",
        function (event, msg) {
            console.log("parent", msg);
            $scope.$broadcast("upc_menu_control", msg);
    });
*子 Controller 监听*

	$scope.$on("upc_menu_control",
        function (event, msg) {
            console.log("upc_menu_control", msg);
            $scope.upc_menu_control = msg;
    });

#####$rootScope 代码实现

*Controller 引用 $rootScope，定义全局变量，HTML 直接引用*

	$rootScope.login_result = true;
	<li ng-show="$root.login_result"><a style="cursor: pointer" href="#upc_detail">Detailed Analysis</a></li>


