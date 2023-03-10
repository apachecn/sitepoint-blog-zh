# 初级，一个 HTML 5 移动应用的前端框架

> 原文：<https://www.sitepoint.com/junior-front-end-framework-html-5-mobile-apps/>

[Junior](http://justspamjustin.github.io/junior/#home) 是一个创建 HTML 5 移动应用的前端框架。依靠外部库，如 [modernizer](http://modernizr.com/) 、 [zepto](http://zeptojs.com/) 、 [zepto flickable](https://github.com/tomlongo/Flickable.js/) 、 [lodash](https://lodash.com/) 和 [backbone](http://backbonejs.org/) ，它通过利用[棘轮 CSS UI 组件](http://goratchet.com/components/)创建具有本地外观和感觉的移动应用。

![Junior Screenshot](img/9d80b02f90e411e92aceb32aeed52eef.png)

## 我们将创造什么

在本教程中，我们将使用初级框架从头开始创建一个简单的待办事项应用程序。我们将有两个屏幕，一个添加待办事项列表项目，第二个查看所有添加的项目。

本教程的源代码可以在 [GitHub](https://github.com/sitepoint-examples/Junior_App_Part_1) 获得。

## 入门指南

首先，[下载](http://justspamjustin.github.io/junior/#home)或克隆初级存储库:

```
git clone https://github.com/justspamjustin/junior.git
```

导航到`junior`目录并安装所需的依赖项:

```
bower install
```

安装完所需的依赖项后，打开`junior`文件夹中的`index.html`文件:

## 创建主视图

让我们首先为我们的应用程序创建一个主视图。在主视图中，我们将显示用户添加的所有待办事项列表项。主视图还会有一个按钮，用于向列表中添加新项目。我们将使用[棘轮](http://goratchet.com/)来创建我们的 UI。

我们将从零开始。在`junior`文件夹中创建一个名为`app.html`的文件。包括所有必需的 js 和 css，并添加 Junior 期望的标记，如下所示:

```
<!DOCTYPE html>
<html>
<head>
    <title></title>

    <meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black">

    <link rel="stylesheet" href="lib/stylesheets/ratchet.min.css">
    <link rel="stylesheet" href="src/stylesheets/junior.css">

    <link rel="stylesheet" href="docs/stylesheets/lib/font-awesome.css">

    <link rel="stylesheet" href="docs/stylesheets/style.css">

</head>
<body>

    <div id="app-container">
        <div id="app-main"></div>
    </div>

    <script src="lib/javascripts/modernizr.custom.15848.js"></script>
    <script src="lib/javascripts/zepto.min.js"></script>
    <script src="lib/javascripts/zepto.flickable.min.js"></script>
    <script src="lib/javascripts/lodash.min.js"></script>
    <script src="lib/javascripts/backbone-min.js"></script>
    <script src="src/javascripts/junior.js"></script>

    <script src="docs/javascripts/app.js"></script>

</body>
</html>
```

在`junior/docs/javascripts`中添加另一个名为`app.js`的文件。在这里我们将定义我们的路线和视图，并将其包含在`app.html`文件中。

```
<script src="docs/javascripts/app.js"></script>
```

[下载](http://goratchet.com/)并将棘轮样式表从`dist/css`复制到`junior/lib/stylesheets/`，替换任何可能已经存在的。

让我们从创建一个列表开始。我们将为列表定义 html，然后使用 junior lib 来扩展视图。将以下内容添加到`app.js`:

```
var HomeTemplate = [
    '<nav class="bar bar-standard">',
    '<header class="bar bar-nav">',

    '<button id="btnAddTask" class="btn pull-right">Add Task</button>',
    '<h1 class="title">ToDo List Junior App</h1>',
    '</header>',
    '</nav>',

    '<div class="bar bar-standard bar-header-secondary">',
    ' <ul class="table-view">',
    ' <li class="table-view-cell">Item 1</li>',
    ' <li class="table-view-cell table-view-cell">Item 2</li>',
    ' <li class="table-view-cell">Item 3</li>',
    ' </ul>',
    '</div>'
].join('\n');
```

上面我们已经将我们的列表定义为一个变量。接下来，我们将使用 junior 来扩展它，以创建一个视图。将以下内容添加到`app.js`:

```
var HomeView = Jr.View.extend({
    render: function() {
        this.$el.html(HomeTemplate);
        return this;
    }
});
[/js]
Now since we created our view, we need to define a route for it.
[js]
var AppRouter = Jr.Router.extend({
    routes: {
        'home': 'home'
    },

    home: function() {
        var homeView = new HomeView();
        this.renderView(homeView);
    }
});
```

上面的`home`函数渲染了`HomeView`。接下来初始化路由器，并通过将以下内容添加到`app.js`来启动主干历史:

```
var appRouter = new AppRouter();   //init the router
Backbone.history.start();     //start backbone history
```

下面是`app.js`现在的样子:

```
var HomeTemplate = [
    '<nav class="bar bar-standard">',
    '<header class="bar bar-nav">',

    '<button id="btnAddTask" class="btn pull-right">Add Task</button>',
    '<h1 class="title">ToDo List Junior App</h1>',
    '</header>',
    '</nav>',

    '<div class="bar bar-standard bar-header-secondary">',
    ' <ul class="table-view">',
    ' <li class="table-view-cell">Item 1</li>',
    ' <li class="table-view-cell table-view-cell">Item 2</li>',
    ' <li class="table-view-cell">Item 3</li>',
    ' </ul>',
    '</div>'
].join('\n');

var HomeView = Jr.View.extend({

    render: function() {
        this.$el.html(HomeTemplate);
        return this;
    }
});

var AppRouter = Jr.Router.extend({
    routes: {
        'home': 'home'
    },

    home: function() {
        var homeView = new HomeView();
        this.renderView(homeView);
    }
});

var appRouter = new AppRouter();
Backbone.history.start();
```

保存`app.js`并浏览到`app.html#home`文件，您应该会看到以下屏幕:

![todo overview](img/642404103662c8264669cb2582ff5173.png)

## 创建添加任务视图

接下来，我们将创建添加任务视图。该视图将使用户能够向现有任务列表添加任务。

将另一个名为`AddTaskView`的变量添加到`app.js`中，如图所示:

```
var AddTaskTemplate = [

    '<nav class="bar bar-standard">',
    '<header class="bar bar-nav">',

    '<button id="btnBack" class="btn btn-link btn-nav pull-left">Back</button>',
    '<h1 class="title">Add Task</h1>',
    '</header>',
    '</nav>',

    '<div class="bar bar-standard bar-header-secondary">',
    '<form>',
    '<input type="text" placeholder="Full name">',
    '<input type="search" placeholder="Search">',
    '<textarea rows="3"></textarea>',
    '<button class="btn btn-positive btn-block">Save Task</button>',
    '</form>',
    '</div>'

].join('\n');
```

使用 junior 扩展视图，并为`Addtask`视图创建一条路线(替换当前的`AppRouter`变量):

```
//extended the view using junior lib
var AddTaskView = Jr.View.extend({

    render: function() {
        this.$el.html(AddTaskTemplate);
        return this;
    }
});

var AppRouter = Jr.Router.extend({
    routes: {
        'home': 'home',
        'addTask': 'addTask' //added the route for Add Task View
    },

    home: function() {
        var homeView = new HomeView();
        this.renderView(homeView);
    },
    addTask: function() {
        var addTaskView = new AddTaskView();
        this.renderView(addTaskView);
    }

});
```

通过添加以下代码将默认视图设置为 home:

```
Jr.Navigator.navigate('home',{
  trigger: true
});
```

现在试着浏览到`app.html`，默认情况下它会加载主视图。在您的浏览器中打开`app.html#addTask`，您应该能够看到如下所示的`AddTask`视图:

![addtask overview](img/2e607957e9dfe2f5d7b95f739b6a1746.png)

接下来，我们将点击事件分别添加到`HomeView`和`AddTaskView`中的`Add Task`和`Back`按钮。

为`HomeView`和`onClickAddTask`函数定义点击事件，以导航到`AddTaskView`。用下面的代码替换当前的`HomeView`变量:

```
var HomeView = Jr.View.extend({

  render: function(){
    this.$el.html(HomeTemplate);  
    return this;
  },
  events: {
    'click #btnAddTask': 'onClickAddTask'
  },
  onClickAddTask: function() {

    Jr.Navigator.navigate('addTask',{
      trigger: true,
      animation: {
        // This time slide to the right because we are going back
        type: Jr.Navigator.animations.SLIDE_STACK,
        direction: Jr.Navigator.directions.RIGHT
      }
    });
  }
});
```

类似地，我们需要为`AddTaskView`中的`Back`按钮定义另一个点击事件。

将`AddTaskView`变量更新为:

```
var AddTaskView = Jr.View.extend({

    render: function() {
        this.$el.html(AddTaskTemplate);
        return this;
    },
    events: {
        'click #btnBack': 'onClickBack'
    },
    onClickBack: function() {

        Jr.Navigator.navigate('home', {
            trigger: true,
            animation: {

                type: Jr.Navigator.animations.SLIDE_STACK,
                direction: Jr.Navigator.directions.LEFT
            }
        });
    }
});
```

尝试浏览到`app.html`，您应该能够使用`Add Task`和`Back`按钮在视图间导航。

## 结论

在本教程中。我们使用[初级框架](http://justspamjustin.github.io/junior/#home)设计了一个简单的待办事项应用。我们使用棘轮组件来创建我们的移动应用程序 UI。在本教程的下一部分，我们将实现设计页面所需的功能。

你觉得朱尼尔怎么样？与许多其他移动 UI 框架相比，它有什么优势吗？

## 分享这篇文章