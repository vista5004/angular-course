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
能够为控制器设置多个依赖关系，并且能够声明这些依赖作为参数传递到构造方法中。为了增加$scope，每个AngularJS应用都有一个一个$rootScope。我们假设你有一个普通的服务用来通过XHR连接后端。你可以声明这两个服务依赖，像如下一样：
<pre><code>
angular.module('myApp',[])
  .controller(ControllerWithDependency',function($rootScope,customService){
    //在这里使用依赖
  });
</code></pre>
当实例化控制器的时候，AngularJS会读取参数列表，并且从这些名字中可以指定哪些需要被依赖注入。你也需要注意到AngularJS自己自带的服务前缀是以$为命名约定的。所以你不应该以$作为你服务的前缀。<br>

&lt;!DOCTYPE html&gt; <br>
&lt;html ng-app="myApp"&gt;<br>
&lt;head &gt;<br>
&lt;script type='text/javascript' src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.16/angular.js" &gt;&lt;/script&gt;<br>
&lt;script src="app.js"&gt;&lt;/script&gt;<br>
&lt;/head&gt;<br>
&lt;body ng-controller="GreetingController"&gt;<br>
{{greeting}} User! The current date/time is <span>{{now | date:'medium'}}&lt;/span&gt;<br>
&lt;/body&gt;<br>
&lt;/html&gt;<br>
<br>
在这里我们把angular的ng-controller指令加入到HTML中，这意味着在&lt;body&gt;&lt;/body&gt;标签之间的所有内容均在控制器的作用域下。每次ng-controller检测，AngularJS为这个特殊的控制器和实例创造一个新的作用域。所以当ng-controller="GreetingController"遇到这个构造方法GreetingController运行的时候，为作用域下的两个模型：greeting和now赋值。在视图中我们可以通过表达式{{}}来获取值。当我们写{{greeting}}AngularJS会用这个已经存在greeting属性的值来替代它。对于now模型来说也是如此。无论在{{}}里写的是什么，都要能和scope对应上。<br>
当你在浏览器里运行HTML的时候你应该看到如下<br>
Hello, User! The current date/time is <current date & time here>.
##添加逻辑到控制器<br>
除了操作用户输入和为控制器赋值，一个控制器也为$scope添加方法，这些方法表现某种类型的逻辑并且和服务一起封装应用的业务数据。
为了明白这些，让我们为$scope添加一个方法，并且在一个随机的语言中返回greeting的值。<br>
下面是如何定义控制器：<br>
```
angular.module('myApp',[])
  .controller('GreetingController',function($scope){
    $scope.now=new Date();
    $scope.helloMessages = ['Hello', 'Bonjour', 'Hola', 'Ciao',Hallo'];
    $scope.greeting = $scope.helloMessages[0];
    $scope.getRandomHelloMessage=function(){
      $scope.greeting=$scope.helloMessages[parseInt(Math.random()*$scope.helloMessages.length)]
    }
  })
```
这里我们把helloMessages字符数组数组添加到$scope当前作用域上，表示hello五种语言的表达。我们也为当前作用域$scope上附加了getRandomHelloMessage()方法随机选择一个信息并且赋值给作用域下greeting模型。作为数据绑定的结果，当$scope.greeting被更新，在视图层表达式{{greeting}}也会被改变。<br>
相应的视图是
&lt;!DOCTYPE html&gt;<br>
&lt;html ng-app="myApp"&gt;<br>
&lt;head&gt;<br>
&lt;script type='text/javascript' src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.16/angular.js"&gt;&lt;/script&gt;
&lt;script src="app.js"&gt;&lt;/script&gt;<br>
&lt;/head&gt;<br>
&lt;body ng-controller="GreetingController"&gt;<br>
{{greeting}} User! The current date/time is &lt;span&gt;{{now | date:'medium'}}&lt;/span&gt;.<br>
&lt;br/&gt;
&lt;button ng-click="getRandomHelloMessage()"&gt;Random Hello Message&lt;/button&gt;<br>
&lt;/body&gt;<br>
&lt;/html&gt;<br>
这里我们增加了一个HTML按钮用来通过调用在控制器的作用域上getRandomHelloMessage()方法来响应点击事件。反过来，这个方法改变greeting模型的值。所以这个变化被反射到视图层。<br>
诚然，这个例子是沈简单的。<br>
但是我们学习到了基本的控制器和如何使用它，现在我们来讨论，控制器不该做什么。
+ 不要进行DOM操作，DOM操作应该在指令中进行。
+ 
+ 



