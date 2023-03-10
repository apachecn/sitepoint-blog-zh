# Cypress 测试:运行 Web 应用程序测试指南

> 原文：<https://www.sitepoint.com/cypress-testing/>

在本文中，我们将深入 Cypress 测试。Cypress 是一个现代的、开源的 web 应用程序自动化测试工具。我们将看看如何设置和使用 Cypress 测试 web 应用程序，我们还将看看使 Cypress 成为一个有吸引力的选择的一些特性。

Cypress 构建在[电子](https://www.sitepoint.com/electron-forge-react-build-secure-desktop-app/)之上，用于 Atom 文本编辑器和许多其他应用程序。它易于安装，有一个简单，清晰的 API。

**柏树测试大纲**

*   [什么是软件测试？](#whatissoftwaretesting)
*   [为什么软件测试很重要](#whysoftwaretestingisimportant)
*   自动化软件测试的好处
*   [柏树是什么？](#whatiscypress)
*   [柏树的特征](#featuresofcypress)
*   【Cypress 测试入门
*   [柏树安装](#cypressinstallation)
*   [柏树配置](#cypressconfiguration)
*   [写柏考](#writingcypresstests)
*   [Cypress 测试示例 1:测试登录表单](#cypresstestingexample1testingaloginform)
*   [Cypress 测试示例 2:测试购物车](#cypresstestingexample2testingashoppingcart)
*   [Cypress 测试示例 3:测试单页面应用程序](#cypresstestingexample3testingasinglepageapplication)
*   [柏树试跑者](#thecypresstestrunner)
*   [局部测试柏树](#testingcypresslocally)
*   [云中柏树测试](#cypresstestinginthecloud)
*   [持续集成](#continuousintegration)
*   [特拉维斯 CI](#travisci)
*   循环
*   詹金斯
*   [调试测试](#debuggingtests)
*   [用命令行测试 Cypress】](#cypresstestingwiththecommandline)
*   [使用浏览器开发工具进行 Cypress 测试](#cypresstestingwiththebrowserdevelopertools)
*   [升级 Cypress 测试套件](#upgradingthecypresstestsuite)
*   [有用的资源](#useful-resources)
*   [从硒迁移](#migratingfromselenium)
*   [网络驱动](#webdriverio)
*   [量角器](#protractor)
*   [结论](#conclusion)

## 什么是软件测试？

![Software Testing by Nick Youngson CC BY-SA 3.0 Pix4free.org](img/31e1283df0e11c404e57daaa7117c5f2.png)

软件测试由 Nick Youngson CC BY-SA 3.0 pix4 free . or

**软件测试**是验证应用程序功能的过程。这可以通过运行应用程序并尝试其各种功能来手动完成，也可以通过编写代码测试应用程序的功能来自动完成。

### 为什么软件测试很重要

测试很重要，因为它有助于确保软件按预期工作。它可以发现那些不被注意到的错误和问题，并且可以帮助提高软件的质量。测试尤其重要，因为它可以快速有效地运行测试，而不需要人工干预。

### 自动化软件测试的好处

自动化软件测试有很多好处:

*   **提高效率**:它们可以快速轻松地运行，无需人工干预。这节省了时间和资源。
*   提高准确性:它们通常比手工测试更准确，因为它们可以覆盖所有可能的场景。
*   增加覆盖面:与手工测试相比，它们可以覆盖更大部分的代码，提供应用程序的全面覆盖。
*   降低成本:从长远来看，他们可以通过减少对人工测试人员的需求来节省资金。

您想了解更多关于前端测试的信息吗？查看我们最新的指南， *[一个前端 Web 开发者测试指南](https://www.sitepoint.com/premium/books/a-frontend-web-developer-s-guide-to-testing/)* 。它将向您介绍一系列领先的、强大的框架，如 Selenium、Cypress、Puppeteer 和剧作家，并作为利用它们的测试覆盖能力的指南。

## 柏树是什么？

[![cypress logo](img/7ed8031468ee1809a2a817659a3ac40b.png)](https://www.cypress.io/)

Cypress 是一个现代的、开源的 web 应用测试套件。它建立在 Electron 的基础上，Atom 文本编辑器和许多其他应用程序都使用它。Cypress 易于安装，有一个简单明了的 API。

Cypress 可以用于自动化所有类型的测试，包括单元测试、功能测试和端到端测试。Cypress 也可以用于在多种浏览器和设备上并行运行测试。

![Test status menu, Command log, Viewport sizing, App review](img/bfce1a5a47cb3fc83b7b0b1cc8723d6f.png)

### 柏树的特征

使 Cypress 成为自动化测试的一个有吸引力的选项的一些特性是:

*   **简单安装:** Cypress 可以使用 npm 或 Yarn 安装。没有必要安装任何其他依赖项。
*   **清晰 API:**Cypress 提供的 API 简单易懂。所有的命令都是可链接的，使得代码易于阅读和编写测试。
*   **自动等待:** Cypress 在对元素执行任何操作之前自动等待元素出现。这使得代码更可靠，也更容易编写。
*   **并行执行:**使用 Cypress Test Runner 可以在多个浏览器和设备上并行运行测试。这使得测试的执行更快。

## Cypress 测试入门

[![node logo](img/7b027013f8460a1ffda377903554a089.png)](https://nodejs.org/en/)

Cypress 在 [Node.js](https://nodejs.org/en/) 12 或 14 及更高版本上工作，所以你需要在你的电脑上安装 Node.js，然后才能安装和使用 Cypress。

### 柏树装置

您可以通过 npm 安装 Cypress:

```
npm install cypress --save-dev 
```

或者你可以用纱线安装:

```
yarn add cypress --dev 
```

一旦安装了 Cypress，您可以使用以下命令运行它:

```
$(npm bin)/cypress open 
```

这将打开 Cypress 测试运行器。然后，您可以从这里运行您的测试。

### 柏树结构

Cypress 可以使用一个`cypress.json`文件进行配置。该文件应该位于项目的根目录下。下面是一个基本的例子:

```
{
  "baseUrl": "http://localhost:8080",
  "viewportWidth": 1200,
  "viewportHeight": 800,
  "fixturesFolder": "tests/e2e/fixtures",
  "integrationFolder": "tests/e2e/specs",
  "pluginsFile": "cypress/plugins.js",
  "supportFile": false,
  "screenshotsFolder": ".cypress/screenshots"  
} 
```

有关配置 Cypress 的更多信息，请参见[文档](https://on.cypress.io/configuration)。

## 编写 Cypress 测试

Cypress 测试使用 JavaScript 编写，并在浏览器中运行。这使得测试易于编写和理解。

每个赛普拉斯测试都被组织成“规格”。一个**规格**是一个特定特性或功能的测试集合。规格位于`integrationFolder`(默认为`tests/e2e/specs`)中。等级库或测试文件可以包含一个或多个测试。

下面是一个规格文件的基本示例:

```
describe('My App', function() {

  it('does something', function() {
    // Some test code here
  });

  it('does something else', function() {
    // Some test code here
  });  

}); 
```

![Spec file search, Browser menu](img/a503d1aec5742c509356d1cd2592a6bf.png)

### Cypress 测试示例 1:测试登录表单

在这个例子中，我们将编写一个 Cypress 测试登录表单。我们假设表单有两个字段，`username`和`password`，以及一个提交按钮。

首先，我们需要打开包含登录表单的页面。我们可以用`cy.visit()`命令来实现:

```
describe('My App', function() {

  it('does something', function() {
    cy.visit('http://localhost:8080/login');
  });

  // More tests here... 
}); 
```

接下来，我们需要填写`username`和`password`字段，并单击提交按钮。我们可以使用`cy.get()`、`cy.type()`和`cy.click()`命令来完成这项工作:

```
describe('My App', function() {

  it('does something', function() {
    cy.visit('http://localhost:8080/login');
    cy.get('#username').type('testuser');
    cy.get('#password').type('secret');    
    cy.get('.btn-submit').click();    

  });  

  // More tests here... 
}); 
```

### Cypress 测试示例 2:测试购物车

在这个例子中，我们将编写一个测试购物车特性的规范。我们假设购物车有两个字段，`quantity`和`item`，以及一个“添加到购物车”按钮。

首先，我们需要打开包含购物车表单的页面。我们可以用`cy.visit()`命令来实现:

```
describe('My App', function() {

  it('does something', function() {
    cy.visit('http://localhost:8080/cart');
  });

  // More tests here... 
}); 
```

接下来，我们需要填写`quantity`和`item`字段，并点击“添加到购物车”按钮。我们可以使用`cy.get()`、`cy.type()`和`cy.click()`命令来完成这项工作:

```
describe('My App', function() {

  it('does something', function() {
    cy.visit('http://localhost:8080/cart');
    cy.get('#quantity').type(5);
    cy.get('#item').type('pants');    
    cy.get('.btn-add-to-cart').click();    

  });  

  // More tests here... 
}); 
```

### Cypress 测试示例 3:测试单页面应用程序

在这个例子中，我们将测试一个使用 React 前端框架的[单页面应用程序](https://en.wikipedia.org/wiki/Single-page_application) (SPA)。该应用程序是一个简单的任务管理器，允许用户添加、编辑和删除任务。

首先，我们需要安装一些依赖项。我们需要以下物品:

*   [反应](https://reactjs.org/)
*   [创建 React 应用](https://github.com/facebookincubator/create-react-app)
*   [柏树](https://docs.cypress.io/)

我们可以用 [npm](https://www.npmjs.com/) 安装这些依赖项:

```
npm install --save react react-dom create-react-app cypress   # Install React and Create React App dependencies, as well as Cypress itself 
```

现在，我们可以使用创建 React 应用程序来创建新的 React 应用程序:

```
create-react-app my-app   # Create a new React app in the my-app folder 
```

接下来，我们需要安装一些额外的 Cypress 依赖项:

```
cd my-app                          # Change directory into the new app folder
npm install --save-dev cypress     # Install Cypress as a development dependency 
```

现在我们的依赖项已经安装好了，我们可以编写测试了。在`cypress/integration`文件夹中创建一个名为`tasks.spec.js`的新文件，内容如下:

```
describe('Tasks', function() {

  beforeEach(function() {           // This runs before each test in the suite
    cy.visit('http://localhost:3000');  // Visit the base URL of the app before each test 
  });

  it('should add a task', function() {   // This is a test case - replace with your own 

    // Enter task details into form and submit 
    cy.get('input[name="taskName"]').type('My Task');
    cy.get('textarea[name="taskDescription"]').type('My Description');
    cy.get('form').submit();       

    // Assert that the task appears in the list
    expect(cy.get('.tasks li').its('length')).to.equal(1);
   });
}); 
```

`beforeEach`功能在规范中的每个测试之前运行。在本例中，我们访问的是应用程序的基本 URL(`http://localhost:3000`)。

`it`函数包含我们实际的测试代码。第一个参数是测试用例的描述，第二个参数是包含测试代码的回调函数。在本例中，我们测试了任务管理器应用程序在使用表单提交任务时是否正确地将任务添加到列表中。我们首先在表单域中输入一些数据，然后提交。最后，我们断言现在列表中有一个任务使用了`expect`。

您可以在 Visual Studio 代码中运行您的测试，方法是从测试资源管理器(**视图- >测试资源管理器**)下打开**集成测试**。

或者，您可以使用 npm 从终端窗口运行它们:

```
npm run e2e                            # Run all tests in headless mode (Chrome)
npm run e2e:open                       # Run all tests with GUI ( Electron) 
```

## 赛普拉斯试跑者

运行每个 Cypress 测试有两种方式:本地和云中。

### 在本地测试 Cypress

您可以通过运行以下命令来打开 Cypress 测试运行器:

```
node_modules/.bin/cypress open 
```

测试运行程序将在新窗口中打开。从这里，您可以通过点击**运行所有规范**或**运行测试文件**按钮来运行所有测试或单个测试。

![Organize results, Test failures](img/3429125caf3dde7e3831d0e23d437c35.png)

### 云中的 Cypress 测试

Cypress 还提供基于云的测试服务，允许你在远程服务器上运行你的 Cypress 测试。如果您想在本地不可用的不同浏览器或设备上进行测试，这尤其有用。

要使用基于云的 Cypress 测试服务，你首先需要在[https://www.cypress.io/](https://www.cypress.io/)注册一个免费账户。一旦您有了一个帐户，您就可以连接您的 GitHub 存储库并选择您想要测试的分支。然后，Cypress 会自动运行您对该分支的每个提交的测试。

您还可以通过点击项目名称旁边的“run”按钮，从 Cypress 仪表板中手动触发一个测试运行。

![specs](img/846fc236c659bb7389fc1b0eecbfe9be.png)

## 连续累计

持续集成(CI)是在代码变更时自动构建和测试代码的实践。这允许开发人员在早期发现错误，并在开发过程的后期避免潜在的问题。

有许多不同的 CI 工具可用，但我们将集中讨论三个最流行的工具:Travis CI、CircleCI 和 Jenkins。

### 特拉维斯·CI

[![travis ci logo](img/622a4e7f3767b0775cba43d4d857cdeb.png)](https://upload.wikimedia.org/wikipedia/en/a/ab/Travis_CI_Logo.svg)

Travis CI 是一个流行的托管 CI 服务，对开源项目是免费的。它与 GitHub 集成在一起，便于设置和使用。

要在 Cypress 中使用 Travis CI，您需要在项目的根目录中创建一个`travis.yml`文件。这个文件告诉 Travis 如何构建和测试您的项目。这里有一个简单的例子:

```
# tell Travis we're using Node.js
language: node_js

# specify which versions of Node to use
node_js: 10

# run our tests
script:
  # or yarn test if you're using Yarn instead of NPM
  - npm run test

# set the base URL environment variable for Cypress
env: CYPRESS_BASE_URL=https://example.com 
```

这个文件告诉 Travis 使用节点 v10 进行测试，并运行`npm run test`命令(或者`yarn test`，如果您使用 Yarn 而不是 npm)。它还设置了`CYPRESS_BASE_URL`环境变量，Cypress 将在运行测试时使用这个变量。

您可以在 [Cypress 文档](https://docs.cypress.io/guides/guides/continuous-integration#section-travis-ci)中了解更多关于配置 Travis CI 以用于 Cypress 的信息。

### 绕圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈圈

[![circleci logo](img/33dadf2c28dc9ef952fd932ff59c4012.png)](https://upload.wikimedia.org/wikipedia/commons/8/82/Circleci-icon-logo.svg)

CircleCI 是另一个流行的托管 CI 服务，为开源项目提供免费计划。像 Travis 一样，它与 GitHub 集成在一起，使设置和使用变得容易。

要在 Cypress 中使用 CircleCI，您需要在项目的根目录中创建一个`circleci/config.yml`文件。这个文件告诉 CircleCI 如何构建和测试您的项目。

这里有一个简单的例子:

```
version: 2.0
jobs:
  build:
    docker:
      # specify the version of Node.js to use
      - image: circleci/node:10

    steps:
      # run our tests
      - run: npm run test

      # set the base URL environment variable for Cypress
      - run: echo "CYPRESS_BASE_URL=https://example.com" >> $BASH_ENV

  deploy:
    # ... deployment steps here ... 
```

这个文件告诉 CircleCI 使用节点 v10 进行测试，并运行`npm run test`命令。它还设置了环境变量`CYPRESS_BASE_URL`，Cypress 将在运行测试时使用这个变量。

您可以在 [Cypress 文档](https://docs.cypress.io/guides/guides/continuous-integration#section-circleci)中了解更多关于配置 CircleCI 与 Cypress 一起使用的信息。

### 詹金斯

[![jenkins logo](img/6bdd0ab843e54aaa5694aa4f03ae94c8.png)](https://upload.wikimedia.org/wikipedia/commons/e/e3/Jenkins_logo_with_title.svg)

Jenkins 是一个流行的自托管 CI 服务器，可用于开源和私有项目。它提供了广泛的特性和插件来扩展它的功能。

要将 Jenkins 与 Cypress 一起使用，您需要从 Jenkins 插件库中安装 [Cypress 插件](https://wiki.jenkins.io/display/JENKINS/Cypress+Plugin)。这个插件增加了一个新的“Cypress”构建步骤，可以用来运行 Cypress 测试。

在项目的 Jenkins 配置中，您需要添加一个“Cypress”构建步骤，并指定项目的`cypress.json`文件的路径。您还需要将该文件中的`baseUrl`属性设置为您的应用程序的 URL(例如`https://example.com`)。这里有一个`cypress.json`文件的例子:

```
{
  "baseUrl": "https://example.com"
} 
```

您可以在 [Cypress 文档](https://docs.cypress.io/guides/guides/continuous-integration#section-jenkins)中了解更多关于配置 Jenkins 用于 Cypress 测试的信息。

## 调试测试

调试 Cypress 测试简单明了。调试测试有两种方法:使用命令行，或者使用浏览器开发工具。

### 用命令行测试 Cypress

Cypress 命令行界面(CLI)有一个内置的调试器，可以用来调试测试。要使用 CLI 调试器，只需运行带有`--debug`标志的`cypress`命令。这将打开 Cypress GUI 并启用调试器。

GUI 打开后，您可以通过单击左侧面板中的行号在测试代码中设置断点。测试将在每个断点处暂停，您将能够检查变量并查看调用堆栈。您还可以恢复测试的执行，并逐句通过每一行代码。

### 使用浏览器开发工具进行 Cypress 测试

如果您没有使用 CLI，您仍然可以使用浏览器开发工具调试您的 Cypress 测试。为此，只需打开 DevTools 控制台，并在控制台中输入`cypress`。这将使您能够访问与 CLI 调试器相同的所有调试功能，包括设置断点和检查变量。

## 升级 Cypress 测试套件

当从 Cypress 的一个版本升级到另一个版本时，了解已经引入的任何突破性变化是很重要的。这些变化可能会破坏您的 Cypress 测试，并且可能需要您更新代码以便继续使用 Cypress 的新版本。

请务必查看官方的[迁移指南](https://docs.cypress.io/guides/references/migration-guide)。

[https://www.youtube.com/embed/mIqKNhLlPcU?rel=0](https://www.youtube.com/embed/mIqKNhLlPcU?rel=0)

<br>

## 有用的资源

如果你想了解更多关于 Cypress 的知识，这里有一些有用的资源:

*   [Cypress . io](https://www.cypress.io/):Cypress 的官方网站
*   [文档](https://docs.cypress.io/):柏树的官方文档
*   [柏博客](https://www.cypress.io/blog/):柏的官方博客
*   [GitHub](https://github.com/cypress-io) :赛普拉斯 GitHub 回购
*   [示例](https://github.com/cypress-io/cypress-example-todomvc):用 Cypress 构建的示例应用程序
*   [视频教程](https://docs.cypress.io/examples/examples/tutorials/):赛普拉斯的一系列视频教程

## 从硒迁移

[![selenium logo](img/1a6451932edfa41bd2d4ffac1a6683db.png)](https://www.selenium.dev/)

如果您目前正在使用 Selenium 测试 web 应用程序，您可能会考虑迁移到 Cypress。您可能希望这样做有几个原因:

*   Cypress 有一个比 Selenium 更简单、更直观的 API。
*   Cypress 测试运行在与被测试的应用程序相同的环境中，这使得调试更加容易。
*   Cypress 可以在与元素交互之前自动等待元素出现在 DOM 中，这使得测试更加健壮。

从 Selenium 迁移到 Cypress 有两个主要选项:WebDriverIO 和量角器。

### WebDriverIO

[WebDriverIO](https://webdriver.io/) 是一个 Node.js 库，它包装了 Selenium WebDriver API。它有一个类似于 Cypress 的 API，可以很容易地将现有的 Selenium 测试迁移到 WebDriverIO。WebdriverIO 网站上有[如何做到这一点的指南](https://webdriver.io/docs/selenium-to-webdriverio.html)。

### 量角器

[量角器](https://www.protractortest.org/)是一个用于角度应用的端到端测试框架，它使用 Selenium WebDriver API。它有一个类似于 Cypress 的架构，可以很容易地将现有的 Selenium 测试迁移到量角器。量角器网站上有[关于如何做的指南](https://www.protractortest.org/#/migration)。

## 结论

像 Cypress 这样的自动化测试工具可以让我们作为开发人员的生活变得更加容易。通过自动运行测试，我们可以快速捕捉错误并避免潜在的回归。

尽管完全依赖自动化测试很有诱惑力，但是手动测试仍然很重要。自动化测试可以捕获一些 bug，但不能捕获所有东西。仍然需要人眼来捕捉诸如拼写错误、布局问题和用户体验问题之类的问题。

如果你还没有使用自动化工具，我推荐你试试 Cypress。它很容易设置和使用，从长远来看，Cypress 测试可以为您节省大量时间和麻烦。

您想了解更多关于前端测试的信息吗？查看我们最新的指南， *[一个前端 Web 开发者测试指南](https://www.sitepoint.com/premium/books/a-frontend-web-developer-s-guide-to-testing/)* 。它将向您介绍一系列领先的、强大的框架，如 Selenium、Cypress、Puppeteer 和剧作家，并作为利用它们的测试覆盖能力的指南。

如果您喜欢这篇文章，您可能还会发现以下内容很有用:

*   [使用 unittest 和 pytest 进行 Python 单元测试的介绍](https://www.sitepoint.com/python-unit-testing-unittest-pytest/)
*   [如何使用 Jest 测试 React 组件](https://www.sitepoint.com/test-react-components-jest/)
*   [使用木偶师学习端到端测试](https://www.sitepoint.com/puppeteer-end-to-end-testing/)
*   [3 种免提连续测试方法](https://www.sitepoint.com/3-methods-for-hands-free-continuous-testing/)
*   [重新介绍 Jenkins:流水线自动化测试](https://www.sitepoint.com/re-introducing-jenkins-automated-testing-with-pipelines/)

## 分享这篇文章