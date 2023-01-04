# 案件:追溯

> 原文：<https://www.sitepoint.com/loccasions-retrospective/>

![](img/9ed8751f891204d6e20db589020ac9c8.png "shutterstock_85202584")

Loccasions 应用程序现在允许我想要生成的大多数基本功能。在提到 loca casions([这里是](https://www.sitepoint.com/rails-deep-dive-application-setup-loccasions/))的系列文章的第一篇中，我以下面的用户故事开始:

*   作为未注册用户，我想看看主页/登录页面
*   作为一名管理员，我希望能够邀请用户参加临时会议
*   作为受邀用户，我希望能够创建一个帐户
*   作为注册用户，我希望能够创建活动
*   作为注册用户，我希望能够创造机会
*   作为一个注册用户，我想在地图上看到场合

We did them all, except the second story dealing with inviting users. Since the first round of user stories is complete, I think it’s time for a retrospective. Also, this will be the last post in the Loccasions series for a while (if not, forever) as I am anxious to free myself of the shackles of this series and move on to other topics in Ruby.

## 什么是回顾展？

如果你从未参加过真正的回顾展，这篇文章不会改变这一点。虽然在软件开发的上下文中，这个术语有许多定义，但我认为“回顾”是指“一个简短的或有时间限制的会议，在这个会议上，你讨论什么进行得好，什么进行得不好，我们可以做得更好，以及我们的优先级如何改变。”通常，您有一个由产品所有者、开发人员和其他利益相关者组成的团队来帮助审查您的应用程序的最新迭代。在这种情况下，它只是小 ol' me 和 15 篇左右的文章。然而，在这两种情况下，回顾都是非常有用的，并且应该是任何开发过程的强制性部分。

如果你是那种喜欢更正式的定义之类的人，这里有一些可以帮助你的:

*   [James Shore，来自敏捷开发的艺术](http://jamesshore.com/Agile-Book/retrospectives.html)
*   [伊恩·伯吉斯在这里给出了一个定义](http://www.ianburgess.me.uk/en/software-development/agile-retrospective-lessons-learned)
*   Matthew Bussa 回顾了一个例子。

I’d be lying if I said I did more than just randomly pick a few from a quick Google search, but looking through each of these, I saw the common ingredients:

*   一杯怎么样？但不要太多，会毁了这道菜。
*   一杯什么错了？
*   一些正确的事情？
*   一点点计划我们的下一次迭代。

That’s it.  If you put in too much of any of these ingredients, it is likely your retrospective will come out burnt, raw, or in some other inedible form. Under no circumstances are any of the following ingredients to be used:

*   责备
*   愤怒
*   夸张(“既然 X 向右，我们应该在任何事情上使用它！”)

In the end, the retrospective is about getting better, not assigning blame. We get better by learning from our mistakes and planning (“Plans are worthless, but planning is everything.” – Dwight D. Eisenhower) a bit better. Also, if your retrospective is more than 4 hours long, I would seriously question the value of anything beyond that time. Remember, the one time you aren’t improving the app is when you’re all sitting in a meeting overanalyzing/fighting/etc.

## 哪里出了问题？

与我们在网上看到的许多人为的例子相反，我开始让 Loccasions 系列成为开发“真正的”Rails 应用程序的一个展示。在我的第一次“迭代”之后，我不认为我完成了这个目标。首先，很少有应用程序是由一个开发人员开发的。此外，我在技术、宝石等方面做出了许多基于不可靠理由的决定。如果你还记得，我选择 MongoDB，基本上是因为我想用它来玩。如果这是一个“真正的”应用程序，我绝不会做出那样的决定。

在许多情况下，我对代码和例子有点粗心。这导致一些文章很难理解，我最终不得不面对可怕的“嗯，它在我的笔记本电脑上运行”类型的应用程序。只是通过一些机警的读者的努力(尤其是尼古拉斯·亨利……那家伙是一台机器)，这个问题才得到一定程度的缓解。

我想我应该选择一个部署平台(应该是 Heroku，因为它是怎么说的？SUPERFANTASTIC ),并持续向其部署应用程序。这可能鼓励了社区的更多参与，并确保我的技术决策不会影响整个部署。

此外，到最后，我几乎忘记了用例。在真实的应用程序中，我会不断地检查用户场景，以确保我没有偏离路径。在本系列的最后，Loccasions 可能只是 Rails 应用程序的一个更复杂的人为例子，而不是我最初看到的真正的应用程序。

## 什么是对的？

随着负面影响的消除，我认为很多事情都进行得很顺利。首先，也是最重要的一点，我学到了很多东西，并且在撰写这个系列的过程中度过了一段美好的时光。作为开发人员，当我们享受我们的工作并关心我们正在解决的问题时，生活是美好的。此外，我认为为客户端代码选择主干是一个完美的选择。越用骨气，越喜欢。它只是足够的框架和足够的方式，从而产生一个伟大的编码体验。

我也赞同茉莉的观点。自从我第一次发现茉莉，我就迷上了它，它继续给我留下深刻印象。同样，Jasmine 关注的是我不想担心的部分，所以我可以测试我的应用程序。此外，我认为 Jasmine 鼓励好的 javascript 设计，就像测试框架应该做的那样。

最后在客户端，我真的很喜欢小册子。如果您的 web 应用程序需要地图，我不会高度推荐传单。美丽的地图是一种快乐的编码，使它成为我目前选择的“光滑”地图。

在服务器上，选择 Devise 很容易，事实证明这是一个很好的选择。通常情况下，随着 device 的流行，宝石可能会变成一堆无用的代码，但 device 对我来说没有这种感觉。

福尔曼，在我看来，现在几乎是势在必行。特别是如果你被部署到 Heroku，Forman 只是让生活更容易。

最后，我认为 Evan Light 的[结对编程集可以说是这个系列的亮点。在我的新工作中，我经常和 Evan 一起工作，他是我写那篇文章时遇到的所有人中最聪明、最全面的好人。我希望我能做更多这样的事。](https://www.sitepoint.com/loccasions-pair-programming/)

## 怎么变好？

最简单，也可能是最有效的方法是让其他人参与进来。通过添加一两个开发人员，这个应用程序将会提高十倍。我信奉的一个信条是开发人员协作的重要性。我最好的作品从未单独完成过。

另一群人是用户，他们的参与将极大地改善位置。让人们真正使用这个应用程序并给出反馈将会推动这个应用程序的方向，以及这个回顾展的方向。在我多年开发定制应用程序的过程中，我目睹了我们的行业/社区从将用户视为不可避免的祸害发展到恰当地将他们置于主导地位。我记得读过一篇博文(我找不到作者了…不好意思！)这句话的意思是“你总是能看出开发者何时设计了你的应用程序。”这当然是正确的位置，它可以使用设计师的触摸。

## 有什么计划？

每当我开始下一次 Loccasions 迭代时，都不会缺少要做的事情和要做的改进。然而，我需要计划下一步我要关注的地方。经过仔细考虑，我想我会把重点放在:

*   部署
*   UX/设计

These are broad terms, obviously. Drilling down a bit, for deployment I would push the app out to Heroku. As a part of it, I think looking into some kind of Continuous Integration server would also help iron out deployment and development scenarios. Investigating CI Servers is also on the immediate planning list.For the UX/Design tasks, I think recruiting a couple of users and a bonafide web designer would be in the cards. If I could get some users to run through the application, give feedback, and get a designer to give the application a once over, Loccasions would be exponentially better.Of course, there are specific tasks that go with each of these higher level planning items, but we’ll stop here. Loccasions was a lot of fun to write, and to write about. It may not be a seminal Rails work, but it covered a lot of topics and answered a lot of questions. Thanks for reading the series, for those of you that stuck with it. Maybe I’ll pick it up again in a few months.[Rearview image via shutterstock](http://www.shutterstock.com/cat.mhtml?lang=en&search_source=search_form&version=llv1&anyorall=all&safesearch=1&searchterm=rearview&search_group=&orient=&search_cat=&searchtermx=&photographer_name=&people_gender=&people_age=&people_ethnicity=&people_number=&commercial_ok=&color=&show_color_wheel=1#id=85202584&src=cf7ee19f7d694327b41376cce98835d5-1-5)

## 分享这篇文章