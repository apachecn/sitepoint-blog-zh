# 用 Flex 构建 Web 2.0 投票小部件:第二部分

> 原文：<https://www.sitepoint.com/voting-widget-flex-3-part-2/>

在 Flex 中很容易构建实用的 Flash 小部件，比如记录用户投票并显示结果的部件。在我的上一篇文章中，我展示了如何设置 Flex Builder 和创建投票计数窗口小部件。在第二篇文章中，我将向您展示如何升级这个小部件，使其不仅可以读取投票，还可以发布投票。我还将演示如何配置 Flex 项目来构建轻量级 Flash 应用程序，它非常适合作为小部件进行部署。

如果您想在家一起玩，请下载本文的[代码档案。](https://i2.sitepoint.com/examples/flex3/flex3-voter.zip)

**注意！同样，在文章的最后会有一个小测验。前 100 名完成测验的人将免费获得一本我的书《Flex 3 入门》，并送货上门。由于 Adobe 的帮助，在有限的时间内，这本书还可以获得免费的 PDF 格式。**

##### 构建投票服务

将这个小部件从一个读取投票结果的小部件升级到一个可以发布投票结果的小部件意味着升级服务器后端。为此，我们将创建一个非常简单的 MySQL 数据库，存储投票主题和每个投票主题的选项以及它们的投票数。

这是 MySQL 代码，我们用它来创建表，并用一个问题预加载它:

```
DROP TABLE IF EXISTS topics; 

CREATE TABLE topics ( 

  id INT NOT NULL AUTO_INCREMENT, 

  text VARCHAR( 255 ) NOT NULL, 

  PRIMARY KEY ( id ) ); 

DROP TABLE IF EXISTS options; 

CREATE TABLE options ( 

  id INT NOT NULL AUTO_INCREMENT, 

  topic_id INT NOT NULL, 

  text VARCHAR( 255 ), 

  votes INT NOT NULL, 

  PRIMARY KEY ( id ) ); 

INSERT INTO topics VALUES ( 0, 'Which is the best Star Wars movie?' ); 

INSERT INTO options VALUES ( 0, 1, 'Episode I', 10 ); 

INSERT INTO options VALUES ( 0, 1, 'Episode II', 20 ); 

INSERT INTO options VALUES ( 0, 1, 'Episode III', 50 ); 

INSERT INTO options VALUES ( 0, 1, 'Episode IV', 150 ); 

INSERT INTO options VALUES ( 0, 1, 'Episode V', 250 ); 

INSERT INTO options VALUES ( 0, 1, 'Episode VI', 30 ); 
```

需要使用`mysqladmin`来创建数据库，就像这样:

```
% mysqladmin --user=root create votes
```

…然后使用`mysql`命令将模式加载到其中:

```
% mysql --user=root votes < vote.sql
```

如果使用`PHPMyAdmin`管理数据库，可以使用`PHPMyAdmin`创建数据库，然后使用数据库恢复功能将这些表加载到数据库中。

下一步是升级投票系统的`AMFPHP`服务代码。完整的新代码如下所示:

```
<?php 

require_once("MDB2.php"); 

include_once(AMFPHP_BASE . "shared/util/MethodTable.php"); 

class VoteService2 

{ 

  function getTopics() 

  { 

    $dsn = 'mysql://root@localhost/votes'; 

    $mdb2 =& MDB2::factory($dsn); 

    $sth =& $mdb2->prepare( "SELECT * FROM topics" ); 

    $res = $sth->execute(); 

    $rows = array(); 

    while ($row = $res->fetchRow(MDB2_FETCHMODE_ASSOC)) { $rows []= $row; }          

    return $rows; 

  } 

  function getOptions( $topic ) 

  { 

    $dsn = 'mysql://root@localhost/votes'; 

    $mdb2 =& MDB2::factory($dsn); 

    $sth =& $mdb2->prepare( "SELECT * FROM options WHERE topic_id=?" ); 

    $res = $sth->execute( $topic ); 

    $rows = array(); 

    while ($row = $res->fetchRow(MDB2_FETCHMODE_ASSOC)) { $rows []= $row; }          

    return $rows; 

  } 

  function addVote( $option ) 

  { 

    $dsn = 'mysql://root@localhost/votes'; 

    $mdb2 =& MDB2::factory($dsn); 

    $sth =& $mdb2->prepare( "SELECT * FROM options WHERE id=?" ); 

    $res = $sth->execute( $option ); 

    $rows = array(); 

    while ($row = $res->fetchRow(MDB2_FETCHMODE_ASSOC)) {  

      $sth2 =& $mdb2->prepare( "UPDATE options SET votes=? WHERE id=?" ); 

      $row['votes'] = $row['votes'] + 1; 

      $res = $sth2->execute( array( $row['votes'], $option ) ); 

      return $row; 

    } 

    return null; 

  } 

} 
```

我不会深入讨论这段代码，因为这超出了本文的重点，但是我要指出，该脚本使用 MDB2 扩展来访问 MySQL 数据库以获得选票。MDB2 库是 PHP 数据库无关访问的新标准。

这三个方法是`getTopics`，它返回可用投票主题的列表，然后是`getOptions`，它返回指定主题的选项数组。每个选项都包括选项文本和票数。最后，`addVote`给一个特定的选项添加一个投票。

为了测试这些功能，让我们调出 [AMFPHP 项目](http://amfphp.org)附带的 AMF 服务浏览器，并使用它来检查服务，如下所示。

![Testing the getTopics function in the AMF votes service](img/4a35fabd9744562f34206b6ef4741777.png)

这个截图显示了当我们运行`getTopics`函数时会发生什么。它返回一个对象数组，每个对象都有`topic`的`id`和主题的文本。

接下来我们可以检查`getOptions`函数，如图所示。

![Testing the getOptions function](img/b0747ad273fe525d13a7e92c2ae3351e.png)

我们看到这个函数返回另一个对象数组，其中每个对象都有选项的`text`、选项的惟一`id`、相关主题的`id`和当前计数`votes`。

最后要测试的服务功能是`addVote`。这就是它的作用。

![Testing the addVote function](img/3e74644155cb04bf30072dbb2b00e998.png)

在这个截屏中，我们指定我们正在添加对选项`1`的`id`的投票，这是“第一集”(认真地)。返回的是一个单个对象，其中包含带有`1`的`id`的选项记录的新值。

对于 web 服务器端，这就是我们需要关心的所有事情。现在有趣的部分来了:将我们的应用程序包装在一个漂亮的 Flex 接口中。

##### 构建投票界面

构建投票应用程序的第一步是获取主题列表，并选择一个主题进行投票。下面的代码只实现了这部分功能，但是它在`RemoteObject`对象中定义了整个 web 服务器 API:

```
<?xml version="1.0" encoding="utf-8"?>  

<mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" layout="vertical"  

  creationComplete="voteRO.getTopics.send()">  

<mx:Script>  

<![CDATA[  

private var topicID:int = 0;  

private function onGetTopics() : void {  

  var topicind:int = Math.round( Math.random() * voteRO.getTopics.lastResult.length );  

  if ( topicind >= voteRO.getTopics.lastResult.length )  

    topicind = voteRO.getTopics.lastResult.length - 1;  

  var topic:Object = voteRO.getTopics.lastResult[ topicind ];  

  topicID = topic.id;  

  topicText.text = topic.text;  

}  

]]>  

</mx:Script>  

<mx:RemoteObject id="voteRO"  

  endpoint="http://localhost/amfphp/gateway.php"  

  source="votes2.VoteService2" destination="votes2.VoteService2"  

  showBusyCursor="true">  

<mx:method name="getTopics" result="onGetTopics()" />  

<mx:method name="getOptions">  

  <mx:arguments><mx:Topic /></mx:arguments>  

</mx:method>  

<mx:method name="addVote">  

  <mx:arguments><mx:Topic /></mx:arguments>  

</mx:method>  

</mx:RemoteObject>  

<mx:Label fontSize="20" id="topicText" />  

</mx:Application> 
```

这里有很多我们在第一篇文章的原始示例中看到的内容，但它包含的 ActionScript 代码确实比前面的任何示例都多。先说熟悉的。

`<mx:RemoteObject>`与第一篇文章中的完全相同，除了我添加了一些方法，其中两个方法有参数。底部的`<mx:Label>`标签将显示问题的文本。

现在，ActionScript 代码可能看起来令人生畏，但它相对简单。调用`onGetTopics`方法是为了响应 web 服务器上对`getTopics`函数的成功调用。该方法获取返回的主题，并使用`Math.random`方法随机选择一个主题。然后，代码将表格的文本设置为主题的文本。

`RemoteObject`上的`getTopics`方法和应用中的`onGetTopics`方法之间的关系值得我们关注。Flex 中的 Web 服务调用是异步的。事实上，Flex 中的几乎所有东西都是异步的和基于事件的。当您在`getTopics`上调用 send 方法时，它会立即返回。然后，Flex 异步调用 web 服务器，收集响应，并触发 result 方法，在本例中，该方法调用`onGetTopics`方法。还有一个对 web 服务错误的回调，在这个例子中我没有具体说明。

不过，回到这个例子。运行这个程序应该会得到如下所示的结果。

![Checking to make sure we pick a topic correctly](img/ec4ee6ef44b57a83ca0c63d89f67485d.png)

如果您向数据库中添加了更多的主题，您应该会看到随机选择的一个主题。我放在 MySQL 代码中的数据集只有一个主题，我们可以在上图中看到。

现在，下一个技巧是让我们构建一个允许用户选择其中一个选项的界面。传统的界面由一组单选按钮和一个**提交**按钮组成。但是对于像这样一个随意的投票系统来说，呈现一堆按钮也是一样容易的，点击一个按钮就可以注册投票。我们将在这里采用这种方法。

下面的 MXML 代码是对前面示例的补充，它为每个选项动态创建按钮，并将它们添加到应用程序的容器中:

```
<?xml version="1.0" encoding="utf-8"?>  

<mx:Application ...>  

<mx:Script>  

<![CDATA[  

import mx.controls.Alert;  

import mx.controls.Button;  

private var topicID:int = 0;  

private function onGetTopics() : void {  

  // ...  

  voteRO.getOptions.send( topicID );  

}  

private function onGetOptions() : void {  

  for each( var opt:Object in voteRO.getOptions.lastResult ) {  

    var optBut:Button = new Button();  

    optBut.label = opt.text;  

    optBut.data = opt.id;  

    optBut.percentWidth = 100;  

    optBut.addEventListener( MouseEvent.CLICK, onOptionButtonClick );  

    addChild( optBut );  

  }  

}  

private function onOptionButtonClick( event:Event ) : void {  

  voteRO.addVote.send( event.currentTarget.data );  

  mx.controls.Alert.show( 'You successfully voted for ' + event.currentTarget.label );  

}  

]]>  

</mx:Script>  

<mx:RemoteObject id="voteRO" ...>  

...  

</mx:RemoteObject>  

<mx:Label fontSize="20" id="topicText" />  

</mx:Application>
```

我们的原始代码保持不变，除了在`onGetTopics`方法中增加了对`getOptions`的调用。当`getOptions`成功完成时，它调用`onGetOptions`，为每个选项创建一个`Button`类，并将其添加到应用程序的容器中。它还注册了`onOptionButtonClick`方法来响应鼠标点击新创建的按钮。

这两个方法`addEventListener`和`addChild`是 Flex 的基础。Flex 框架中的几乎每个对象在改变状态时都会发布消息。要监听这些消息，调用`addEventListener`并指定您想要监听的消息，以及当该消息被触发时要调用的方法。

***注意:使用`EventDispatcher`发布事件**
上面讨论的消息传递模型是由`EventDispatcher`基类实现的，并且使用了`dispatchEvent`方法。如果您希望创建一个发布事件的对象，那么您需要确保您的对象继承自`EventDispatcher`类。*

`addChild`方法是 Flex 框架可视化层次结构的基础方法。就像 HTML 文档一样，Flex 应用程序的可视化层次结构可以用树来表示。根容器几乎总是应用程序对象。在其中，可以有容器和控件等等，反过来，几乎每个容器或控件都支持子控件或容器。您可以使用`addChild`添加子控件(正如我在上面的代码中所做的那样)，并且可以使用`removeChild`和`removeAllChildren`方法移除子控件。

好了，关于 Flex 的小入门已经结束了，让我们看看当我们从 Flex Builder 启动这个代码时会发生什么——请看这个图。

![The dynamically generated option buttons](img/0350b1cb4537c030f432bce4201079f8.png)

图中显示了应用程序选择了一个问题，并为每个不同的选项设置了按钮。然后，用户可以通过单击其中一个按钮来响应投票，如下图所示。

![The ](img/f80bea6021a2c7f55a5511ff69b21c97.png)

单击其中一个按钮调用`onOptionButtonClick`，它又调用服务器上的`addVote`函数。该应用程序还显示了一个漂亮的消息框，通知投票人选票已经统计完毕。

我要做的最后一个改变是显示投票结果的饼状图，而不仅仅是警告框。代码的最终版本如下所示:

```
<?xml version="1.0" encoding="utf-8"?>   

<mx:Application ...>   

<mx:Script>   

<![CDATA[   

import mx.controls.Button;   

private var topicID:int = 0;   

private var voted:Boolean = false;   

private function onGetTopics() : void {   

// ...   

}   

private function onGetOptions() : void {   

  if ( voted )   

    resultPie.dataProvider = voteRO.getOptions.lastResult;   

  else {   

    for each( var opt:Object in voteRO.getOptions.lastResult ) {   

      var optBut:Button = new Button();   

      optBut.label = opt.text;   

      optBut.data = opt.id;   

      optBut.percentWidth = 100;   

      optBut.addEventListener( MouseEvent.CLICK, onOptionButtonClick );   

      optionsBox.addChild( optBut );   

    }   

  }   

}   

private function onOptionButtonClick( event:Event ) : void {   

  voted = true;   

  voteRO.addVote.send( event.currentTarget.data );   

}   

private function onVoteResult() : void {   

  contentStack.selectedChild = pieBox;   

  voteRO.getOptions.send( topicID );   

}   

]]>   

</mx:Script>   

<mx:RemoteObject ...>   

...   

</mx:RemoteObject>   

<mx:Label fontSize="20" id="topicText" />   

<mx:ViewStack id="contentStack" creationPolicy="all" width="100%" height="100%">   

  <mx:VBox width="100%" height="100%" id="optionsBox">   

  </mx:VBox>   

  <mx:VBox width="100%" height="100%" id="pieBox">   

  <mx:PieChart width="100%" height="100%" id="resultPie" showAllDataTips="true">   

    <mx:series>   

      <mx:PieSeries field="votes" nameField="text" explodeRadius="0.1" />   

    </mx:series>   

  </mx:PieChart>   

  </mx:VBox>   

</mx:ViewStack>   

</mx:Application>
```

这里最大的变化出现在文件的末尾，我添加了一个包含两个`<mx:VBox>`容器的`<mx:ViewStack>`对象。一副`ViewStack`很像一副纸牌。你可以选择一张卡片放在最上面，而其他的都隐藏在下面。在这种情况下，第一个`VBox`、`optionsBox`将持有选项按钮。第二个`VBox`，即`pieBox`，将持有`PieChart`。`PieChart`与[第一篇文章](https://www.sitepoint.com/article/voting-widget-flex-3-part-1)中的一模一样。

`ActionScript`中的`onGetOptions`方法也发生了变化。它现在检查我们是否已经投票。如果我们已经投票，代码将把`PieChart`上的`dataProvider`设置为来自`getOptions`的更新响应。如果我们没有投票，它会使用`addChild`将按钮添加到`optionsBox`中。

`onVoteResult`方法，我们用来放置警告框的地方，现在简单地将`ViewStack`卡片翻转给`pieBox`来呈现饼状图。一旦再次触发`onGetOptions`方法，饼图就会更新。

当我们在浏览器中提出这个问题，并进行投票时，我们可以看到最终的饼状图。

![The voting results pie chart](img/c89bad77e93955f835a9719b78f21435.png)

还不错！我们现在有了一个小部件，它可以与 web 服务对话，随机选择一个问题，提出这个问题，然后如果用户选择投票，就用一个漂亮的饼状图显示投票结果。

唯一的问题是 Flash 应用程序本身相当重。事实上，在我写这篇文章的时候，我的机器上的 SWF 文件大约有 350-400k。对于一个轻量级的小部件来说，这有点太重了。怎么办？

##### 移动到运行时共享库

谢天谢地，Adobe 已经修复了胖 SWF 问题——而且它甚至不需要任何编码更改。你所要做的就是稍微调整一下你的项目。诀窍是使用运行时共享库。

这个想法很简单。当您编译 SWF 文件而不是将库包含在 SWF 本身中时，您告诉 Flash player 从特定的 URL 获取库。然后，Flash player 会自动为您下载该库并进行缓存，因此下次查找该库时，它将只使用这个存储的版本。

要进行更改，右键单击“voter”项目并选择“Properties”然后从左侧栏中选择“Flex 构建路径”。结果如下所示。

![The Library Path tab in the project configuration](img/8346965327e1a5b6e5f0a63e3398add0.png)

从那里，选择如下所示的`framework.swc`文件。

![The framework link options](img/fd40118520a3e8daec669f2e12ea2412.png)

双击链接类型。这将弹出一个对话框，您可以取消选中“使用与框架相同的链接”选项，并从下拉列表中选择“运行时共享库(RSL)”，如图所示。

![Setting the linkage to a runtime shared library (RSL)](img/e6a75b18ab8ab8a077df5087409aaf6c.png)

最后一步是点击**添加**按钮。这将打开如下所示的对话框。

![Specifying the location of the framework swz file on your site](img/e80827cbb55a2c7431ea8e61ecea01c1.png)

现在，我们输入框架的`swz`文件所在的 URL。此文件必须从 Flex Builder 安装目录复制到您的 web 服务器。

因为这个特殊的例子使用了图表类，所以我们也将遵循与`datavisualization.swc`库相同的过程。

##### 从这里去哪里

这两篇文章应该可以帮助您开始使用 Flex。像这个投票应用程序这样的小应用程序是扩展应用程序的完美地方。

如果让我定义 Flex 的三大优势，那就是:

*   Flex 使开发人员能够轻松构建 Flash 应用程序。
*   Flash 应用程序几乎可以在任何地方运行良好，并且和 Ajax 应用程序一样好用。
*   Flex 3 可用于构建小到足以创建 Web 2.0 小部件的 Flash 应用程序。

我希望这两篇文章能让你朝着同一个方向前进(一定要下载这篇文章的[代码档案)。](https://i2.sitepoint.com/examples/flex3/flex3-voter.zip)

##### 考考自己！

**用一个[小测验](https://www.sitepoint.com/quiz/flex3/part2)来测试你对本文的理解——如果你反应快，你将在有限的时间内免费得到我的书【Flex 3 入门，这要感谢 Adobe。**

 ****[参加小测验！](https://www.sitepoint.com/quiz/flex3/part2)**** 

## 分享这篇文章