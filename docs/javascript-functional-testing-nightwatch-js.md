# 用 Nightwatch.js 进行 JavaScript 功能测试

> 原文：<https://www.sitepoint.com/javascript-functional-testing-nightwatch-js/>

不久前，Eric Elliott 写了[JavaScript Testing:Unit vs Functional vs Integration Tests](https://www.sitepoint.com/javascript-testing-unit-functional-integration/)，其中他解释了不同类型的测试以及何时使用。

在今天的文章中，我想更深入地探讨 JavaScript 功能测试。为此，我们将探索并使用 [Nightwatch.js](http://nightwatchjs.org/) 库。

但是在开始之前，请允许我提醒您什么是功能测试，以及它为什么重要。粗略地说，功能测试是一个过程，旨在确保从用户的角度来看，应用程序按预期工作。

我们不是在谈论技术测试，比如单元测试或者集成测试。这里的目标是确保用户可以无缝地执行某个场景，比如登录平台、购买产品等等。

## Nightwatch.js 简介

[Nightwatch.js](http://nightwatchjs.org/) 描述自己是一个 a Node.js 驱动的端到端测试框架。它依赖于 [Selenium](https://github.com/SeleniumHQ/selenium) ，一个旨在促进网络浏览器自动化的项目。

通过一种对人友好的语法，Nightwatch.js 使“脚本化”场景成为可能，然后由浏览器自动播放(不一定是无头的)。

## 安装守夜人

Nightwatch 本身是一个 Node.js 模块，这意味着您需要在您的机器上安装 Node。最简单的方法是使用一个[版本管理器，比如 nvm](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/) 。Nightwatch 分布在 npm 上，所以你可以像安装其他模块一样安装它——或者用`-g`全局安装，或者用`--save-dev`在当前项目内部安装。

```
npm install --save-dev nightwatch 
```

Nightwatch 依赖于 Selenium WebDriver API，因此需要一个 Selenium WebDriver 服务器。这在 Java 上运行，这意味着您还必须在您的环境中安装 Java 开发工具包(JDK 7+)。你可以从甲骨文网站下载 JDK。

下载并安装后，您可以使用`java -version`确保 Java 在您的机器上正确可用。最后一步是从 [Selenium 下载页面](http://selenium-release.storage.googleapis.com/index.html)下载打包成 jar 的 Selenium 独立服务器。我建议您将它放在项目中的一个`bin`文件夹中。

```
your_project/
|
|– bin/
|   |– selenium-server-standlone-2.53.1.jar
|
`– package.json 
```

好了，我们都准备好了。让我们开始吧。

## 配置守夜

可想而知，Nightwatch 的配置很多。幸运的是，我们不需要知道所有的事情就可以开始。配置可以位于项目根目录下的一个`nightwatch.json`文件中，也可以位于一个`nightwatch.conf.js`文件中。我会推荐后者，因为它更灵活一些，还能让你添加评论。

```
var SELENIUM_CONFIGURATION = {
  start_process: true,
  server_path: 'bin/selenium-server-standalone-2.53.0.jar',
  host: '127.0.0.1',
  port: 4444
};

var FIREFOX_CONFIGURATION = {
  browserName: 'firefox',
  javascriptEnabled: true,
  acceptSslCerts: true
};

var DEFAULT_CONFIGURATION = {
  launch_url: 'http://localhost',
  selenium_port: 4444,
  selenium_host: 'localhost',
  desiredCapabilities: FIREFOX_CONFIGURATION
};

var ENVIRONMENTS = {
  default: DEFAULT_CONFIGURATION
};

module.exports = {
  src_folders: ['tests'],
  selenium: SELENIUM_CONFIGURATION,
  test_settings: ENVIRONMENTS
}; 
```

*注意:我个人发现，当配置文件被分割成更小的配置对象时，阅读起来更容易，而 JSON 文件不允许这样做。*

在我们的例子中，我们告诉 Nightwatch，我们的测试将保存在一个`tests`文件夹中，使用特定的 Selenium 配置和特定的测试设置。让我们看一下每一部分:

```
var SELENIUM_CONFIGURATION = {
  start_process: true,
  server_path: 'bin/selenium-server-standalone-2.53.0.jar',
  host: '127.0.0.1',
  port: 4444
}; 
```

有了这个配置对象，我们告诉 Selenium 在`127.0.0.1:4444`上运行，这恰好是 Nightwatch 的默认值。我们还确保它使用我们下载并存储在`bin`文件夹中的 Selenium 服务器自动启动。

*注意:对于更高级的用法，请务必查看所有 Selenium 选项的[列表。](http://nightwatchjs.org/guide#selenium-settings)*

转到实际的测试设置:

```
var DEFAULT_CONFIGURATION = {
  launch_url: 'http://localhost',
  selenium_port: 4444,
  selenium_host: 'localhost',
  desiredCapabilities: FIREFOX_CONFIGURATION
};

var ENVIRONMENTS = {
  default: DEFAULT_CONFIGURATION
}; 
```

Nightwatch 的`test_settings`选项需要一个对象，它的关键字是每个环境的名称，映射到另一个配置对象。在我们的例子中，我们还没有设置自定义环境，所以我们使用`default`。后来，我们可以有一个`staging`或`production`测试环境。

在环境配置中，我们告诉 Nightwatch 打开哪个 URL(例如，对于 staging 来说是不同的)，以及应该使用什么浏览器来运行测试。

*注意:如需更高级的用法，请务必查看所有测试选项的[列表。](http://nightwatchjs.org/guide#full-list-of-settings)*

```
var FIREFOX_CONFIGURATION = {
  browserName: 'firefox',
  javascriptEnabled: true,
  acceptSslCerts: true
}; 
```

在我们的场景中，我们将使用没有启用 JavaScript 的 Firefox，允许 SSL 证书。我们可以更进一步，指定特定的浏览器版本(用`version`)或操作系统(用`platform`)。

*节点:如需更高级的用法，请务必查看所有功能选项的[列表。](https://github.com/SeleniumHQ/selenium/wiki/DesiredCapabilities)*

好了，我们现在有了一个合适的配置。是时候写第一个测试了！

## 编写守夜测试

对于我们的测试，我们将考虑一个在`/login`的登录页面，包含一个电子邮件字段、一个密码字段和一个提交按钮。当提交表单时，用户应该被重定向到一个写着“新闻提要”的页面。

在我们的配置中，我们指定测试位于名为`tests`的文件夹中。让我们创建这个`tests`文件夹，以及一个名为`login.js`的文件。

```
your_project/
|
|– bin/
|   |– selenium-server-standlone-2.53.1.jar
|
|– tests/
|   |– login.js
|
|- nightwatch.conf.js
`– package.json 
```

这个文件将导出一个描述我们场景的对象。每个键(如果有几个)是测试的名称，映射到包含要执行的步骤的函数。

```
module.exports = {
  'Login test': function (client) {
    // Steps to execute
  }
}; 
```

测试函数公开了一个对象，该对象提供了描述场景所需的 API。首先要做的是导航到登录 URL。然后，填写字段并按下按钮。最后，检查我们是否能发现“新闻提要”文本。

```
module.exports = {
  'Login test': function (client) {
    client
      .url('http://foobar.qux/login')
      .setValue('input[name="email"]', 'foo@bar.com')
      .setValue('input[name="password]', 'p455w0rdZ')
      .click('button[type="submit"]')
      .assert.containsText('main', 'News feed')
      .end();
  }
}; 
```

*注意:总是使用`.end()`来终止指令列表，以便正确关闭 Selenium 会话。*

这很简单！我们现在可以运行我们的测试，看看它是否有效:

```
./node_modules/.bin/nightwatch 
```

这应该会给我们这样的结果:

![Implementing JavaScript functional testing with Nightwatch.js](img/5ac323bfded1dcf31e288d790bc5d5c0.png)

*注意:随着 Firefox 47 的发布，[基于扩展的版本 FirefoxDriver 停止工作](http://www.theautomatedtester.co.uk/blog/2016/selenium-webdriver-and-firefox-47.html)。这已经在 Firefox 47.1 和 Selenium 2.53.1 中[修复。要使用不同的浏览器运行测试，请参考](https://github.com/eviltester/startUsingSeleniumWebDriver)[项目的 wiki](https://github.com/nightwatchjs/nightwatch/wiki) 。*

为了避免每次都到达 Nightwatch 二进制文件，我们可以做的最后一件事是在`package.json`中创建一个小的 npm 脚本来给它起别名:

```
{
  "scripts": {
    "test": "nightwatch"
  }
} 
```

## 改进守夜测试

拥有大量的功能测试会导致大量的重复信息，使得维护(是的，测试套件也需要维护)变得困难。为了防止这种情况，我们可以使用页面对象。

在端到端测试领域，页面对象方法是一种流行的模式，包括将测试过的页面(或页面片段)包装成对象。目标是抽象出底层的 HTML 和一般配置来简化场景。

幸运的是，Nightwatch 有一个简单的方法来处理页面对象。我们需要做的第一件事是在配置中添加`page_objects_path`选项。感觉`tests/pages`有道理；你可以指定任何你想要的文件夹。

```
module.exports = {
  src_folders: ['tests'],
  page_objects_path: 'tests/pages',
  selenium: SELENIUM_CONFIGURATION,
  test_settings: ENVIRONMENTS
}; 
```

现在，我们可以在这个文件夹中创建一个`login.js`文件。文件名稍后将被用作检索该文件中指定的所有配置的关键字，所以我建议给它一个合理的名称。

在这个文件中，我们将指定一个 URL 并用一个友好的名称为一些 HTML 元素起别名，以便更容易地编写未来的场景。

```
module.exports = {
  url: function () {
    return this.api.launch_url + '/login';
  },
  elements: {
    emailField: 'input[name="email"]',
    passwordField: 'input[name="password"]',
    submitButton: 'button[type="submit"]'
  }
}; 
```

请注意，我们没有对 URL 进行硬编码。相反，我们让它依赖于环境配置中定义的`launchUrl`选项。这样，我们的页面对象是上下文不可知的，无论在什么环境下都可以工作。

现在修改我们的测试来使用 page 对象是非常简单的。首先，我们需要通过客户端的`page`对象检索页面。每个页面对象都被公开为以页面对象文件的名称命名的函数(例如`login()`)。

然后我们可以用我们的别名替换我们的 CSS 选择器，以符号`@`为前缀来表示我们引用的是一个自定义名称。就是这样。

```
module.exports = {
  'Login test': (client) => {
    const page = client.page.login();

    page.navigate()
      .setValue('@emailField', 'foo@bar.com')
      .setValue('@passwordField', 'p455w0rdZ')
      .click('@submitButton')
      .assert.containsText('main', 'News feed');

    client.end();
  }
}; 
```

注意我们是如何在客户端而不是页面上终止会话的。

## 使用多种环境

能够在不同的环境中运行功能测试对于确保本地工作没有破坏任何用户路径是有用的，或者例如，阶段化和产品化工作是相同的。

为了在特定的环境中运行测试，我们可以在 CLI 中使用`--env`选项。当我们省略选项时，使用`default`环境(已经在我们的配置中)。

让我们在配置中添加一个暂存环境。

```
var STAGING_CONFIGURATION = Object.assign({}, DEFAULT_CONFIGURATION, {
  launch_url: 'http://staging.foobar.qux'
});

var ENVIRONMENTS = {
  default: DEFAULT_CONFIGURATION,
  staging: STAGING_CONFIGURATION
}; 
```

现在，当运行我们的测试时，`launch_url`选项将根据环境的不同而不同。

```
npm test --env staging 
```

## 包装东西

我们来总结一下这一切。js 是一个 JavaScript 框架，用于编写端到端的功能测试。它依赖于 Selenium WebDriver API，能够自动运行不同的浏览器。

编写测试主要包括定义一个典型的用户场景。有一个简单但非常完整的 API 可用于此目的。

从那以后，我将把它留给你，并鼓励你开始为你最大的项目编写功能测试，以确保你再也不会破坏用户特性了！

## 分享这篇文章