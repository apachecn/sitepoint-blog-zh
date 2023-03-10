# 用 Node.js 构建您自己的定制 SlackBot

> 原文：<https://www.sitepoint.com/custom-slackbot-with-node/>

*这篇文章由[丹·普林斯](https://www.sitepoint.com/author/dprince)和[马修·威尔金](https://www.sitepoint.com/author/mwilkin/)进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

Slack 在开发人员和非开发人员的技术社区中都有一定的吸引力和大量的粉丝。它光滑的用户界面，保持沟通独立和相关的团队和渠道概念，大量的生产力集成(Dropbox，box，Google Calendar，Hangouts 等)以及 giphy 和 [reminders](https://get.slack.help/hc/en-us/articles/201259356-Using-slash-commands) 之类的东西，使它使用起来很有趣。此外，他们的 API 帮助开发人员扩展功能并为他们的团队构建定制体验。

如果你在想“这绝不是 Slack 独有的，HipChat(或你最喜欢的应用)也有这一切！”，你可能想看看这个:[http://slackvshipchat.com/](http://slackvshipchat.com/)

## 教程的目标

本教程旨在帮助您使用一个简单的 node 应用程序，将您的 Slack 通道变成一个定制的命令行终端。我们将使用一个叫做 [slack-terminalize](https://www.npmjs.com/package/slack-terminalize) ( *免责声明*:我开发的)的助手模块，它抽象出消息的初始处理。它使用 Slack 的[实时 API 节点客户端](https://github.com/slackhq/node-slack-client)，并准备一个机器人来监听和响应你的请求。

注意，我们在这里不会使用[斜杠命令](https://api.slack.com/slash-commands)，而是将常规消息解释为命令。所以如果你想学习斜杠命令，这可能不是合适的教程。

## 开始之前

我的假设是你有 JavaScript 和 NodeJS 的工作知识，并且你熟悉 Slack 术语:团队、渠道、[机器人](https://api.slack.com/bot-users)和[集成](https://slack.com/integrations)。你需要安装*节点*和*NPM*；您可以按照这篇精彩的 NPM[site point 简介](https://www.sitepoint.com/beginners-guide-node-package-manager/)来设置您的开发环境。

## 发展的动力*松弛-终结*

虽然有许多奇特的 [hubot](https://github.com/slackhq/hubot-slack) [脚本](https://hubot.github.com/docs/#scripts)来响应自然语言查询，但正如任何 Linux 爱好者都会同意的那样，许多事情都可以通过简短的命令和最少的按键来实现。简单的命令在移动设备上特别有意义，可以帮助你少打字，多做事。如果你考虑一个命令行系统，大多数时候 shell 所做的是获取、解析、标记和分发命令的单调工作(我知道这是一个非常简单的工作)。

考虑到这一点，我觉得需要一个能做到这一点的模块。如果你愿意，可以称之为松弛频道的外壳。通过处理和调度方法以及类似插件的架构来添加定制命令， *slack-terminalize* 将事情抽象化，因此您可以更专注于定义应用程序的行为。

## 说够了，让我们开始吧

首先，让我们为您的团队创建一个新的 bot 用户，它可以接受您的订单！转到`https://<your-team-name>.slack.com/services/new/bot`，为它选择一个用户名，然后点击*添加机器人集成*。

![Add Bot User](img/d976938fc1da09327eeb380f4acd3e4d.png "Add Bot User")

复制显示给您的 API 令牌，因为这是您的 bot 能够与通道交互所必需的。配置机器人的其他细节，它的个人资料图像和真实姓名，并点击*保存集成*。

![Save Bot User](img/591204244156087b4cf78766f71a522c.png "Save Bot User")

现在，让我们克隆[示例应用程序](https://github.com/ggauravr/slack-sample-cli)并安装依赖项:

```
git clone https://github.com/ggauravr/slack-sample-cli.git
cd slack-sample-cli
npm install 
```

### 项目结构演练

从`package.json`中的依赖项列表来看，唯一需要的依赖项是`slack-terminalize`，但是因为示例应用程序有一个示例来展示如何处理异步命令，所以使用[请求](https://www.npmjs.com/package/request)模块来进行 REST 调用。

![Project Structure](img/2bdd42642bbef852f88edcc0a526ae34.png "Project Structure")

#### 配置/

您的应用程序可能需要的所有 JSON 文件都可以放在这里。我说“可以”,是因为它相当灵活，您可以通过配置参数(稍后将详细介绍)更改它，使其适用于不同的目录。这只是你构建应用程序的众多方式之一，但是如果你是松散集成的新手，我建议你坚持这样做。

#### 命令. json

这使得添加自定义命令变得轻而易举。每个命令都由一个键-值对来表示:键是命令名(我称之为*主名*)，值是一个带有自定义键-值对的对象，您希望将它用于命令。

这里，我为每个命令使用以下自定义字段:

*   `alias`–这些是命令的别名(让我们称它们为*二级名*),也可以在 slack 通道中使用它们来调用命令。最好将最小的名字作为主要名字，将更有意义、更长的名字作为别名。

*   `description`–简短易读的命令描述

*   `help`–帮助信息，做类似`man <command-name>`或`help <command-name>`的事情

*   `exclude`–指示该命令是否应显示在用户可用命令列表中的标志。一些命令可能只是用于开发目的和/或不需要向用户公开的助手(例如上面的*错误*命令)。

*   `endpoint`–命令应该与之对话的 REST 端点，以防它依赖任何外部服务来执行其任务

在上面的关键字中，`alias`是唯一被查找以将用户键入的命令映射到其主要名称的关键字。其余的都是可选的，您可以随意使用 command 对象中的任何字段。

#### 命令/

这是神奇的地方，是您定义命令行为的地方。在 *config/commands.json* 中指定的每个命令都应该在这里有其匹配的实现，文件名与 json 中使用的键(主要名称)相匹配。这就是调度程序调用正确的处理程序的方式。是的，有点固执己见，我同意，但仍然有用和可定制的。

```
{
    "help": {
        "alias": [ "halp" ],
        "endpoint": "#",
        "help": "help [command](optional)",
        "description": "To get help on all supported commands, or a specified command"
    },

    "gem": {
        "alias": [],
        "endpoint": "https://rubygems.org/api/v1/gems/{gem}.json",
        "help": "gem [gem-name]",
        "description": "Fetches details of the specified Ruby gem"
    },

    "error": {
        "exclude": true
    }
} 
```

再次注意，这个文件中的键名与`commands/`目录中的文件名相同。

### 代码走查

将 *index.js* 中`SLACK_TOKEN`的值替换为你的机器人的值。`CONFIG_DIR`和`COMMAND_DIR`分别告诉`slack-terminalize`在哪里寻找配置和命令实现。

```
var slackTerminal = require('slack-terminalize');

slackTerminal.init('xoxb-your-token-here', {
    // slack client options here
    }, {
    CONFIG_DIR: __dirname + '/config',
    COMMAND_DIR: __dirname + '/commands'
}); 
```

接下来，使用以下命令启动应用程序:

```
node . 
```

通过网络或应用程序登录您的 Slack 团队。默认情况下，bot 被添加到#general channel，但是您可以使用斜杠命令:`/invite @<your-bot-name>`邀请 bot 加入任何频道，甚至是私有频道。只要你输入`/invite @`，Slack 就会自动提示你用户名。如果您没有看到您的机器人在那里列出，请返回并检查您是否已经正确集成了机器人。

![Invite Bot](img/f2e31a63a675ff812d9b48b76fe81751.png "Invite Bot")

键入`help`或`halp`(别名，还记得吗？)和“瞧！”，机器人应该会响应你的请求。继续操作`commands/help.js`来改变你在响应中看到的内容。从实现中可以看到，这个命令只是从`config/commands.json`文件中加载命令细节来响应，所以是同步的。有时，您可能需要执行异步任务，比如查询数据库或调用 REST 端点来获取响应。让我们看看如何去做。

正如我之前提到的，我使用`request`模块进行 REST 调用，下面的代码片段(gem 命令)搜索用户在 Slack 中键入的 gem 名称。瞥一眼`commands/gem.js`，你会看到它记住了发送消息的通道(使用[闭包](https://www.sitepoint.com/demystifying-javascript-closures-callbacks-iifes/#immediately-invoked-function-expressions-iifes))并将响应发送回同一个通道！

```
var request = require('request'),
    util    = require('../util');

module.exports = function (param) {
    var channel  = param.channel,
        endpoint = param.commandConfig.endpoint.replace('{gem}', param.args[0]);

    request(endpoint, function (err, response, body) {
        var info = [];

        if (!err && response.statusCode === 200) {
            body = JSON.parse(body);

            info.push('Gem: ' + body.name + ' - ' + body.info);
            info.push('Authors: ' + body.authors);
            info.push('Project URI: ' + body.project_uri);
        }
        else {
            info = ['No such gem found!'];
        }

        util.postMessage(channel, info.join('\n\n'));
    });

}; 
```

尝试在 Slack 频道中键入`gem ab`,您应该会看到类似这样的内容:

![Gem Response](img/51c18b4751d5f221984bd3f4438c36e8.png "Gem Response")

再次尝试在`commands/gem.js`中摆弄响应的格式，以掌握它的窍门。现在，我们有一个机器人监听被邀请的频道，并响应我们的请求。让我们看看如何添加自定义命令。

### 添加自定义命令实现

在`config/commands.json`中添加您的新命令。如前所述，这个键将是主要的命令名。该命令的别名作为一个数组值输入到*别名*中，如下所示。

```
{
    "your-new-command": {
        "alias": [ "command-alias", "another-alias", "yet-another-alias" ],
        "help": "A short help message for the awesome new command",
        "description": "Brief description of what the command does"
    }
} 
```

目前，**不支持带空格的命令名**。在`commands/`目录中创建一个与上面命令的主要名称相同的文件(在本例中为`your-command-name.js`)。将`module.exports`分配给命令执行功能，如下图所示。

```
var util = require('../util');

module.exports = function (param) {
    // param object contains the following keys:
    // 1\. command - the primary command name
    // 2\. args - an array of strings, which is user's message posted in the channel, separated by space
    // 3\. user - Slack client user id
    // 4\. channel - Slack client channel id
    // 5\. commandConfig - the json object for this command from config/commands.json

    // implement your logic here.. 
    // .. 

    // send back the response
    // more on this method here: https://api.slack.com/methods/chat.postMessage
    util.postMessage(param.channel, '<your-message-to-be-posted-back-in-the-channel>');
}; 
```

有关`User`和`Channel`对象的更多信息，请参考节点松弛客户端的[文档](https://github.com/slackhq/node-slack-client)。

编写您的新命令，重启应用程序，就这样！你应该有你的新命令工作。键入命令，看看是否得到预期的响应。

### 通过配置自定义行为

`slack-terminalize`模块有两个参数，一个选项对象和一个配置对象。

```
var slackTerminal = require('slack-terminalize');

slackTerminal.init({
    autoReconnect: true // or false, indicates if it should re-connect after error response from Slack
    // other supported options can be seen here: https://github.com/slackhq/node-slack-client/blob/master/lib/clients/rtm/client.js
}, {
    CONFIG_DIR: __dirname + '/config',
    COMMAND_DIR: __dirname + '/commands',
    ERROR_COMMAND: "error" // The filename it looks for in COMMAND_DIR, in case the user entered command is invalid
}) 
```

关于参数的更多信息，您可以查看文档[这里](https://github.com/ggauravr/slack-terminalize)。

## 接下来呢？

*   去为你的团队定义一些很酷的命令:享受乐趣，提高生产力。
*   分叉项目[松弛终止](https://github.com/ggauravr/slack-terminalize)及其[样本 app](https://github.com/ggauravr/slack-sample-cli) 。到处玩，贡献并帮助改进它。如果你发现任何错误，在回购上创建一个问题！
*   请在下面评论你是如何使用 Slack 来提高工作效率的，或者你有什么改进的建议。我洗耳恭听，学习 Slack API 赋予开发者的能力的创造性应用

## 链接和资源

*   [Slack Bot 用户](https://api.slack.com/bot-users)
*   [松弛 API](https://api.slack.com/)
*   [Hubot](https://hubot.github.com/)
*   [轮毂松弛](https://github.com/slackhq/hubot-slack)
*   [节点松弛客户端](https://github.com/slackhq/node-slack-client)
*   [web sockets 简介](http://blog.teamtreehouse.com/an-introduction-to-websockets)
*   [REST vs WebSockets](https://www.pubnub.com/blog/2015-01-05-websockets-vs-rest-api-understanding-the-difference/)
*   [如何创建和发布您的第一个节点模块](https://medium.com/@jdaudier/how-to-create-and-publish-your-first-node-js-module-444e7585b738#.apmx8akep)
*   新的开源 JS 项目清单

## 分享这篇文章