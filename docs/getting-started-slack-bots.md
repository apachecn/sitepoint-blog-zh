# Slack 机器人入门

> 原文：<https://www.sitepoint.com/getting-started-slack-bots/>

Slack 是一种鼓励团队成员之间更好沟通的流行工具。它附带了一系列有用的功能和服务，包括第三方集成和 webhook 支持。Slack webhooks 既有趣又有用，而且很容易安装。在本教程中，我们将使用 [Node.js](http://nodejs.org/) 和 [Heroku](https://www.heroku.com/) 建立一个简单的服务器，它将作为一个“机器人”响应来自 Slack 的团队消息熟悉 JavaScript 和 Git 会有帮助。如果你之前没有用过 Git，可以看看 [Git for 初学者](https://www.sitepoint.com/git-for-beginners/)入门。访问 Slack 帐户也是假设的，但是不要担心，它们是免费的！

## 创建新的松弛集成

从 Slack web 界面，打开左上角团队名称旁边的菜单，选择[配置集成](http://slack.com/services/new)。在这里，您可以看到一个包含所有可用集成的仪表板。滚动到最底部的 **DIY 集成&定制**，并添加一个**外发 WebHook** 。阅读简短描述，然后点击**添加外向 WebHooks 集成**。

![DIY Integrations & Customizations](img/6c28bab40c67a081bbf0e4b3708d3139.png)

您将看到输出的 WebHook 配置，以及请求和响应有效负载的示例。我们的第一个机器人在被调用时会简单地问候用户。为此，我们将允许钩子在任何通道上监听触发字“hello”。我们还不知道它将发布到哪个 URL，所以我们稍后会回到这个页面。

## 创建 Web 服务器

我们将在 Node.js 上使用 [Express 4](http://expressjs.com/) 来构建一个简单的 web 服务器。

为您的应用程序创建一个新目录，并运行`npm init`来生成您的`package.json`文件。在你的`package.json`属地中，包括`"express": "^4.x.x"`和`"body-parser": "^1.x.x"`。`body-parser`包将用于解析 Slack 发送的有效载荷。创建名为`app.js`的服务器文件。在这里，我们将`require`必要的模块，添加主体解析器中间件、错误处理器和测试路径。然后，我们告诉服务器开始监听。

```
var express = require('express');
var bodyParser = require('body-parser');

var app = express();
var port = process.env.PORT || 3000;

// body parser middleware
app.use(bodyParser.urlencoded({ extended: true }));

// test route
app.get('/', function (req, res) { res.status(200).send('Hello world!') });

// error handler
app.use(function (err, req, res, next) {
  console.error(err.stack);
  res.status(400).send(err.message);
});

app.listen(port, function () {
  console.log('Slack bot listening on port ' + port);
});
```

使用`node app`运行服务器，并验证访问`http://localhost:3000`是否响应“Hello world！”。

## 编写机器人逻辑

创建一个名为`hellobot.js`的文件

这个模块将是我们的机器人逻辑生活的地方。它将包括一个导出的函数，该函数向 Slack 返回一条消息，状态代码为`200`。我们将从解析 Slack 发送给服务器的数据开始，然后我们将使用自己的适当格式化的有效载荷进行响应。要做到这一点，让我们在[配置集成](https://slack.com/services)下重新审视我们的输出 WebHook。**传出数据**的例子向我们展示了当一个传出的 WebHook 被触发时 Slack 发送的所有信息。

![Outgoing WebHook Data Example](img/b08c314c83e0aa4776b0bb8992bfb49b.png)

您可以看到一些数据对于认证或路由是有用的，但是现在我们只关注`user_name`属性。使用这个值，我们可以向用户发送个性化的问候。

```
module.exports = function (req, res, next) {
  var userName = req.body.user_name;
  var botPayload = {
    text : 'Hello, ' + userName + '!'
  };

  // avoid infinite loop
  if (userName !== 'slackbot') {
    return res.status(200).json(botPayload);
  } else {
    return res.status(200).end();
  }
}
```

这里我们创建了我们的`botPayload`对象。唯一需要的属性是`botPayload.text`，它定义了当我们响应时，我们的 bot 将显示什么。向用户添加带有问候语的`text`属性，并使用 JSON 和状态代码`200`进行响应。请注意，我们的机器人的响应是我们发出的 WebHook 的触发词！为了避免无限循环，我们可以检查主体的`user_name`。所有挂钩都发布为“slackbot”，即使名称在聊天中出现的方式不同。我们可以利用这个事实来防止我们的钩子对自己做出反应。

回到`app.js`，要求顶部的 bot 模块。

```
var hellobot = require('./hellobot');
```

然后，添加一个侦听从`POST`到`/hello`的路由。

```
app.post('/hello', hellobot);
```

让我们做一个快速测试。使用`node app`再次运行应用程序，并在另一个终端窗口中使用`curl`来确保应用程序如我们所预期的那样响应。(注意:因为我们的 bot 在这里只寻找一个值，所以没有必要在我们的测试中包含整个 Slack 有效负载)。

```
curl -X POST --data "user_name=foobar" http://localhost:3000/hello
```

如果我们的应用程序运行正常，我们应该会看到类似这样的响应:

```
{"text":"Hello, foobar!"}
```

现在，是时候让我们的机器人上线了。

## 在 Heroku 上部署应用程序

Heroku 是一项方便的服务，可以帮助你在很短的时间内免费获得网络服务器。部署是通过将您的代码推送到 git 服务器来完成的，在那里代码会被自动部署和运行。在 Heroku 上创建一个帐户，如果您已经有帐户，请登录。

登录后，您将看到您的 Heroku 仪表盘。点击右上角的`+`创建一个新的应用程序。按照提供的说明在您的计算机上登录 Heroku。部署很容易，但第一次部署应用时需要了解很多东西。当你设置你的应用程序时，确保你遵循[这个指南](https://devcenter.heroku.com/articles/getting-started-with-nodejs#introduction)。我们将在此过程中进行一些双重检查，但是其中有许多有用的信息超出了本文的范围。

在部署我们的机器人之前，我们需要告诉 Heroku 如何运行这个应用程序。这是通过创建一个 [Procfile](https://devcenter.heroku.com/articles/getting-started-with-nodejs#define-a-procfile) 来完成的。您的 Procfile 只需要一行代码:

```
web: node app
```

一旦你完成了这些，你就可以提交你的修改并推送到`heroku`。Heroku 自动安装节点模块，您将看到部署的结果。如果一切顺利，你会在底部看到你的应用程序的链接。复制那个 URL，然后再次访问你的出站 WebHook 的配置页面。将您复制的 URL 添加到附加了`/hello`路线的 **URL(s)** 输入字段，并根据需要修改挂钩的名称和图标。

![Outgoing WebHook URL(s)](img/0eb9ffc1a22ba0cfc3358e0d5578ef9b.png)

让机器人上线还有一步:我们需要给它一个 [Dyno](https://devcenter.heroku.com/articles/getting-started-with-nodejs#scale-the-app) 。在终端中，输入:

```
heroku ps:scale web=1
```

这告诉 Heroku 给你的应用一个 web worker。小心；你的应用程序每月只能获得一个免费的动态！

你的机器人现在应该在线了，所以回到 Slack 测试一下吧！

![Hellobot Response](img/8e21a76b6cc5cd0b1114ca9ec0191a0d.png)

如果您没有得到响应，请返回并检查您的代码。您可以使用终端命令`heroku logs`来查看关于请求的细节以及您添加的任何`console.log()`语句。

不太难，是吗？我们成功地在 Slack 中设置了一个 webhook，将我们的应用程序部署到 Heroku，并让两者相互对话！比一个简单的“hello world”程序要多做一点工作，但是回报更大。现在我们已经运行了服务器，我们可以轻松地添加更多的挂钩和响应。Hellobot 相当友好，但没那么有用。在下一节中，我们将使用 Slack 的其他 webhook 类型来创建一个骰子滚动聊天命令。

## DiceBot–使用 Slack 命令和传入的 WebHooks

在上一节中，我们使用了一个传出的 WebHook，它监听聊天中的触发词，向服务器发送消息，并显示响应。在这一节中，我们将使用另外两个工具来创建定制集成:**斜杠命令**和**传入 WebHooks** 。访问[配置集成](http://slack.com/services/new)页面，查看 **DIY 集成&定制**下的其他选项。让我们从添加**斜杠命令**集成开始。阅读简短描述，将命令设置为“/roll”，并添加集成。下一页显示了配置。您可以看到输出数据看起来类似于我们之前配置的输出 WebHook。在本节中，我们将使用我们在上一节中创建的应用程序，因此我们可以使用相同的 URL，但这次我们将`POST`到路线`/roll`。

请注意，斜杠命令集成可以响应用户，但只能私下响应。如果你能看到骰子，掷骰子有什么用？Slack 建议使用一个传入的 WebHook 和 Slash 命令来发送聊天消息。返回到[配置集成](http://slack.com/services/new)页面，添加一个**传入 WebHook** 。阅读描述，选择一个频道，并添加挂钩。不要担心你选了什么频道；我们可以在机器人的响应中指定通道。因为我们可以在我们的应用程序中选择频道，这意味着我们也可以将这种集成用于我们创建的任何其他机器人。传入的 WebHook 没有太多的配置，但是它有很多重要的信息用于构造我们的 bot 的响应有效负载。最重要的部分是 **Webhook URL** 。为了给 Slack 发送消息，我们将通过我们的应用程序对此进行处理。

### 响应来自服务器的斜杠命令

因为我们已经将斜杠命令指向了路线`/roll`，所以我们可以将该路线添加到我们的服务器中 hellobot 旁边。我们将为传入的钩子使用一个秘密的 URL，所以如果你想公开你的代码，最好把它隐藏起来。一个好方法是使用 Heroku 的[配置变量](https://devcenter.heroku.com/articles/getting-started-with-nodejs#define-config-vars)。

让我们也将`request`添加到我们的包中，以便与传入挂钩一起使用:

```
npm install --save request
```

现在我们来添加`dicebot.js`。Dicebot 将导出一个函数，但是我们还需要两个额外的函数:一个是传入 WebHook 的`POST`,另一个是掷骰子的。

```
var request = require('request');

module.exports = function (req, res, next) {
};

function roll () {
}

function send () {
}
```

正如许多桌面游戏玩家所知，骰子的种类远不止六面骰子。其他骰子有四边、八边、十边、二十边等等！掷骰子时使用一种特殊的符号:`<number of dice>d<sides of dice>`。所以，掷出两个六面骰子记为“2d6”。我们可以让用户使用这种符号掷出多个骰子，但是我们必须检查用户的输入是否正确。

让我们从初始化一些变量并解析输入开始。我们将设置一些默认值，以防用户想要省略输入。

```
module.exports = function (req, res, next) {
  // default roll is 2d6
  var matches;
  var times = 2;
  var die = 6;
  var rolls = [];
  var total = 0;
  var botPayload = {};

  if (req.body.text) {
    // parse roll type if specified
    matches = req.body.text.match(/^(\d{1,2})d(\d{1,2})$/);

    if (matches && matches[1] && matches[2]) {
      times = matches[1];
      die = matches[2];
    } else {
      // send error message back to user if input is bad
      return res.status(200).send('<number>d<sides>');
    }
  } 
}
```

上面的正则表达式对于解析掷骰子是相当可靠的，并且将值限制为两位数，这样用户就不会发疯。如果用户弄乱了他们的语法，我们会发回一个提示。

用用户输入掷骰子很容易。参见[这篇文章](http://stackoverflow.com/a/1527820)了解随机掷骰子的工作原理。对于真正的随机卷，你可以联系 random.org 的[API](https://api.random.org/json-rpc/1/)。我们来写 roll 函数。

```
function roll (min, max) {
  return Math.floor(Math.random() * (max - min + 1) + min);
}
```

然后，在我们的主函数中，我们将进行一些滚动，并构建一个消息来响应。让我们包括滚轮的名称，这样其他用户可以看到谁使用了`/roll`命令。

```
// roll dice and sum
for (var i = 0; i < times; i++) {
  var currentRoll = roll(1, die);
  rolls.push(currentRoll);
  total += currentRoll;
}

// write response message and add to payload
botPayload.text = req.body.user_name + ' rolled ' + times + 'd' + die + ':\n' +
                          rolls.join(' + ') + ' = *' + total + '*';
```

我们将消息添加到了`botPayload.text`属性中，并在`total`周围添加了一些宽松的格式以使其加粗。我们还可以使用响应有效负载来配置名称、图标和通道。我们将根据用户发出滚动命令的通道来设置通道。Slack 建议在有效载荷中使用通道的名称，但这意味着我们必须在它前面加上“#”。相反，我们可以使用频道 ID，这将允许我们在公共频道和私人群组中发布内容。对于图标，有一个方便的骰子表情符号，尽管你可以使用`icon_url`提供一个更好的图像。

```
botPayload.username = 'dicebot';
botPayload.channel = req.body.channel_id;
botPayload.icon_emoji = ':game_die:';
```

有效载荷看起来不错。我们来写`send`函数。我选择在 Heroku 的配置变量中只存储路径标记。确保以 JSON 字符串的形式发送正文。

```
function send (payload, callback) {
  var path = process.env.INCOMING_WEBHOOK_PATH;
  var uri = 'https://hooks.slack.com/services' + path;

  request({
    uri: uri,
    method: 'POST',
    body: JSON.stringify(payload)
  }, function (error, response, body) {
    if (error) {
      return callback(error);
    }

    callback(null, response.statusCode, body);
  });
}
```

在我们的主函数中，我们将发起发送请求。如果有效，我们可以用一个空的`200`来响应斜杠命令。如果没有，我们可以使用来自我们的`send()`回调的值来构建一个错误。这将触发`app.js`中的错误处理程序并发回`400`，通知用户该错误。

```
// send dice roll
send(botPayload, function (error, status, body) {
  if (error) {
    return next(error);
  } else if (status !== 200) {
    // inform user that our Incoming WebHook failed
    return next(new Error('Incoming WebHook: ' + status + ' ' + body));
  } else {
    return res.status(200).end();
  }
});
```

让我们再次运行我们的服务器。这一次，我们需要包括`channel_id`。你可以使用 [Slack API 测试器](https://api.slack.com/methods/channels.list/test)找到正确的`channel_id`。由于传入的 WebHook 已经设置好了，它应该会听到我们的服务器调用它。我们还需要在我们的环境中包含传入的 WebHook 路径，因为该应用程序还没有在 Heroku 上。

```
INCOMING_WEBHOOK_PATH=/your/path/tokens node app
```

```
curl -X POST --data "user_name=foobar&channel_id=C0123ABCD&text=4d6" http://localhost:3000/roll`
```

![Dicebot Response](img/045658a31e90770a756494cf35e86e0d.png)

看起来不错！我们来部署一下。

### 重新部署到 Heroku

因为我们使用 Heroku 的配置变量来存储我们的 webhook 令牌，所以不要忘记在应用程序的**设置**菜单中设置它。

![Heroku Config Vars](img/66ab1ae2f7f1c78dd4e8659ccc2ea4c6.png)

然后使用 git 添加新文件，提交您的更改，并推送到您的`heroku`遥控器。Heroku 将安装节点模块并启动服务器。在测试之前，我们先开始实时看日志:
`heroku logs -t`

我们的钩子都装好了，我们的翻滚指令应该准备好了。试用几卷！如果有些东西不工作，您可以使用 Heroku 日志进行调试。要查看完整的应用程序，请查看这个 [GitHub repo](https://github.com/jsprodotcom/getting-started-with-slack-bots) 。

### 持续发展

这两个机器人展示了开始编写自己的聊天机器人和集成所需的所有工具。你可以将许多服务集成到机器人中，或者你可以尝试成为一个令人信服的对话伙伴。API 和节点模块可以帮助您实现这两个目标。

关于机器人的一些其他想法:

*   一个使用 GitHub API 向你展示打开拉请求的机器人
*   一个使用 Yelp API 帮助你的团队决定午餐的机器人
*   一个为你做谷歌搜索的机器人
*   一个只会用流行语和行话跟你说话的机器人

如果你有好的想法，分享你的代码！制作机器人很有趣，而且从来没有像现在这样简单。

## 分享这篇文章