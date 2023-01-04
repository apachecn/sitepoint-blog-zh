# 7 个开源测试自动化框架

> 原文：<https://www.sitepoint.com/7-open-source-test-automation-frameworks/>

*本文最初由 [TestProject](https://blog.testproject.io/2017/10/11/open-source-test-automation-frameworks/) 发布。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

随着我们进入 2017 年的最后一个季度，TestProject 的团队决定收集最好的开源测试自动化框架，以帮助您选择适合您的框架！

以下是 7 种不同的开源测试自动化框架的优缺点。

## [1。机器人框架](http://robotframework.org/?utm_source=sitepoint&utm_medium=article&utm_campaign=testproject_automation&utm_content=title)

[Robot Framework (RF)](https://blog.testproject.io/2016/11/22/robot-framework-introduction/) 是一个用于验收测试和验收测试驱动开发(ATDD)的测试自动化框架。这个框架是用 Python 写的，但是也可以在 Jython (Java)和 IronPython(。NET)，因此是跨平台的(Windows、Linux 或 MacOS)。

**优点:**

*   它通过利用[关键字驱动测试(KDT)](https://blog.testproject.io/2015/09/15/kdt-keywords-driven-testing-vs-test-automation-scripts/) 方法简化了测试自动化过程，这有助于测试人员创建易于进行的可读测试。
*   具有易于使用的测试数据语法。
*   围绕它有一个丰富的生态系统，由作为独立项目开发的各种通用测试[库](http://robotframework.org/#libraries)和[工具](http://robotframework.org/#tools)组成。
*   有许多 API，使它具有高度的可扩展性。
*   尽管它不是一个内置的功能，RF 能够通过 [pabot 库](https://github.com/mkorpela/pabot)或 [Selenium Grid](https://yuanjiang.space/setup-selenium-grid-to-use-in-robotframework) 执行并行测试。

**缺点:**

*   自定义 HTML 报表并不容易。

**底线:**如果您的目标是使用广泛的库和扩展来实现 KDT 自动化，那么强烈推荐这个跨平台框架。如果你想添加新的关键字(通过 [RF 测试库 API](http://robotframework.org/robotframework/latest/RobotFrameworkUserGuide.html#different-test-library-apis)，需要 Java/Python/C 编程语言的基础知识。

## [2。JUnit](http://junit.org/junit4/?utm_source=sitepoint&utm_medium=article&utm_campaign=testproject_automation&utm_content=title)

JUnit 是 Java 应用程序单元测试的框架，用于编写和运行可重复的测试。

**优点:**

*   测试是用纯 Java 编写的，Java 被认为是全球领先的编程语言。
*   支持测试驱动开发(TDD)。
*   使您能够创建自己的单元测试用例集。
*   与其他工具(例如， [Maven](https://maven.apache.org/) )和 ide(例如， [IntelliJ](https://www.jetbrains.com/idea/) )集成得非常好。
*   有历史——所以它有很大的用户基础，很容易找到相关文档。

**缺点:**

*   如果需要嘲讽能力，需要添加 [Mockito](http://site.mockito.org/) (或者其他一些嘲讽库)。
*   测试对于非技术人员来说是不可读的，因为例如 JUnit 中的方法名受到 Java 约定的约束。

**底线:**如果你想为你的 Java 应用程序编写单元测试，这可能是最好的选择。但是，对于功能测试或非 Java 应用程序，您应该考虑其他解决方案。

## [3。斯波克](http://spockframework.org/?utm_source=sitepoint&utm_medium=article&utm_campaign=testproject_automation&utm_content=title)

Spock 是 Java 和 Groovy 应用程序的测试和规范框架。它基于 JUnit。

**优点:**

*   创建可读的测试并支持简单的英语句子，使其易于阅读。
*   提供了周围的环境，因此它很容易使您理解如何修复故障。
*   具有内置的模仿和存根功能。
*   支持数据驱动测试(DDT)。

**缺点:**

*   需要 Groovy 编程语言的基础知识。

**底线:**如果您的应用程序基于 JVM，并且您的目标是使用 [DSL](https://en.wikipedia.org/wiki/Domain-specific_language) 实现 BDD 测试自动化，那么这个框架就是为您准备的！

## [4。努尼特](http://nunit.org/?utm_source=sitepoint&utm_medium=article&utm_campaign=testproject_automation&utm_content=title)

NUnit 是一个面向所有人的单元测试框架。网络语言。最初受 Junit 的启发，它完全是用 C#编写的，并且已经完全重新设计以利用许多。NET 语言特性。

**优点:**

*   快速启动和测试执行。
*   附带断言和注释。
*   支持并行测试。
*   支持测试驱动开发(TDD)。

**缺点:**

*   它不是跨平台的，因为它仅用于。网络语言。
*   它没有集成到 Visual Studio 生态系统中，所以使用它意味着更多的维护。

**底线:**一个不错的 C#单元测试开源框架，有着悠久的历史和良好的声誉。但是，如果您已经在使用。NET 语言，你可以考虑 MSTest。

## [5。测试](http://testng.org/doc/?utm_source=sitepoint&utm_medium=article&utm_campaign=testproject_automation&utm_content=title)

TestNG 是一个 Java 测试自动化框架，受 JUnit 和 NUnit 的启发，但包括改进的和新的功能(NG-下一代)。它旨在覆盖所有的测试自动化类别:单元测试、功能测试、端到端、集成测试等。

**优点:**

*   它很容易集成到 Maven 循环中。
*   让开发人员能够编写灵活而强大的测试。
*   支持数据驱动测试(DDT)。
*   注释很容易理解。
*   测试用例可以很容易地分组。
*   允许您创建并行测试。

**缺点:**

*   只支持 Java，所以你至少需要具备 Java 编程语言的基础知识。
*   你必须在框架设置和设计上投入时间。

**底线:**如果您使用 Java，正在寻找端到端的测试自动化框架，并且愿意在框架设置上投入一些时间——您肯定应该考虑使用 TestNG。

## [6。茉莉](https://jasmine.github.io/?utm_source=sitepoint&utm_medium=article&utm_campaign=testproject_automation&utm_content=title)

[Jasmine](https://blog.testproject.io/2016/08/14/javascript-unit-testing-with-tdd-jasmine-and-karma/) 是一个 JavaScript 单元测试框架。它也被称为 JavaScript 的行为驱动开发(BDD)测试框架。它适用于网站、Node.js 项目或 JavaScript 可以运行的任何地方。主要是[搭配](https://blog.testproject.io/2016/09/01/front-end-development-unit-test-automation-trends2/)。

**优点:**

*   除了 JavaScript 之外，它还可以在 Python 和 Ruby 中运行，如果您想在运行服务器端测试的同时运行客户端测试，这可以为您提供极大的帮助。
*   受到许多 ci(Codeship、Travic 等)的支持。).
*   具有内置的断言语法。

**缺点:**

*   在大多数情况下，它需要一个测试运行者(比如 [Karma](https://karma-runner.github.io/1.0/index.html) )。
*   在异步测试方面有困难。

**底线:**如果你正在寻找一个统一的(客户机-服务器)单元测试解决方案，Jasmin 可能是你的理想选择。

## 7 .[。摩卡](https://mochajs.org/?utm_source=sitepoint&utm_medium=article&utm_campaign=testproject_automation&utm_content=title)

Mocha 是一个 JavaScript 单元测试框架，在 [Node.js](https://nodejs.org/en/) 上运行测试。它是[主要搭配 ReactJS](https://blog.testproject.io/2016/09/01/front-end-development-unit-test-automation-trends2/) 。

**优点:**

*   内置了自己的测试运行器。
*   支持异步测试。
*   允许灵活性，因为您可以使用任何断言库(Chai、expect.js、Must.js 等)。)来满足您的需求(作为 Node 标准‘assert’函数的替代)。

**缺点:**

*   对该领域而言相对较新(2012 年开发)，这意味着它仍在变化，其用户群和支持在某些方面可能会有所欠缺。
*   仅提供基本的测试结构，因此需要额外的设置和配置(对某些人来说可能是一种优势)。

**底线:**如果你正在寻找 JavaScript 独立单元测试框架，Mocha 是你的首选框架！

## 分享这篇文章