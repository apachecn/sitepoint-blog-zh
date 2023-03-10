# React 和 PHP 的游戏开发:它们的兼容性如何？

> 原文：<https://www.sitepoint.com/game-development-with-reactjs-and-php-how-compatible-are-they/>

***“我想做一个多人的，基于经济的游戏。有点像《T2》中的《星谷 T3》，但是没有友好的一面，而是以玩家为基础的经济***

当我决定尝试用 PHP 和 React 开发一个游戏时，我就开始考虑这个问题了。问题是，我对多人游戏的动态一无所知，也不知道如何思考和实现基于玩家的经济。

![Stardew valley](img/cdc6c2e616d3b0758e8dacb83d3956e1.png)

我甚至不确定自己对 React 的了解是否足以证明使用它是正确的。我的意思是，最初的界面——我主要关注游戏的服务器和经济方面——非常适合 React。但是当我开始做耕作/互动方面的时候呢？我喜欢围绕经济体系建立一个等距界面的想法。

我曾经看过一个由 [dead_lugosi](https://twitter.com/dead_lugosi) 做的演讲，她描述了用 PHP 构建一个中世纪的游戏。Margaret 启发了我，那次谈话是促使我写[一本关于 JS 游戏开发](https://www.amazon.com/dp/1484224922)的书的原因之一。我决心写下我的经历。或许其他人也可以从我的错误中吸取教训。

* * *

*这部分的代码可以在:[github . com/assert Chris-tutorials/site point-making-games/tree/part-1](https://github.com/assertchris-tutorials/sitepoint-making-games/tree/part-1)找到。我已经用 PHP `7.1`和谷歌 Chrome 的最新版本测试过了。*

* * *

## 设置后端

我搜索的第一件事是关于建立多人游戏经济的指导。我发现[是一个优秀的堆栈溢出线程](http://gamedev.stackexchange.com/questions/11697/guidance-for-building-a-proper-in-game-economy)，其中人们解释了各种需要考虑的事情。我读到一半时才意识到我可能从错误的地方开始。

首先，我需要一台 PHP 服务器。我将有一堆 React 客户端，所以我想要能够高并发的东西(甚至可能是 WebSockets)。它需要坚持不懈:即使玩家不在身边，事情也必须发生。”

我着手建立一个异步 PHP 服务器——处理高并发性并支持 WebSockets。我添加了[我最近用 PHP 预处理器](https://www.sitepoint.com/how-to-make-modern-php-more-modern-with-preprocessing)做的工作，使事情更清晰，并制作了第一对端点。

从`config.pre`开始:

```
$host = new Aerys\Host();
$host->expose("*", 8080);

$host->use($router = Aerys\router());
$host->use($root = Aerys\root(.."/public"));

$web = process .."/routes/web.pre";
$web($router);

$api = process .."/routes/api.pre";
$api($router); 
```

我决定对应用程序的 HTTP 和 WebSocket 部分使用 [Aerys](https://github.com/amphp/aerys) 。这段代码看起来与 Aerys 的文档非常不同，但那是因为我很清楚自己需要什么。

运行 Aerys 应用程序的通常过程是使用这样的命令:

```
vendor/bin/aerys -d -c config.php 
```

有很多代码需要重复，而且它没有处理我想要使用 PHP 预处理的事实。我创建了一个加载文件。

从`loader.php`开始:

```
return Pre\processAndRequire(__DIR__ . "/config.pre"); 
```

然后，我安装了我的依赖项。这是来自`composer.json`:

```
"require": {
  "amphp/aerys": "dev-amp_v2",
  "amphp/parallel": "dev-master",
  "league/container": "^2.2",
  "league/plates": "^3.3",
  "pre/short-closures": "^0.4.0"
},
"require-dev": {
  "phpunit/phpunit": "^6.0"
}, 
```

我想使用`amphp/parallel`，将阻塞代码移出异步服务器，但是它不能用稳定的标签`amphp/aerys`安装。这就是为什么我去了`dev-amp_v2`分公司。

我认为包含某种模板引擎和服务定位器是个好主意。我选择了 PHP 联盟版本。最后，我添加了`pre/short-closures`，这既是为了处理`config.pre`中的自定义语法，也是为了处理我计划在之后使用的短闭包…

然后我开始创建路由文件。从`routes/web.pre`开始:

```
use Aerys\Router;
use App\Action\HomeAction;

return (Router $router) => {
  $router->route(
    "GET", "/", new HomeAction
  );
}; 
```

并且，从`routes/api.pre`开始:

```
use Aerys\Router;
use App\Action\Api\HomeAction;

return (Router $router) => {
  $router->route(
    "GET", "/api", new HomeAction
  );
}; 
```

虽然路线很简单，但这些帮助我测试了`config.pre`中的代码。我决定让这些 routes 文件返回闭包，这样我可以传递给它们一个类型化的`$router`，它们可以向其中添加自己的路由。最后，我创建了两个(类似的)动作。

从`app/Actions/HomeAction.pre`开始:

```
namespace App\Action;

use Aerys\Request;
use Aerys\Response;

class HomeAction
{
  public function __invoke(Request $request,
    Response $response)
  {
    $response->end("hello world");
  }
} 
```

最后一点是添加快捷脚本，启动 Aerys 服务器的开发和生产版本。

从`composer.json`开始:

```
"scripts": {
  "dev": "vendor/bin/aerys -d -c loader.php",
  "prod": "vendor/bin/aerys -c loader.php"
},
"config": {
  "process-timeout": 0
}, 
```

完成所有这些后，我可以启动一个新的服务器，只需输入:

```
composer dev 
```

## 设置前端

*“好了，现在我已经得到了 PHP 方面相对稳定的东西；我该如何构建 ReactJS 文件？也许我可以用[拉勒维尔混音](https://laravel.com/docs/5.4/mix)……？”*

我并不热衷于创建一个全新的构建链，而且 Mix 已经被重新构建，以便在非 Laravel 项目上也能很好地工作。尽管它相对容易配置和扩展，但默认情况下它更喜欢 VueJS。

我必须做的第一件事是安装一些 NPM 依赖项。从`package.json`开始:

```
"devDependencies": {
  "babel-preset-react": "^6.23.0",
  "bootstrap-sass": "^3.3.7",
  "jquery": "^3.1.1",
  "laravel-mix": "^0.7.5",
  "react": "^15.4.2",
  "react-dom": "^15.4.2",
  "webpack": "^2.2.1"
}, 
```

Mix 使用了 [Webpack](https://www.sitepoint.com/beginners-guide-webpack-module-bundling/) 来预处理和捆绑 JS 和 CSS 文件。我还需要安装 [React](https://facebook.github.io/react) 和相关的 [Babel](http://babeljs.io) 库来构建`jsx`文件。最后，我添加了 [Bootstrap](http://getbootstrap.com) 文件，用于一点默认样式。

Mix 自动加载了一个自定义配置文件，所以我添加了以下内容。从`webpack.mix.js`开始:

```
let mix = require("laravel-mix")

// load babel presets for jsx files

mix.webpackConfig({
  "module": {
    "rules": [
      {
        "test": /jsx$/,
        "exclude": /(node_modules)/,
        "loader": "babel-loader" + mix.config.babelConfig(),
        "query": {
          "presets": [
            "react",
            "es2015",
          ],
        },
      },
    ],
  },
})

// set up front-end assets

mix.setPublicPath("public")

mix.js("assets/js/app.jsx", "public/js/app.js")
mix.sass("assets/scss/app.scss", "public/css/app.css")
mix.version() 
```

我需要告诉 Mix 如何处理`jsx`文件，所以我添加了通常放在`.babelrc`中的相同类型的配置。我计划用单一的 JS 和 CSS 入口点进入应用程序的各个部分。

*注意:Mix 的未来版本将内置对构建 ReactJS 资产的支持。发生这种情况时，可以删除`mix.webpackConfig`代码。*

我又一次创建了一些快捷脚本，以节省大量的打字时间。从`package.json`开始:

```
"scripts": {
  "dev": "$npm_package_config_webpack",
  "watch": "$npm_package_config_webpack -w",
  "prod": "$npm_package_config_webpack -p"
},
"config": {
  "webpack": "webpack --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js"
}, 
```

所有这三个脚本都使用了 Webpack 变量命令，但是除此之外，它们还做了些什么。`dev`构建了 JS 和 CSS 文件的调试版本。`-w`开关启动了 Webpack watcher(这样可以部分重建包)。`-p`开关实现了捆绑包的精益生产版本。

因为我使用的是包版本控制，所以我需要一种方法来引用像`/js/app.60795d5b3951178abba1.js`这样的文件，而不需要知道散列。我注意到 Mix 喜欢创建一个清单文件，所以我创建了一个助手函数来查询它。来自`helpers.pre`:

```
use Amp\Coroutine;

function mix($path) {
  $generator = () => {
    $manifest = yield Amp\File\get(.."/public/mix-manifest.json");
    $manifest = json_decode($manifest, true);

    if (isset($manifest[$path])) {
        return $manifest[$path];
    }

    throw new Exception("{$path} not found");
  };

  return new Coroutine($generator());
} 
```

Aerys 知道如何处理以`$val = yield $promise`形式出现的承诺，所以我使用了 Amp 的承诺实现。当文件被读取和解码时，我可以寻找匹配的文件路径。我调了一下`HomeAction`。来自`app/Actions/HomeAction.pre`:

```
public function __invoke(Request $request,
  Response $response)
{
  $path = yield mix("/js/app.js");

  $response->end("
    <div class='app'></div>
    <script src='{$path}'></script>
  ");
} 
```

我意识到我可以继续创建返回承诺的函数，并以这种方式使用它们来保持我的代码异步。下面是我的 JS 代码，来自`assets/js/component.jsx`:

```
import React from "react"

class Component extends React.Component
{
  render() {
    return <div>hello world</div>
  }
}

export default Component 
```

…并且，从`assets/js/app.jsx`开始:

```
import React from "react"
import ReactDOM from "react-dom"
import Component from "./component"

ReactDOM.render(
  <Component />,
  document.querySelector(".app")
) 
```

毕竟，我只是想看看 Mix 是否会编译我的`jsx`文件，以及我是否可以使用 async `mix`函数再次找到它们。结果成功了！

*注意:每次使用`mix`函数是很昂贵的，尤其是当我们加载相同的文件时。相反，我们可以在服务器引导阶段加载所有模板，并在需要时从我们的操作内部引用它们。我们用来启动 Aerys 的配置文件可以返回一个承诺(就像 [`Amp\all`](http://amphp.org/docs/amp/managing-concurrency.html#all) 给我们的那种)，所以我们可以在服务器启动之前解析所有的模板。*

## 与 WebSockets 连接

我差点被陷害了。最后要做的是通过 WebSockets 连接后端和前端。我发现这相对简单，有一个新的类。从`app/Socket/GameSocket.pre`开始:

```
namespace App\Socket;

use Aerys\Request;
use Aerys\Response;
use Aerys\Websocket;
use Aerys\Websocket\Endpoint;
use Aerys\Websocket\Message;

class GameSocket implements Websocket
{
  private $endpoint;
  private $connections = [];

  public function onStart(Endpoint $endpoint)
  {
    $this->endpoint = $endpoint;
  }

  public function onHandshake(Request $request,
    Response $response)
  {
    $origin = $request->getHeader("origin");

    if ($origin !== "http://127.0.0.1:8080") {
      $response->setStatus(403);
      $response->end("<h1>origin not allowed</h1>");
      return null;
    }

    $info = $request->getConnectionInfo();

    return $info["client_addr"];
  }

  public function onOpen(int $clientId, $address)
  {
    $this->connections[$clientId] = $address;
  }

  public function onData(int $clientId,
    Message $message)
  {
    $body = yield $message;

    yield $this->endpoint->broadcast($body);
  }

  public function onClose(int $clientId,
    int $code, string $reason)
  {
    unset($this->connections[$clientId]);
  }

  public function onStop()
  {
    // nothing to see here…
  }
} 
```

…以及对网络路线的轻微修改(从`routes/web.pre`):

```
use Aerys\Router;
use App\Action\HomeAction;
use App\Socket\GameSocket;

return (Router $router) => {
  $router->route(
    "GET", "/", new HomeAction
  );

  $router->route(
    "GET", "/ws", Aerys\websocket(new GameSocket)
  );
}; 
```

现在，我可以修改 JS 来连接到这个 WebSocket，并向连接到它的每个人发送消息。从`assets/js/component.jsx`开始:

```
import React from "react"

class Component extends React.Component
{
  constructor()
  {
    super()
    this.onMessage = this.onMessage.bind(this)
  }

  componentWillMount()
  {
    this.socket = new WebSocket(
      "ws://127.0.0.1:8080/ws"
    )

    this.socket.addEventListener(
      "message", this.onMessage
    )

    // DEBUG

    this.socket.addEventListener("open", () => {
      this.socket.send("hello world")
    })
  }

  onMessage(e)
  {
    console.log("message: " + e.data)
  }

  componentWillUnmount()
  {
    this.socket.removeEventListener(this.onMessage)
    this.socket = null
  }

  render() {
    return <div>hello world</div>
  }
}

export default Component 
```

当我创建一个新的`Component`对象时，它将连接到 WebSocket 服务器，并为新消息添加一个事件监听器。我添加了一些调试代码——以确保连接正确，并发回新消息。

我们稍后会谈到 PHP 和 WebSockets 的本质，不要担心。

## 摘要

在这一部分中，我们研究了如何设置一个简单的异步 PHP web 服务器，如何在非 Laravel 项目中使用 Laravel Mix，甚至如何用 WebSockets 将后端和前端连接在一起。

唷！这涉及了很多方面，我们还没有编写一行游戏代码。当我们开始构建游戏逻辑和一个 React 界面时，请加入我的第二部分。

*这篇文章由[尼克拉斯·凯勒](https://github.com/kelunik)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章