# 用 MATLAB 和均值堆栈创建 Web 应用程序

> 原文：<https://www.sitepoint.com/creating-a-web-app-with-matlab-and-the-mean-stack/>

[MATLAB](http://in.mathworks.com/products/matlab/) 是一种用于技术计算的高级语言。它在一个易于使用的环境中集成了计算、可视化和编程，其中问题和解决方案用熟悉的数学符号表示。世界上有很多项目是由数百万科学家和工程师用 MATLAB 编写和开发的。人们从 MATLAB 获得的各种实验和操作的数据可以用来驱动 web 应用程序，但有几个障碍:

*   MATLAB 理解矩阵格式的数据，而 web 应用程序更喜欢 JSON 或 XML 格式的数据。
*   通常，数据是在 MATLAB 程序中创建和使用的，这限制了开发人员在保存、使用数据等方面的自由

如果 MATLAB 提供 JSON 格式的数据，那么创建应用程序会容易得多，而 web 应用程序可以使用来自 MATLAB 的 JSON 数据来创建奇妙的东西。

在本文中，我们将开发一个小演示来演示如何让 MATLAB 和 MEAN stack 一起工作。

## 关于 web 应用程序

web 应用程序将涉及从 MATLAB 到浏览器的实时数据传输。为了简单起见，我们将从 MATLAB 传输当前时间，并在浏览器上显示。我们将使用 [JSONlab](http://uk.mathworks.com/matlabcentral/fileexchange/33381-jsonlab--a-toolbox-to-encode-decode-json-files-in-matlab-octave) ，一个在 MATLAB 中编码/解码 JSON 文件的工具箱。将使用平均堆栈创建 web 应用程序。如果你不熟悉均值堆栈，我建议你在继续之前阅读文章[均值堆栈介绍](https://www.sitepoint.com/introduction-mean-stack)。

## JSONlab 简介

JSONlab 是一个针对 MATLAB 语言的 JSON 编码器/解码器的免费开源实现。它可用于将 MATLAB 数据结构(数组、结构、单元、结构数组和单元数组)转换为 JSON 格式的字符串，或将 JSON 文件解码为 MATLAB 数据。

它为我们提供了四个功能:`loadjson()`、`savejson()`、`loadubjson()`和`saveubjson()`。最后两个函数用于处理 [UBJSON 格式](http://ubjson.org/)。`loadjson()`用于将一个 JSON 字符串转换成相关的 MATLAB 对象。在我们的项目中，我们将只使用将 MATLAB 对象(cell、struct 或 array)转换成 JSON 字符串的`savejson()`函数。它可以通过以下方式使用:

```
json = savejson(rootname, obj, filename)
json = savejson(rootname, obj, opt)
json = savejson(rootname, obj, 'param1', value1, 'param2', value2, ...)
```

因为我们必须写一个文件，我们将使用第一个签名。它返回一个 JSON 字符串，并将该字符串写入文件。

### JSONlab 安装

首先，[下载 JSONlab](http://sourceforge.net/projects/iso2mesh/files/jsonlab/1.0%20%28Optimus%20Final%29/) ，解压缩归档文件，并使用以下命令将文件夹的路径添加到 MATLAB 的路径列表中:

```
addpath('/path/to/jsonlab');
```

如果想永久添加这个路径，需要键入`pathtool`，浏览到 JSONlab 根文件夹，将其添加到列表中。一旦完成，你必须点击“保存”。然后，在 MATLAB 中运行`rehash`，输入`which loadjson`。如果您看到一个输出，这意味着 JSONlab 安装正确。

## MATLAB 代码

我们需要当前时间，所以我们将使用`clock`命令。它以`[year month day hour minute seconds]`格式返回包含当前日期和时间的六元素日期向量。为了重复获取时间，我们将`clock`命令放在一个无限的`while`循环中。因此，我们获得实时数据，直到使用 MATLAB 命令窗口上的`Ctrl+C`终止脚本执行。

以下代码实现了这一想法:

```
format shortg;
y=0;
while y = 0
    % c = [year month day hour minute seconds]
    c=clock;
    % Rounding every value to an integer
    c=fix(c);
    x.clock=c;
    % accessing the 4th column of c, i.e hours
    x.hours=c(:,4);
    % accessing the 5th column of c ,i.e minutes
    x.minutes=c(:,5);
    % accessing the 6th column of c, i.e seconds
    x.seconds=c(:,6);
    % converting x into JSON and writing as matlabData.json
    savejson('',x,'data/matlabData.json');
end
```

在我们的项目中，我们关心小时、分钟和秒。上面代码中使用的`fix(c)`函数将矩阵的所有元素四舍五入为最接近的整数。为了获得`hour`数据，我们需要矩阵的第 4 <sup>列</sup>的值，所以我们使用命令`c(:,4)`。使用相同的方法，我们检索分和秒。

我们将把`clock`和它的一些独立变量分别发送到 web 应用程序，以展示不同数据类型从 MATLAB 对象到 JSON 的转换。当`clock`数据将被转换成`Array`时，小时、分钟和秒的值将被转换成`Number`，我们将在后面看到。

在我们的项目中，我们将使用`savejson()`函数在文件`matlabData.json`中使用 JSON 格式转换和写入变量`x`。为了简单起见，`rootname`参数将是一个空字符串。

有了前面的代码，我们需要的所有 MATLAB 代码都完成了。现在，只要我们运行脚本，我们就可以观察到 JSON 文件是在`data`文件夹中创建的，并且文件中的数据会自动更新。JSON 文件的内容示例如下所示:

```
{
   "hours": 19,
   "minutes": 28,
   "seconds": 28,
   "clock": [2015,5,27,19,28,28]
}
```

我们将观察这个文件，并使用 Node.js 读取最新的数据。

## web 应用程序

现在我们来自 MATLAB 的数据已经被转换成 JSON 并存储在一个文件中，我们可以独立地读取这个文件并通过观察它的变化来获得数据。这个操作完全独立于 MATLAB。在本文的剩余部分，我将假设您对 [socket.io](http://socket.io/) 以及 MEAN stack 有一些了解，尽管我们将只使用它们的基本概念。

让我们开始编写 web 应用程序。

### 创建 Package.json 文件

从我们的应用程序开始，让我们定义项目的依赖项。为此，我们将创建如下所示的 package.json 文件:

```
{
  "name": "matlab-mean-demo",
  "version": "1.0.0",
  "description": "A demo web-app using Matlab and MEAN stack",
  "main": "server.js",
  "dependencies": {
    "express": "latest",
    "mongoose": "latest",
    "socket.io": "^1.2.0"
  }
```

创建文件后，在项目的根文件夹中运行`npm install`,以便安装所有的依赖项。如果你不熟悉 [npm](https://www.npmjs.com/) ，我建议你阅读[NPM 初学者指南——节点包管理器](https://www.sitepoint.com/beginners-guide-node-package-manager/)。

### 服务器端代码

这部分代码涉及 Node.js、Express 和 MongoDB 的使用。服务器执行的操作包括:

*   提供一个`index.html`文件
*   观察和读取 JSON 文件中的数据
*   使用 MongoDB 在数据库中保存数据
*   使用 socket.io 将数据发送到浏览器

我们将在根文件夹中创建一个名为`server.js`的文件，在这里我们将编写所有描述的特性所需的代码。

我们使用 Express 提供静态文件:

```
// Defining the root directory for static files
app.use(express.static(__dirname + '/app'));

// Serving the static HTML
app.get("/", function(req, res) {
    res.sendfile("/index.html");
});
```

每当一个请求被发送到`/`，存储在`app`目录中的`index.html`文件将被提供。

为了观察文件的任何变化，我们使用`fs.watch()`，为了在每次变化时读取文件，我们使用`fs.readFile()`。一旦检测到更改，就会读取文件并检索数据。整个过程是用下面的代码完成的:

```
fs.watch('folderName',function(event,filename){
	fs.readFile('folderName' + filename, function(err,data){
  		console.log(data);
	});
});
```

当与客户端建立连接并开始获取数据时，我们执行两个操作:

1.  使用 socket.io 的`emit()`函数将数据发送到浏览器
2.  使用[mongose](http://mongoosejs.com/)中间件将数据保存在 MongoDB 中

为了执行第二个操作，我们创建一个数据模式，然后基于该模式创建一个模型。这是通过如下所示的代码完成的:

```
// Creation of the schema
var dataSchema = mongoose.Schema({
   clock: Array,
   hours: Number,
   minutes: Number,
   seconds: Number
});

// Creating a model based on schema
var appData = mongoose.model('appData', dataSchema);
```

在前面代码片段的最后一条语句中，我们根据定义的模式创建了模型。传递给函数的第一个参数是我们的模型所代表的集合的单数名称。Mongoose 自动将复数名称分配给集合。所以在这里，`appData`是一个`appDatas`集合的模型。

当我们获得新数据时，我们用最新数据创建该模式的新实例，并使用`save()`方法将其保存在数据库中。这个实例被称为**文档**。在下面的代码中`savingData`是一个文档。

该部分的最终代码如下所示:

```
var express = require('express');
var mongoose = require('mongoose');
var fs = require('fs');
var app = express();

//Make a connection to MongoDB
mongoose.connect('MongoDB://localhost/matlabMeanDemo');
var io = require('socket.io')(app.listen(3000));

//Defining the root directory for static files
app.use(express.static(__dirname + '/app'));

//serving the static HTML
app.get("/", function (req, res) {
    res.sendfile("/index.html");
});

var appData;
var db = mongoose.connection;
db.on('error', console.error.bind(console, 'connection error:'));
db.once('open', function callback() {
    var dataSchema;

    dataSchema = mongoose.Schema({
        clock: Array,
        hours: Number,
        minutes: Number,
        seconds: Number
    });
    appData = mongoose.model('appData', dataSchema);

    //Sending and receiving data
    io.on('connection', function (socket) {
        fs.watch('data', function (event, filename) {
            fs.readFile('data/' + filename, function (err, data) {
                if (!err) {
                    try {
                        var x = JSON.parse(data);
                        socket.emit('updated', x);

                        // Create a new instance of appData model
                        // i.e also known as a document
                        var savingData = new appData({
                            clock: x.clock,
                            hours: x.hours,
                            minutes: x.minutes,
                            seconds: x.seconds
                        });
                        //save data
                        savingData.save();
                    } catch (e) {
                        console.log('malformed data');
                    }
                }
            })
        });
    });
});
```

我们使用`try`和`catch`来防止应用程序崩溃。如果我们不使用它，并且`JSON.parse`抛出一个错误`unexpected user input`，因为有时数据由于快速变化率而没有被完全读取，应用程序可能会崩溃。我们想要避免的事情！

另外，请确保 MongoDB 服务器正在运行，否则应用程序会崩溃。

### 客户端代码

在这一节中，我们将创建一个简单的静态 HTML 页面。当通过 socket.io 接收到新数据时，我们会更新页面上显示的数据。这些数据还可以用来创建实时图形和图表。

下面是`index.html`文件的简单代码:

```
<body ng-app="demo" ng-controller="demoController" ng-cloak class="ng-cloak">
    <div>{{data.hours}} : {{data.minutes}} : {{data.seconds}}</div>
</body>

<script src="/path/to/angular.js"></script>
<script src='/path/to/socket.io.js'></script>
<script> var socket = io.connect();

angular.module('demo', []).controller('demoController', ['$scope', function($scope) {
    socket.on('updated', function(data) {
        $scope.$apply(function(){
            $scope.data = data;
        });
    });
}]); </script>
```

`ngCloak`指令用于防止浏览器在加载我们的应用程序时以原始(未编译)形式短暂显示 AngularJS 的模板。

最后，我们需要添加下面的 CSS 代码，以便在 HTML 主体之后加载 AngularJS 时使用。

```
[ng\:cloak],
[ng-cloak],
[data-ng-cloak],
[x-ng-cloak],
.ng-cloak,
.x-ng-cloak {
    display: none !important;
}
```

控制器是以长函数形式编写的，所以我们不需要注入参数。

每当收到新数据时，我们需要使用`$scope.apply()`来更新视图上的数据。`$scope.apply()`接受函数或 AngularJS 表达式字符串，并执行它。然后，它自动调用`$scope.$digest()`来更新任何观察者。另一种选择是`$timeout`(由 AngularJS 提供)，它类似于`setTimeout`，但默认情况下会自动将我们的代码包装在`$apply`中。

### 运行应用程序

在启动 Node.js 服务器之前，我们需要确保 MATLAB 代码和 MongoDB 服务器正在运行。要运行 MongoDB 服务器，您需要在终端上执行命令`mongod`。要运行 Node.js 服务器，您必须在项目文件夹的根目录中执行命令`node server.js`。

显示当前时间的静态页面将在`127.0.0.1:3000`提供。

### 结论

在本文中，我们使用 MEAN stack 创建了一个 web 应用程序，它从 MATLAB 程序中获取 JSON 格式的数据。这些数据是在 JSONlab 的帮助下转换的。然后使用 socket.io 将数据发送到浏览器，这样浏览器上的变化就会实时反映出来。该演示的完整源代码可在 [GitHub](https://github.com/sitepoint-editors/matlab-mean-demo) 上获得。

我希望你喜欢这篇文章，我期待着阅读您的评论。

## 分享这篇文章