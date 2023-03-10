# 协调你的 Flash 网站

> 原文：<https://www.sitepoint.com/co-ordinate-flash-site/>

当 Flash 网站被分成更小的电影(即“模块化”)时，加载速度会更快。虽然这个想法看起来很明显，但仍然值得一提！但是不太明显的是，一旦你开始走这条路，“协调”的问题就占了先机；而且，无论开发人员是经验丰富的专业人员还是相对的新手(像我一样)，他们最好提前认识到这些问题。

本文希望完成三件事:首先，强调模块化的价值。第二，澄清一些重要的，但以前报道不足的影响。最后，通过解释如何系统地完成“协调”任务，为这些影响提供补救措施。

参考了我为其他人设计的几个网站，以及 Flash 开发人员(特别是那些希望减少加载时间的人)在将网站分成小块之前应该考虑的问题。

##### 个案研究

我之前已经把我最喜欢的一个网站的几个部分分解成更小的 swf，并把它们加载到主电影的不同层次。事实上，这对于 MP3 或音乐选择器、时钟之类的东西来说似乎很合理。我已经习惯了我的主要电影的超大尺寸。所以，当我看到最近 SitePoint 上一篇关于将主要电影《T2》模块化的文章时，我怀着极大的兴趣研究了它。

的确，成功了！事实上，它工作得如此天衣无缝，我浏览了我的整个网站，并模块化了 6-8 个附加部分！太好了，我想！现在，主要的电影在尺寸上要小得多——至少，是在我从它的库中删除了所有剩余的未使用的项目之后。这样，我注意到这个站点的加载时间减少了，并且我可以更容易地隔离任何剩余的关注区域。出乎意料的是，通过将主要的电影分成更小的片段，识别网站中可以重复使用的部分变得更加容易！

例如，当网站最初加载时，一组门打开，显示一个位于屏幕中央的图像，它位于四个主要导航按钮的正上方。在模块化之前，这种“开门程序”只会出现一次——第一次加载站点时。这是我模块化的第一件事，在我模块化之后，很明显我可以重用或“协调”每次用户点击主导航按钮时执行的开门例程！我所要做的就是从每个按钮内部调用开门程序。简单！

我还没有意识到的是，协调的任务最终会变得一点也不简单。试着协调 25-50 个不同的子电影，让它们像用户期望的那样互动，既和主电影互动，也和其他电影互动！让我告诉你，它会变得有点笨拙！

它到底有多笨重？

好吧，我只想说，尽管有明显的好处，模块化提出了重要的协调问题(这反过来又提出了时间和精力的问题——例如，你有多少时间，你愿意花费多少精力？).但是，第一个问题问:“*模块化多少才够，多少才算多？*“是否存在最优性点？考虑一下这个。

你有四个导航按钮，都被编程到你的 main .swf 中。每个按钮都与一个不同的子菜单项或选项相关联，并且每个按钮都有一个不同的背景图像(见图 1)。将这四个按钮模块化成四个更小、加载更快的按钮。主权财富基金和你有问题。检查这个:

*   当选择任何其他主导航按钮时，需要关闭第一按钮的子菜单选择和/或背景图像；
*   用户选择的第二个按钮也是如此——需要打开它的相关信息；当这一切发生的时候，
*   第一个按钮不应该消失或不起作用。
*   背景音乐不应受到按钮选择的影响，但当选择某些按钮(但不是全部)时，音乐开始时出现的音乐家图像应该会消失。

对于一个经验丰富的 Flash 设计师或开发人员来说，这些都不是特别令人不安的；一切都是理所应当的。但对于我们其余的人来说，渴望成为一个经验丰富的闪光灯“什么的”，这一切都来自于蓝色。

这意味着我们需要对每个导航按钮进行编程，以发出一系列命令，使其余按钮的某些特征卷起、滑出或以其他方式消失——以允许其他按钮显示它们的信息，而不会相互干扰。简单地说，按钮在执行之前需要评估它们的“环境”并与之交互。

这就引出了第二个问题:"*我如何变小？swf 之间交换命令，以及与主电影之间交换命令？*

##### 协调任务

比方说，网站加载后，用户点击其中一个主导航按钮。正如他们所料，该按钮显示其子菜单:

![949_image1](img/d1216e558c5ca6fb359afdc77f75eb1d.png)

而且，当用户将鼠标滑过每个子菜单项时，该项目的文本会高亮显示，并且主“显示区域”会显示一幅图像，该图像直观地传达了当前(被滑过的)项的主要思想。

然后(因为我们都有这些真正聪明的用户)，第一个菜单仍然在“向下”的位置，*但没有从子菜单*中选择任何东西，用户决定点击第二个导航按钮，它有自己的菜单和(你猜对了)一个图像，可以有效地覆盖相同的显示区域:

![949_image2](img/fb125851fc2751be480b4d981a96d68d.png)

嗯，如果这两个按钮是模块化的(也就是说它们是分开存在的。swf)，并且不协调—子菜单和/或背景图像将显示在另一个之上，使两个按钮无效:

![949_image3](img/1d43a634f578f20e8bcd63f04db5518a.png)

够了！

对于某些项目，这可能会产生预期的效果。但是，如果这不是你想要的，你可能会想进一步阅读。

