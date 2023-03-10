# 查看 XMPP 和 XMPP4R Gem

> 原文：<https://www.sitepoint.com/looking-xmpp-xmpp4r-gem/>

![XMPP](img/85f471d8bee983f2b98ea782784e6347.png)

XMPP 官方标志

> XMPP 是“一个开放的、受 XML 启发的协议，用于接近实时的、可扩展的即时消息和在线信息。”— [维基百科](http://en.wikipedia.org/wiki/Extensible_Messaging_and_Presence_Protocol)
> 
> 可扩展消息和在线状态协议(XMPP)是一种用于实时通信的开放式技术，它支持广泛的应用程序，包括即时消息、在线状态、多方聊天、语音和视频呼叫、协作、轻量级中间件、内容联合和 XML 数据的通用路由。—[xmpp.org](http://xmpp.org/about-xmpp/)

e **X** 可扩展 **M** 消息和 **P** resence **P** 协议，也称为 Jabber(该协议最初被命名为 Jabber，由 Jabber 开源社区开发)是一种在两个实体之间交换消息的协议。这些消息以 XML 格式通过网络传输。Jabber、Google Talk、Google Wave、Pidgin、Kopete 和各种开源即时消息应用程序都使用它。

### XMPP 的优势

#### 分散的

XMPP 网络本质上是分散的。没有主服务器或中央服务器。任何人都可以拥有或运行 XMPP 服务器。

#### 开放标准

互联网工程任务组已经将 XMPP 正式确定为一种被认可的即时消息和存在技术，命名为 XMPP(最新的规范是 RFC 6120 和 RFC 6121)。实现对这些规范的支持不需要版税，它们的开发也不依赖于单一的供应商。

#### 稳定安全

XMPP 从 1999 年开始生效，此后许多应用程序都使用这个协议。因为 XMPP 服务器可以被隔离或者托管在安全防火墙后面的私有网络上，所以它非常安全。

#### 灵活的

这个协议的主要优点之一是它的灵活性。自定义功能可以非常容易地构建在 XMPP 之上。该协议的灵活性使其成为编写即时消息程序、聊天客户端等的完美选择。，因为它将自动与 XMPP 提供的所有 IM 服务一起工作。

## XMPP 4 r——XMPP 的 Ruby 客户端库

XMPP4R 是 XMPP/Jabber/Ejabberd 使用的标准 XML 的 Ruby 包装器，因此允许我们使用 Ruby 而不是生成 XML。

### 装置

```
gem install xmpp4r
    or
sudo gem install xmpp4r
```

## XMPP 服务器

Ejabberd 是一个使用 Erlang 构建的 Jabber/XMPP 服务器，并且是开源的。Ejabberd 的另一个流行的替代品是 Openfire。ejabbered 的安装说明可在[这里](http://www.process-one.net/docs/ejabberd/guide_en.html)找到。在这个例子中，我们不会使用像 ejabbered 或 Openfire 这样的 XMPP 服务器，而是选择一个简单方便的 Google 帐户。

## XMPP 机器人

构建一个 Bot 是引入 XMPP 协议和 XMPP–XMPP 4 r 的 Ruby 包装器的理想方式。因此，我将建立一个机器人，响应我们的消息，并回复自定义消息或结果。

### 安装所需的 Gems 库

```
gem install xmpp4r
```

### 连接到 Jabber 服务器

```
require 'xmpp4r'
require 'xmpp4r/client'
include Jabber

class Bot

  attr_reader :client

  def initialize jabber_id
    @jabber_id = jabber_id
    @jabber_password = ENV['jabber_password']
  end

  def connect
    jid = JID.new(@jabber_id)
    @client = Client.new jid
    @client.connect
    @client.auth @jabber_password
    @client.send(Presence.new.set_type(:available))
    puts "Hurray...!!  Connected..!!"
  end
end

Bot.new('neo@codingarena.in').connect
```

上面的代码说明了如何连接到我们的 Jabber 服务器，或者在我们的例子中，连接到 Google 的服务器。
实例化一个新的`Bot`实例时会提供 Jabber ID，并且从环境变量中检索密码。`connect`方法创建 Jabber 客户机的一个新对象，连接到服务器，并使用指定的密码进行认证。此外，在线状态被设置为“可用”。其他可以通过的选项有`:busy`、`:invisible`。

### 发送和接收消息

发送消息非常简单，如下所示:

```
message = Message.new("manu@codingarena.in", "Hello... Manu?")
message.type=:chat
@client.send(message)
```

`add_method_callback`方法可用于接收和处理我们的机器人接收到的消息。

```
@client.add_message_callback do |message|
  unless message.body.nil? && message.type != :error
    puts "Received message: #{message.body}"
    #Echo the received message back to the sender.
    reply = Message.new(message.from, message.body)
    reply.type = message.type
    @client.send(reply)
  end
end
```

### 接受新邀请

```
def roster
  @roster ||= Roster::Helper.new(@client)
end

def process_invite
  roster.add_subscription_request_callback do |_, presence|
    inviter = JID::new(presence.from)
    roster.accept_subscription(inviter)
    invite(JID::new(inviter))
  end
end
```

是我们机器人的联系人列表。每当一个新用户添加我们的机器人并发送邀请时，`roster_add_subscription_request_callback`就会被触发，它会自动接受新的邀请。

### 教我们的机器人一些命令

已经使用 XMPP 构建了一个简单的基本 bot，现在我们将尝试处理 bot 收到的消息，并做一些小的改动。

不如我们问我们的机器人一些问题或命令，看看它是否用自定义或经过处理的回复来响应我们的消息？我们的聊天机器人收到的消息可以被处理，以便它可以回复一些特定的命令。例如，我们的机器人将处理“帮助”以返回我们的机器人知道的所有命令，或者处理“时间”以回复当前时间。

```
@client.add_message_callback do |message|
  unless message.body.nil? && message.type != :error
    reply = case message.body
      when "Time" then reply(message, "Current time is #{Time.now}")
      when "Help" then reply(message, "Available commands are: 'Time', 'Help', 'Latest sitepoint articles.")
      when "Latest sitepoint articles" then latest_sitepoint_articles(message)
      else reply(message, "You said: #{message.body}")
    end
  end
end

def reply message, reply_content
  reply_message = Message.new(message.from, reply_content)
  reply_message.type = message.type
  @client.send reply_message
end

def latest_sitepoint_articles message
  feeds = SitepointFeedParser.get_feeds
  feeds.items.each do |item|
    reply(message, item.link)
  end
end

class SitepointFeedParser
  URL = 'https://www.sitepoint.com/feed/'
  class << self
    def get_feeds
      open(URL) do |rss|
        @feeds = RSS::Parser.parse(rss)
      end
      @feeds
    end
  end
end
```

当用户发送“最新的 sitepoint 文章”时，我们的机器人将解析来自 sitepoint 的提要，并返回最新文章的链接。RSS 模块用于解析提要，对提要进行迭代，并将每个链接返回给用户。

### 将机器人妖魔化

```
require 'rubygems'
require 'daemons'

Daemons.run('bot.rb',
  {
    app_name: 'myIMBot',
    monitor: true,
    log_output: false
  }
)
```

使用[守护进程](http://daemons.rubyforge.org/) gem，我们将我们的机器人作为守护进程运行。它还为我们提供了分别使用`ruby im.rb start`、`ruby im.rb restart`和`ruby im.rb stop`来启动、重启和停止守护进程的选项。我们可以使用`ruby im.rb status`获得守护进程的当前状态。

此外，monit 为我们提供了监控守护进程的选项。如果`log_output`设置为 true，则可以记录守护程序的输出。

## 把所有的放在一起

```
require 'xmpp4r'
require 'xmpp4r/client'
require 'xmpp4r/roster'
require 'rss'
require 'open-uri'
include Jabber

class Bot

  attr_reader :client

  def initialize jabber_id
    @jabber_id = jabber_id
    @jabber_password = ENV['jabber_password']
  end

  def connect
    jid = JID.new(@jabber_id)
    @client = Client.new jid
    @client.connect
    @client.auth @jabber_password
    @client.send(Presence.new.set_type(:available))
    puts "Hurray...!!  Connected..!!"
    on_message
  end

  private
  def on_message
    mainthread = Thread.current
    @client.add_message_callback do |message|
      unless message.body.nil? && message.type != :error
        reply = case message.body
          when "Time" then reply(message, "Current time is #{Time.now}")
          when "Help" then reply(message, "Available commands are: 'Time', 'Help'.")
          when "Latest sitepoint articles" then latest_sitepoint_articles(message)
          else reply(message, "You said: #{message.body}")
        end
      end
    end
    Thread.stop
    @client.close
  end

  def reply message, reply_content
    reply_message = Message.new(message.from, reply_content)
    reply_message.type = message.type
    @client.send reply_message
  end

  def latest_sitepoint_articles message
    feeds = SitepointFeedParser.get_feeds
    feeds.items.each do |item|
      reply(message, item.link)
    end
  end

end

class SitepointFeedParser

  URL = 'https://www.sitepoint.com/feed/'
  class << self
    def get_feeds
      open(URL) do |rss|
        @feeds = RSS::Parser.parse(rss)
      end
      @feeds
    end
  end

end

@bot = Bot.new 'manusajith@gmail.com'
@bot.connect
```

代码可以在[这里](https://github.com/manusajith/xmpp4r-bot)找到。

## 结论

如果您没有尝试过 XMPP 或 XMPP4R，我建议您尝试一下。XMPP4R 有很多其他选项，本教程没有涉及到。详情请查看官方[网站](http://xmpp4r.github.io)。

PS:我是 xmpp4r 目前的维护者之一，如果有什么问题，就喊我一声。此外，如果您发现任何错误/问题，请[报告它们](https://github.com/xmpp4r/xmpp4r/issues)。提前感谢。

## 分享这篇文章