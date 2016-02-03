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
<br>
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
诚然，这个例子是很简单的。<br>
但是我们学习到了基本的控制器和如何使用它，现在我们来讨论，控制器不该做什么。
+ 不要进行DOM操作，DOM操作应该在指令中进行。
+ 不要在控制器中格式化模型的值，过滤器的作用就在此，我们已经见过内置过滤器的操作。
+ 不要在控制器中写重复的代码。而是要封装到服务中，例如你要在多个地方从服务中获取数据。所以你与其在控制器中重复代码，不如把代码封装在服务中，当需要的时候注入到控制器中。当所有的控制器线程处理完用户输入后，为当前作用域$scope设置属性和方法，并且和服务进行交互来表现业务逻辑。<br>
控制器应该做：
+通过为控制器附加模型来设定$scope的初始状态，
+为作用域附加方法用来处理任务。
##为控制器附加实例方法和属性
尽管控制器经常为作用域设置方法和属性，你也可以为控制器创建一个实例方法和属性。记住，AngularJS实例化你的控制器通过调用你提供的构造方法。这意味着你有创建实例属性和实例方法的自由。让我们定义以前的控制器，用实例属性代替作用域模型。
```
angular.module('myApp',[])
  .controller('GreetingController',function($scope){
    this.now=new date();
    this.helloMessage=['Hello', 'Bonjour', 'Ola', 'Ciao', 'Hallo'];
    this.greeting = this.helloMessages[0];
    this.getRandomHelloMessage = function() {this.greeting = this.helloMessages[parseInt((Math.random()*this.helloMessages.length))];
  }
})
```
在视图层有一个小调整。<br>
&lt;!DOCTYPE html&gt;<br>
&lt;html ng-app="myApp"&gt;<br>
&lt;head&gt;<br>
&lt;script type='text/javascript'<br> src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.16/angular.min.js"&gt;&lt;/script&gt;<br>
&lt;script src="app.js"&gt;&gt;/script&gt;<br>
&lt;/head&gt;<br>
&lt;body ng-controller="GreetingController as greetingController"&gt;<br>
{{greetingController.greeting}} User! The current date/time is&lt;span&gt;{{greetingController.now | date: 'medium'}}&lt;/span&gt;.<br>
&lt;br/&gt;<br>
&lt;button ng-click="greetingController.getRandomHelloMessage()"&gt;Random Hello Message&lt;/button&gt;<br>
&lt;/body &gt;<br>
&lt;/html &gt;<br>
这个GreetingController作为控制器完成了变换。这个作为关键字给GreetingController的实例设置作用域。所以作为关键字把控制器暴露给视图层。并且作为结果，我们能够通过这个reference参数获取这个实例的变量和方法。<br>
不是所有的开发者，支持这个实现方法。让我们看看作为关键字什么是好的什么是坏的。<br>
######缺点
+在许多案例中暴露整个控制器实例不是一个好的方法。这个作用域对象清楚的存在于控制器和视图层之间。
+这个实现方法不是主流的，并且也需要更多的代码。
######优点
+当控制器是嵌套的，并且内层和外层控制器作用域对于模型有相同的名字，作为关键字就派上用场了。<br>
```
<div ng-controller='OuterController as outer'>
<div ng-controller='InnerController as inner'>
Outer={{outer.someModel}} and inner={{inner.someModel}}
```
##在控制器中使用依赖注入
我们已经见过如何把依赖注入到控制器中，AngularJS从构造函数的参数的名字来推断控制器的依赖，当部署代码的时候压缩Javascipt代码，这个参数的名字将会缩短，结果AngularJS将会无法识别出依赖。这种情况，你有两个选择来理清依赖当压缩代码的时候，让我们来检查这两种方法。
```
function DemoController($rootScope,$scope,$http){
  
}
DemoController.$inject=['$rootScope','$scope','$http'];
angular.module('myApp',[])
  .controller('DemoController',DemoController)
```
这个$inject属性表明控制器到构造函数参数之间的依赖关系。<br>
第二个选择在开发者中更受欢迎。你可以在angular.module()中第二个参数传递一个数组，而不是传递一个构造函数。这个数组存储依赖的名字，数组中的最后一项就是控制器构造函数的方法。这里我们如何做。
```
angular.module('myApp',[])
  .controller('DemoController',['$rootScope','$scope','$htp',function($rootScope,$scope,$http){
    
  }])
```
你会喜欢上面简单的代码，注入到构造函数中依赖关系是在数组中按顺序声明的。<br>
这些方法达到同样的目的，第一种实现方法代码有点长，相比第二种在行内并且代码要短。第二种实现方法的情况下，参数列表紧密的在一起。结果，如果你需要改变什么，你需要一个单独的位置来编辑。你可以在两行把参数列表对齐，这样你就可以看到他们是不是相互匹配。

##双向数据绑定方法概述
现在你已经明白了模块和控制器，是时候往前走一点，介绍AngularJS最基本的特性之一双向数据绑定。尽管本节，给了一个双向数据绑定的概述，重要的是在第三章会讲述的。

