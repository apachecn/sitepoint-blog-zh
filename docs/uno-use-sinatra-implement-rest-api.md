# Uno！使用 Sinatra 实现 REST API

> 原文：<https://www.sitepoint.com/uno-use-sinatra-implement-rest-api/>

![Card game](img/eb4be3926b5e8ea3b67846d471006647.png)

表述性状态转移(REST)体系结构提供了一种非常方便的机制来在客户机和服务器之间传送数据。Web 服务和协议，像 HTTP，已经使用 REST 架构很多年了，所以它是一个经过良好测试的成熟概念。

Sinatra 可以用来有效地实现 REST 架构。Sinatra 的 DSL(领域特定语言)似乎是为 REST 精神定制的。事实上，Sinatra 的路由语言包括了 HTTP 使用的相同动词，包括`GET`、`POST`、`PUT`和`DELETE`。因为这些 REST API 事务的外观和行为非常像 HTTP，所以网络设备将以几乎相同的方式对待您的 REST 事务。

在本文中，我们将研究使用 Sinatra 在服务器端实现 REST。我们还将看一个简单的客户端 REST 应用程序来验证 Sinatra 服务器实现。我们将使用一个简单的纸牌游戏进行演示，其中一副纸牌将由服务器保存并分发给四个客户端。最后，我们将看到创建跨客户机和服务器运行的应用程序是多么简单，使用 REST 来帮助来回传送数据。

在我们开始之前，有一个小问题:为什么不使用 Ruby on Rails 来移动数据呢？当然，您可以使用 Rails(或任何其他 web 框架)，但是请考虑 Rails 不仅检索数据，还呈现视图。这种呈现过程会给服务器带来额外的负载，尤其是当数据高度动态且无法缓存时。例如，对于在线游戏，数据会不断变化。因此，最好让客户端呈现他们自己的视图，而不是每次玩家移动时都强迫服务器重新呈现视图。

## 建立 Sinatra

在安装 Sinatra 之前，了解它是如何工作的以及需要什么来使它工作是有帮助的。

如上所述，Sinatra 是 DSL。它本质上包装了另一个名为“Rack”的程序，让 Ruby 程序员更容易接受。例如，Sinatra 允许您编写下面的“route”，它捕获客户端对位于 http://www.example.com/getmypage.html:的页面的 HTTP `GET`请求

```
get '/getmypage.html' do
  'Hello World!'
end
```

上面的代码运行在 Sinatra 下的服务器上，指向 URL 的浏览器将检索到一个简单的“Hello World”响应。(注意，为了简单起见，我没有将响应包装在 HTML 标记中。)

Rack 提供了 Ruby web 框架(如 Sinatra 和 Rails)和实际 web 服务器(如 WEBrick 或 Thin)之间的标准接口。

在本例中，我们将使用 Sinatra + Rack + Thin 作为我们服务器的基础。

要设置这个环境，请确保您已经安装了 gems for Sinatra、Rack 和 Thin。一般你安装 Sinatra 的时候，它会安装 Rack。瘦，不会自动安装，所以你需要手动安装。

```
$ gem install sinatra
$ gem install thin
```

一旦安装了 Sinatra，你只需要在你的源文件中`require`它。它将自动使用瘦 web 服务器。

您可以运行这个非常简单的文件`myapp.rb`，如下所示，并在本地测试它。(这个测试文件直接来自 GitHub Sinatra 页面。)

```
# myapp.rb
require 'sinatra'
get '/' do
  'Hello World'
end
```

使用以下命令行运行该文件:

```
ruby myapp.rb
```

然后，您可以在本地 web 浏览器 http://localhost:4567 上查看结果

请注意，TCP 套接字被自动分配给端口 4567。您可以在部署应用程序时将其更改为标准端口 80。

还要注意，作为 DSL，Sinatra 允许你实现`PUT`、`POST`和`DELETE`以及`GET`动词。例如，我们可以如下处理客户的`POST`请求:

```
post '/join' do
  # process the POST command here ...
end
```

上面的代码可以放在`myapp.rb`文件中，就在前面的`get`路径下面。Sinatra 例程只是从头到尾扫描文件，查找动词并相应地处理它们。

请访问 Sinatra 网站,获取优秀的文档以及开始使用这种轻量级且高效的 web DSL 的技巧。

## 纸牌游戏

我们将实现一个名为 Uno 的娱乐性纸牌游戏的开始，使用所有的纸牌，包括两个小丑。这个游戏的主要目的是说明 Sinatra REST API 的作用，所以我们不会完全开发游戏逻辑。

