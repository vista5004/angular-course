##scope揭秘
你已经从书中开始的部分看到了关于scope作用域的内容。现在我们要去揭秘AngularJS作用域。<br>
基本上，一个scope作用域就是一个纯粹的Angularjs对象。
```
var myObject={name:'AngularJS',creator:'Misko'}
```
一个scope作用域就像其他的对象一样，能够对其附加属性和方法。唯一的区别就是我们不通常手动构建一个scope作用域。然而，AngularJS自动创建和注入一个scope作用域。<br>
在AngularJS的世界里，一个scope对象被用来存储视图层提供的模型的值。在先前的例子中，在控制器中的scope作用域上附加属性：（$scope.greeting='hello'）;我们这样做以便这个附加的属性能够通过表达式{{greeting}}呈现在视图层中。每一个AngularJS应用都至少有一个scope作用域叫做根作用域$rootscope。这是作为任何HTML元素附加ng-app指令的结果创建的。换句话说，当AngularJS应用启动，就会创建一个$rootscope。接下来，当你对任何元素附加ng-controller指令的时候，它就会创建一个继承$rooptscope的新的scope作用域。进一步说，你可以嵌套scope作用域通过把一个ng-controller指令放在另一个ng-controller指令中。请看下面的片段：<br>
```
<div ng-app> <!-- creates a $rootScope -->
  <div ng-controller="OuterController"> <!--creates a scope(call it scope 1) that inherits from $rootScope-->
    <div ng-controller="InnerController"> <!-- Creates a childscope (call it scope 2) that inherits from scope 1
    </div>
  </div>
</div>
```
$rootscope是所有scope作用域的父及。结果是，所有附加在$rootscope上的属性能够被scope隐式的获取。<br>
如果你仔细观察上面的代码，你会发现scpe作用域以一种作用域和DOM元素相关联的形式混合嵌套在一起的形式。最重要的是AngularJS的scope作用域继承是继承两者之间的原型。Javascript不像其他的编程语言一样，例如Java、C++、c#，不支持传统的继承。Javascipt用不同的方法来实现，并且简单和易于使用。每一个javascript构造函数都有一个pototype属性，直接指向对象。当你获取一个对象上的属性(someObject.somePrototype)，javascript会在这个对象上搜寻这个属性。这个prototype原型属性就是一个对象。如果在原型prototype上找到就会返回。如果没有找到，这个搜寻就会沿着原型链往上寻找，知道这个属性被找到或者Object.prototype被找到。下面这个例子会给出原型继承的一个了解。<br>
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
正如你看到的，这个color属性在car的对象上被找到并且返回了。但是car对象上没有year的属性。所以Javascipt会在Car的原型上搜索，找到后并返回。所以无论你在构造函数上设置什么属性，将会通过实例来获取他。<br>





