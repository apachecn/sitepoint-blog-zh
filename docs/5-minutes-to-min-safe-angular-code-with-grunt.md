# 距离最小安全角度代码还有 5 分钟

> 原文：<https://www.sitepoint.com/5-minutes-to-min-safe-angular-code-with-grunt/>

优化页面速度无疑是任何构建 web 应用程序的开发人员的主要关注点。像 [Grunt](http://gruntjs.com/) 这样的任务运行者可以在开发过程中发挥关键作用，因为他们自动化了代码连接和缩减的活动，这将是本教程的主要主题。具体来说，我们将使用一组 Grunt 插件来确保我们的 [AngularJS](https://angularjs.org/) 应用程序在缩小时是安全的。在我开始讨论 AngularJS 和缩小之前，我想强调所有技能水平的开发者都可以从本教程中受益，但是 Grunt 的基础知识是可取的。在这篇文章中，我们将使用 Grunt 生成新的文件夹，因此那些不熟悉使用任务运行器的人将会对它的工作方式有一个很好的感受。

## 缩小角度应用的问题是

默认情况下，AngularJS 应用程序不是最小安全的。它们必须使用数组语法编写。如果您对数组语法的确切含义感到困惑，请不要担心，您可能已经编写了利用它的代码。让我们来看两个 AngularJS 控制器的例子，它们被传递了`$scope`和`$http`参数。

在下面的第一个例子中，模块的工厂和控制器被包装在以 DI 注释开始的数组中，正如你所看到的，它没有遵循 [DRY(不要重复自己)原则](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)。

```
var form = angular.module('ControllerOne', [])
form.factory('Users', ['$http', function($http) {
    return {
        get: function() {
            return $http.get('/api/users');
        },
        create: function(userData) {
            return $http.post('/api/users', userData);
        },
        delete: function(id) {
            return $http.delete('/api/users/' + id);
        }
    };
}]);

form.controller('InputController', ['$scope', '$http', 'Users', function($scope, $http, Users) {
    formData = {};
    $scope.createUser = function () {
        if ($scope.formData != undefined) {
            Users.create($scope.formData)
                .success(function (data) {
                $scope.users = data;
                $scope.formData = {};
                $scope.myForm.$setPristine(true);
            });
        }
    };   
}]);
```

在下一个例子中，`crud.config`模块代码仍然不是最小安全的，但是代码比前一个短。它只是命名服务，然后将必要的依赖项作为参数传递给函数，而不必首先将它们作为字符串写出。只要不被删节，这段代码就会运行得很好。因此，很容易理解为什么人们在编写 AngularJS 代码时经常选择这种语法。

```
var form = angular.module('ControllerTwo', [])
form.factory('Users', function($http) {
    return {
        get: function() {
            return $http.get('/api/users');
        },
        create: function(userData) {
            return $http.post('/api/users', userData);
        },
        delete: function(id) {
            return $http.delete('/api/users/' + id);
        }
    };
});

form.controller('InputController', function($scope, $http, Users) {
    formData = {};
    $scope.createUser = function() {
        if ($scope.formData != undefined) {
            Users.create($scope.formData)
            .success(function(data) {
                $scope.users = data;
                $scope.formData = {};
                $scope.myForm.$setPristine(true);
            });        
        }
    };
});
```

既然您已经了解了这两种代码之间的物理差异，我将很快向您解释为什么这种语法对于缩小是不安全的。

## 数组符号的工作原理

如上所述，数组符号以 DI 注释开始，这在使代码最小安全方面起着关键作用。当 [UglifyJS](https://github.com/mishoo/UglifyJS) 运行时，它会将我们的参数从`$scope`和`$http`分别重命名为`a`和`b`。DI 注释作为字符串在数组中传递，这阻止了它们被重命名。因此，这些重命名的参数仍然可以访问必要的依赖项。

如果这些注释不存在，代码将会中断。如您所见，以这种方式手动编写代码是非常低效的。为了帮助您避免这种情况，我现在将展示如何使用 Grunt 来注释、连接和缩小您的 AngularJS 应用程序，使它们在缩小方面得到充分优化，并可用于生产。

## 使用咕噜声

项目的整个存储库，包括我们将要定位的文件，可以在 GitHub 上找到。对于那些习惯使用 Grunt 的人来说，可以随意跟随并创建自己的构建，或者将这些代码添加到现有的项目中。如果您从一个空目录开始工作，您必须确保您的目录中有一个“package.json”文件。该文件可以通过运行命令`npm init`来创建。

一旦项目中有了“package.json”文件，就可以通过运行以下命令来下载插件:

```
npm install grunt-contrib-concat grunt-contrib-uglify grunt-ng-annotate --save-dev
```

这将把 Grunt 安装到您的项目中，以及我们将使用的三个插件:

*   咕噜-贡献-连接
*   咕哝-贡献-丑陋
*   咕哝-ng-注释

虽然没有 Grunt 也可以使用`ng-annotate`,但是您很快就会看到无缝 Grunt 是如何完成注释、连接和缩小代码的过程的。它为缩小 AngularJS 代码提供了一个简单而有效的解决方案。如果您从头开始跟踪这个项目，您应该在项目的根目录中有一个 Gruntfile.js，它将保存您所有的 Grunt 代码。如果还没有，现在就创建它。

## 最小安全代码的三个步骤

### 步骤 1–配置 Grunt 来读取“package.json”文件

要访问我们之前安装的插件，首先需要配置 Gruntfile 的`pkg`属性来读取“package.json”文件的内容。config 对象直接从 Grunt 的包装函数的顶部开始，在下面的例子中从第 3 行扩展到第 5 行，但是很快就会包含大部分代码。

```
module.exports = function(grunt) {
    //grunt wrapper function 
    grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),
          //grunt task configuration will go here 
    });
}
```

### 步骤 2-加载并注册繁重的任务

在配置 Grunt 读取我们的“package.json”文件后，需要加载插件，这样 Grunt 就可以访问它们。这是通过将插件的名称作为字符串传递给`grunt.loadNpmTask()`来实现的。重要的是要确保这些插件被加载到包装函数的内部的*，而不是配置对象的*外部的*。如果不满足这些条件，Grunt 将无法正常工作。*

我们需要做的下一件事是创建一个默认任务，在没有特定目标的情况下调用 Grunt 时将执行该任务。您应该注意添加这些任务的顺序，因为它们将根据配置运行。这里，ngAnnotate 被配置为首先运行，在 concat 和 UglifyJS 之前，我认为这是构建您的代码的最佳方式。此外，重要的是要记住`grunt.registerTask()`必须在插件加载后放置。

根据我们刚才讨论的内容，Gruntfile.js 应该如下所示:

```
module.exports = function(grunt) {
    grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),
          //grunt task configuration will go here 
    });

    //load grunt tasks
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    grunt.loadNpmTasks('grunt-ng-annotate'); 

    //register grunt default task
    grunt.registerTask('default', ['ngAnnotate', 'concat', 'uglify']);
}
```

### 步骤 3–配置插件

#### ngAnnotate

现在我们的 Gruntfile 已经准备好了，让我们回到 config 对象中，指定我们希望`ngAnnotate`插件指向的文件。为了做到这一点，我们必须首先为`ngAnnotate`创建一个部分，并创建一个目标，在本例中称为`spApp`。在这个目标中，您将指定您希望添加 DI 注释的文件，以及生成它们的文件夹。在这个例子中，Grunt 将获取在`public/js`中指定的三个文件，并将它们生成到一个名为`public/min-safe`的新文件夹中。

一旦完成了配置，您就可以运行`grunt ngAnnotate`并查看代码是如何生成的。此外，你可以访问 GitHub 页面查看 grunt-ng-annotate ，并查看它允许你指定的不同选项。

```
ngAnnotate: {
    options: {
        singleQuotes: true
    },
    app: {
        files: {
            './public/min-safe/js/appFactory.js': ['./public/js/appFactory.js'],
            './public/min-safe/js/FormController.js': ['./public/js/FormController.js'],
            './public/min-safe/app.js': ['./public/js/app.js']
        }
    }
}
```

#### 串联

现在，您已经生成了一个包含新注释的 AngularJS 代码的文件夹，让我们继续将这些代码编译或连接成一个文件。以同样的方式，我们为`ngAnnotate`创建了一个部分，现在我们将为 [concat](https://github.com/gruntjs/grunt-contrib-concat) 和 [UglifyJS](https://github.com/gruntjs/grunt-contrib-uglify) 做同样的事情。就像`ngAnnotate`一样，这两个任务都有一个目标，在本例中是`js`。有许多配置选项可以传递给这些任务，但是我们只需指定`src`和`dest`来指向正确的文件。您可能已经猜到了，这些插件将获取传递给`src`对象的文件内容，并将它们处理到`dest`之后指定的文件夹中。

让我们试着理解这里发生了什么。您可以通过简单地在您的终端中运行`grunt concat`来测试这一点，它应该会导致`./public/min/app.js`的创建。

```
concat: {
    js: { //target
        src: ['./public/min-safe/app.js', './public/min-safe/js/*.js'],
        dest: './public/min/app.js'
    }
}
```

#### 缩小

我们需要做的最后一件事是通过缩小代码来移除无用的空间。这就是 UglifyJS 插件发挥作用的地方。当使用 UglifyJS 时，我们希望 Grunt 完成缩小应用程序的最后过程。因此，我们希望将保存所有新连接代码的文件作为目标，在本例中是`public/min/app.js`。为了测试这一点，运行`grunt uglify`，看看你新缩小的。

以下是该任务的相关配置:

```
uglify: {
    js: { //target
        src: ['./public/min/app.js'],
        dest: './public/min/app.js'
    }
}
```

在本课中，我们分别使用了所有这些任务。现在，让我们使用之前创建的默认任务。它将允许 Grunt 按照注册的顺序，一个接一个地运行所有指定的任务。现在，只需在项目中运行`grunt`就可以对代码进行注释、连接和缩减。

## 结论

我希望通过这篇简短的教程，你能很好地理解数组符号，以及为什么它对于使 AngularJS 应用程序最小安全是必不可少的。如果你是 Grunt 的新手，我强烈建议你使用这些插件，以及其他插件，因为它们可以节省你大量的时间。一如既往，欢迎在下面发表评论，或者如果你有任何问题，请按我简历中的地址给我发邮件。

## 分享这篇文章