# Userfly:来自实际用户的可用性

> 原文：<https://www.sitepoint.com/userfly-usability-from-your-actual-users/>

分类广告网站 [Oodle](http://www.oodle.com/) 的高级工程师克里斯·埃斯特雷奇今天试运行了一个面向网络开发者的新网站，名为 [Userfly](http://www.userfly.com/) 。使用 JavaScript，Userfly 捕获并记录你的访问者的浏览数据，并让你稍后以电影的形式回放。这个网站真的很粗糙——这是一个非常 beta 的版本——可能还没有准备好接受任何媒体报道，但我对这个演示印象深刻(玩起来很有趣),我无法抗拒。

要实现 Userfly，开发人员只需在他们网站的每个页面上添加一行 JavaScript 代码。一旦访问者进入您的网站，该服务将捕获并记录浏览行为，并继续记录直到他们离开网站。Userfly 捕捉点击流数据、滚动和鼠标移动以及文本输入，并且它能够与一些非常重型的 JavaScript 元素进行交互，这令人印象深刻。

然后，网站开发人员可以登录他们的帐户，通过观看录制的电影(类似于截屏)来查看用户交互。Userfly 将被跟踪的用户导航到的网站上的任何页面复制到它的服务器上，然后将访问者交互电影覆盖在每个页面的本地副本上。目前，Userfly 每小时有一个用户会话是免费的，可根据要求提供更高的批量定价。最终，Estreich 打算增加一些额外的功能，比如对大容量网站的随机用户抽样，以及邀请特定用户进行可用性研究的能力。

<object width="400" height="302"><param name="allowfullscreen" value="true"><param name="allowscriptaccess" value="always"><param name="movie" value="https://vimeo.com/moogaloop.swf?clip_id=2451370&amp;server=vimeo.com&amp;show_title=1&amp;show_byline=1&amp;show_portrait=0&amp;color=&amp;fullscreen=1"><embed src="https://vimeo.com/moogaloop.swf?clip_id=2451370&amp;server=vimeo.com&amp;show_title=1&amp;show_byline=1&amp;show_portrait=0&amp;color=&amp;fullscreen=1" type="application/x-shockwave-flash" allowfullscreen="" allowscriptaccess="always" width="400" height="302"></object>
[userfly.com](https://vimeo.com/2451370)从[克里斯·埃斯特雷奇](https://vimeo.com/user930239)到[维梅奥](http://vimeo.com)。

然而，Userfly 将不得不处理一些问题。其中最主要的可能是隐私。Estreich 表示，他的服务不会捕获账户数据或信用卡信息，但按键跟踪仍然会让访问者非常警惕，如果他们知道这是在进行的话，尽管他们保证自己的数据是安全的。然而，根据 Estreich 的说法，为了跟随用户进入一个经过认证的网站，Userfly 需要设置一个代理，或者进行 cookie 捕获，这可能包括敏感信息。Estreich 计划只提供这种高级服务。

Estreich 必须努力让用户感到他们的隐私得到了保护。他肯定希望避免 Userfly 被隐私倡导者重新命名为“Userspy”。

另一个问题是这种可用性研究是否真的有用。当要弄清楚你的用户是如何与你的网站互动，并试图弄清哪些部分需要改进时，筛选成百上千的视频并不理想。Joshua Gross，一个类似服务的创始人，现已解散的 Tapefailure.com，通过在黑客新闻上的[帖子表达了确切的情绪。](http://news.ycombinator.com/item?id=390308)

他写道:“主要的问题是，会议没有重点(你不知道用户的目标)，观看几十/几百/几千个记录来了解用户在你的网站上做什么或不做什么是不切实际的。”根据格罗斯的观点，为了从数据中收集有用的信息，需要对数据进行汇总。"*他们在页面上做什么*？他们需要多长时间？总的来说，什么是重点，什么不是重点？用户如何与表单交互呢？这些只是一些非常广泛的例子，但有许多方法可以提取记录的数据，我发现这些方法比浏览会话本身更有见地。”

弄清楚如何获取大量记录数据并从中提取趋势将是 Estreich 的下一个重大挑战。不过，如果他能想出如何有效地做到这一点，Userfly 肯定能发展成为网络开发者的必备服务。到目前为止，这是一个良好的开端。

另外，不要错过我们列出的用便宜的进行可用性测试的 [5 种方法。](https://www.sitepoint.com/5-ways-to-get-usability-testing-on-the-cheap/)

## 分享这篇文章