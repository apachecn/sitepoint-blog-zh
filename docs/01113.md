# 如何用 Selenium WebDriver 和 Mocha 测试您的 JavaScript

> 原文：<https://www.sitepoint.com/how-to-test-your-javascript-with-selenium-webdriver-and-mocha/>

*这篇文章最初发表在[测试项目](https://blog.testproject.io/2017/06/07/javascript-testing-with-selenium-webdriver-mocha/)上。*

如果你想用 JavaScript 写一个功能测试，下面的教程为 UI 自动化工程师提供了完美的结构参考材料，用于用 [Selenium WebDriver 3](https://blog.testproject.io/2016/11/07/selenium-webdriver-3/) 、Mocha 和 NodeJS 进行 [JavaScript 测试](https://blog.testproject.io/category/javascript/)。

如今，JavaScript 是一种无处不在的 web 语言，它似乎克服了其“臭名昭著”的过去，不仅对于客户端，而且对于服务器领域都成为了一个更加坚实的平台。Mocha.js，或简称 Mocha，是一个运行在 Node.js 上的功能丰富的 JavaScript 测试框架，它提供了一个平台和 API，以使用 Google 的 V8 JavaScript 引擎为基础构建独立的服务器端应用程序。

***注意:**要开始学习这篇 JavaScript 教程，您需要熟悉 [NodeJS](https://nodejs.org/en/docs/guides/) 和 [JavaScript](https://www.javascript.com/learn/javascript/strings) 编程语言的基础知识。

**教程概述:**

**1。摩卡测试构造**

*   介绍
*   装置
*   安装 Chai 断言模块
*   测试套件和测试用例结构
*   用 Mocha 构建测试
*   运行 Mocha 的测试套件和测试用例
*   管理异步测试代码的同步

**2。使用与 MochaJS 集成的 Javascript Selenium 3 API**

*   硒介绍
*   硒装置
*   web 驱动程序构造
*   将 MochaJS 与 Selenium WebDriver 3 集成

**使用的版本:**

*   使用的节点版本:6.10.1 (LTS)
*   摩卡:2.5.3
*   WebDriverJS: 3.3.0

## 1.用 Mocha 构建测试

### 摩卡简介

如前所述，Mocha 是一个在 Node 上运行测试的 JavaScript 测试框架。Mocha 通过 npm 以节点包的形式出现，允许您使用任何断言库来替代 Node 的标准“assert”函数，如 ChaiJS。此外，Mocha 有几个类似于 [Jasmine](https://blog.testproject.io/2016/08/14/javascript-unit-testing-with-tdd-jasmine-and-karma/) 的组件，Jasmine 是另一个流行的测试自动化框架，我们在[前端和单元测试自动化趋势](https://blog.testproject.io/2016/09/01/front-end-development-unit-test-automation-trends2/)的研究中提到过。

Mocha 提供了一个 API，它指定了一种将测试代码组织成测试套件和测试用例模块的方法，以便执行，并随后生成测试报告。Mocha 提供了两种运行模式:通过命令行(CLI)或编程方式(Mocha API)。

### 安装摩卡

如果要在 CLI 中使用 Mocha，那么它应该作为 Node.js 全局安装。

```
npm install -g mocha 
```

**安装柴断言模块**

```
npm install --save chai 
```

`–save`选项用于在项目范围内安装模块，而不是全局安装。

### 测试套件和测试用例结构

在 Mocha 中，测试套件由接受回调函数的“describe”关键字定义。一个测试套件可以包含子/内部测试套件，内部测试套件可以包含它们自己的子测试套件，等等。测试用例由“it”函数表示，它接受回调函数并包含测试代码。

Mocha 支持测试套件设置和测试用例设置功能。测试套件设置由`before`表示，而测试用例设置应用`beforeEach`。`beforeEach`实际上是套件中每个案例的通用设置，将在每个案例之前执行。

与设置一样，Mocha 支持测试套件和测试用例拆卸功能。测试套件的拆除用`after`表示，而测试用例的拆除用`afterEach`实现，分别在测试套件和每个测试用例之后执行。

创建一个将“托管”测试套件的文件，例如 test_suite.js，并将以下内容写入其中；

```
describe("Inner Suite 1", function(){

    before(function(){

        // do something before test suite execution
        // no matter if there are failed cases

    });

    after(function(){

        // do something after test suite execution is finished
        // no matter if there are failed cases

    });

    beforeEach(function(){

        // do something before test case execution
        // no matter if there are failed cases

    });

    afterEach(function(){

        // do something after test case execution is finished
        // no matter if there are failed cases

    });

    it("Test-1", function(){

        // test Code
        // assertions

    });

    it("Test-2", function(){

        // test Code
        // assertions

    });

    it("Test-3", function(){

        // test Code
        // assertions

    });

}); 
```

### 运行 Mocha 测试套件和测试用例

Mocha 以三种方式支持测试的执行:整个测试套件文件、通过“grep”模式过滤的测试和在目录树中查找的测试 grep 过滤(递归选项)

**运行整个测试套件文件:**

```
mocha /path/to/test_suite.js 
```

从特定的套件文件运行特定的套件或测试。

如果选择了一个套件，那么将执行所有子套件和/或测试。

```
mocha -g “Test-2” /path/to/test_suite.js 
```

通过在目录树中递归搜索来运行特定的套件或测试文件。

```
mocha --recursive -g “Test-2” /directory/ 
```

对于广泛的 CLI 选项:

```
mocha –-help 
```

### 管理异步测试代码的同步

万一异步函数与 Mocha 一起使用并且没有正确处理，您可能会发现自己在挣扎。如果异步代码(例如 http 请求、文件、selenium 等。)将在测试用例中使用，**遵循这些准则来克服意外结果:**

**1。`done`功能**

在您的测试函数(`it`)中，您需要沿着回调链向下传递`done`函数——这确保它在您的最后一步之后执行。

下面的例子强调了完成功能。在这种情况下，测试功能结束时将出现三秒钟的超时。

```
it(‘Test-1’, function(done){

    setTimeout(function(){

        console.log(“timeout!”);

  // mocha will wait for done to be called before exiting function.
        done();     
    }, 3000);

}); 
```

**2。退货承诺**

返回一个承诺是确保 Mocha 在使用异步函数时已经执行了所有代码行的另一种方法(在这种情况下不需要‘done’函数)。)

```
it(‘Test-1’, function(done){

    var promise;
    promise = new Promise(function(resolve, reject){
        setTimeout(function(){

            console.log("Timeout");
            resolve();

        }, 3000);

    });
    // mocha will wait for the promise to be resolved before exiting
    return promise;  
}); 
```

## 2.Javascript Selenium 3 与 MochaJS 的集成

### 硒介绍

[Selenium](http://www.seleniumhq.org/) 是一个控制网页浏览器并模拟用户行为的库。更具体地说，Selenium 为用户提供了称为“绑定”的特定语言库 API。这些“绑定”充当客户端以执行对中间组件的请求，并充当服务器以最终控制浏览器。

Selenium APIs 或绑定现在存在于所有流行的开发语言中。所有语言实现现在都同意与 API 函数的命名约定保持一致。

中间组件可以是实际的 web 驱动程序，可以在每个 selenium 包、Selenium 独立服务器以及供应商的本地浏览器控制驱动程序(如 Mozilla 的 Geckodriver、Chrome 的 chromedriver 等)中找到。此外，Selenium webdriver 通过“JsonWired 协议”与浏览器驱动程序通信，并成为 W3C Web 标准。

### 硒装置

在深入研究 Selenium 与 MochaJS 的集成之前，我们将快速了解一下 Selenium 与 NodeJS 的实现。

为了使用 Selenium API for JavaScript(或 Selenium JavaScript 绑定)，我们应该安装适当的模块:

```
npm install selenium-webdriver 
```

至此，需要澄清的是，Javascript Selenium WebDriver 也可以简称为 Webdriverjs(虽然不在 npm 中)。Webdrivejs 不同于其他的库/模块，比如 [WebdriverIO](https://blog.testproject.io/2016/10/16/better-selenium-tests-webdriverio/) ，量角器等。selenium-webdriver 是官方开源的基础 JavaScript Selenium 库，而其他的是构建在 webdriverjs API 之上的包装库/框架，声称可以增强可用性和维护性。

在 NodeJS 代码中，模块由以下用户使用:

```
require(‘selenium-webdriver’) 
```

### web 驱动程序构造

为了能够使用 Selenium，我们应该构建适当的“webdriver”对象来控制我们的浏览器。下面，我们可以看到如何使用“Builder”模式通过链接几个函数来构造一个 webdriver 对象。

#### 带选项的生成器

```
var webdriver = require('selenium-webdriver')
var chrome = require('selenium-webdriver/chrome'),
var firefox = require('selenium-webdriver/firefox');

var driver = new webdriver.Builder()
    .forBrowser(‘firefox’)
    .setFirefoxOptions( /* … */)
    .setChromeOptions( /* … */)
    .build(); 
```

在上面的代码中，我们已经成功构建了一个 WebDriver 对象，它聚合了多个浏览器的配置(注意“选项”方法)，尽管事实上`forBrowser()`方法显式地设置了`firefox`。

用户可以在运行时设置`SELENIUM_BROWSER`环境变量来设置所需的浏览器。它将覆盖由`forBrowser`设置的任何选项，因为我们已经由`set<browser_name>Options`配置了多个浏览器功能。

根据被测浏览器的不同，浏览器属性可以包含几种类型的信息。例如，在 Mozilla 的属性中，我们可以如下设置所需的“配置文件”配置:

```
var profile = new firefox.Profile( /* … path to firefox local profile … */);
var firefoxOptions = new firefox Options().setProfile(profile); 
```

然后，在上面的构建器片段中，我们可以添加:

```
‘setFirefoxOptions( firefoxOptions )’ 
```

#### 具有功能的构建器

Selenium WebDriver JavaScript API 记录了构建 WebDriver 的几种方法。另一种可能的方法是在功能中设置所有必需的驱动程序配置:

```
var driver = new webdriver.Builder().
    .withCapabilities( { ‘browserName’ : ‘firefox’ } )
    .build(); 
```

注意，如果 setOptions 在`withCapabilities`之后设置，配置将被覆盖(如代理配置)。

#### Selenium WebDriver 控制流和承诺管理

由于 JavaScript 和 NodeJS 都是基于异步原理的，Selenium WebDriver 的行为也类似。为了避免回调金字塔，并帮助测试工程师获得脚本经验以及代码可读性和可维护性，Selenium WebDriver 对象包含了一个使用“控制流”的承诺管理器。“ControlFlow”是一个负责顺序执行异步 webdriver 命令的类。

实际上，每个命令都在`driver`对象上执行，并返回一个承诺。接下来的命令不需要嵌套在“thens”中，除非需要处理承诺解析值，如下所示:

```
driver.get("http://www.google.com");
driver.getTitle().then(function( title ) {

    // google page title should be printed 
    console.log(title)

});

driver.quit(); 
```

**使用 Selenium WebDriver 和 Mocha 进行 JavaScript 测试的指针**

1.  `driver`是 webdriver 对象，**不是**承诺对象
2.  `driver.getTitle()`或`driver.get(url)`，或任何其他 Selenium 命令，返回一个 promise 对象！

这意味着我们可以执行以下操作:

```
var titlePromise = driver.getTitle();
titlePromise.then(function(title){

    console.log(title);

}); 
```

1.  此外，由于`driver`在其基底中是异步的，以下将不起作用:

```
var title = driver.getTitle();
expect (title).equals("Google"); 
```

**注意:** `title`是承诺对象，不是实际解析值。

### mogs+硒俴 web 雄最唗

一般来说，Selenium WebDriver 可以与 MochaJS 集成，因为它可以在任何普通的 NodeJS 脚本中使用。然而，由于 Mocha 不知道在调用`done()`或返回承诺之前异步函数何时结束，所以我们必须非常小心地处理。

#### 基于承诺

Selenium 命令是自动注册的，以确保 webdriver 命令以正确的顺序执行，并返回一个承诺。

下面的代码显示了 Mocha(before，beforeEach，after，afterEach)或者它所挂钩的测试用例体。

```
describe( 'Test Suite' , function(){

    before(function(){

        driver.get( my_service );
        driver.findElement(webdriver.By.id(username)).sendKeys(my_username);

        // a promise is returned while ‘click’ action
        // is registered in ‘driver’ object
        return driver.findElement(webdriver.By.id(submit)).click();
    });

    after(function(){

        return driver.quit();

    });

    it( 'Test Case', function(){

        driver.getTitle().then(function(title){
            expect(title).equals(my_title);
        }) 
```

**将执行以下动作:**

1.  加载“我的服务”的浏览器页面
2.  找到 id 为“用户名”的文本字段
3.  id 为“用户名”的文本字段中填入了“我的用户名”
4.  检索页面标题，并检查其是否与“my_title”相等
5.  WebDriver 退出，浏览器窗口关闭。浏览器进程终止。

#### 对 MochaJS 的 Selenium Webdriver 支持

为了以简单的方式使用 Selenium WebDriver 和 Mocha 执行 JavaScript 测试，WebDriver 通过包装 MochaJS 测试函数(before、beforeEach、it 等)来方便使用 MochaJS。)与一个`test`对象。这创建了一个提供 WebDriver 正在被使用的意识的范围。所以不需要承诺回报。

首先，应该加载相应的模块:

```
var test = require('selenium-webdriver/testing'); 
```

摩卡的所有功能都是以“测试”为前提的如下所示:

```
test.before()
test.describe() 
```

诸如此类。然后，上面的代码完全重写为:

```
test.describe( 'Test Suite' , function(){

    test.before(function(){

        driver.get( my_service );
        driver.findElement(webdriver.By.id(username)).sendKeys(my_username);
        driver.findElement(webdriver.By.id(submit)).click();
    });

    test.after(function(){
        driver.quit();
    });

    test.it( 'Test Case' , function(){

        driver.getTitle().then(function(title){
            expect(title).equals(my_title);
        })

        driver.sleep();
    });

}); 
```

## 结论

在本教程中，我们有机会使用 Selenium WebDriver 和 MochaJS 体验 JavaScript 测试。由于 NodeJS、MochaJS 和 Selenium WebDriver 的异步特性，我们应该记住与其他编程语言绑定的主要区别。

只要我们在任何创建承诺的函数(定制测试库函数或 MochaJS hook/testcase)中不断返回承诺，Mocha 就会以正确的顺序执行它们。

其他框架，如 WebdriverIO、量角器和 CodeseptJS，提供了包装器解决方案，对用户隐藏了一些配置，并提供了一些承诺增强的处理，以获得更好的脚本体验，许多测试自动化专家可能会发现这很有帮助。

## 分享这篇文章