虽然通过模块化我的网站，我学到了很多关于 Flash 的东西，但通过花时间协调按钮和其他我愉快地拆除的例程，我学到了两倍的东西。毕竟一部加载很快又很容易修复的电影，如果运行不出预期，又有什么好呢？不多。

无论如何，为了让你对按钮之间的交互有一个概念，这里有一个从我的“主页”按钮发出的命令列表:

```
on(release) {   

\ 1\. these commands turn off the music, if it's already been turned on.   

_root.music_holder. unloadMovie("miles_and_john.swf");   

_root.music_holder. unloadMovie("love_supreme.swf");   

_root.music_holder. unloadMovie("cannonball_adderley.swf");   

_root.music_holder. unloadMovie("finding_forrester.swf");   

_root.music_holder. unloadMovie("tribute_to_miles.swf");   

_root.music_holder.music.gotoAndStop("back");    

\ 2\. these commands make the images, which are designed to automatically    

\ pop-up for certain songs (above), invisible.   

_root.music_holder.music.miles._visible = false;   

_root.music_holder.music.herbie._visible = false;   

_root.music_holder.music.john._visible = false;   

_root.music_holder.music.jazzfestival._visible = false;   

_root.music_holder.music.sax._visible = false;   

\ 3\. if any of the navigational buttons are currently in their "display"    

\ positions, these commands will bring them back to their    

\ original positions.     

_root.movieholder.button_rack.gotoAndStop("start");   

_root.movieholder.button_rack.teaching.gotoAndStop("start");   

_root.movieholder.button_rack.research.gotoAndStop("start");   

_root.movieholder.button_rack.info.gotoAndStop("start");   

_root.movieholder.button_rack.contact.textAnimation.gotoAndStop("start");   

\ 4\. these commands turn off the background images that are associated    

\ with each one of the (above) navigational buttons.     

_root.movieholder.mc_button_1_holder.unloadMovie();   

_root.movieholder.mc_button_2_holder.unloadMovie();   

_root.movieholder.mc_button_3_holder.unloadMovie();   

_root.movieholder.mc_button_4_holder.unloadMovie();   

\ 5\. this command turns on the background image that comes up    

\ when the website initially loads.   

_root.movieholder.mc_picture_base._visible = true;   

\ 6\. in case the user had previously clicked "credits," this command    

\ makes the credits display go to its initial (off) position.   

_root.clock_holder.credits.gotoAndStop("off");   

\ 7\. this command unloads any previously loaded movie, in case it    

\ had been displayed.    

_root.wipe1_holder.unloadMovie();    

\ 8\. this command unloads yet another sub-movie, in case it    

\ had been displayed.   

_root.movieholder._level1.unloadMovie();   

\ 9\. finally, this command loads a very small movie that appears    

\  when the website initially loaded, telling the user that    

\ they are now "back home."   

loadMovie("wipe1.swf", "wipe1_holder");   

}
```

正如您所看到的，有很多事情正在发生——至少是潜在的——这就是为什么您必须发出这么多命令的原因。请记住，需要向其他按钮发出类似的命令，以便它们类似地被告知在给定情况下应该如何操作。同样，按钮需要与它们的“环境”交互——然而，当用户决定做他们想做的事情时，环境可能会被配置。

正如你可能想象的那样，上面的几个命令可能没有用，但是没关系。有效的协调是关于预先考虑和解决各种可能的相互作用。你需要不断地问自己“如果”

虽然有几个命令可能会“闲置”，但它们的存在很重要，这样用户就可以在他们想做的时候做他们想做的事情，“而不是每次打开煤气都要用水。”

##### 在模块化之前

也就是说，这里有一些可能对其他新手有益的指导。这些都是在你模块化你的网站之前应该确定的事情。

1.  列出你计划模块化的按钮/例程和它们的“环境”之间可能发生的所有预期交互对于导航按钮，您可能需要考虑它们的(预期)操作是否应该受到其他按钮操作的约束，或者受制于其他按钮的操作。

3.  **确定模块化按钮/程序一旦模块化后，需要哪些命令来与它们的“环境”交互**。而对 _root 的绝对引用。可能会有很大帮助(见上文)，其他技术(例如，加载较小。swf 到给定电影的不同级别)在某些情况下甚至可能更好地工作。
4.  **确定*在哪里*需要从**发出这些命令。如果您想在网站上包含测验，Macromedia 在 Flash MX 的“文件”菜单下提供了一些测验模板。如果您想将已完成的测验模块化并将其加载到您的主电影中，它可能会在报告其结果时遇到问题。回答:将它作为一个独立的程序加载，并调用它(从主电影上的一个按钮)到它自己的浏览器窗口中。
5.  **确定*何时*需要发出这些命令**。当协调行动时，你会发现几个地方加载命令，使网站将按照你的意图运行。有时，你想从一个按钮发出命令，有时从一部电影，有时从一个框架，有时从一个浏览器窗口，有时从一个 JavaScript，等等。不要害怕跳出框框思考！
6.  最后，在模块化之前,(为你自己)确定你能够并且愿意在一个给定的项目上花费多少时间和精力。正如我希望在这里展示的那样，每个新创建的模块都带来了额外的需求，即与网站的其余部分有效地交互。唉，生活中很少是自动的，Flash 就更少了！

***底线:*** 模块化是值得的，应该多做。但是，因为它只是等式的一半，所以您应该知道它的成本！

## 分享这篇文章