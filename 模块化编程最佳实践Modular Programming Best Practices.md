当你把你的项目源码分为不同的模块的时候，一般由两个选择：
（1）通过层来实现模块化（2）通过特征来实现模块化
首先我们进入angular seed（https://github.com/angular/angular-seed/tree/69c9416c8407fd5806aab3c63916dfcf0522ecbc）
文件的app/js文件中，看看采用的哪种模式，当你打开文件夹，你会看到如下几个文件：
1、app.js 
2、controller.js
3、directive.js
4、filter.js
5、server.js
这就是通过层的概念来实现的模块化
每一种组件的类型，被装进了一种特殊的模块。最终这个主要的依赖于其他四个模块的入口app模块在app.js模块中定义。这是通过angular.module()
的第二个参数来指定的。你只需要参考在html中ng-app指令。这种类型模块化在很多场景都很有用。例如当你遇到一个特殊类型的bug，找到服务，你就
指定在哪里发现。
但是当你的app应用规模和复杂性增长到一定程度，或许就适用于通过层来划分模块的方式。可以通过特征的方式来实现。例如你的站点需要一个登陆
模块。所有这些模块都独立存在，并且紧密的耦合在一起。通常你为每个模块创建一个独立的文件用来放置相关的JavaScript文件在每个相关的文件夹中。
这些文件夹中的代码是可重用的，因为你能把某一个模块的一整个文件夹放入另外一个不同的项目中。并且不同的团队之间可以同时开发不同的模块，
这些模块并没有紧密的耦合在一起。此外，单元测试也就变得小菜一碟了，只要你加载需要的模块，并且分开测试也变得容易。
我们调整一下Angular Seed Project来使用这种方式，例如博客系统，可以使用这种模式：
结构如下：
/app
/img -- application level images
/css -- application level css
/js
    app.js -- the main app module
/modules
/login
/js
    controllers.js --controllers for login module
    directives.js --directives for login module
/views -- views for login module
/css
/img
    loginModule.js -- Main login module definition
/comment
/js
    controllers.js --controllers for login module
    directives.js --directives for login module
/views -- views for comment module
/css
/img
    commentModule.js -- Main comment module definition
    ...
    ...
index.html

在这些的文件中我们定义自己的模块。比如登陆模块，如下
app/modules/login/js/controller
angular.module('mainApp.login.controller',[]);

app/modules/login/js/directive.js
angular.module('mainApp.login.directives',[])

app/modules/login/loginModule.js
angular.module('loginModule',['mainApp.login.controller','mainApp.login.directives'])
对于评论模块，定义如下
/app/modules/comment/js/controllers.js
angular.module('mainApp.comment.controllers',[]);

/app/modules/comment/js/directives.js
angular.module('mainApp.comment.directives',[]);

/app/modules/comment/loginModule.js
angular.module('commentModule',['mainApp.comment.controllers',mainApp.comment.directives']);

在主要的模块中定义/app/app.js如下
angular.module('mainApp',['loginModule','commentModule'])
最终在index.html我们启动文件通过写入
ng-app='mainApp'

正如你看到的，这个登陆模块loginModule和评论模块commentModule互相是不通信的。但是通过mainApp模块组合在一起。在HTML文件中，当angular遇到
ng-app，它就通过加载指定的模块启动。在这种情况下，angular启动app程序通过加载mainApp模块。但是angular发现这个mainApp模块依赖于另外两个
模块loginModule和commentModule。但是这两个模块本身也依赖于其他两个模块（控制器模块和指令模块），也需要angular来加载。这样angular启动程序
通过加载需要的模块。





