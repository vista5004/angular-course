##scope揭秘
你已经从书中开始的部分看到了关于scope作用域的内容。现在我们要去揭秘AngularJS作用域。<br>
基本上，一个scope作用域就是一个纯粹的Angularjs对象。
```
var myObject={name:'AngularJS',creator:'Misko'}
```
一个scope作用域就像其他的对象一样，能够对其附加属性和方法。唯一的区别就是我们不通常手动构建一个scope作用域。然而，AngularJS自动创建和注入一个scope作用域。<p>
在AngularJS的世界里，一个scope对象被用来存储视图层提供的模型的值。在先前的例子中，在控制器中的scope作用域上附加属性：（$scope.greeting='hello'）;我们这样做以便这个附加的属性能够通过表达式{{greeting}}呈现在视图层中。每一个AngularJS应用都至少有一个scope作用域叫做根作用域$rootscope。这是作为任何HTML元素附加ng-app指令的结果创建的。换句话说，当AngularJS应用启动，就会创建一个$rootscope。接下来，当你对任何元素附加ng-controller指令的时候，它就会创建一个继承$rooptscope的新的scope作用域。进一步说，你可以嵌套scope作用域通过把一个ng-controller指令放在另一个ng-controller指令中。请看下面的片段：<p>
```
<div ng-app> <!-- creates a $rootScope -->
  <div ng-controller="OuterController"> <!--creates a scope(call it scope 1) that inherits from $rootScope-->
    <div ng-controller="InnerController"> <!-- Creates a childscope (call it scope 2) that inherits from scope 1
    </div>
  </div>
</div>
```
$rootscope是所有scope作用域的父及。结果是，所有附加在$rootscope上的属性能够被scope隐式的获取。<p>
如果你仔细观察上面的代码，你会发现scpe作用域以一种作用域和DOM元素相关联的形式混合嵌套在一起的形式。最重要的是AngularJS的scope作用域继承是继承两者之间的原型。Javascript不像其他的编程语言一样，例如Java、C++、c#，不支持传统的继承。Javascipt用不同的方法来实现，并且简单和易于使用。每一个javascript构造函数都有一个pototype属性，直接指向对象。当你获取一个对象上的属性(someObject.somePrototype)，javascript会在这个对象上搜寻这个属性。这个prototype原型属性就是一个对象。如果在原型prototype上找到就会返回。如果没有找到，这个搜寻就会沿着原型链往上寻找，知道这个属性被找到或者Object.prototype被找到。下面这个例子会给出原型继承的一个了解。<p>
```
function Car (color,steering){
  this.color=color;
  this.steering=steering;
}
Car.prototype.year=2012;
var car=new Car('red',left);
console.log(car.color) //print the color form car
console.log(car.year)  //print the year form Car.prototype
console.log(car.hasOwnPrototype(year)) //return false
```
正如你看到的，这个color属性在car的对象上被找到并且返回了。但是car对象上没有year的属性。所以Javascipt会在Car的原型上搜索，找到后并返回。所以无论你在构造函数上设置什么属性，将会通过实例来获取他。<p>
##访问prototype
当我们讨论对象中的值的时候，就变得有趣起来。让我们一起看两个例子。<p>
####写一个原始的对象
我们的对象car没有year属性，但是Car.prototype有。当你试图读取car.year，你会从Car.prototype中读取到。但是你仍然可以像这样获取year
```
car.year=2000;
console.log(car.year);//从car的对象上获取，而不是原型上
console.log(car.hasOwnPrototy('year'))//返回true，由于car有自己的属性
```
当你附加一个新属性到这个对象上，这个属性就直接附加到这个对象上了，而不是其原型prototype上。随后当你访问这个属性的时候，Javascript也不再访问这个对象的原型，因为会在这个对象本身上直接找到。<p>
####关于对象的对象类型
我们给<code>Car.prototype</code>添加一个叫做<code>data</code>的属性。<p>
代码如下：<p>
```
car.data.engine='rear'; //This does not create a new property called 'data' on car object
console.log(car.data.engine); //returns 'rear' and it comes from Car.prototype
console.log(car.hasOwnProperty('data')); // false, as car doesn't have own property 'data'
Car.prototype.hasOwnProperty('data'); // 'data' property is created in prototype.
```
当我们写一个像data这样的对象类型的时候，Javascript会找到prototype，在这个例子中是Car.prototype，并且检查是否有data的原型。这样就创建了一个新的原型<code>Car.prototype.data</code>。<p>
####对象的继承
在Javascipt中对象能够继承对象，这是明白angular中作用域继承的关键。看下面代码：<p>
```
var ferrari=Object.create(car);
console.log(Object.getPrototypeOf(ferrari)); //Car {}
```
<code>Object.create()</code>创建了一个新的对象，其内部的__proto__ 属性直接指向作为第一个参数函数对象。结果ferrari对象的__proto__指向了<code>car</code>，所以ferrari拥有car实例对象的所有属性。<p>
我们快速的过了一遍属性的继承，现在让我们看看AngularJS如何利用这个在scope中实现继承。<p>
#### AngularJS Scopes中的原型继承
$rootScope有一个叫做$new()的方法用来创建新的子作用域。前面那个例子中我们混合了两个控制器，其中一个的作用域就是$rootScope。下面是代码：<p>
```
<div ng-app> <!-- creates a $rootScope -->
  <div ng-controller="OuterController"> <!--creates a scope(call it scope 1) that inherits from $rootScope-->
    <div ng-controller="InnerController"> <!-- Creates achild scope (call it scope 2) that inherits from scope 1
    </div>
  </div>
</div>
```
下面是AngularJS的scope的层次结构。<p>
1.Angularjs找到ng-app后会创建$rootScope对象。<br>
2.当$rootScope遇到ng-controller的时候，找到并指向OuterController。所以，它会调用$rootScope.$new()，用来创建一个子scope $scope1，来继承$rootscope。在这一点上这个子作用域的prototype(__proto__)指向了$rootScope。所以任何附加在$rootScope上的属性都能够被子scope获取。如果OuterController通过给声明的构造函数增加一个参数$scope来实现声明依赖。AngularJS用最新创建的子$scope来触发它。<br>
3.当遍历DOM的时候，AngularJS遇到另外一个ng-controller指令指向InnerController。现在它会创建另外一个子作用域scope，继承上一层级的$scope。像从前那样在$scope1引用$new()创建一个新的子scope $scope2。这样继承的结果是$scope2能够获取$scope1的所有属性。<br>
<p><br>
内层的scope能够通过获取外层的例子定义的属性。除去controller和directive可以创建子作用域。一些指令能够在不产生子scope的前提下使用父scope。另外的一些指令创建创建一些单独的指令，但是不会继承父scope，存在在他们自己的scope上。我们稍后在指令上讨论单独的scope。<p>
我们通过一个小例子来增强理解。我们将会创建一个三本书的书单的app。使用Angular Seed结构，经过下面三步来创建：
1、创建myApp模块，进入app/js/app.js.<p>
```
'use strict';
angular.module('myApp', ['myApp.controllers']);
angular.module('myApp').run(function($rootScope){
  $rootScope.title='Famous Books';
  $rootScope.name="Root Scope";
});
```
2.在app/js/controllers.js中创建需要的控制器。<p>
```
angular.module('myApp.controllers',[]).controller('SiteController', function($scope){
  $scope.publisher='SitePoint';
  $scope.type="Web Development";
  $scope.name="Scope for SiteController";
});
angular.module('myApp.controllers').controller('BookController', function($scope){
  $scope.books = ['Jump Start HTML5','Jump Start CSS','Jump StartResponsive Web Design'];
  $scope.name="Scope for BookController";
});
```
3.直接在app文件夹最外面创建视图命名为scopes.html。<p>
```
<!DOCTYPE html>
<html ng-app="myApp">
<head>
<meta charset="utf-8" />
<title ng-bind="title"></title>
</head>
<body ng-controller="SiteController">
  <span>{{publisher}} excels in {{type}} books</span>
  <div ng-controller="BookController">
  <h3>Some of the popular books from {{publisher}}</h3>
  <ul>
    <li ng-repeat="book in books">
      {{book}}
    </li>
  </ul>
  </div>
</body>
<script src="lib/angular/angular.js"></script>
<script src="js/app.js"></script>
<script src="js/controllers.js"></script>
</html>
```





