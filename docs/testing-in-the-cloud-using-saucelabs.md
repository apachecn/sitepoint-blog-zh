# 使用 SauceLabs 在云中测试

> 原文：<https://www.sitepoint.com/testing-in-the-cloud-using-saucelabs/>

任何熟悉测试过程的开发人员都会同意这是纯粹的邪恶。在传统的设置测试中，每次都需要设置新的环境，这最多只是偶尔使用。不仅仅是你的大部分资源处于闲置状态，它还会消耗大量的时间。对于我们大多数人来说，测试是一项非关键的业务活动。但这是绝对必要的，除非你想面红耳赤地见你的客户。

今天，软件开发世界已经进入了云时代，一切都被视为服务。可悲的是，这些好处很少传递给世界上的试验方。嗯，不再是了。在这篇文章中，我将向您展示如何使用 [SauceLabs](https://saucelabs.com/) 在云中建立您的测试基础设施。

## 什么是测试基础设施？

让我们假设你正在建立下一个亚马逊。在成为主流之前，你已经雇佣了一些优秀的开发人员，你可能想要检查它在不同的操作系统、浏览器和分辨率下的表现。我们还需要检查您的应用程序在变成下一个 IRCTC 并被淹没之前能够管理多少个并行事务。显然，你需要某种基础设施来支持你的团队。这被称为测试基础设施。

通常情况下，您需要根据您的需求采购多个系统，并根据您选择的操作条件分别配置每个系统。对于负载测试，您甚至需要设置一个更强大、更昂贵的系统来有效地模拟您的主服务器。显然，这需要成本，对于小型组织来说是不可行的。

另一个问题是测试不是一项全职工作。只有当生产或操作条件发生变化时，才需要它。其他时候，你的资本只是闲置在空调房里。成本螺旋上升，甚至在你意识到你将没钱去关注其他重要的任务之前。

当云计算出现时，很多讨论都围绕着将测试基础设施迁移到云中。这个想法是通过将你的测试团队从测试基础设施中分离出来来获得效率。当您在云中设置您的测试基础架构时，它有两个作用——它消除了前期 Cap-Ex 的需要，并且它允许您根据需要灵活地扩展/缩减您的操作，从而有效地利用您的资源。它还有效地消除了必须单独配置每个系统的麻烦。不幸的是，这也不是一帆风顺的。对于云中的数据完整性和安全性有许多疑虑，这很容易让任何人放弃迁移。

## 酱油实验室

SauceLabs 是一个基于著名的 Selenium Web-driver 之上的跨浏览器自动化工具，对于我们这些不知道 Selenium 是什么的人来说，“这是一种允许程序员向 Web 浏览器发送命令以使它们执行任务的技术，就像它们被人类使用一样。从这个意义上说，它就像一个网页浏览机器人。”引用他们的网站。本质上，Selenium 让您可以通过测试脚本自由地跨浏览器自动测试页面。Selenium 服务器(本地安装)然后指示您的浏览器(启用 Selenium 扩展)测试您的应用程序。然而，安装和管理完整的 Selenium 套件是一件痛苦的事情，尤其是如果您正在考虑跨浏览器支持的话。你还必须面对硬件故障的冲击，以及维护的噩梦。

![](img/a007b93359fbf46af5980777f21c0cbb.png)

SauceLabs 是由 Selenium 背后的同一批人开发的，作为这个问题的解决方案。SauceLabs 负责设置测试环境，只是这次设置驻留在他们的云服务器上。然后，我们几乎可以从任何地方使用任何浏览器连接到这些机器。可以把 SauceLabs 想象成一个包含测试环境的容器，你可以通过互联网访问它。我们现在摆脱了所有与配置相关的麻烦，可以专注于真正重要的事情。当然，与任何云设置一样，您可以根据需要灵活地启动额外的实例，甚至运行并行测试。

## 入门指南

一旦你注册了 SauceLabs 账户，你将获得 200 分钟的免费测试时间，之后你将不得不支付一小笔费用来继续使用这项服务。进入管理仪表板后，您只需点击小指针图标即可设置您的测试环境。

一旦 SauceLabs 完成了对你的机器的配置，你就可以连接到你的网络应用程序进行进一步的测试。值得注意的一件有趣的事情是，所有的浏览器都预装了开发工具来帮助你动态调试问题。您可以浏览您的应用程序，并验证其功能性和非功能性问题。SauceLabs 允许你直接从工具中立即提出 bug。它会自动上传漏洞所在页面的截图，以及显示如何到达该页面的视频。这应该可以消除在修复问题过程中出现的任何歧义。它还生成一个 Selenium 风格的日志，这在以后会很方便。仪表板还为您提供了一个丰富多彩的、全面的测试场景概述，让您的经理满意。

## 自动进入

手动测试是多余的，耗时的，无聊的。Selenium 提供了一个强大的测试套件来自动化您的测试。最好的一点是，您不需要将自己局限于开发应用程序所用的语言。事实上，你可以用任何你喜欢的编程语言来编写你的测试脚本。出于本教程的目的，我们将使用 Ruby。本教程假设您的机器上已经安装了 Ruby 的工作副本。

让我们首先用

```
gem install selenium-webdriver
```

我们现在将编写一个基本的代码片段来检查搜索在 CloudSpring.com 是否有效。把你的钥匙放在手边。

```
require 'rubygems'
 require 'selenium-webdriver'</code>

caps = Selenium::WebDriver::Remote::Capabilities.firefox

caps[:name] = "Learning SauceLabs- CloudSpring"

driver=Selenium::WebDriver.for(
 :remote,
 :url => "http://saucelabs_username:saucelabs_accesskey@ondemand.saucelabs.com:80/wd/hub",
 :desired_capabilities => caps)

driver.navigate.to "http://CloudSpring.com"

element = driver.find_element(:xpath, "(//input[@name='s'])")
 element.send_keys "A newbie's guide to Amazon EC2"
 element.submit

puts "Yay! Search works."
 puts driver.title

driver.quit
```

将该文件保存为 test.rb，并通过以下方式执行

```
ruby test.rb
```

您的结果应该如下所示

![](img/669b13550b946c92c46c41fcf8c5b946.png)

前往仪表板，您会发现测试日志。它列出了接下来的步骤，甚至展示了脚本如何执行的视频。

我们编写的代码是不言自明的(请注意，完全没有用)，但是为了简洁起见，让我们绕一点弯路。

```
caps = Selenium::WebDriver::Remote::Capabilities.firefox
 caps[:name] = "Learning SauceLabs- CloudSpring"

driver=Selenium::WebDriver.for(
 :remote,
 :url => "http://saucelabs_username:sauce_labs_access_key@ondemand.saucelabs.com:80/wd/hub",
 :desired_capabilities => caps)
```

能力定义了脚本的范围。这是你定义执行环境的地方，比如操作系统，浏览器，甚至是版本。SauceLabs 以此为基础设置你的 VM。WebDriver 将测试运行的位置与 TI，或者在本例中是浏览器的位置分开。它连接到您的虚拟机，并在您设置的环境中开始执行。这就是 SauceLabs 让我们的生活变得更容易的地方。我们只需指定在哪个浏览器上运行脚本，它会负责安装等工作。我们没必要再动一根手指。

```
driver.navigate.to "http://CloudSpring.com"
```

![](img/47c34ae856ec487a273b1adb8a9363cd.png)

现在我们指示浏览器导航到您想要执行测试的 URL。您的驱动程序对象现在将拥有整个页面。

```
element = driver.find_element(:xpath, "(//input[@name='s'])")
 element.send_keys "A newbie's guide to Amazon EC2"
 element.submit
```

find_element()根据我们传递的参数遍历 DOM。我要求浏览器查找名为“s”的输入字段，并将搜索字符串传递给它。

作者注:我们本质上是在编写相同的旧 Selenium 脚本，并使用 SauceLabs 作为执行它的平台。如果您已经编写了任何 selenium 脚本，它们应该以同样的方式工作。对于我们这些不熟悉 Selenium 或者不太理解 WebDriver 如何工作的人来说，下载 Firefox 的 Selenium IDE 扩展，就可以直接从浏览器本身创建测试脚本。

在实时场景中，我们将使用验证..()或断言..()方法来验证脚本。但是因为这篇文章关注的是如何将你的测试脚本转移到云中，所以我在这里画了一条线。

## 安全问题

许多人避开云，只是因为他们觉得它不安全。这只是一个神话，但这是另一天的讨论。为了消除任何这种怀疑，你可能让 SauceLabs 的团队采用了一种新的方法——通过纯度来保证安全。他们向您保证，您的云虚拟机一旦被使用，所有数据都会被完全销毁，并且不会重新分配给其他任何人。有一个要测试的敏感数据池。别担心。他们提供一种叫做 [SauceConnect](https://saucelabs.com/docs/connect) 的安全服务来防止任何数据泄露。随着最初的障碍被排除，我强烈建议您利用这项出色的技术。

## 包扎

在云端测试，尤其是对初创公司来说，提供了大量的东西。你不需要预先支付一分钱，你为你所使用的东西付费，而不是浪费资源。有了 SauceLabs，你所有的测试脚本都会被自动记录，不再需要人工干预。当然，我们今天只是触及了表面，在您的应用程序可以在云上完全测试之前，还有很长的路要走。我们希望在以后的文章中纠正这一点。

你希望我们将来在这里报道什么？欢迎加入我们的讨论，让我们知道你的想法。感谢阅读，敬请关注。

## 分享这篇文章