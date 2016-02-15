##scope揭秘
你已经从书中开始的部分看到了关于scope作用域的内容。现在我们要去揭秘AngularJS作用域。<br>
基本上，一个scope作用域就是一个纯粹的Angularjs对象。
```
var myObject={name:'AngularJS',creator:'Misko'}
```
一个scope作用域就像其他的对象一样，能够对其附加属性和方法。唯一的区别就是我们不通常手动构建一个scope作用域。然而，AngularJS自动创建和注入一个scope作用域。<br>
在AngularJS的世界里，一个scope对象被用来存储视图层提供的模型的值。在先前的例子中，在控制器中的scope作用域上附加属性：（$scope.greeting='hello'）;我们这样做以便这个附加的属性能够通过表达式{{greeting}}呈现在视图层中。


