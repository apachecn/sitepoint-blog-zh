# 创建一个 Slack 机器人与你的维基互动

> 原文：<https://www.sitepoint.com/create-a-slack-bot-to-interact-with-your-wiki/>

![Slackbots](img/a44fb0107bd2f834f4f4b9511088feb4.png)

Wiki 平台已经成为许多团队的“首选”工具。它们也被许多开源项目用作交流和组织的媒介。

在过去的几年里，Slack 已经发展到许多公司使用工具的阶段。它的关键目标是团队成员之间的有效沟通。除了聊天，Slack 还提供应用程序，让你可以轻松连接到其他服务，如 Trello、MailChimp、Blue Jeans 等。但应用集成并不是 Slack 提供的唯一功能。今天我们将看一些不同的东西。聊天应用中的机器人并不是一个新事物，它们可以追溯到 IRC 时代，那时我们会有机器人来处理不同的事情，比如聊天不当行为、天气预报、游戏等等。同样的概念也适用于 Slack。

在这篇文章中，我们将研究一个维基机器人。你的团队有时会需要机器人的帮助，对吗？

我们将使用的维基平台是 MediaWiki。Mediawiki 是一个开源的 wiki 包，最初起源于维基百科。这是最常用的维基系统之一。

## 设置

我们将在本教程中使用两个主要的 Ruby gem:[media-wiki-API](https://github.com/wikimedia/mediawiki-ruby-api)和 [slack-ruby-bot](https://github.com/dblock/slack-ruby-bot) 。我们还想让我们的机器人生活在某个地方，所以让我们使用 Sinatra 框架并在 Heroku 上托管我们的机器人。

首先创建一个名为 **slack-wikibot** 的目录，并在其中创建两个 Ruby 文件，名为 **slack-wikibot.rb** 和 **Gemfile** 。在**gem 文件**中，添加:

Gemfile:

```
source 'http://rubygems.org'

gem 'slack-ruby-bot'
gem 'celluloid-io' 
gem 'puma'
gem 'sinatra'
gem 'dotenv'
gem 'mediawiki_api' 
```

让我们运行 bundle install 来安装这些 gem:

`bundle install`

创建另一个同名的目录( **slack-wikibot** )，并在该目录中创建一个名为 **bot.rb** 的 Ruby 文件。 **bot.rb** 文件看起来像:

bot.rb:

```
module SlackWikibot
  class Bot < SlackRubyBot::Bot
  end
end 
```

创建一个 **web.rb** 文件，该文件将保存我们的 web 应用程序，并使其易于托管 bot。像 Heroku 和 OpenShift 这样的平台使得部署 Sinatra 应用程序变得非常容易。

web.rb:

```
require 'sinatra/base'

module SlackWikibot
  class Web < Sinatra::Base
    get '/' do
      'Bot is running...'
    end
  end
end 
```

## MediaWiki

要通过 API 创建一篇文章，我们首先需要指向我们的 MediaWiki API URL:

```
wiki_connection = MediawikiApi::Client.new "http://MEDIAWIKI-ADDRESS/api.php" 
```

我们使用`create_page`方法创建一篇文章，它需要两个参数:文章的标题和内容。

```
wiki_connection.create_page "SitePoint", "Some basic info about SitePoint" 
```

![Wiki article created](img/b03f2fa32fb72db657d995f9a9612202.png)

这创建了一个页面，但是当这个代码直接从脚本中运行时，我们得不到任何反馈。我们可以使用一个对象来访问经过解析的 MediaWiki API 响应。

输入:

```
response = wiki_connection.create_page "SitePoint", "Some basic info about SitePoint" 
```

如果我们现在在`response`上调用`data`方法，我们会得到一个很好的散列。

```
{"result"=>"Success", "pageid"=>26, "title"=>"SitePoint", "contentmodel"=>"wikitext", "nochange"=>""} 
```

现在我们可以访问新维基文章的`pageid`。我们可以做一个简单的查询`http://MediaWiki-Address/?curid=26`，这将返回文章。

让我们来看看另一种方法是用来删除我们的文章。`delete`方法需要两个参数:页面的标题和删除文章的原因。由于 MediaWiki 提供了修订版，可以让你回到过去，所以有一个删除的理由是非常有用的。

```
wiki_connection.delete_page "SitePoint", "Reason for deletion" 
```

要使用这种方法，您需要有一个帐户并登录。这很简单:

```
wiki_connection.log_in "username", "password" 
```

现在无论我们在哪里使用 **wiki_connection** 对象，我们都被认证了。如果您仍然无权删除文章，这意味着您不在允许此类操作的组中。即使我是 MediaWiki 安装的第一个用户，我也有这个问题。在这种情况下，你需要前往**特殊:用户权利**页面，将自己添加到**官僚**和**系统操作员**组。

现在创建一个名为 **commands** 的目录，我们将在其中添加 SlackWikiBot 将使用的命令。在**命令**目录中，创建一个名为 **wiki.rb** 的新文件。这个新文件的路径是**slack-wiki bot/slack-wiki bot/commands/wiki . Rb**。

这是我们的命令生活的地方，它能以许多不同的方式被构造。我建议你看一下 slack-ruby-bot gem 的文档。我使用的是`match`操作符，但是也有其他的方式，比如`command`和`scan`。在这种情况下，我们的机器人正在监视我们的聊天，只要它看到我们使用了**！维基创建**或者**！维基删除**关键词，它会执行一些动作。

维基. rb:

```
require 'mediawiki_api'
module SlackWikibot
  module Commands
    class Wiki < SlackRubyBot::Commands::Base
      wiki_connection = MediawikiApi::Client.new "http://MediaWiki-URL/api.php"
      wiki_connection.log_in "username", "password"
      match(/^!wiki create (?<titlewiki>\w*)\s(?<contentwiki>.*)$/) do |client, data, match |
        response = wiki_connection.create_page match[:titlewiki], match[:contentwiki]
        pageid = response.data["pageid"]
        client.say(channel: data.channel, text: "Your wiki is created http://MediaWiki-URL?curid=#{pageid}")
      end
      match(/^!wiki delete (?<titlewiki>\w*)\s(?<reason>.*)$/) do |client, data, match |
        wiki_connection.delete_page match[:titlewiki], match[:reason]
        client.say(channel: data.channel, text: "Article #{match[:titlewiki]} page is deleted. Reason: #{match[:reason]}")
      end
    end
  end
end 
```

让我们看看我们的项目结构:

```
└── slack-wikibot
    ├── Gemfile
    ├── Gemfile.lock
    ├── slack-wikibot.rb
    └── slack-wikibot
        ├── bot.rb
        └── commands
            └── wiki.rb 
```

现在编辑我们在根目录中创建的 **slack-wikibot.rb** ，并需要 slack-ruby-bot gem。这样，我们的 bot 和 wiki 命令就创建好了。

slack-wikibot.rb:

```
require 'slack-ruby-bot'
require 'slack-wikibot/bot'
require 'slack-wikibot/commands/wiki' 
```

为我们的 Rack 应用程序创建一个 **config.ru** 文件。我们将为 bot 创建一个线程并运行 web 服务器:

config.ru

```
$LOAD_PATH.unshift(File.dirname(__FILE__))

require 'dotenv'
Dotenv.load

require 'slack-wikibot'
require 'web'

Thread.abort_on_exception = true

Thread.new do
  begin
    SlackWikibot::Bot.run
  rescue Exception => e
    STDERR.puts "ERROR: #{e}"
    STDERR.puts e.backtrace
    raise e
  end
end

run SlackWikibot::Web 
```

让我们使用 Foreman，因为它允许我们轻松地管理我们的运行时依赖。Foreman 在我们有环境变量时也很有用，比如在**中。env** 文件。此外，Heroku 还使用 Foreman。

我们需要一个来自 Slack 的 API 密钥，这样我们就可以在聊天中与机器人进行交互。当您登录到您的 slack 团队时，请访问[机器人服务](https://my.slack.com/services/new/bot)页面。第一步是为我们的机器人设置一个用户名:

![slackbot_username](img/b467409d163756708b5a09abeb52d492.png)

单击“添加机器人集成”按钮后，下一步是添加关于我们的机器人的信息。我们可以设置一个名称，上传一个图像，并设置一些其他属性。此外，所需的 API 密钥也在此页面上:

![Slack API Key](img/4b84602366eb59d432aadd96d32839e3.png)

创建一个**。env** 文件并添加 API 键:

```
SLACK_API_TOKEN=xoxb-68226463922-ceoVi1mZgGIQJxuVD6Gpu2CV 
```

在根目录中创建一个 **Procfile** ，Foreman 将在您运行以下命令时使用它:

轮廓

```
web: bundle exec puma -p $PORT 
```

现在让我们运行我们的机器人:

```
foreman start 
```

## 部署到 Heroku

您需要安装 Heroku 工具:

```
gem install heroku 
```

使用 Heroku 命令创建应用程序:

```
heroku create 
```

它将分配一个随机名称，并添加一个 git 远程存储库。现在把你的代码推到 Heroku 仓库。

**注意**:这部分你必须使用 git，所以如果你还没有，确保在推送前`git add`和`git commit`你的代码。

```
git push heroku master 
```

现在，如果您访问您的 Heroku 应用程序 URL(您的会有所不同):

```
https://agile-retreat-96798.herokuapp.com/ 
```

您应该会看到一个页面，显示 **Bot 正在运行……**

回到 Slack，使用`/invite @YourBotsName`邀请你的机器人进入频道。然后，尝试我们创建的命令之一:

![Wiki-bot_in_action](img/d1b978012088739d7ce6cb9ee1f018fe.png)

## 结论

机器人是一种非常方便的工具，可以使我们的一些工作自动化。你可以在 SitePoint 的[这篇文章中找到更多关于制作 Slack 机器人的信息。](https://www.sitepoint.com/building-a-slackbot-with-ruby-and-sinatra/)

每当你发现自己在做一件重复性的工作时，就想一想这是否可以由一个机器人来完成。如果答案是肯定的，那么没有时间可以浪费了，开始创造一个吧。让机器人做我们的工作有很多好处:它们速度快，很少甚至没有错误，这只是举几个例子。现在是时候接受我们新的基于 Slack-bot 的领主了:)

## 分享这篇文章