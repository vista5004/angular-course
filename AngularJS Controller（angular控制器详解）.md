#Controller控制器
##控制器的定义
控制器的作用是通过附加模型和和方法来为其扩大作用域，为了随后在视图层能够访问的到。AngularJS中的控制器就是在html文件中AngularJS程序遇到ng-controller指令的时候生成的构造函数。我们将会在下一章中探索AngularJS的作用域。正如我在第一章中提到的，作用域scope就是控制器和视图层之间的胶水，所以控制器能够添加属性，视图层能够访问到这些属性。<br>
##为控制器附加属性和方法
下一步，我们写一个控制器，用来通过一个作用域传递现在的时间到视图层。<br>
######controller
<pre><code>
angular.module('myAPP',[])
  .controller('GreetingController',function($scope){
    $scope.now=new Data();//把时间模型附加到作用域上
    $scope.greeting='Hello';
  })
</code></pre>
通过在angular.module()上调用controller()来注册一个控制器。这个控制器需要两个参数：第一个参数是这个控制器的名字，第二个是当在html中执行到ng-controller指令时被调用的实例方法。能够忽略这个实例方法中的作用域$scope参数吗？当你声明这个$scope作为一个控制器实例的参数，你就表明你的控制器是依赖于这个$scope对象的。这个$scope的参数有特殊意义。AngularJS根据实例方法参数的名字来推断这个控制器的依赖。在这个例子中，当AngularJS在HTML文件中找到ng-controller='GreetingController'这个指令时，AngularJs会为控制器创建一个新的作用域，当实例化这个方法时，AngularJS会传递这个作用域作为一个参数到这个实例方法。这叫做依赖注入，是AngularJS的核心。我们将会在后面的章节中讨论依赖注入的话题。
######多个依赖
能够为控制器设置多个依赖关系，并且能够声明这些依赖作为参数传递到构造方法中。为了增加$scope，每个AngularJS应用都有一个一个$rootScope