我们将一副牌创建为一个字符串数组，每个字符串明确地描述一张牌。例如，“4-diamond”代表 4-of-diamond 牌。

我们将为服务器创建三个打牌功能:

1.  `join_game` —允许玩家加入游戏
2.  `deal` —洗牌并向每位玩家发牌
3.  `get_cards` —向玩家展示分发的牌

我们将游戏限制在四个玩家，服务器一次只能运行一个游戏。

为了实现所有这些逻辑，我们将开发两个文件:

1.  `uno-server.rb` —包含游戏玩家逻辑以及 Sinatra REST API 服务器代码
2.  `uno-client.rb` —包含客户端播放器逻辑

卡牌游戏的核心运行在`UnoServer`类中，如下所示。注意，这个类完全独立于服务器，尽管服务器代码将放在同一个文件中。

```
# Ruby Uno Server
require 'sinatra' 
require 'json' 

class UnoServer 
  attr_reader :deck, :pool, :hands, :number_of_hands 
  MAX_HANDS = 4 
  def initialize 
    @hands = Array.new 
    @number_of_hands = 0 
    @pool = Array.new 
    @deck = %w(2-diamond 3-diamond 4-diamond 5-diamond 6-diamond 7-diamond 8-diamond 9-diamond 10-diamond) 
    @deck.concat %w(2-heart 3-heart 4-heart 5-heart 6-heart 7-heart 8-heart 9-heart 10-heart) 
    @deck.concat %w(2-club 3-club 4-club 5-club 6-club 7-club 8-club 9-club 10-club) 
    @deck.concat %w(2-spade 3-spade 4-spade 5-spade 6-spade 7-spade 8-spade 9-spade 10-spade) 
    @deck.concat %w(jack-diamond jack-heart jack-club jack-spade) 
    @deck.concat %w(queen-diamond queen-heart queen-club queen-spade) 
    @deck.concat %w(king-diamond king-heart king-club king-spade) 
    @deck.concat %w(ace-diamond ace-heart ace-club ace-spade) 
    @deck.concat %w(joker joker) 
  end 

  def join_game player_name 
    return false unless @hands.size < MAX_HANDS 
    player = { 
        name: player_name, 
        cards: [] 
    } 
    @hands.push player 
    true 
  end 

  def deal 
    return false unless @hands.size > 0 
    @pool = @deck.shuffle 
    @hands.each {|player| player[:cards] = @pool.pop(5)} 
    true 
  end 

  def get_cards player_name 
    cards = 0 
    @hands.each do |player| 
      if player[:name] == player_name 
        cards = player[:cards].dup 
        break 
      end 
    end 
    cards 
  end 

end
```

注意第 2 行中对 Sinatra 库的调用。

卡片组在第 12 行到第 20 行构建成一个大数组。该副牌的洗牌版本将被放入`pool`阵列，从中发牌。

玩家在第 23 行使用`join_game`方法加入游戏。玩家提供一个名字，玩家数量限制在四个(`MAX_HANDS`)。

在第 33 行使用`deal`方法发牌。本质上，甲板被洗牌，结果被放入池中。然后，每位玩家从牌库中抽取五张牌。

最后，玩家可以在第 40 行使用`get_cards`方法查看他们的牌。他们只需要表明自己的身份，就会得到一份身份证复印件。

这个文件的后半部分包含了激动人心的部分:Sinatra REST API 的实现。

```
uno = UnoServer.new 

###### Sinatra Part ###### 

set :port, 8080 
set :environment, :production 

get '/cards' do 
  return_message = {} 
  if params.has_key?('name') 
    cards = uno.get_cards(params['name']) 
    if cards.class == Array 
      return_message[:status] == 'success' 
      return_message[:cards] = cards 
    else 
      return_message[:status] = 'sorry - it appears you are not part of the game' 
      return_message[:cards] = [] 
    end 
  end 
  return_message.to_json 
end 

post '/join' do 
  return_message = {} 
  jdata = JSON.parse(params[:data],:symbolize_names => true) 
  if jdata.has_key?(:name) && uno.join_game(jdata[:name]) 
    return_message[:status] = 'welcome' 
  else 
    return_message[:status] = 'sorry - game not accepting new players' 
  end 
  return_message.to_json 
end 

post '/deal' do 
  return_message = {} 
  if uno.deal 
    return_message[:status] = 'success' 
  else 
    return_message[:status] = 'fail' 
  end 
  return_message.to_json 
end
```

注意，在第 53 行，我们将一个 Uno 游戏实例化为`uno`对象。后面几行中的 Sinatra REST API 将使用这个对象。

