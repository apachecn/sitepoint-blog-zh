# 记录客户端应用程序中的错误

> 原文：<https://www.sitepoint.com/logging-errors-client-side-apps/>

这篇文章由[Panayiotis pvgr Velisarakos](https://github.com/pvgr)、[詹姆斯·赖特](https://github.com/jamesseanwright)和 [Stephan Max](https://www.sitepoint.com/author/smax/) 进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

![Detective makes notes while standing over a dead body, surrounded by potential murder weapons](img/67fdccf3c03917b778858cccab28d140.png)

### 目录 

*   [控制台](#theconsole)
*   [增强控制台](#enhancingtheconsole)
*   [记录日志](#logdown)
*   [控制台.消息](#consolemessage)
*   [控制台的局限性](#limitationsoftheconsole)
*   [其他需要考虑的事情](#otherthingstoconsider)
*   [捕捉全局错误](#capturingglobalerrors)
*   [堆栈痕迹](#stacktraces)
*   [TraceKit](#tracekit)
*   [stacktrace.js](#stacktracejs)
*   [将客户端错误记录到服务器](#loggingclientsideerrorstotheserver)
*   [开发自己的服务器端日志记录器](#rollingyourownserversidelogger)
*   [log4javascript](#log4javascript)
*   [其他库](#otherlibraries)
*   [推出您自己的批处理兼容记录器](#rollyourownbatchcompatiblelogger)
*   [基于自托管服务器的选项](#selfhostedserverbasedoptions)
*   [错误位](#errbit)
*   [基于 SaaS 服务器的选项](#saasserverbasedoptions)
*   [日志](#loggly)
*   [{track.js}](#trackjs)
*   [总之](#insummary)

日志记录是任何软件应用程序的重要组成部分，无论是在活动开发期间还是在生产模式下运行。

当您在服务器上工作时，无论您选择哪种服务器端语言，都有数百个库可供您使用，有各种各样的存储机制和各种各样的工具可以用来处理生成的日志。

然而，当涉及到客户端应用程序时，日志记录是经常被忽略的东西，并且可供您选择的选项相当有限。

在本文中，我将介绍一些在客户端应用程序中实现日志记录的方法；尤其是在大量使用 JavaScript 的单页面应用程序(SPA)中。

## 控制台

也许记录错误和消息的最常见和最明显的方式是控制台。虽然它可能看起来是一个原始的解决方案，但毫无疑问，它是开发过程中调试的一个无价工具，所以它可能是一个好的起点。

`console`的实现并不总是一致的——尤其是在 IE 中，这也许并不奇怪——但是一般来说有四种关键方法可供你使用:

```
console.log()
console.info()
console.warn()
console.error() 
```

这四种方法的输出略有不同，大多数 web 控制台实现(即开发工具)允许您根据所用的方法过滤消息；即日志记录级别。

为了减少浏览器之间的差异，你可以使用一个包装函数——比如 Paul Irish 的这个函数。WHATWG 正试图[标准化控制台 API](https://console.spec.whatwg.org/) ，但该规范仍处于早期阶段，在一段时间内不太可能实现。

> **提示:**如果你发现你的代码中充斥着`console.log()`语句，你可能会发现诸如 [grunt-remove-logging](https://github.com/ehynds/grunt-remove-logging) 或 [grunt-strip](https://github.com/jsoverson/grunt-strip) 之类的工具，或者 [gulp-strip-debug](https://www.npmjs.com/package/gulp-strip-debug) 之类的工具在你将一个应用程序投入生产时会很有用。

### 增强控制台

有几个库可以用来给控制台“充电”。

#### 测井记录

Logdown 是一个很小的库，为控制台提供了一些增强功能。你可以在这里找到一个[演示](http://caiogondim.github.io/logdown/)。

Logdown 允许您在实例化时指定前缀；这样做的一个可能用途是按模块分离日志消息，例如:

```
var uiLogger = new Logdown({prefix: 'MyApp:UI'});
var networkServiceLogger = new Logdown({prefix: 'MyApp:Network'}); 
```

然后，您可以通过前缀启用或禁用记录器，例如:

```
Logdown.disable('MyApp:UI');
Logdown.enable('MyApp:Network');
Logdown.disable('MyApp:*'); // wildcards are supported, too 
```

禁用记录器可以有效地使其静音。

一旦您安装了一个或多个记录器，您可以使用`log()`、`warn()`、`info()`和`error()`方法记录消息:

```
var logger = new Logdown();
logger.log('Page changed');
logger.warn('XYZ has been deprecated in favour of 123');
logger.info('Informational message here');
logger.error('Server API not available!'); 
```

Logdown 还提供降价支持:

```
var logger = new Logdown({markdown: true}); // Technically "markdown: true" isn't required; it's enabled by default
logger.warn('_XYZ_ has been *deprecated* in favour of _123_'); 
```

#### 控制台.消息

[console.message](https://github.com/astoilkov/console.message) 是另一个美化控制台输出的库。

这是文档中的一个快速动画，展示了它的一些特性:

![console.message in action](img/9964679b20859ec9932faf15eb9970b9.png)

本质上，这个库提供了一个可链接的接口，它的方法允许你格式化文本，将消息组合在一起并使它们可折叠，将交互式 DOM 元素或对象发送到日志——甚至包括图像。

### 控制台的局限性

当你构建一个应用程序时，控制台是很棒的，你可以让它在你面前打开，但是除非你碰巧越过用户的肩膀看，*和*他们碰巧在他们的浏览器中打开了 web 控制台，否则你不会看到结果。

相反，我们可以做的是将任何错误——甚至是开发期间的调试消息——发送到某处的服务器，以便我们可以远程访问它们。

## 其他需要考虑的事情

现在，我们已经了解了一些可供您使用的解决方案，接下来让我们来看看其他一些注意事项。

### 捕获全局错误

至少，捕捉和记录任何未处理的异常是值得的。您可以使用`window.onerror`来完成此操作。这里有一个非常简单的例子:

```
window.onerror = function(message, file, line) {
  console.log('An error occured at line ' + line + ' of ' + file + ': ' + message);
}; 
```

### 堆栈跟踪

[堆栈跟踪](https://en.wikipedia.org/wiki/Stack_trace)在错误发生时提供了额外的细节，您可能希望在开发中使用。有几个库可以帮助构建它们。

#### 跟踪工具包

TraceKit 允许您将堆栈跟踪注入异常，并通过订阅它们来对它们做一些事情(例如，将它们发送到您的服务器端日志记录组件)。

代码可能是这样的:

```
TraceKit.report.subscribe(function yourLogger(errorReport) {
  //send via ajax to server, or use console.error in development
  //to get you started see: https://gist.github.com/4491219
}); 
```

然后，在您的应用程序中:

```
try {
  /*
   * your application code here
   *
   */
  throw new Error('oops');
} catch (e) {
  TraceKit.report(e); //error with stack trace gets normalized and sent to subscriber
} 
```

#### stacktrace.js

引用文档中的话来说，stacktrace.js 是“[一个]与框架无关的微库，用于在所有 web 浏览器中获取堆栈跟踪”。

它提供了一个名为`printStackTrace()`的方法，您可以在错误处理程序中使用该方法将堆栈跟踪添加到日志记录函数中。例如，我们可以如下增强我们的服务器端记录器:

```
function log(data, level) {
  $.post(
    'https://your-app.com/api/logger',
    {
      context     :   navigator.userAgent,
      level       :   level || 'error',
      data         :   data,
      stack_trace :    printStackTrace()
    }
  );
} 
```

## 将客户端错误记录到服务器

向服务器发送日志条目有很多好处:

1.  您可以从您的应用程序中捕获日志条目，而无需在计算机前(非常适合生产)
2.  您可以在同一个地方管理您的服务器端和客户端日志，可能使用相同的工具
3.  您可以设置警报(例如，如果发生严重错误，则发送一条延迟通知或短信)
4.  在控制台不可用或难以查看的地方(例如，当使用手机的网络视图时)，更容易看到发生了什么

让我们来看几个方法。

### 滚动您自己的服务器端记录器

在某些情况下，最简单的解决方案可能是使用您自己的服务器端日志记录机制。

下面是使用 jQuery 的客户端部分的一个极其简单的例子:

```
function log(data, level) {
  $.post(
    'https://your-app.com/api/logger',
    {
      context   :   navigator.userAgent,
      level     :   level || 'error',
      data       :   data
    }
  );
} 
```

一些用法示例:

```
try {
  // some function
} catch (e) {
  log({
    error : e.message
  });
} 
```

```
log('Informational message here', 'info'); 
```

考虑到这一点，这里有一个非常基本的服务器端组件，它是使用 Node.js 和 Express 以及优秀的 [Winston](https://www.npmjs.com/package/winston) 日志库构建的:

```
/**
 * Load the dependencies
 */
var express = require( 'express' );
var bodyParser = require('body-parser');
var winston = require( 'winston' );

/**
 * Create the Express app
 */
var app = express();

app.use(bodyParser.urlencoded({ extended: true }));

/**
 * Instantiate the logger
 */
var logger = new ( winston.Logger )({
  transports: [
    new ( winston.transports.Console )(
      { 
        level: 'error'
      }
    ),
    new ( winston.transports.DailyRotateFile )(
      { 
        filename: 'logs/client.log',
        datePattern: '.yyyy-MM-dd'
      }
    )
  ]
});

app.post ('/api/logger', function( req, res, next ) {

  logger.log(
    req.body.level || 'error',
    'Client: ' + req.body.data
  );

  return res.send( 'OK' );

});

var server = app.listen( 8080, function() {
  console.log( 'Listening on port %d', server.address().port );
}); 
```

实际上，这种过于简化的记录器有一些基本限制:

1.  大多数日志记录机制允许您配置最低日志记录级别，以便您可以过滤掉某些条目
2.  它会立即发送日志条目，这可能会导致服务器端组件过载

处理第二个问题的更好的方法是缓冲日志条目并成批发送它们。一种常见的方法是使用`localStorage`来存储日志条目，然后在特定的时间间隔发送它们——基于时间，当挂起条目的数量达到某个阈值时，或者当用户关闭窗口或利用`window.onbeforeunload`事件导航离开应用程序时。

为了解决这些问题，让我们看看一个现成的从 JS 应用程序进行日志记录的解决方案。

### log4javascript

log4javascript 基于无处不在的 [log4j](http://logging.apache.org/log4j/2.x/) ，这是一个 Java 日志框架，也已经被[移植到 PHP](http://logging.apache.org/log4php/) ，所以如果你来自服务器端背景，你可能已经对它有些熟悉了。

log4javascript 使用了 [appenders](http://log4javascript.org/docs/manual.html#appenders) 的概念，它决定了当您调用它的一个日志记录方法时会发生什么。当您拥有大多数现代浏览器提供的开发工具时，默认的`PopUpAppender`可以说是多余的。

可能更有用的是 [AjaxAppender](http://log4javascript.org/docs/manual.html#ajaxappender) ，可以用它将日志条目发送回服务器。您可以配置`AjaxAppender`使用`setTimed()`按时间间隔批量发送条目，使用`setBatchSize()`发送特定数量的条目，或者使用`setSendAllOnUnload()`卸载窗口。

log4javascript 可以从 Sourceforge 下载[，或者类似的 Log4js 在 Github](http://sourceforge.net/projects/log4javascript/) 上有[。您可以参考](http://stritti.github.io/log4js/)[快速入门](http://log4javascript.org/docs/quickstart.html)快速入门。

这里有一个例子:

```
var log = log4javascript.getLogger();
var ajaxAppender = new log4javascript.AjaxAppender('http://example.com/api/logger');
ajaxAppender.setThreshold(log4javascript.Level.ERROR);
ajaxAppender.setBatchSize(10); // send in batches of 10
ajaxAppender.setSendAllOnUnload(); // send all remaining messages on window.beforeunload()
log.addAppender(ajaxAppender); 
```

或者，要以特定的时间间隔发送消息:

```
ajaxAppender.setTimed(true);
ajaxAppender.setTimerInterval(10000); // send every 10 seconds (unit is milliseconds) 
```

### 其他图书馆

如果您的项目使用 jQuery，您可能想要查看允许您通过 Ajax 登录的 jQuery logger；但是，它不支持批处理。不过，作为后端，它确实与[空气制动](https://airbrake.io/)很好地集成在一起。

loglevel 是一个轻量级和可扩展的基于 JS 的日志框架，它通过独立的[服务器发送](https://github.com/artemyarulin/loglevel-serverSend)插件支持 Ajax。

## 滚动您自己的批处理兼容记录器

这是一个简单的日志记录器概念验证，它批量发送消息。它是使用带有 ES6 特性的普通 JavaScript 编写的。

```
"use strict";
class Logger {

  // Log levels as per https://tools.ietf.org/html/rfc5424
  static get ERROR()  { return 3; }
  static get WARN()   { return 4; }
  static get INFO()   { return 6; }
  static get DEBUG()  { return 7; }

  constructor(options) {

    if ( !options || typeof options !== 'object' ) {
      throw new Error('options are required, and must be an object');
    }

    if (!options.url) {
      throw new Error('options must include a url property');  
    }

    this.url         =   options.url;
    this.headers     =   options.headers || [ { 'Content-Type' : 'application/json' } ];
    this.level       =   options.level || Logger.ERROR;
    this.batch_size =   options.batch_size || 10;
    this.messages   =   [];

  }

  send(messages) {    
    var xhr = new XMLHttpRequest();
    xhr.open('POST', this.url, true);

    this.headers.forEach(function(header){      
      xhr.setRequestHeader(
        Object.keys(header)[0],
        header[Object.keys(header)[0]]
      );
    });

    var data = JSON.stringify({
      context   :   navigator.userAgent,
      messages  :   messages
    });    
    xhr.send(data);
  }

  log(level, message) {
    if (level <= this.level) {
      this.messages.push({
        level : level,
        message : message
      });      
      if (this.messages.length >= this.batch_size) {
        this.send(this.messages.splice(0, this.batch_size));        
      }
    }
  }

  error(message) {
    this.log(Logger.ERROR, message);
  }

  warn(message) {
    this.log(Logger.WARN, message);
  }

  info(message) {
    this.log(Logger.INFO, message);
  }

  debug(message) {
    this.log(Logger.DEBUG, message);
  }

} 
```

用法很简单:

```
var logger = new Logger({
  url : 'http://example.com/api/batch-logger',
  batch_size : 5,
  level : Logger.INFO
});

logger.debug('This is a debug message'); // No effect
logger.info('This is an info message');
logger.warn('This is a warning');
logger.error('This is an error message');
logger.log(Logger.WARN, 'This is a warning'); 
```

## 基于自托管服务器的选项

### 错误位

Errbit 是一个开源的、自托管的错误捕获解决方案。它是用 Ruby 实现的，使用 MongoDB 进行存储。

如果你想给 Errbit 一个快速旋转，有一个[厨师食谱](https://github.com/millisami/chef-errbit)或 [Dockerfile](https://github.com/yosssi/docker-errbit) 你可以使用。还有一个[在线演示](http://errbit-demo.herokuapp.com/users/sign_in)你可以试试。

> 要登录在线演示，请使用电子邮件`demo@errbit-demo.herokuapp.com`和密码`password`。

## 基于 SaaS 服务器的选项

有许多用于日志记录的 SaaS 解决方案。其中包括 [Loggly](https://www.loggly.com/) 、 [track.js](http://trackjs.com/) 、[error exception](https://errorception.com/)、 [Airbrake](https://airbrake.io/) 和 [New Relic](http://newrelic.com/) 。

让我们简单看看几个这样的解决方案。

### 对数地

这是众多 SaaS 解决方案中的一个。我打算以它为例，因为它很容易上手，而且免费。使用免费计划，您每天最多可以登录 200MB，数据可以存储 7 天。

要在客户端应用程序中使用 Loggly，您需要包含以下代码片段:

```
<script type="text/javascript" src="http://cloudfront.loggly.com/js/loggly.tracker.js" async></script>
<script>
  var _LTracker = _LTracker || [];
  _LTracker.push({'logglyKey': 'YOUR-LOGGING-KEY',
  'sendConsoleErrors' : true });
</script> 
```

> **注意:**你需要将`YOUR-LOGGING-KEY`替换为你的应用程序特有的值，当你注册并登录后，你将通过进入*源设置*获得该值。

如果您检查这段代码，您会看到`_LTracker`对象最初被实例化为一个数组。这是许多分析库中使用的“填充”技术，这意味着您可以在库加载之前调用它的`push()`。当库可用时，您推送到该阵列的任何错误或消息都将排队等候。

用法很简单:

```
_LTracker.push(data); 
```

您可以用它来发送一段文本:

```
_LTracker.push( 'An error occured: ' + e.message ); 
```

或者，也许更有用的是，您可以使用 JSON —例如:

```
try {
  // some operation
} catch (e) {
  _LTracker.push({
    level   : 'error',
    message : e.message,
    trace   : e.trace,
    context : navigator.userAgent
  });
} 
```

虽然这是一个相当基本的解决方案，但您可以简单地使用以下代码来捕获错误:

```
window.onerror = function(message, file, line) {        
  _LTracker.push({
    context: navigator.userAgent,
    error: message,
    file: file,
    line: line
  });
}; 
```

这种方法有一些限制。如果您有细微的不同构建，或者当您缩减 JS 代码时，行号实际上是没有用的。

您还会注意到，在上面的 Loggly 代码片段中，`sendConsoleErrors`被设置为`TRUE`，这将自动为您记录某些错误，而不必手动发送它们。例如，如果 RequireJS 超时，以下内容将被发送到 Loggly:

```
{
  "category": "BrowserJsException",
  "exception": {
    "url": "http://example.com/js/require.js",
    "message": "Uncaught Error: Load timeout for modules: main\nhttp://requirejs.org/docs/errors.html#timeout",
    "lineno": 141,
    "colno": 15
  },
  "sessionId": "xyz-123-xyz-123"
} 
```

### {track.js}

[{track.js}](http://trackjs.com/) 是另一个用于日志记录的 SaaS 解决方案。

他们提供免费计划；它被限制为每分钟 10 个错误，每月 10，000 次点击，并且您的数据只能存储 24 小时。最基本的付费计划是每月 29.99 美元——你可以在他们的定价页面上找到更多细节。

> **注意:**每当库初始化时，记录一个“命中”。

设置起来很简单:

```
<!-- BEGIN TRACKJS -->
<script type="text/javascript">window._trackJs = { token: 'YOUR-TOKEN-HERE' };</script>
<script type="text/javascript" src="//d2zah9y47r7bi2.cloudfront.net/releases/current/tracker.js" crossorigin="anonymous"></script>
<!-- END TRACKJS --> 
```

一旦您已经获取了适当的文件并初始化了库，您就可以使用诸如`track()`之类的方法:

```
/**
  * Directly invokes an error to be sent to TrackJS.
  *
  * @method track
  * @param {Error|String} error The error to be tracked. If error does not have a stacktrace, will attempt to generate one.
  */
trackJs.track("Logical error: state should not be null");

try {
  // do something
} catch (e) {
  trackJs.track(e);
} 
```

或者使用控制台，控制台会将消息发送到 web 服务:

```
trackJs.console.debug("a message"); // debug severity
trackJs.console.log("another message"); // log severity 
```

您可以使用{track.js}做更多事情——查看文档了解更多信息。

## 概括起来

客户端日志记录经常被忽略，但是可以说它和记录服务器端错误一样重要。然而，毫无疑问，它更难设置。然而，有很多选择，其中一些我们已经在本文中讨论过了。

您如何处理客户端应用程序中的日志记录？你有自己的方法吗？你使用这里没有提到的东西吗？请在评论中告诉我。

## 分享这篇文章