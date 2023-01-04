# 使用 Node.js 创建 Battery viz:入门和服务器

> 原文：<https://www.sitepoint.com/creating-a-battery-viz-using-js-getting-started-and-server/>

如果你对这篇文章标题的第一反应是类似于*什么？*，我想让你安心。你不必相信我的话！我要做的是向你展示如何构建一个可以在多个操作系统上运行的优秀软件，与它们进行交互，并以一种令人愉快的方式呈现结果。整个任务将通过使用 JavaScript 和少量的 bash/powershell 命令来完成。

说到这里，你可能想知道我为什么要做这个实验。这可能会令人惊讶，但是“冬天的夜晚漫长而孤独，我需要一些东西来消磨时间”不是这个问题的答案。也许“我想提高我的技能，掌握 JS”这句话会更贴切一些。

虽然这个项目本身没有很高的价值，但我的拙见是它会:

*   为你提供构建 RESTful 服务和你喜欢的操作系统界面的技巧(和一些基本设计)
*   让您专注于跨操作系统兼容性
*   向您介绍有价值的 JavaScript 设计模式和有用的 Node.js 模块。

考虑到这一点，让我们开始谈论服务器。我们需要的是创建一个(RESTful)服务，为我们实时提供来自操作系统的最新读数。

## 我们为什么需要服务器？为什么是 RESTful？

这两个聪明问题的答案很简单。首先，我们需要一台服务器，因为出于安全原因，浏览器不可能允许你在操作系统上执行命令(我敢打赌，如果任何令人毛骨悚然的网站能够删除你所有的文件，你不会太高兴，不是吗？).其次，我们将有一个 RESTful 服务，因为使用 REST 接口有几个优点。这超出了我们的讨论范围，但是我会在本文末尾向感兴趣的读者推荐一些好的参考资料，让他们了解关于这个主题的更多信息。

现在，我们想要的是至少有一个端点可以通过 HTTP 协议从任何服务中调用，因此与它提供的数据的实际表示分离，并作为响应将数据发送回调用者。

要发回这些数据，我们当然需要就格式达成一致。我们可以发送回一些原始文本，将解析工作留给客户端，或者，我们可以发送结构化数据(例如使用 XML)。最后选择了 JSON。原因是我们将拥有结构化数据，但远没有 XML 那么冗余。注意，通过同意数据的格式，我们为客户端引入了某种耦合，现在必须遵守我们的格式。然而，这种选择有几个优点:

*   我们可以将格式指定为接口的一部分:客户端自然必须遵守他们使用的任何服务的 API(例如，方法的名称或公开的端点)，只要我们不改变格式，就不会有任何区别。很明显，在进入版本 1 之前，我们仍然应该考虑这种格式。事实上，我们应该(几乎)永远不要改变公共接口，以免客户端被破坏。
*   我们会通过将解析委托给客户机来明智地降低客户机的速度。
*   我们通过为所有操作系统提供一个通用的格式来实现不同操作系统的解耦。为了支持一个新的操作系统，我们只需要一个适配器来处理我们从它那里收到的数据。

此时，我们需要开始讨论如何以及在哪里获取发送给客户端的数据。这可能是游戏中最棘手的部分，但幸运的是 Node.js 有大量的模块，允许我们的服务器与我们的操作系统对话，甚至了解我们的机器上运行的是哪个操作系统。

### 创建端点

为了创建服务的核心，我们需要使用 Node.js 的 HTTP 模块来处理传入的 GET 请求:

```
var http = require('http');
var PORT = 8080;
```

