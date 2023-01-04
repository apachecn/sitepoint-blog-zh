# 使用 Node.js 提供静态文件

> 原文：<https://www.sitepoint.com/serving-static-files-with-node-js/>

在本文中，我们将研究如何使用 Node 设置一个静态文件服务器。有很多模块可以帮助我们做到这一点，但我们将重点关注其中的三个:[节点静态](https://github.com/cloudhead/node-static)、[报童](https://github.com/felixge/node-paperboy)和[http-服务器](https://github.com/nodeapps/http-server)。

所有这些模块都可以通过 [npm(节点包管理器)](http://npmjs.org/)获得。npm 从版本 0.6.3 开始就和节点[打包在一起了，但是如果你还没有安装它，用这个一行程序就足够简单了:](https://raw.github.com/joyent/node/v0.6.3/ChangeLog)

```
curl http://npmjs.org/install.sh | sh

```

让我们依次看看每个静态文件服务器，从 node-static 开始。

## 节点静态

可以使用以下命令通过 npm 安装 node-static:

```
npm install node-static

```

要使用它:通过 require 函数在脚本中包含该模块，创建一个服务器，向它传递您的首选选项，并告诉它提供文件:

```
var static = require('node-static'),
  http = require('http'),
  util = require('util');

var webroot = './public',
  port = 8080;

var file = new(static.Server)(webroot, { 
  cache: 600, 
  headers: { 'X-Powered-By': 'node-static' } 
});

http.createServer(function(req, res) {
  req.addListener('end', function() {
    file.serve(req, res, function(err, result) {
      if (err) {
        console.error('Error serving %s - %s', req.url, err.message);
        if (err.status === 404 || err.status === 500) {
          file.serveFile(util.format('/%d.html', err.status), err.status, {}, req, res);
        } else {
          res.writeHead(err.status, err.headers);
          res.end();
        }
      } else {
        console.log('%s - %s', req.url, res.message); 
      }
    });
  });
}).listen(port);

console.log('node-static running at http://localhost:%d', port);

```

这里，当我们创建一个节点静态服务器的新实例时，我们传递它:

*   我们希望它通过“webroot”变量提供文件的目录(node-static 默认为提供当前目录中的文件，除非您另外告诉它)
*   缓存值为 600，因此提供的每个文件将被缓存 10 分钟(缓存的默认值为 3600，表示文件被缓存 1 小时)
*   一个定制的“X-Powered-By”标题(我使用 X-Powered-By 只是作为一个例子，你可能想，也可能不想[公开你的服务器正在运行的软件](https://www.google.com/search?sourceid=chrome&ie=UTF-8&q=remove+x-powered-by+header)

然后，我们使用 http 模块的 createServer 函数，并为请求的结束事件添加一个事件处理程序，其中我们使用 node-static 的实例来服务文件。

当我们调用 file.serve 时，我们传递给它一个可选的回调函数，让我们自定义如何处理任何错误:

*   我们检查错误状态，并相应地服务于 404.html 或 500.html
*   如果出现错误，并且状态代码不是 404 或 500，我们会将状态代码和标头显式发送回客户端——当您将回调函数传递给 file.serve 并且出现错误时，node-static 不会自己响应客户端。

## 送报人

paperboy 提供了一个 [fluent <abbr title="Application Programming Interface">API</abbr>](http://en.wikipedia.org/wiki/Fluent_interface) 来设置你的服务器，让你配置端口和 IP 地址，设置 HTTP 头，处理请求前和响应后的事件。你也可以告诉它在出错时该做什么。让我们看看如何使用 paperboy 创建一个静态文件服务器:

```
var paperboy = require('paperboy'),
    http = require('http'),
    path = require('path');

var webroot = path.join(__dirname, 'public'),
    port = 8080;

http.createServer(function(req, res) {
  var ip = req.connection.remoteAddress;
  paperboy
    .deliver(webroot, req, res)
    .addHeader('X-Powered-By', 'Atari')
    .before(function() {
      console.log('Request received for ' + req.url);
    })
    .after(function(statusCode) {
      console.log(statusCode + ' - ' + req.url + ' ' + ip);
    })
    .error(function(statusCode, msg) {
      console.log([statusCode, msg, req.url, ip].join(' '));
      res.writeHead(statusCode, { 'Content-Type': 'text/plain' });
      res.end('Error [' + statusCode + ']');
    })
    .otherwise(function(err) {
      console.log([404, err, req.url, ip].join(' '));
      res.writeHead(404, { 'Content-Type': 'text/plain' });
      res.end('Error 404: File not found');
    });
}).listen(port);

console.log('paperboy on his round at http://localhost:' + port);

```

在导入我们需要的模块后，我们使用几个变量来存储我们希望从其获取文档的 webroot，以及我们希望服务器监听的端口。请注意，当前版本的 paperboy (0.0.3)似乎不支持用相对文件路径指定的 webroot。/public。因此，我们使用 path.join(__dirname，' public ')来给出我们要从中提供文件的公共文件夹的绝对路径( [__dirname](http://nodejs.org/docs/latest/api/globals.html#__dirname) 是 Node 的一个全局变量，它给出了当前正在执行的脚本所在的目录的名称)。

我们将 webroot 传递给 deliver 函数，以便报童知道从哪个目录提供文件。addHeader 然后添加一些 HTTP 头。它有两个参数:头的名称和头的值。然后，我们使用 before、after、error 和 otherwise 函数通过一些回调函数添加事件处理程序:

*   before 将在请求的文件传递之前触发它的回调函数。要停止提供文件，您可以从回调函数返回 false。
*   一旦响应被发送回客户端并且文件已被传送，after 将触发它的回调函数。向回调函数传递一个参数，该参数表示发送回客户端的 HTTP 响应的状态代码
*   如果在处理请求的过程中出错，将触发 error。回调传递两个参数:响应的 HTTP 状态代码和错误消息。请注意，您需要在错误回调函数中自己关闭与服务器的连接；上面的示例通过行 response.end(…)实现了这一点。
*   否则，如果在 webroot 中找不到匹配的文件，或者如果 before 回调函数返回 false，则引发。回调函数被传递一个包含错误细节的参数。报童的默认回调函数显示了一个简单的 HTTP 404 文件未找到页面。

最后，我们告诉服务器监听哪个端口。注意，当收到对'/'的请求时，默认情况下，只要 index.html 存在，paperboy 就会附加 index.html 并提供该文件。

## http-服务器

最后，让我们看看 [http-server](https://github.com/nodeapps/http-server) 。安装是通过 npm 完成的，但是请确保将-g 开关传递给 npm，以使 http-server 在全局可用:

```
npm install http-server -g

```

以这种方式安装 http-server 可以通过命令行使用它:

```
$> type http-server
http-server is hashed (/usr/local/bin/http-server)

```

因为它可以从命令行获得，所以您不需要编写任何代码就可以启动并运行它。你只需要输入:

```
$> http-server
Starting up http-server, serving ./public on port: 8080
http-server successfully started: http://localhost:8080
Hit CTRL-C to stop the server

```

这将从。/public 目录(如果存在),或者当前工作目录(如果不存在)。http-server 使用的默认端口是 8080，但是您可以通过命令行开关更改该端口和其他一些选项:

*   -p 指定监听哪个端口
*   -a 告诉它要绑定到哪个 IP 地址
*   -i 配置是否显示自动索引(默认为 true)
*   -s 或–silent 关闭控制台日志记录
*   -h 或–help 显示可用命令的列表

在本文中，我们研究了 Node 中可用的三个静态文件服务器模块:node-server、paperboy 和 http-server。虽然它们都实现了相同的目标，但是您选择使用哪一个可能取决于您想要编写多少代码，以及您需要对您的节点静态文件服务器进行多少控制。

当然，您可能会决定从头开始编写自己的静态文件服务器。如果您这样做了，您可能会做得比仔细阅读我们今天看到的三台服务器的源代码更糟糕:

*   节点服务器—[https://github.com/cloudhead/node-static](https://github.com/cloudhead/node-static)
*   报童——[https://github.com/felixge/node-paperboy](https://github.com/felixge/node-paperboy)
*   http-server-[https://github.com/nodeapps/http-server](https://github.com/nodeapps/http-server)

## 分享这篇文章