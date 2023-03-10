# 使用 PHP Web Sockets 构建 Live-score 小部件

> 原文：<https://www.sitepoint.com/building-live-score-widget-using-php-web-sockets/>

web sockets 的引入使得 web 应用程序能够处理接近实时的数据，而无需求助于诸如长轮询之类的“黑客手段”。

需要最新数据的应用程序的一个例子是体育比分。即使现在，许多显示这些信息的网站使用 Flash 应用程序，因为 Actionscript 提供了通过基于套接字的连接进行通信的工具。然而，web 套接字允许我们仅使用 HTML 和 Javascript 来复制这种功能。这就是我们在本教程中要构建的东西，还有一个 PHP 的轻量级“服务器”。

![image](img/5ddb692c802e9ba09f1f50d79a03de42.png)

## 安装和设置

我们将围绕 [Ratchet 库](http://socketo.me/)来举例，它提供了 web sockets 的 PHP 实现。

创建下面的`composer.json`文件，它既安装了这个依赖项，又为我们将要编写的代码设置了一个自动加载器:

```
{
    "require": {
        "cboden/Ratchet": "0.2.*"
    },
    "autoload": {
        "psr-0": {
            "LiveScores": "src"
        }
    }    
}
```

现在设置目录结构:

```
[root]
    bin
    src
        LiveScores
    public
        assets
            css
                vendor
            js
                vendor
    vendor
```

您可能想要克隆[库](https://github.com/phpmasterdotcom/LukasWhite-LiveScores)，它包含许多 CSS / JS / image 资产，以及本教程中的所有代码。如果您想按照本文从头开始构建它，那么您需要做的就是将克隆/下载包中的`public/assets/*/vendor`文件夹复制到您自己的适当位置。

当然，不要忘记运行`php composer.phar update`，如果您没有安装 composer，那么在运行之前运行`curl -sS https://getcomposer.org/installer | php`。

我们将从构建一个驻留在服务器上的类开始，它充当一种消息代理——接受连接和发送消息。稍后，我们还将使用它来维护正在进行的游戏的信息。这是一个框架实现，展示了通用消息代理可能如何操作:

```
// src/LiveScores/Scores.php

<?php namespace LiveScores;

use Ratchet\MessageComponentInterface;
use Ratchet\ConnectionInterface;

class Scores implements MessageComponentInterface {

    private $clients;    

    public function __construct() 
    {    
        $this->clients = new \SplObjectStorage;
    }

    public function onOpen(ConnectionInterface $conn) 
    {
        $this->clients->attach($conn);
    }

    public function onMessage(ConnectionInterface $from, $msg) 
    {            
        foreach ($this->clients as $client) {
            if ($from !== $client) {
                // The sender is not the receiver, send to each client connected
                $client->send($msg);
            }
        }
    }

    public function onClose(ConnectionInterface $conn) 
    {
        $this->clients->detach($conn);
    }

    public function onError(ConnectionInterface $conn, \Exception $e) 
    {     
        $conn->close();
    }

}
```

需要注意的要点；

*   该类需要实现`MessageComponentInterface`来充当“消息代理”
*   我们将所有连接到服务器的客户端列表作为一个集合进行维护
*   当客户端连接时，`onOpen`事件被触发，我们将客户端添加到我们的集合中
*   当客户端断开连接时，我们做相反的事情
*   该接口还要求我们实现一个简单的错误处理程序(`onError`)

接下来，我们需要创建一个服务器守护进程来实例化我们的新类，并开始监听连接。创建以下文件:

```
// bin/server.php

<?php
use Ratchet\Server\IoServer;
use Ratchet\WebSocket\WsServer;
use LiveScores\Scores;

require dirname(__DIR__) . '/vendor/autoload.php';

$server = IoServer::factory(
    new WsServer(
        new Scores()
    )
    , 8080
);

$server->run();
```

这应该是不言自明的；`WsServer`是更通用的`IoServer`的实现，它使用 web 套接字进行通信，我们将它设置为在端口 8080 上监听。当然，你可以自由选择不同的端口——只要它没有被你的防火墙阻止——但是 8080 通常是一个非常安全的选择。

## 保持状态

我们将让服务器跟踪游戏的当前状态；不需要将它提交到存储中，我们只是将它保存在内存中以获得最佳性能。每次一个游戏中发生一个事件，我们将更新服务器上的分数，然后将事件广播给所有收听的客户端。

不过，首先，我们需要生成 fixtures(即游戏列表)。为了简单起见，我们将随机进行，并在守护进程执行期间保持这组设备活动。

```
// src/LiveScores/Fixtures.php
<?php namespace LiveScores;

class Fixtures {

    public static function random()
    {
        $teams = array("Arsenal", "Aston Villa", "Cardiff", "Chelsea", "Crystal Palace", "Everton", "Fulham", "Hull", "Liverpool", "Man City", "Man Utd", "Newcastle", "Norwich", "Southampton", "Stoke", "Sunderland", "Swansea", "Tottenham", "West Brom", "West Ham");

        shuffle($teams);

        for ($i = 0; $i <= count($teams); $i++) {
            $id = uniqid();
            $games[$id] = array(
                'id' => $id,
                'home' => array(
                    'team' => array_pop($teams),
                    'score' => 0,
                ),
                'away' => array(
                    'team' => array_pop($teams),
                    'score' => 0,
                ),
            );
        }

        return $games;
    }

} 
```

请注意，我们为每个游戏分配了一个唯一的标识符，稍后我们将使用它来指示事件发生在哪个游戏中。回到我们的`Scores`课:

```
// src/LiveScores/Scores.php

public function __construct() {

    // Create a collection of clients
    $this->clients = new \SplObjectStorage;

    $this->games = Fixtures::random();
}
```

因为客户可以在游戏的任何阶段调用我们的小部件，所以他们获得最新的信息是很重要的。一种方法是简单地“回复”一个新的连接请求，方法是发送游戏的当前状态，然后在客户端呈现游戏列表及其分数。

下面是`onOpen`实现，它就是这样做的:

```
// src/LiveScores/Scores.php

public function onOpen(ConnectionInterface $conn) {
    // Store the new connection to send messages to later
    $this->clients->attach($conn);

    // New connection, send it the current set of matches
    $conn->send(json_encode(array('type' => 'init', 'games' => $this->games)));

    echo "New connection! ({$conn->resourceId})\n";
}
```

注意，我们发送的消息实际上是一个 JSON 对象，事件类型被设置为一个属性。使用 JSON 发送消息没有要求——你可以发送任何你想要的格式——但是这样做允许我们发送不同类型的结构化消息。

## HTML

因为我们将通过 web socket 加载当前分数，并使用 Javascript 呈现它们，所以页面的 HTML 非常简单:

```
<div id="scoreboard">

    <table>

    </table>

</div>
```

渲染后，评分表中的一行将如下所示:

```
<tr data-game-id="SOME-IDENTIFIER">
    <td class="team home">
        <h3>HOME TEAM NAME</h3>
    </td>
    <td class="score home">
        <div id="counter-0-home"></div>
    </td>
    <td class="divider">
        <p>:</p>
    </td>
    <td class="score away">
        <div id="counter-0-away"></div>
    </td>
    <td class="team away">
        <h3>AWAY TEAM NAME</h3>
    </td>
</tr>
```

`counter-*-*`元素是一个 JS 插件的占位符，我们稍后将使用它来呈现一个奇特的分数小部件。

## JavaScript

现在让我们开始构建 JS。首先要做的是打开一个 web 套接字:

```
var conn = new WebSocket('ws://localhost:8080');
```

根据您的“服务器”运行的位置，您可能需要替换主机名和/或端口号。

接下来，将一个事件处理程序附加到该连接，每当收到消息时都会触发该事件处理程序:

```
conn.onmessage = function(e) { 
```

消息本身作为事件`e`的`data`属性提供。因为我们以 JSON 格式发送消息，所以我们需要首先解析它:

```
var message = $.parseJSON(e.data);
```

现在我们可以检查`type`，并调用适当的函数:

```
switch (message.type) {
    case 'init':
        setupScoreboard(message);
        break;
    case 'goal':
        goal(message);
        break;
}
```

`setupScoreboard`函数非常简单:

```
function setupScoreboard(message) {

    // Create a global reference to the list of games
    games = message.games;

    var template = '<tr data-game-id="{{ game.id }}"><td class="team home"><h3>{{game.home.team}}</h3></td><td class="score home"><div id="counter-{{game.id}}-home" class="flip-counter"></div></td><td class="divider"><p>:</p></td><td class="score away"><div id="counter-{{game.id}}-away" class="flip-counter"></div></td><td class="team away"><h3>{{game.away.team}}</h3></td></tr>';

    $.each(games, function(id){        
        var game = games[id];                
        $('#scoreboard table').append(Mustache.render(template, {game:game} ));        
        game.counter_home = new flipCounter("counter-"+id+"-home", {value: game.home.score, auto: false});
        game.counter_away = new flipCounter("counter-"+id+"-away", {value: game.away.score, auto: false});
    });

}
```

在这个函数中，我们简单地遍历游戏数组，使用 Mustache 呈现一个新行，添加到记分牌表中，并为每个游戏实例化几个动画计数器。`games`数组将存储游戏客户端的当前状态，并包含对这些计数器的引用，因此我们可以根据需要更新它们。

接下来是`goal`函数。我们通过 web 套接字接收到的表示目标的消息将是一个 JSON 对象，其结构如下:

```
{
    type: 'goal',
    game: 'UNIQUE-ID',
    team: 'home'
}
```

`game`属性包含唯一标识符，`team`或者是“家”或者是“离开”。使用这些信息，我们可以更新`games`数组中的相关分数，找到合适的计数器对象并递增它。

```
function goal(message) {    
    games[message.game][message.team]['score']++;
    var counter = games[message.game]['counter_'+message.team];
    counter.incrementTo(games[message.game][message.team]['score']);
}
```

剩下的就是某种方式来表明已经进球了。为了简单起见，我们将把它添加到客户端；点击一个队的名字将表明他们得分了。实际上，你有一个单独的应用程序或页面，但是原理是一样的。我们将简单地添加一个点击处理程序，如下所示，它通过 web 套接字发送一个简单的 JSON 消息:

```
$(function () {

    $(document).on('click', '.team h3', function(e){
        var game = $(this).parent().parent().attr('data-game-id');        
        var team = ($(this).parent().hasClass('home')) ? 'home' : 'away';
        conn.send(JSON.stringify({ type: 'goal', team: team, game: game }));
    });

});
```

服务器“监听”这些消息，如果它收到进球的消息，它就更新它的记录。收到的所有消息会立即重新广播给所有连接的客户端。

```
// src/LiveScores/Scores.php
public function onMessage(ConnectionInterface $from, $msg) {

    foreach ($this->clients as $client) {        
        $client->send($msg);            
    }

    $message = json_decode($msg);

    switch ($message->type) {
        case 'goal':
            $this->games[$message->game][$message->team]['score']++;
            break;
    }

}
```

最后，要启动并运行它，您需要从命令行启动服务器:

```
php bin/server.php
```

就是这样——试着并排打开几个窗口，点击一个团队名称来表示一个目标。您应该会立即看到记分牌更新！

## 结论

在本文中，我使用 web sockets 演示了一个简单的 HTML 和 Javascript“live scores”小部件。它有其局限性；通常你会期望看到进球者和每个进球的时间，以及额外的信息，如预订和罚下。然而，因为我们使用 JSON 对象来表示事件，所以添加这样的特性应该相对简单。本教程的一个[现场演示](http://scores.demos.lukaswhite.com/)可用。

(注意:计数器的 Javascript 和样式是由 Chris Nanney 提供的，来自[这篇文章](http://cnanney.com/journal/code/apple-style-counter-revisited/)。)

## 分享这篇文章