因为我们构建的应用程序只能在本地主机上运行，所以我们可以为端口使用一个静态(常量)值。另一种选择是从命令行读取它，并在没有提供常量值时返回常量值。我们可以从 [`process.argv`](https://nodejs.org/docs/latest/api/process.html#process_process_argv) 中读取命令行参数。因为第一个参数总是为`"node"`，第二个参数是我们正在运行的 JavaScript 文件的名称，所以我们对第三个参数感兴趣:

```
var PORT = Number(process.argv[2]) || 8080;
```

HTTP 模块使得创建服务器和监听端口变得容易。我们只需要使用模块中声明的两个函数， [`createServer()`](https://nodejs.org/api/http.html#http_http_createserver_requestlistener) 和 [`listen()`](https://nodejs.org/api/http.html#http_server_listen_port_hostname_backlog_callback) 。前者接受带有两个参数的回调作为输入，即请求及其响应，而后者只接受我们需要监听的端口号。我们想要创建 REST 端点，所以我们需要检查请求了什么路径。此外，我们希望根据它匹配的端点来执行不同的操作。假设我们希望电池信息的路径是`/battery`。考虑到小的变化(如`/battery/`)，我们将定义一个正则表达式来匹配我们的端点:

```
var RE_BATTERY = /\/battery\/?/;
```

回到`createServer()`参数，它将是一个提供对请求(和响应)对象访问的函数，该对象又有一个包含所请求 URL 的字段。综上所述，我们应该有以下代码:

```
var server = http.createServer(function (request, response) {
  var requestUrl = request.url;

  if (RE_BATTERY.test(requestUrl)) {
    getBatteryStatus(response, onBatteryInfo, onError);
  }
}).listen(PORT);
```

`getBatteryStatus()`是我们将很快定义的一个函数。我们通过使用两个`response`的方法: [`write()`](https://nodejs.org/api/http.html#http_response_write_chunk_encoding_callback) 和 [`end()`](https://nodejs.org/api/http.html#http_response_end_data_encoding_callback) 将向调用者发送响应的责任委托给这个函数。

### 提供静态内容

除了定义我们的端点之外，我们还需要提供一些将由同一个服务器提供的静态内容。具有两个服务器的不同设计也是可能的，一个用于静态内容，一个用于动态内容。然而，如果不是有害的话，这可能是不必要的，因为如果我们认为我们将是请求静态内容的唯一客户端，就没有必要再占用一个端口。

即使在这种情况下，HTTP 模块也能提供帮助。首先，如果客户请求我们的`root`，我们会将他们重定向到我们的主页:

```
if (requestUrl === '/' || requestUrl === '') {
   response.writeHead(301, {
      Location: BASE_URL + 'public/demo.html'
   });
   response.end();
} else if (RE_BATTERY.test(requestUrl)) {
   getBatteryStatus(response, onBatteryInfo, onError);
}
```

然后，我们将在上面的条件中添加一个“else”分支。如果请求与我们的任何端点都不匹配，我们的服务器将检查该路径是否存在静态文件，并为其提供服务，或者用 404(未找到)HTTP 代码进行响应。

```
else {
   fs.exists(filePath, function (exists) {
      if (exists) {
         fs.readFile(filePath, function (error, content) {
            if (error) {
               response.writeHead(500);
               response.end();
            } else {
               response.writeHead(200);
               response.end(content, 'utf-8');
            }
         });
      } else {
         response.writeHead(404, {'Content-Type': 'text/plain'});
         response.write('404 - Resurce Not found');
         response.end();
      }
   });
}
```

### 运行操作系统命令

为了从 Node.js 运行操作系统的命令，我们需要另一个名为 [`child_process`](https://nodejs.org/api/child_process.html) 的模块，它也将为我们提供一些实用方法。

```
var child_process = require('child_process');
```

特别是，我们将使用 [exec()](https://nodejs.org/api/child_process.html#child_process_child_process_exec_command_options_callback) 方法，该方法允许在 shell 中运行命令并缓冲它们的输出。

```
child_process.exec("command", function callback(err, stdout, stderr) {
   //....
});
```

然而，在此之前，我们还有几个步骤要遵循:首先，因为我们希望我们的仪表板与多个操作系统和命令一起工作，以便从一个操作系统到另一个操作系统获得不同的电池状态，我们需要一种方法让我们的服务器根据我们当前的操作系统表现不同。不言而喻，我们需要为我们想要支持的所有操作系统识别和测试正确的命令。

#### 识别当前操作系统

Node.js 提供了一种检查底层操作系统的简单方法。我们需要检查`process.platform`，并打开它的值(注意命名中的一些特殊情况):

```
function switchConfigForCurrentOS () {
  switch(process.platform) {
    case 'linux': 
      //...
      break;
    case 'darwin': //MAC
      //...
      break;
    case 'win32':
      //...
      break;
    default:
      //...
  }
}
```

一旦我们获得了这些信息，我们就可以专注于在不同的平台上检索正确的命令。除了不同的语法之外，返回的字段将具有不同的命名/格式。因此，当我们检索命令的结果时，我们必须考虑这一点。以下各节描述了适用于不同操作系统的命令。

##### OsX

```
pmset -g batt | egrep "([0-9]+\%).*" -o
```

##### Linux 操作系统

```
upower -i /org/freedesktop/UPower/devices/battery_BAT0 | grep -E "state|time to empty|to full|percentage"
```

##### Windows 操作系统

```
wmic Path Win32_Battery
```

#### 应用模板模式——操作系统相关的设计

我们可以检查每个调用运行在哪个操作系统上，但这似乎是一种浪费。在我们的服务器生命周期中，底层操作系统是不太可能改变的。如果我们的服务器进程以某种方式通过封送/解封，这在理论上是可能的，但是这肯定是不实际的，既不容易也不明智。

为此，我们可以在服务器启动时检查当前的操作系统，并根据它选择最合适的命令和解析函数。

尽管一些细节发生了变化，但处理请求的一般工作流程在所有操作系统中都是相同的:

1.  我们调用`child_process.exec`来运行一个命令；
2.  我们检查命令是否成功完成，否则我们处理错误；
3.  假设成功了，我们处理命令的输出，提取我们需要的信息；
4.  我们创建一个响应，并将其发送回客户端。

这是在[四人帮书](http://en.wikipedia.org/wiki/Design_Patterns)中描述的 [`Template method design pattern`](http://en.wikipedia.org/wiki/Template_method_pattern) 的完美案例。

由于 JavaScript 不是真正面向类的，我们实现了该模式的一个变体，其中细节，而不是子类的细节，被委托给将被“覆盖”(通过赋值)的函数，这取决于当前的操作系统。

```
function getBatteryStatus(response, onSuccess, onError) {

    child_process.exec(CONFIG.command, function execBatteryCommand(err, stdout, stderr) {
        var battery;

        if (err) {
            console.log('child_process failed with error code: ' + err.code);
            onError(response, BATTERY_ERROR_MESSAGE);
        } else {
            try {
                battery = CONFIG.processFunction(stdout);
                onSuccess(response, JSON.stringify(battery));
            } catch (e) {
                console.log(e);
                onError(response, BATTERY_ERROR_MESSAGE);
            }
        }
    });
}
```

##### 命令

现在，我们可以将已经发现的命令插入到我们的`switchConfigForCurrentOS()`函数中。如上所述，我们需要根据当前操作系统覆盖命令运行和后处理功能。

```
function switchConfigForCurrentOS() {
    switch (process.platform) {
        case 'linux':
            return {
                command: 'upower -i /org/freedesktop/UPower/devices/battery_BAT0 | grep -E "state|time to empty|to full|percentage"',
                processFunction: processBatteryStdoutForLinux
            };
        case 'darwin':
            //MAC
            return {
                command: 'pmset -g batt | egrep "([0-9]+\%).*" -o',
                processFunction: processBatteryStdoutForMac
            };
        case 'win32':
            return {
                command: 'WMIC Path Win32_Battery',
                processFunction: processBatteryStdoutForWindows
            };
        default:
            return {
                command: '',
                processFunction: function () {}
            };
    }
}
```

##### 处理 Bash 输出

我们的策略是为每个操作系统提供不同版本的后处理方法。我们希望有一个一致的输出——我们的数据 API，正如引言中提到的——用相同的信息映射到相同的字段，不管平台是什么。为了完成这项任务，我们基本上为每个操作系统定义了输出字段和从数据中检索到的相应字段的名称之间的不同映射。

一种替代方法是向客户端发送一个额外的`"OS"`参数，但是我认为耦合引入了。此外，与任何可能的简化或性能增益相比，将服务器(它所属的地方)和客户机之间的逻辑分开将是一个更大的障碍。

```
function processLineForLinux(battery, line) {
    var key;
    var val;

    line = line.trim();
    if (line.length > 0) {
        line = line.split(':');
        if (line.length === 2) {
            line = line.map(trimParam);
            key = line[0];
            val = line[1];
            battery[key] = val;
        }
    }
    return battery;
}

function mapKeysForLinux(battery) {
    var mappedBattery = {};
    mappedBattery.percentage = battery.percentage;
    mappedBattery.state = battery.state;
    mappedBattery.timeToEmpty = battery['time to empty'];
    return mappedBattery;
}

function mapKeysForMac(battery) {
    var mappedBattery = {};
    mappedBattery.percentage = battery[0];
    mappedBattery.state = battery[1];
    mappedBattery.timeToEmpty = battery[2];
    return mappedBattery;
}

function processBatteryStdoutForLinux(stdout) {
    var battery = {},
    processLine = processLineForLinux.bind(null, battery);
    stdout.split('\n').forEach(processLine);
    return mapKeysForLinux(battery);
}

function processBatteryStdoutForMac(stdout) {
    var battery = stdout.split(';').map(trimParam);
    return mapKeysForMac(battery);
}
```

Windows 的处理函数稍微复杂一些，为了简单起见，在这里省略了它们。

### 把所有的放在一起

此时，我们只需要做一些连接，用 JSON 编码我们的数据，以及一些我们仍然需要声明的常量。[可以在 GitHub](https://github.com/sitepoint-editors/battery_viz/blob/master/server.js) 上看一下服务器的最终代码。

## 结论

在这个迷你系列的第一部分中，我们讨论了我们正在构建的服务的细节以及您将学到的内容。然后我们讨论了为什么我们需要一个服务器，以及为什么我选择创建一个 RESTful 服务。在讨论如何开发服务器时，我趁机讨论了如何识别当前的操作系统，以及如何使用 Node.js 在其上运行命令。

在本系列的第二部分，也是最后一部分，您将发现如何构建客户机部件，以一种良好的方式向用户呈现信息。

## 分享这篇文章