# 3 种无需手动的连续测试方法

> 原文：<https://www.sitepoint.com/3-methods-for-hands-free-continuous-testing/>

*本文最初发表于[crossbrowsertest](https://crossbrowsertesting.com/blog/test-automation/continuous-testing-with-jenkins-scheduling/)。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

**为了在速度和质量之间达到完美的[平衡，希望实现](https://crossbrowsertesting.com/blog/development/choosing-speed-vs-quality/)[持续集成和持续交付](http://crossbrowsertesting.com/continuous-integration)的团队也必须实施[持续测试](http://crossbrowsertesting.com/continous-testing)。**

这往往说起来容易做起来难。让你的团队每时每刻都运行测试实际上是不可能的——毕竟他们在工作之外也有自己的生活，而且在运行一个 [Selenium 脚本](https://crossbrowsertesting.com/selenium-testing/script)的同时，很难把晚餐放在桌子上。那么，即使你不在座位上，你如何运行你的脚本呢？

幸运的是，您可以实现各种各样的无需人工干预的连续测试选项，这将在您吃饭、睡觉或者甚至完成一些探索性测试时让测试继续运行。

### 为什么要持续测试

持续测试有什么意义？有几个主要的优势可以让你在日常生活中获得更多。

*   **睡觉时测试**–通过持续测试，你可以在睡觉时进行测试。通过将测试设置为通宵运行，您可以体验到第二天早上就能看到测试结果的奢侈，并且只需看一眼就能知道测试结果是否通过。[问问美国的测试厨房](https://crossbrowsertesting.com/blog/test-automation/americas-test-kitchen-quality-assurance/)有多方便就知道了。
*   **跟上敏捷、DevOps 和 CI/CD**–当软件开发团队不断集成时，您需要一个测试策略来确保那些新特性、修复和变更通过质量流程，无论它们实现的频率有多高。持续测试是 QA 团队跟上不断变化的特性、频繁的集成和快速工作流的唯一方式。
*   **在每次回归中捕捉 bug**–当应用程序的代码不断有小的变化时，它可能会破坏以前正常运行的东西。你不想让你的顾客被传染。持续的测试将帮助你第一个发现新的错误，这样你就可以快速调试并放心地发布给用户。
*   **升级您的自动化游戏**–通过持续测试自动化您的自动化，并根据您的需要设置运行测试的频率。然后回来查看测试结果或屏幕截图，它们会准确地告诉您需要了解的关于应用程序健康状况的信息。另外，[并行测试](https://crossbrowsertesting.com/automated-testing/parallel)允许你进一步加速连续测试，以增加浏览器覆盖率，而不影响运行时间。

### 使用 CI 服务器进行持续测试

Selenium WebDriver 长期以来为团队提供了跨浏览器自动化测试的能力。但是为了持续运行这些测试，需要一个持续集成工具，比如 Jenkins、VS Team Services、Buildbot、Bamboo、CircleCI、TravisCI、Codeship 或 TeamCity。

到目前为止，最流行的选择是用 Java 编写的开源自动化服务器 [Jenkins](https://jenkins.io/) 。事实上，根据 [SmartBear 2018 年测试状态调查](https://smartbear.com/resources/ebooks/state-of-testing-report-2018/)，66%的参与者选择 Jenkins 作为他们的 CI 服务器。对于采用敏捷和 DevOps 工作流的开发团队来说，Jenkins 是持续集成和交付的可靠工具。

但是那些想利用 Jenkins 用 Selenium 进行持续测试的测试团队怎么办呢？与 Jenkins 的 [CrossBrowserTesting 集成](https://help.crossbrowsertesting.com/integrations/jenkins)让您可以使用 Selenium、Appium 和可视屏幕截图测试运行自动化功能测试，这意味着每次运行您的构建流程时，您都可以跨[数千种浏览器](http://crossbrowsertesting.com/browsers)同时运行您的测试套件。

对于已经使用 Selenium 和 CI 工具进行部署的团队来说，这是开始持续测试的理想选择。虽然 Jenkins 往往是 CI 和自动化测试调度的最受欢迎的选择，但 crossbrowsertest 也集成了 TeamCity 和 VS Team Services，因此无论您已经在使用什么工具，都可以很容易地将 crossbrowsertest 集成到您当前的工作流中。

### 使用记录和回放工具进行连续测试

对于不太熟悉 Selenium 但仍然希望自动化的团队来说，记录和回放工具可能是更实用的选择。

[Record & Replay](http://crossbrowsertesting.com/record-and-replay) 使团队能够跨浏览器实现[自动化，就像他们使用 Selenium 一样，不需要脚本和框架知识。这为技术水平较低的团队成员，或者那些刚刚开始自动化的人，提供了一个更容易访问的选项，来创建自动化测试。随着像](http://crossbrowsertesting.com/browser-automation) [Selenium IDE 及其替代品](https://crossbrowsertesting.com/blog/selenium/selenium-ide-alternatives/)这样的工具重新成为人们关注的焦点，这些选项对现代测试团队越来越有吸引力。

但是记录和回放工具通常只运行一次测试来检查测试是否通过，测试人员如何实现连续测试呢？

有了 CrossBrowserTesting 中的新[调度，你可以调度一个记录&重放套件，根据你需要的频率运行。对于技术测试人员较少的组织，这允许您实现连续测试，而不需要运行 Selenium 和 Jenkins 或其他自动化服务器的技能。](https://crossbrowsertesting.com/automated-testing/scheduling)

### 连续截图测试

当他们所有的测试都通过了，而用户仍然指出视觉上的错误时，那些只关注功能测试而没有结合视觉测试的团队将会失望。

当视觉元素可能会因浏览器或设备而异，使用户的体验变得困难或不可能，但仍能正常工作时，在回归中运行[比较截图](https://crossbrowsertesting.com/visual-comparisons)非常重要。将这些页面并排放在一起并与它们的历史版本进行比较，意味着您可以更好地了解会破坏应用程序的更改，以便在它们到达最终用户之前进行调试。

虽然您总是可以在 CrossBrowserTesting 中运行自动截图，但需要手动操作才能以一定的节奏重新运行这些测试。现在，您可以设置这些截图测试来运行新的调度功能，就像您可以使用 Record & Replay 一样。

### 每个团队的持续测试

无论您已经在使用什么工具，您处于什么技术水平，或者您的团队在他们的测试自动化之旅中走了多远，都有帮助每个团队在 [CrossBrowserTesting](https://crossbrowsertesting.com/) 中实现持续测试的选项。

借助 Selenium、Record & Replay 和自动截图安排测试的选项，您可以将自动化提升到一个新的水平，并根据您的决定，每天、每周或每月通过电子邮件或 Slack 接收测试结果。

**如果你喜欢这篇文章，你可能会发现下面的内容很有用:**

*   Cypress 测试:运行 Web 应用程序测试的指南
*   [使用 unittest 和 pytest 进行 Python 单元测试的介绍](https://www.sitepoint.com/python-unit-testing-unittest-pytest/)
*   [如何使用 Jest 测试 React 组件](https://www.sitepoint.com/test-react-components-jest/)
*   [使用木偶师学习端到端测试](https://www.sitepoint.com/puppeteer-end-to-end-testing/)
*   [重新介绍 Jenkins:流水线自动化测试](https://www.sitepoint.com/re-introducing-jenkins-automated-testing-with-pipelines/)

## 分享这篇文章