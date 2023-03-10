# 用推送器、节点和引导程序构建一个实时投票应用程序

> 原文：<https://www.sitepoint.com/build-a-real-time-voting-app-with-pusher-node-and-bootstrap/>

在本文中，我将带你构建一个全栈、实时的哈利波特之家投票 web 应用程序。

实时应用通常使用 WebSockets，这是一种相对较新的传输协议，与 HTTP 相反，HTTP 是一种单向通信，只有在用户请求时才会发生。WebSockets 允许服务器和用户以及所有与应用程序连接的用户之间的持久通信，只要连接保持打开。

> 实时 web 应用程序是指信息(几乎)在用户和服务器之间(以及用户和其他用户之间)即时传输的应用程序。这与传统的 web 应用程序形成对比，在传统的 web 应用程序中，客户端必须向服务器请求信息。— [Quora](https://www.quora.com/What-is-a-real-time-web-app)

我们的哈利波特投票 web 应用程序将显示选项(所有四个房子)和右侧的图表，当连接的用户投票时，图表会自动更新。

为了让您对外观有一个简单的了解，最终的应用程序将如下所示:

![Harry Potter with Chart JS](img/3aae5b67f4ad43226ab16206d8b4e2a4.png)

下面是实时应用程序如何工作的一个小预览:

[https://www.youtube.com/embed/oINRzFdZ5SY?rel=0&controls=0&showinfo=0](https://www.youtube.com/embed/oINRzFdZ5SY?rel=0&controls=0&showinfo=0)

为了使我们的应用程序实时，我们将使用 Pusher 和 WebSockets。Pusher 作为一个实时层位于您的服务器和客户端之间。它保持与客户机的持久连接——如果可能的话，通过 WebSocket，并退回到基于 HTTP 的连接——这样，一旦您的服务器有新数据推送到客户机，它们就可以通过 Pusher 立即这样做。

## 构建我们的应用程序

让我们使用命令`npm init`创建新的应用程序。你会被交互式地问一些关于你的申请细节的问题。以下是我所知道的:

```
praveen@praveen.science ➜ Harry-Potter-Pusher $ npm init
{
  "name": "harry-potter-pusher",
  "version": "1.0.0",
  "description": "A real-time voting application using Harry Potter's house selection for my article for Pusher.",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/praveenscience/Harry-Potter-Pusher.git"
  },
  "keywords": [
    "Harry_Potter",
    "Pusher",
    "Voting",
    "Real_Time",
    "Web_Application"
  ],
  "author": "Praveen Kumar Purushothaman",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/praveenscience/Harry-Potter-Pusher/issues"
  },
  "homepage": "https://github.com/praveenscience/Harry-Potter-Pusher#readme"
}

Is this OK? (yes) 
```

因此，我将大多数设置保留为默认值。现在是安装依赖项的时候了。

### 安装依赖项

我们需要 Express、body-parser、跨源资源共享(CORS)、Mongoose 和 Pusher 作为依赖项安装。要在一个命令中安装所有内容，请使用以下命令。您还可以浏览一下这个命令输出的内容。

```
praveen@praveen.science ➜ Harry-Potter-Pusher $ npm i express body-parser cors pusher mongoose
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN ajv-keywords@3.2.0 requires a peer of ajv@^6.0.0 but none is installed. You must install peer dependencies yourself.

+ pusher@2.1.2
+ body-parser@1.18.3
+ mongoose@5.2.6
+ cors@2.8.4
+ express@4.16.3
added 264 packages in 40.000s 
```

### 需要我们的模块

由于这是一个 Express 应用程序，我们首先需要包含`express()`。在做的同时，我们还需要一些配套的模块。首先，让我们从这个开始:

```
const express = require("express");
const path = require("path");
const bodyParser = require("body-parser");
const cors = require("cors"); 
```

### 创建快速应用程序

现在让我们开始构建我们的 Express 应用程序。首先，我们需要将函数`express()`的返回对象分配给一个新变量`app`:

```
const app = express(); 
```

### 服务静态资产

在初始的 includes 集合之后添加上面的行将把我们的`app`初始化为一个 Express 应用程序。我们需要做的下一件事是设置静态资源。让我们在当前项目中创建一个名为`public`的新目录，并使用 Express 的静态中间件来服务静态文件。在目录中，让我们创建一个简单的`index.html`文件，写着“Hello，World”:

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width" />
    <title>Hello, World</title>
  </head>
  <body>
    Hello, World!
  </body>
</html> 
```

为了提供静态文件，我们有一个内置的[。在 express 中使用带有](https://expressjs.com/en/api.html#app.use) [express.static()](https://expressjs.com/en/starter/static-files.html) 的()函数。语法如下:

```
app.use( express.static( path.join(__dirname, "public") ) ); 
```

我们还需要使用 body parser 中间件来获取作为 JSON 的 HTTP POST 内容，以便在`req.body`中访问。我们还将使用`urlencoded`来获得只解析`urlencoded`主体并且只查看`Content-Type`头与`type`选项匹配的请求的中间件。该解析器只接受主体的 UTF-8 编码，并支持自动膨胀`gzip`和`deflate`编码:

```
app.use( bodyParser.json() );
app.use( bodyParser.urlencoded( { extended: false } ) ); 
```

要允许跨域请求，我们需要启用 CORS。让我们使用以下代码来启用 CORS 模块:

```
app.use( cors() ); 
```

现在所有的初始配置都已经设置好了。我们现在需要做的就是设置一个端口，并监听特定端口上的传入连接:

```
const port = 3000;
app.listen(port, () => {
  console.log(`Server started on port ${port}.`);
}); 
```

确保您的最终`app.js`看起来像这样:

```
const express = require("express");
const path = require("path");
const bodyParser = require("body-parser");
const cors = require("cors");

// Create an App.
const app = express();

// Serve the static files from public.
app.use( express.static( path.join(__dirname, "public") ) );

// Include the body-parser middleware.
app.use( bodyParser.json() );
app.use( bodyParser.urlencoded( { extended: false } ) );

// Enable CORS.
app.use( cors() );

// Set the port.
const port = 3000;
// Listen to incoming connections.
app.listen(port, () => {
  console.log(`Server started on port ${port}.`);
}); 
```

运行命令启动服务器:

```
$ npm run dev 
```

在新标签页打开您的`http://localhost:3000/`,看看神奇之处。你应该会看到一个新的页面，上面写着“你好，世界”。

![Preview of Hello World in Browser](img/9013ea73eda7c372100d8863b853f9ee.png)

## 构建应用程序的后端

首先，让我们创建一个名为`routes`的目录，并在其中创建一个文件，比如说`vote.js`。我们需要将这个文件与我们的`app.js`文件连接起来，所以让我们回到它，并将它包含在我们的`express()`初始化中:

```
const app = express();

// Vote route.
const vote = require("./routes/vote") 
```

由于`routes`目录与`app.js`在同一个目录中，我们将从`./`开始。为了能够与路由器一起使用，让我们到底部，在端口定义之前添加如下所示的路由器中间件代码:

```
app.use("/vote", vote); 
```

任何通过`/vote` URL 的东西都将被由`vote`变量提供的`vote.js`文件处理。

### 处理 GET 和 POST 请求

使用 Express 的路由器实例，我们可以通过我们的`/vote`路径处理`GET`和`POST`方法。现在让我们创建一个默认的`GET`路线，并发送一个默认的文本，比如说`"You are in /vote"`。

```
const express = require("express");
const router = express.Router();

// Default get route.
router.get("/", (req, res) => {
  res.send("You are in /vote");
}); 
```

上面的代码将所有请求路由到路径`/vote`到我们新成立的`routes/vote.js`。

### 处理发布请求

我们还需要一个`POST`处理程序，在这里我们可以触发 Pusher API。对`/`的`POST`请求将会是`Router.post()`，因为我们的中间件，所以所有的请求都将会发送到`/vote`。我们将在这里给出相同类型的箭头函数，让我们给出一个类似`"You have POSTed to /vote."`的消息:

```
// Default POST route.
router.post("/", (req, res) => {
  res.send("You have POSTed to /vote.");
}); 
```

`res.send()`函数将来会被 Pusher API 调用所取代。

### 导出路由器

最后，我们必须将路由器导出为一个模块。像这样在结尾使用`module.exports`。这应该是文件的结尾，尽管你可以把它放在任何地方。请记住，JavaScript 是面向事件的，而不是过程化的:

```
// Export the router.
module.exports = router; 
```

此时，当您看到完整的`vote.js`文件时，它应该是这样的:

```
const express = require("express");
const router = express.Router();

// Default GET route.
router.get("/", (req, res) => {
  res.send("You are in /vote.");
});

// Default POST route.
router.post("/", (req, res) => {
  res.send("You have POSTed to /vote.");
});

// Export the router.
module.exports = router; 
```

请确保您保存了所有内容，现在尝试在我们的 web 浏览器中运行这两个 URL。

*   [本地主机主页](http://localhost:3000/)
*   [本地主机投票页面](http://localhost:3000/vote/)

您应该可以在 web 浏览器中看到输出。

### 与 Pusher API 集成

让我们首先修改我们为`POST`处理程序编写的代码——我们在`vote.js`文件中编写的代码。这是我们真正想要触发的按钮。让我们快速进入我们的 Pusher 仪表盘，选择您的 Pusher 应用程序(在我的例子中是`praveen-science-app`)，然后点击*入门*选项卡。您将看到 kickstart 的代码。

在我们的`vote.js`中，我们需要定义(或要求)推库。然后我们需要创建一个`Pusher`类的新实例(一个对象)，最后在`POST`中触发 pusher 服务。我正在更改`vote.js`文件，如下所示:

*注意:确保将您的`appId`、`key`、`secret`更改为仪表板中给出的那个。*

```
const express = require("express");
const router = express.Router();

// ///// Step 1: Include Pusher ///// //
const Pusher = require('pusher');

// ///// Step 2: Instantiate an Object ///// //
const pusher = new Pusher({
  appId: 'appId',
  key: 'key',
  secret: 'secret',
  cluster: 'eu',
  encrypted: true
});

// Default GET route.
router.get("/", (req, res) => {
  res.send("You are in /vote.");
});

// Default POST route.
router.post("/", (req, res) => {
  // ///// Step 3: Trigger the Pusher service ///// //
  pusher.trigger('my-channel', 'my-event', {
    "message": "hello world"
  });
});

// Export the router.
module.exports = router; 
```

当用户提交表单时，我们的应用程序向这个路由发出一个`POST`请求，这个请求将到达 Pusher API 并使用`pusher.trigger()`函数调用触发 Pusher 服务。还有，我们不想用`my-channel`和`my-event`的默认值，就改成`hp-voting`和`hp-house`吧。我们也不需要发送`message`，但是相反，我们想给出`points`和`house`信息:

```
router.post("/", (req, res) => {
  pusher.trigger('hp-voting', 'hp-house', {
    "points": 1,
    "house": req.body.house
  });

}); 
```

现在，我们将把值`1`赋给`points`(稍后我会解释为什么)，并且我们将`req.body.house`用于`house`，因为该值将来自*表单数据*，并且是使用`req.body`提供的，就像我们使用`body-parser`一样。

最后，我们将使用`res.json()`函数向浏览器返回一个 JSON 作为响应，并传递一个带有布尔值`success`和`message`的对象，以感谢用户的投票，并表明它已被成功接收:

```
router.post("/", (req, res) => {
  pusher.trigger('hp-voting', 'hp-house', {
    "points": 1,
    "house": req.body.house
  });

  return res.json({
    "success": true,
    "message": "Thanks for voting."
  });
}); 
```

## 构建应用程序的前端

我用过 jQuery 和 Bootstrap 做前端。这是我们允许用户投票的部分。

![Harry Potter Form](img/0e3190a39c53642a5ad7eff14c2023b4.png)

我还将添加一个图表容器，它将在收到投票时实时绘制图表。

![Harry Potter App Landing page after adding Logo](img/653a2a173061bb1eb227a6d40fb1ddce.png)

## 整合一切

我们已经完成了后端。现在我们将看到如何在点击投票按钮时向推送服务发送请求，这要感谢前端 JavaScript。我们将在用户点击按钮时触发表单的`submit`事件，它需要向我们的后端发出一个`POST`请求到路由`/vote`。

### 事件监听器、用户数据和 Ajax

让我们为表单提交添加一个事件侦听器、用于捕获用户数据的代码和 Ajax 调用:

```
// Execute only after the whole document is fetched and assets are loaded.
$(document).ready(function () {
  // Form submission event listener (event handler)
  $("#voteForm").submit(function (e) {
      e.preventDefault();
      // Get the checked input element's value.
      var house = $(".form-check-input:checked").val();
      // Construct the data to be sent as a payload to the AJAX call.
      var data = {
          "house": house
      };
      $.post("/vote", data, function (res) {
          // Log the output in the console.
          console.log(res);
        });
    });
}); 
```

## 使用推动器和图表

当提交表单时，Ajax 调用触发`/vote`端点，后端节点 app 也将使用下面`routes/vote.js`中的代码触发 Pusher 服务:

```
pusher.trigger('hp-voting', 'hp-house', {
  "points": 1,
  "house": req.body.house
}); 
```

当上面的代码被点击(或运行)时，Pusher service 将触发一个带有`hp-voting`和`hp-house`的事件。我们还没有捕捉或订阅该事件。因此，我们将实现 CanvasJS 来构建我们的图表，我们将订阅上述事件，并将通过触发器添加数据点，该触发器由表单的`submit`事件侦听器指定。

### 添加画布

一旦您正确地添加了所有的位，您的客户端`script.js`应该类似于:

```
// Execute only after the whole document is fetched and assets are loaded.
$(document).ready(function () {
  // Form submission event listener (event handler)
  $("#voteForm").submit(function (e) {
    // Prevent the default event.
    e.preventDefault();
    // Get the checked input element's value.
    var house = $(".form-check-input:checked").val();
    // Construct the data to be sent as a payload to the Ajax call.
    var data = {
      "house": house
    };
    // Fire the POST request Ajax call to our /vote end point.
    $.post("/vote", data, function (res) {
      // Log the output in the console.
      console.log(res);
    });
  });
  // Create the base data points.
  var dataPoints = [
    {
      label: "Gryffindor",
      y: 0
    }, {
      label: "Hufflepuff",
      y: 0
    }, {
      label: "Ravenclaw",
      y: 0
    }, {
      label: "Slytherin",
      y: 0
    }
  ];
  // Initialize Chart using jQuery selector.
  // Get the chart container element.
  var chartContainer = $("#chartContainer");
  // Check if the element exists in the DOM.
  if (chartContainer.length === 1) {
    // Construct the options for the chart.
    var options = {
      "animationEnabled": true,
      "theme": "light1",
      "title": {
        "text": "Harry Potter House Results"
      },
      "data": [
        {
          "type": "column",
          "dataPoints": dataPoints
        }
      ]
    };
    // Initialize the chart.
    $("#chartContainer").CanvasJSChart(options);
  }
}); 
```

现在保存文件，当您重新加载页面时，您应该能够看到一个占位符图表。这绝对是一个真实的图表，但没有任何价值。您应该能够看到类似这样的内容:

![Harry Potter with Chart JS](img/0f6e995d18a0dea2935ecdb4a585ece3.png)

现在我们已经实现了右侧的 CanvasJS 图表。

#### 客户端推送器的初始化

在推送器记录之后，我们必须初始化`Pusher`对象。因为我们已经有了一个客户端`config.js`，所以我们将在这一部分利用这段代码:

```
// Initialise a Pusher Object.
var pusher = new Pusher(PusherConfig.key, {
  cluster: PusherConfig.cluster,
  forceTLS: PusherConfigforceTLS.
}); 
```

在 Pusher 对象初始化之后，我们需要订阅我们的通道，在那里我们的消息由服务器端发布。我们将从 Pusher Dashboard 复制代码，但稍作修改以订阅我们的`hp-voting`频道和`hp-house`事件。关于我们的后端代码，`my-channel`和`my-event`的默认值需要像这样更新:

```
// Subscribe to the channel.
var channel = pusher.subscribe('hp-voting');
// Bind to a particular event and listen to the event data.
channel.bind('hp-house', function(data) {
  alert(JSON.stringify(data));
}); 
```

我们希望基本上将数据添加到图表中，而不是使用`data`消息。我们可以通过使用我们的`dataPoints`并根据服务器的响应操作数组来实现。对于已经存在的`dataPoints`变量(请记住，我们使用了`var`而不是`const`，因为我们应该能够在稍后的阶段更改它)，我们将使用一个更高阶的 [Array.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 函数，如下所示:

```
// Bind to a particular event and listen to the event data.
channel.bind('hp-house', function(data) {
  // Use a higher order Array map.
  dataPoints = dataPoints.map(function (d) {
    // Check if the current label is the updated value.
    if (d.label == data.house) {
      // Increment the house's value by the number of new points.
      d.y += data.points;
    }
    // Return the original value as this is a map function.
    return d;
  });
}); 
```

我们正在遍历所有的`dataPoints`标签，当一个特定的标签与当前标签匹配时，我们将用更新的点数增加当前标签的值。由于我们使用的是 JavaScript 函数`Array.map()`，我们必须将初始值`d`返回给函数调用。一旦我们更新了`dataPoints`，我们必须重新呈现图表。

在`map()`函数之后，我们将执行以下操作:

```
channel.bind('hp-house', function(data) {
  // Use a higher order Array map.
  dataPoints = dataPoints.map(function (d) {
    // Check if the current label is the updated value.
    if (d.label == data.house) {
      // Increment the house's value by the number of new points.
      d.y += data.points;
    }
    // Return the original value as this is a map function.
    return d;
  });

  // Re-render the chart.
  $("#chartContainer").CanvasJSChart(options);
}); 
```

一旦你写了上面所有的代码，在你的浏览器中保存并运行应用程序，启动你的 web 开发工具并检查控制台。您应该看到 Pusher 服务正在与您的应用程序通信。我能够在我的控制台中看到以下内容(我已经隐藏了我的`appId`和`secret`，所以除了那些敏感的信息，其他的都显示出来了):

![Pusher in Console](img/83de16e65bf7d45b9ee071f6ec0b0120.png)

## 结论

在这个时间点，当你尝试打开同一个应用的两个窗口，在一个屏幕上投票，你可以看到所有的屏幕同时更新。这基本上就是您如何使用 Pusher 服务来创建实时应用程序。

我们的哈利波特投票网络应用程序现在显示选项(所有四个房子)和右侧的图表，当连接的用户投票时，图表会自动更新。下一个明显的步骤是使用一个数据库，比如 MongoDB，来存储所有的信息，以确保即使我们重新加载应用程序的页面，这些信息仍然存在。

## 分享这篇文章