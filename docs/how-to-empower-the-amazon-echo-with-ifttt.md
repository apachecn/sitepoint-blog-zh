# 如何用 IFTTT 增强亚马逊回声

> 原文：<https://www.sitepoint.com/how-to-empower-the-amazon-echo-with-ifttt/>

亚马逊 Echo 是一款支持语音的扬声器和个人助理，其功能范围每天都在增长。作为一名对物联网和人工智能都很着迷的开发人员，Echo 拥有如此大的潜力！

开始试验自己的 Echo 定制的最佳方式是通过 IFTTT (If This Then That)，这是一种允许在一系列设备和服务之间进行基于规则的操作和触发而无需编程的服务。在本文中，我们将探索 Echo 与 IFTTT 配合使用时可以实现的一些功能示例。

如果你在澳大利亚——像我一样——并且不太确定如何获得 Echo 并在这里设置它，我有一个关于如何在澳大利亚设置 Amazon Echo 的快速指南[我在 Dev Diner 上就这个主题写了这篇文章！虽然你还不能让所有的东西都工作起来，但是开发人员已经做了足够的工作，你很快就可以为 Echo 开发了。](http://devdiner.com/answers/how-do-i-set-up-an-amazon-echo-in-australia)

## 对 IFTTT 完全陌生？

如果您对 IFTTT 完全陌生，我们在关于使用 IFTTT 将 LIFX 灯泡连接到物联网的[的早期文章中有一个更一般的概述。我们还通过 IFTTT Maker 频道](https://www.sitepoint.com/connecting-lifx-light-bulbs-iot-using-ifttt/)将其连接到 [Node.js，并通过 IFTTT WordPress 频道](https://www.sitepoint.com/connecting-the-iot-and-node-js-to-ifttt/)将其连接到 [WordPress。在整篇文章中，如果您发现 IFTTT 中的任何概念有点令人困惑，请查看以前的文章——但是我的目标是对其进行组织，以便大多数读者可以仅从这篇文章中获得一些东西。](https://www.sitepoint.com/automate-wordpress-with-ifttt/)

## 将你的回声连接到 IFTTT

为了启用下面的任何功能，我们需要将 [Amazon Alexa channel](https://ifttt.com/amazon_alexa) 添加到 IFTTT。前往[亚马逊 Alexa 页面](https://ifttt.com/amazon_alexa)，点击“连接”:

![Connecting the Alexa channel](img/e717fb00a2fe4b772f732f85cd0b2c05.png)

从这里，您将被提示输入您的亚马逊帐户的详细信息。确保您输入了 Echo 所分配到的帐户的详细信息(如果您有多个帐户):

![Signing into Amazon](img/11683fa8e25f0fb279decfb27869bf06.png)

点击“确定”同意 IFTTT 访问您的所有 Alexa 信息:

![Allowing Amazon permissions](img/22f51cc9da5666bfb3585410a842587b.png)

当您授予它适当的权限时，您将被引导回 IFTTT，并且应该看到“通道已连接！”消息:

![Amazon successfully connected](img/fffaebd868a5fe7aeb958b7d5cc0c98e.png)

从那里开始，用 Echo 开始一个新食谱的步骤总是相同的，因为 Echo 只是 IFTTT 中的一个触发器，而不是一个动作。换句话说，你只能在 IFTTT 中使用 Echo 来引起动作发生，你不能让 IFTTT 让 Echo 对你说话或查找信息(我希望这将是未来的附加功能！).

像往常一样，要创建一个新的食谱，你可以点击右上角的用户名。然后点击“创建”:

![Creating a new recipe](img/934d0784ab985b832623df913dcf5a6a.png)

从那里，搜索亚马逊 Alexa 频道。如果你搜索“echo”也会出现，所以你可以通过一系列方法找到它！

![Choosing the Echo trigger channel](img/5f35d102b34db107b9c12066d23177f7.png)

从这里开始，选择的触发选项将根据我们想要创建的配方而有所不同。下面的每个配方都将从这一点开始。

## 将 Echo 连接到 LIFX 灯泡

虽然有一个 LIFX 技能你可以安装到 Echo 上，但是当我尝试使用它的时候，它对我不起作用。如果你也有同样的问题，你可以使用 IFTTT 来实现相同的结果，就像我们在 SitePoint 的[文章中使用 IFTTT](https://www.sitepoint.com/connecting-lifx-light-bulbs-iot-using-ifttt/) 将 LIFX 灯泡连接到物联网一样。

我们想通过声音来触发灯光。为此，我们使用回声触发通道“说出特定短语”触发:

![Choosing the echo voice trigger](img/410c273ea8adb0a4ba1e56ad021016ff.png)

从这里，我们设置我们想要使用的短语。作为初始测试，我们可以写下“打开我的灯”，然后单击“创建触发器”*(注意—您的短语需要全部小写才能被接受)*:

![Setting the phrase](img/e2b22b5d58a34b701647cdf0726a708c.png)

要将您的 Echo 连接到 LIFX 灯泡，请选择 LIFX 动作频道:

![Choosing the LIFX action channel](img/2f377f68a17737e828e251757648cdce.png)

然后，选择“开灯”动作:

![Turning LIFX lights on](img/a7b8cecab80840a510babe331c5038d1.png)

最后，选择您希望命令打开的灯(您可以在 Echo 触发器中编写更具体的短语，如“打开我卧室的灯”并在此规则内设置细节)。选择你希望灯光淡入的速度——我们选择了“即时”,因为我们希望它们直接打开。当你发出这个命令时，你可以选择设置灯的颜色和亮度，但是我们让它保持原样，这样它就保持了灯泡的最后设置。这个不需要高级选项！点击继续创建配方。

![Completing the action fields](img/fa7a3068b55cdf8fe63fc9a5f44f0197.png)

然后你应该有一个配方和触发句的确认。它显示我们的触发句是“Alexa trigger 打开我的灯”——这是因为我们在 IFTTT 内给出的任何句子都将以“trigger”开头。我将很快向您展示我对此使用的解决方法！现在，选择“创建配方”以完成该过程:

![Creating our new recipe](img/6ad0c0645eeb6adbaa04daddee3b22b8.png)

如果你现在对你的回声说“Alexa 触发打开我的灯”，你应该会发现你的 LIFX 灯打开了！这是物联网魔法在起作用！

你可以使用相同的过程来创建一个配方，让 Alexa 以相同的方式关闭你的灯。

## 以更自然的方式重命名触发器

说“Alexa 触发打开我的灯”有点别扭。相反，你可以通过编辑食谱来尝试一些短语，看看什么感觉是对的。

为此，请访问 IFTTT 上的[我的食谱](https://ifttt.com/myrecipes/personal)页面，找到你的食谱。向下滚动，你会发现“什么短语？”部分。我发现最好以尽可能短的格式来写这个短语，以句子中的单词“trigger”为基础。对于我的光，我们现在只有短语“我的光”。这导致了“Alexa，触发我的光”——一个听起来更自然的短语来记住！

![Renaming our trigger](img/dc8a1e4ca75065ca3105776dcb426354.png)

## 闹铃响起时打开灯

我们可以通过其他非语音触发器来触发 LIFX 灯，从而为这种触发 LIFX 灯的想法带来更多智能。如果你需要一点额外的激励在早上醒来，你可以设置你的 LIFX 灯在你的 Echo 闹钟响起时打开。为此，LIFX 操作将完全相同，只需将 Echo 触发器设置为“您的警报响起”:

![Triggering on Amazon Echo's alarm](img/ec61ac3b57b051bd9f75b54b5e9b3a03.png)

## 触发 Android 短信

使用 IFTTT，我们可以通过 Android 设备发送短信。IFTTT 有一个通用的“短信”频道，但这只能让你给自己发短信。Android 短信频道可以让你把它们发送到你定义的任何号码。我已经设置了一个触发器，让我的回声能够给我的未婚妻发送一条消息，让她知道我正在去见她的路上。

有了这些触发器，我们可以为 IFTTT 和 Echo 设置一系列模板消息。我的信息说“告诉我的未婚妻我在路上了”:

![SMS template message](img/2f37e6bac4ca9d293a470f18a3dadcb7.png)

然后，我们选择“安卓短信”行动渠道:

![Choosing Android SMS Action Channel](img/9a4020e5c8707ec00cdbbf44f9122273.png)

在此操作的设置中，我们包括了我们要发送短信的电话号码以及我们要发送的消息:

![Setting up phone number and message](img/76af13d7c3c358d74a6a8faece3ef2cd.png)

当我说“Alexa，触发告诉我的未婚妻我在路上”时，它会给她发一条短信:

![SMS in action](img/53deae14834cbb0995ccbcfadfc93346.png)

## 通过电子邮件发送你的待办事项列表

Echo 也有一个待办事项列表，你可以通过说“Alexa，把‘买牛奶’加到我的待办事项列表中”这样的话来添加事情。IFTTT 可以在任何时候将一个项目添加到这个待办事项列表中时触发动作。例如，我们可以在每次添加内容时，通过电子邮件将待办事项列表发送到我们的 Gmail 帐户。

为了开始这个概念，我们选择“添加到您的待办事项列表中的项目”作为我们的回显触发器:

![Triggering upon new to do list item](img/d667355eddf848dbb2b4827bfdc0a11a.png)

然后，我们继续添加动作。您可以使用“电子邮件”行动通道或“Gmail”行动通道。我们在这里使用了“Gmail”行动通道:

![Choosing Gmail Action Channel](img/5217728821d86073f3d176904173b8d0.png)

如果您以前没有使用过 Gmail 频道，您需要授予它访问权限:

![Connecting Gmail channel](img/750654680afd780557366cf1927da3b6.png)

一旦 IFTTT 访问了我们的 Gmail，我们选择一个可用的操作——“发送电子邮件”:

![Choosing the send email action](img/9db3798c2539051e0a6f10622a85919c.png)

然后，我们添加电子邮件地址和主题行，我们想发送这些电子邮件。在正文中，我们有两个标签将数据嵌入到我们的电子邮件中— `AddedItem`和`EntireList`。`AddedItem`将打印出我们刚刚添加到待办事项列表中的项目，`EntireList`将打印出整个待办事项列表。一旦我们对电子邮件的布局感到满意，我们就点击“创建操作”:

![Completing email action fields](img/eea22ea35a916faeebad3f5ace44e3e7.png)

一旦我们添加了食谱，如果你在你的待办事项列表中添加了一个项目，它将通过电子邮件向你发送该项目以及你列表中所有项目的列表:

![The email to-do list in action](img/50358188562f6794511b07068dc69117.png)

我把“sing”添加到了我的待办事项列表中，因为当我让 Echo“在我的待办事项列表中添加一些东西”时，它误解了我的意思……但我觉得这有点好笑，就把它留了下来。

## 将待办事项添加到谷歌电子表格中

对某些人来说，电子邮件可能有点混乱。IFTTT 还可以连接到 Google Drive，因此你也可以将项目添加到电子表格中。为了将待办事项添加到电子表格中，我们将动作通道设置为“Google Drive”:

![Choosing Google Drive Action Channel](img/ffe41dcdfb56b2fcb54c4036d1e40577.png)

然后为操作选择“将行添加到电子表格”:

![Choosing to add row to spreadsheet](img/b047ef873413fb6e4a67b7cbd09178f2.png)

在此操作的操作字段中，我们为电子表格设置了一个名称(例如“Alexa To Do List”)，并设置了我们希望如何格式化该行。在我们的例子中，我们有“`CreateTime`| |`AddedItem`”。“|||”表示分栏符，所以`CreateTime`会在一列中，而`AddedItem`会在另一列中。`CreateTime`添加待办事项列表项创建时的时间戳，`AddedItem`显示实际的待办事项列表项本身。

![Formatting our spreadsheet row](img/0c01cfa0155ca330db0eddd312c6f979.png)

当您添加新的待办事项列表项目时，它会为您创建电子表格(如果您尚未设置电子表格)，然后将待办事项列表项目添加到电子表格中:

![Our spreadsheet in action](img/444790019288c53d96601acb9689792e.png)

## 结论

将 Echo 和 IFTTT 的能力结合起来可以实现各种可能性。希望上面的例子已经激发了你自己的一些想法！

在接下来的几个月里，我们将探索如何超越 IFTTT，为 Echo 编程完全定制的功能，包括 AI 和更多！

如果你有一个回声，并给 IFTTT 集成一个去，我很想听听你配对在一起！你通过 IFTTT 把回声和什么连接起来了？请在下面的评论中告诉我，或者在 Twitter 上通过 [@thatpatrickguy](https://www.twitter.com/thatpatrickguy) 与我联系。

## 分享这篇文章