第 57 行和第 58 行允许我们确定这个实现的环境。我们希望使用 TCP 端口 8080，并且我们设置了一个生产环境。生产环境以提供更少的调试信息为代价来优化速度。

REST API 处理纸牌游戏中设置的三个主要功能:添加玩家(`join`)、发牌(`deal`)和查看纸牌(`cards`)。每个都有自己的网址。

注意在这个实现中使用了`GET`和`POST`动词。当用户想要查看他们的卡时，他们使用 URL**http://localhost:8080/cards**发出一个`GET`命令。这个`GET`命令包括一个 JSON 结构，表示玩家的名字。代码简单地调用本地`uno`对象的`get_cards`方法来获取卡片。如果`get_cards`返回一个数组，该数组将被回传给用户。如果没有返回一个数组，那么这个用户就不是这个游戏的玩家，所以会返回一个“抱歉”的消息。

当玩家想要加入游戏时，他们向 URL**http://localhost::8080/join**发出`POST`命令。如果当前游戏中有空位，则允许用户加入。如果没有，则返回“抱歉”消息。

当玩家想要发牌时，他们向 URL**http://localhost:8080/deal**发出`POST`命令。与上面的场景类似，本地的`uno`对象被发送一个命令来洗牌并给每个玩家发五张牌。如果游戏中当前没有玩家，它可能会失败。

在上述所有情况下，基于 JSON 的值都返回给玩家。

我们现在来看看客户端代码，看看客户端 REST API 是如何实现游戏环境的。

```
# Ruby Uno Client 

require 'json' 
require 'rest-client' 

class UnoClient 
  attr_reader :name 

  def initialize name 
    @name = name 
  end 

  def join_game 
    response = RestClient.post 'http://localhost:8080/join', :data => {name: @name}.to_json, :accept => :json 
    puts JSON.parse(response,:symbolize_names => true) 
  end 

  def get_cards 
    response = RestClient.get 'http://localhost:8080/cards', {:params => {:name => @name}} 
    puts response 
  end 

  def deal 
    response = RestClient.post 'http://localhost:8080/deal', :data =>{}.to_json, :accept => :json 
    puts response 
  end 

end
```

可以看出，客户端通信看似简单。我们使用`rest-client` Ruby 库来创建最小功能的方法，模拟服务器上的游戏方法。

我们在实例化`UnoClient`类时为这个播放器提供了一个名称。此后，我们可以加入游戏，发牌，并得到我们的卡的副本。

加入和处理需要`POST`命令，如第 13 行和第 23 行所示。

获取卡片需要一个`GET`命令，如第 18 行所示。

注意，我们只是打印出服务器的响应，它以 JSON 格式发送给我们。一个完全开发的游戏显然会读取返回的 JSON 结构，并在浏览器上提供相应的结果。

下面的清单显示了当我们实例化客户端时会发生什么。使用 irb，为 Bob、Carol、Ted、Alice 和 Ralph 手动创建五个玩家对象。请注意当可怜的拉尔夫试图加入游戏时的失败消息。此外，请注意，每个玩家都有一套独特的纸牌。所有的操作都发生在服务器上，只有结果通过 API 发送回客户端。看来 Sinatra REST API 正在工作！

```
> require './uno-client.rb'
> bob_uno = UnoClient.new 'bob'
> carol_uno = UnoClient.new 'carol'
> ted_uno = UnoClient.new 'ted'
> alice_uno = UnoClient.new 'alice'
> ralph_uno = UnoClient.new 'ralph'
> bob_uno.join_game
{:status=>"welcome"}
> carol_uno.join_game
{:status=>"welcome"}
> ted_uno.join_game
{:status=>"welcome"}
> alice_uno.join_game
{:status=>"welcome"}
> ralph_uno.join_game
{:status=>"sorry - game not accepting new players"}
> bob_uno.deal
{"status":"success"}
> bob_uno.get_cards
{"cards":["3-diamond","2-club","4-spade","9-diamond","8-spade"]}
> carol_uno.get_cards
{"cards":["9-club","5-spade","king-spade","6-club","7-heart"]}
> ted_uno.get_cards
{"cards":["4-heart","6-heart","2-spade","8-club","ace-heart"]}
> alice_uno.get_cards
{"cards":["4-club","7-club","3-club","king-heart","jack-club"]}
```

## 摘要

REST 架构提供了一种非常方便的机制来在客户机和服务器之间移动数据。当您需要在处理高度动态的数据时减少 web 服务器上的工作负载时，这一点尤其重要。正如本文中的例子所展示的，Sinatra 库可以用来以一种非常简单直接的方式实现 REST 架构。

## 分享这篇文章