##什么是数据绑定
数据绑定是在视图层和模型层自动同步数据。当我们说双向的时候，那意味着同步方式两种都进行。在angular中我们创建模型并且为其赋予scope对象。
然后我们把UI组件和这些模型绑定在一起。这就建立了双向绑定在视图层组件和模型数据之间。当这个模型数值变化的时候，这个视图层来更新自己反应变化。另一方面，当视图层变化的时候，模型层也会更新自己。<br>
如果你有Flex或者Actionscript背景，可能你已经每天使用这种绑定方式，但是这种特性在HTML中还是全新的，让我们把模型层的数据当作数据的唯一来源，反过来，把你从操作DOM结构中解放出来。AngularJS保证你的视图层总是和最新的模型层数据保持更新。所以你仅仅改变这个数据，你的视图层会自动更新没有副作用的。换种说法，没必要使用innerHTML。但是如果你愿意，你仍然能够手动的更新DOM。<br>

##AngularJS中的双向绑定
接下来的例子，简单的介绍了双向绑定。简单起见，绕过控制器，集中到控制器。<br>
```
<!doctype html>
<html lang="en" ng-app>
<head>
<meta charset="utf-8">
<title>Two way data binding</title>
</head>
<body ng-init="name='AngularJS'">
  <input type="text" ng-model="name"/>
  <div><h2>Hello, {{name}}</div>
  <script src="lib/angular/angular.js"></script>
</body>
</html>
```
如果你运行上面的代码，你会看到当input输入框中的值发生改变的时候，这个div中的值，也会发生变化。让我们一步一步看看发生了什么：<br>
1、第一，这个ng-app指令启动这个应用。我们没有模块，所以我们仅仅写下ng-app没有指定值。有一点需要注意的是，当AngularJS遇到ng-app，它会给HTML产生rootscope根作用域。这个作用域scope是我们存储模型数据的地方这样才能被视图层获取。<br>
2、接下来，ng-init指令产生了一个名为name的模型，把它保存在rootscope根作用域。这个模型被初始化为AngularJS的值。<br>
3、我们已经把ng-modal指令附加到input元素上。这是基本的双向数据绑定。当你在input输入框输入东西的时候，这个作用域自动更新。所以，这样你就不必要手动为input输入框写一个keyup事件句柄来更新数据。<br>
4、 {{name}}被绑定在模型层数据上作为一个表达式被视图层单向知道。这就是双向数据绑定的第二个方面，表达式监听着作用域模型值，并且更新这个DOM，当值变化的时候。<br>
5、所以当我们在input输入框输入内容的时候，作用域模型层数据发生变化，在视图层中的表达式{{name}}，当name属性发生变化会自动更新。<br>
##做一些酷酷的事情
当事情变得复杂一点点时候数据绑定的能力就变得明显了。所以，让我们干点酷酷的事情，比如说，你已经被赋予了以下的任务。<br>
1.为使用者提供一个input输入框用来输入facebook账号。<br>
2.只要使用者输入完成，显示相应的facebook资料图片。<br>
唯一的约束是让代码的数量行数最小化，这个第一个选择是使用纯javascript代码（或者使用jquery）。所以，打开你的编辑器，写下这些代码为了这些功能，然后回到这里。但是我给你一个线索，这个接下来的URL，返回一个facebook的资料图片。Facebook ID: https://graph.facebook.com/[id here]/picture?type=normal.<br>
纯javascript代码。<br>
```
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>The Plain JS Way</title>
<script type="text/javascript">
  document.addEventListener('DOMContentLoaded',function(e){
    doucument.getElementById('fbID').addEventListener('keyup',function(event){
      var fbID=document.getElementById('fbID').value;
      var pictureURL='https://graph.facebook.com/' + fbID +'/picture?type=normal';
      document.getElementById('profilePic').src=pictureURL;
    })
  })
</script>
</head>
<body>
<input type="text" id="fbID" />
<br/>
<span><img src="" title="fb image" id="profilePic"/></span>
</body>
</html>
```
在AngularJS中使用双向绑定
```
<!doctype html>
<html lang="en" ng-app>
<head>
<meta charset="utf-8">
<title>Two way data binding</title>
</head>
<body ng-init="fbID='sandeep.panda92'">
  <input type="text" ng-modal="fbID"><br/>
  <span><img ng-src="https://graph.facebook.com/{{fbID}}picture?type=normal"/></span>
  <script src="lib/angular/angular.js"></script>
</body>
</html>
```
当你看到angularJS的版本的时候会有一点混乱。那这是如何造成的。数据双向绑定的威力刚刚显示出来。我们先来了解场景背后的内容：<br>
1.当我们在input输入的时候，多亏了AngularJS这个fbID模型就会更新。<br>
2.当我们在视图中使用表达式{{fbID}}，当fbID模型的值会更新它自己，这样会显示出新图标。<br>














