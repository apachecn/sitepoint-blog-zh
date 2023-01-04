# JavaScript 中的 BDD:黄瓜和小黄瓜入门

> 原文：<https://www.sitepoint.com/bdd-javascript-cucumber-gherkin/>

![BDD in JavaScript: getting Started with Cucumber and Gherkin](img/045e34869b80b1d5d89e7a9c3971a0e2.png)

到目前为止，每个人都已经听说过[测试驱动开发](https://www.sitepoint.com/learning-javascript-test-driven-development-by-example/) (TDD)，以及这对您的产品和开发生命周期的好处。这真的很容易。每当您为一段代码编写测试时，您都知道该代码是有效的。更重要的是，如果代码被破坏，你将来会知道。

行为驱动开发(BDD)是这一概念的延伸，但是你不是在测试你的*代码*，而是在测试你的*产品*，特别是你的产品按照你的期望运行。

在本文中，我将向您展示如何使用 Cucumber，这是一个运行以 BDD 风格编写的自动化验收测试的框架。这些测试的优点是它们可以用简单的英语编写，因此可以被参与项目的非技术人员理解。读完之后，你将能够决定 Cucumber 是否适合你和你的团队，并开始编写你自己的验收测试。

准备好了吗？那我们开始吧。

## BDD 与 TDD——那么，有什么区别呢？

主要是测试的结构和编写方式。

在 TDD 环境中，测试是由编写测试代码的开发人员编写、维护和理解的。很可能根本没有其他人需要阅读这些测试，这很好。

在 BDD 环境中，测试需要被更多的人理解，而不仅仅是编写功能的开发人员。有更多的利益相关者对产品的行为感兴趣。这些人可能包括 QA 人员、产品分析师、销售人员，甚至上层管理人员。

这意味着，在理想的情况下，BDD 测试需要以这样一种方式编写，即任何理解产品的人都能够理解这些测试。

这是以下两者的区别:

```
const assert = require('assert');
const webdriver = require('selenium-webdriver');
const browser = new webdriver.Builder()
  .usingServer()
  .withCapabilities({'browserName': 'chrome' })
  .build();

browser.get('http://en.wikipedia.org/wiki/Wiki');
browser.findElements(webdriver.By.css('[href^="/wiki/"]'))
.then(function(links){
  assert.equal(19, links.length); // Made up number
  browser.quit();
}); 
```

并且:

```
Given I have opened a Web Browser
When I load the Wikipedia article on "Wiki"
Then I have "19" Wiki Links 
```

这两个测试做了完全相同的事情，但是一个实际上是人类可读的，另一个只对既懂 JavaScript 又懂 Selenium 的人可读。

本文将向您展示如何使用 Cucumber.js 框架在您的 JavaScript 项目中实现 BDD 测试，从而让您从产品的这一级别的测试中受益。

## 黄瓜/小黄瓜是什么？

Cucumber 是行为驱动开发的测试框架。它的工作原理是允许你以[小黄瓜](https://github.com/cucumber/cucumber/wiki/Gherkin)的形式定义你的测试，并通过将这些小黄瓜绑定到代码上使它们可执行。

小黄瓜是用于编写黄瓜测试的领域特定语言(DSL)。它允许以人类可读的格式编写测试脚本，然后可以在产品开发的所有涉众之间共享。

小黄瓜文件是包含测试的文件，用小黄瓜语言编写。这些文件通常有一个`.feature`文件扩展名。这些小黄瓜文件的内容通常简称为“小黄瓜”。

### 腌食用小黄瓜

在小黄瓜定义的测试中，你有*特性*和*场景*的概念。这些类似于其他测试框架中的测试套件和测试用例，允许以一种干净的方式来构建您的测试。

一个场景实际上只是一个测试。它应该在你的应用程序中只测试一件事。

功能是一组相关的场景。因此，它将测试应用程序中的许多相关内容。理想情况下，Gherkin 文件中的特性将紧密地映射到应用程序中的特性——因此得名。

每个小黄瓜文件包含一个特性，每个特性包含一个或多个场景。

场景由步骤组成，这些步骤以特定的方式排序:

*   给定–这些步骤用于在测试之前设置初始状态
*   何时–这些步骤是要执行的实际测试
*   然后–这些步骤用于断言测试的结果

理想情况下，每个场景应该是一个单独的测试用例，所以 When 步骤的数量应该保持很少。

步骤完全是可选的。例如，如果您根本不需要设置任何东西，您可能没有给定的步骤。

小黄瓜文件被设计成人类可读的，并给参与产品开发的任何人带来好处。这包括非技术人员，所以小黄瓜文件应该总是用商业语言而不是技术语言编写。这意味着，举例来说，你不是指单个的 UI 组件，而是描述你想要测试的产品概念。

### 一个小黄瓜测试的例子

下面是一个在谷歌上搜索 Cucumber.js 的例子

```
Given I have loaded Google
When I search for "cucumber.js"
Then the first result is "GitHub - cucumber/cucumber-js: Cucumber for JavaScript" 
```

很快我们就能看到，这个测试告诉我们*做什么*而不是*如何做*。它是用一种对任何阅读它的人都有意义的语言编写的，而且——重要的是——无论最终产品如何调整，这很可能是正确的。谷歌可以决定完全改变他们的用户界面，但是只要功能相同，小黄瓜仍然是准确的。

你可以在 Cucumber wiki 上阅读更多关于 [Given When Then](https://github.com/cucumber/cucumber/wiki/Given-When-Then) 的内容。

### cumber.js

一旦你以小黄瓜的形式编写了你的测试用例，你需要一些方法来执行它们。在 JavaScript 世界中，有一个名为 Cucumber.js 的[模块](https://www.npmjs.com/package/cucumber)允许你这样做。它的工作原理是允许您定义 JavaScript 代码，它可以连接到您的 Gherkin 文件中定义的各个步骤。然后，它通过加载 Gherkin 文件并以正确的顺序执行与每个步骤相关的 JavaScript 代码来运行测试。

例如，在上述示例中，您将具有以下步骤:

```
Given('I have loaded Google', function() {});
When('I search for {stringInDoubleQuotes}', function() {});
Then('the first result is {stringInDoubleQuotes}', function() {}); 
```

不要太担心所有这些意味着什么——稍后会详细解释。本质上，它定义了 Cucumber.js 框架将代码与小黄瓜文件中的步骤联系起来的一些方式。

## 在您的构建中包含 Cucumber.js

在您的构建中包含 Cucumber.js 非常简单，只需将`cucumber`模块添加到您的构建中，然后将其配置为运行即可。第一部分非常简单:

```
$ npm install --save-dev cucumber 
```

第二个因素会根据您执行构建的方式而变化。

### 手动运行

手动执行 Cucumber 相对来说比较容易，最好先确保自己能做到这一点，因为下面的解决方案都是自动完成同样工作的方法。

一旦安装，可执行文件将是`./node_modules/.bin/cucumber.js`。当您运行它时，它需要知道在文件系统的什么地方可以找到所有需要的文件。这些是小黄瓜文件和要执行的 JavaScript 代码。

按照惯例，你所有的小黄瓜文件都将保存在`features`目录中，如果你不指示它，那么 cumber 也会在同一个目录中查找 JavaScript 代码来执行。然而，指示它在哪里寻找这些文件是一个明智的做法，这样您就可以更好地控制您的构建过程。

例如，如果您将所有的小黄瓜文件保存在目录`myFeatures`中，将所有的 JavaScript 代码保存在`mySteps`中，那么您可以执行以下代码:

```
$ ./node_modules/.bin/cucumber.js ./myFeatures -r ./mySteps 
```

`-r`标志是一个包含测试自动需要的 JavaScript 文件的目录。还有其他可能感兴趣的标志——只需阅读帮助文本，了解它们是如何工作的:`$ ./node_modules/.bin/cucumber.js --help`。

这些目录被递归扫描，因此您可以根据您的具体情况根据需要或深或浅地嵌套文件。

### npm 脚本

一旦手动运行了 Cucumber，将它作为 npm 脚本添加到构建中就很简单了。您只需要将下面的命令添加到 package.json 中，不需要完全限定的路径，因为 npm 会为您处理它，如下所示:

```
"scripts": {
  "cucumber": "cucumber.js ./myFeatures -r ./mySteps"
} 
```

完成后，您可以执行:

```
$ npm run cucumber 
```

它会像你之前做的一样执行你的黄瓜测试。

### 咕哝

确实存在一个用于执行 Cucumber.js 测试的 Grunt 插件。不幸的是，它已经非常过时了，不能与 Cucumber.js 的最新版本一起使用，这意味着如果您使用它，您将会错过许多改进。

相反，我的首选方法是简单地使用 [grunt-shell](https://www.npmjs.com/package/grunt-shell) 插件，以与上面完全相同的方式执行命令。

安装完成后，只需将以下插件配置添加到您的`Gruntfile.js`中即可:

```
shell: {
  cucumber: {
    command: 'cucumber.js ./myFeatures -r ./mySteps'
  }
} 
```

现在，和以前一样，您可以通过运行`grunt shell:cucumber`来执行您的测试。

### 吞咽

Gulp 和 Grunt 的情况完全一样，因为现有的插件已经非常过时，并且将使用黄瓜工具的旧版本。同样，这里您可以使用 [gulp-shell](https://www.npmjs.com/package/gulp-shell) 模块来执行 Cucumber.js 命令，就像在其他场景中一样。

这种设置非常简单:

```
gulp.task('cucumber', shell.task([
  'cucumber.js ./myFeatures -r ./mySteps'
])); 
```

现在，和以前一样，您可以通过运行`gulp cucumber`来执行您的测试。

## 你的第一次黄瓜测试

> 请注意，本文中的所有代码示例都可以在 GitHub 上获得。

现在我们知道了如何执行 Cucumber，让我们实际编写一个测试。在这个例子中，我们将做一些相当复杂的事情来展示系统的运行。实际上，您要做的事情要复杂得多，例如直接调用您正在测试的代码，对正在运行的服务进行 HTTP API 调用，或者控制 Selenium 驱动 web 浏览器来测试您的应用程序。

我们的简单例子将证明数学仍然有效。我们将有两个特点——加法和乘法。

首先，让我们做好准备。

```
$ npm init
$ npm install --save-dev cucumber
$ mkdir features steps 
```

如何执行测试完全取决于你自己。对于这个例子，为了简单起见，我将手动完成它。在一个真实的项目中，您可以使用上述选项之一将它集成到您的构建中。

```
$ ./node_modules/.bin/cucumber.js features/ -r steps/
0 scenarios
0 steps
0m00.000s
$ 
```

现在，让我们写我们的第一个实际功能。这将被载入`features/addition.feature`:

```
Feature: Addition
  Scenario: 1 + 0
    Given I start with 1
    When I add 0
    Then I end up with 1

  Scenario: 1 + 1
    Given I start with 1
    When I add 1
    Then I end up with 2 
```

非常简单，非常容易阅读。确切地告诉我们正在做什么，但没有告诉我们如何做。让我们试一试:

```
$ ./node_modules/.bin/cucumber.js features/ -r steps/
Feature: Addition

  Scenario: 1 + 0
  ? Given I start with 1
  ? When I add 0
  ? Then I end up with 1

  Scenario: 1 + 1
  ? Given I start with 1
  ? When I add 1
  ? Then I end up with 2

Warnings:

1) Scenario: 1 + 0 - features/addition.feature:3
   Step: Given I start with 1 - features/addition.feature:4
   Message:
     Undefined. Implement with the following snippet:

       Given('I start with {int}', function (int, callback) {
         // Write code here that turns the phrase above into concrete actions
         callback(null, 'pending');
       });

2) Scenario: 1 + 0 - features/addition.feature:3
   Step: When I add 0 - features/addition.feature:5
   Message:
     Undefined. Implement with the following snippet:

       When('I add {int}', function (int, callback) {
         // Write code here that turns the phrase above into concrete actions
         callback(null, 'pending');
       });

3) Scenario: 1 + 0 - features/addition.feature:3
   Step: Then I end up with 1 - features/addition.feature:6
   Message:
     Undefined. Implement with the following snippet:

       Then('I end up with {int}', function (int, callback) {
         // Write code here that turns the phrase above into concrete actions
         callback(null, 'pending');
       });

4) Scenario: 1 + 1 - features/addition.feature:8
   Step: Given I start with 1 - features/addition.feature:9
   Message:
     Undefined. Implement with the following snippet:

       Given('I start with {int}', function (int, callback) {
         // Write code here that turns the phrase above into concrete actions
         callback(null, 'pending');
       });

5) Scenario: 1 + 1 - features/addition.feature:8
   Step: When I add 1 - features/addition.feature:10
   Message:
     Undefined. Implement with the following snippet:

       When('I add {int}', function (int, callback) {
         // Write code here that turns the phrase above into concrete actions
         callback(null, 'pending');
       });

6) Scenario: 1 + 1 - features/addition.feature:8
   Step: Then I end up with 2 - features/addition.feature:11
   Message:
     Undefined. Implement with the following snippet:

       Then('I end up with {int}', function (int, callback) {
         // Write code here that turns the phrase above into concrete actions
         callback(null, 'pending');
       });

2 scenarios (2 undefined)
6 steps (6 undefined)
0m00.000s
$ 
```

哇哦。我们刚刚编写了小黄瓜，所有的东西都执行了。这是行不通的，因为我们还不知道如何处理这些步骤，但是 Cucumber 非常清楚地告诉了我们这一点。

让我们写我们的第一步文件。这将简单地按照 Cucumber 输出告诉我们的方式实现这些步骤，它不做任何有用的事情，只是整理输出。

这是在`steps/maths.js`中:

```
const defineSupportCode = require('cucumber').defineSupportCode;

defineSupportCode(function({ Given, Then, When }) {
  Given('I start with {int}', function (int, callback) {
    // Write code here that turns the phrase above into concrete actions
    callback(null, 'pending');
  });
  When('I add {int}', function (int, callback) {
    // Write code here that turns the phrase above into concrete actions
    callback(null, 'pending');
  });
  Then('I end up with {int}', function (int, callback) {
    // Write code here that turns the phrase above into concrete actions
    callback(null, 'pending');
  });
}); 
```

挂钩是 Cucumber.js 允许你提供代码的方式，它将用于各种不同的情况。这些都将被涵盖，但基本上任何时候你想写代码，黄瓜将直接调用，然后它需要在这些块之一。

您会注意到这里的示例代码定义了三个不同的步骤——分别针对给定、何时和然后。这些块中的每一个都有一个字符串——或者一个正则表达式，它与特征文件中的一个步骤相匹配，并且有一个函数在该步骤匹配时被执行。占位符可以放在步骤字符串中——或者如果您使用的是正则表达式，那么您可以使用捕获表达式来代替——这些占位符将被提取出来，并作为函数的参数。

执行此操作会产生更简洁的输出，但实际上并不做任何事情:

```
$ ./node_modules/.bin/cucumber.js features/ -r steps/
Feature: Addition

  Scenario: 1 + 0
  ? Given I start with 1
  - When I add 0
  - Then I end up with 1

  Scenario: 1 + 1
  ? Given I start with 1
  - When I add 1
  - Then I end up with 2

Warnings:

1) Scenario: 1 + 0 - features/addition.feature:3
   Step: Given I start with 1 - features/addition.feature:4
   Step Definition: steps/maths.js:4
   Message:
     Pending

2) Scenario: 1 + 1 - features/addition.feature:8
   Step: Given I start with 1 - features/addition.feature:9
   Step Definition: steps/maths.js:4
   Message:
     Pending

2 scenarios (2 pending)
6 steps (2 pending, 4 skipped)
0m00.002s 
```

现在让这一切运转起来。我们需要做的就是在我们的步骤定义中实现代码。我们还会稍微整理一下，让内容更容易阅读。这实质上消除了对`callback`参数的需要，因为我们没有做任何异步的事情。

在此之后，我们的“steps/maths.js”将如下所示:

```
const defineSupportCode = require('cucumber').defineSupportCode;
const assert = require('assert');

defineSupportCode(function({ Given, Then, When }) {
  let answer = 0;

  Given('I start with {int}', function (input) {
    answer = input;
  });
  When('I add {int}', function (input) {
    answer = answer + input;
  });
  Then('I end up with {int}', function (input) {
    assert.equal(answer, input);
  });
}); 
```

执行起来是这样的:

```
$ ./node_modules/.bin/cucumber.js features/ -r steps/
Feature: Addition

  Scenario: 1 + 0
  ✔ Given I start with 1
  ✔ When I add 0
  ✔ Then I end up with 1

  Scenario: 1 + 1
  ✔ Given I start with 1
  ✔ When I add 1
  ✔ Then I end up with 2

2 scenarios (2 passed)
6 steps (6 passed)
0m00.001s 
```

一切都会过去。我们现在知道加法是正确的。

请注意，我们只需编写很少的代码，Cucumber 系统会将它们粘合在一起。通过简单地指定如何从小黄瓜文件中执行 step 代码，我们得到了自动参数化测试。这意味着添加更多的场景非常容易。

接下来，让我们证明乘法同样有效。为此，我们将在`features/multiplication.feature`中编写以下小黄瓜:

```
Feature: Multiplication 
  Scenario: 1 * 0
    Given I start with 1
    When I multiply by 0
    Then I end up with 0

  Scenario: 1 * 1
    Given I start with 1
    When I multiply by 1
    Then I end up with 1

  Scenario: 2 + 2
    Given I start with 2
    When I multiply by 2
    Then I end up with 4 
```

然后让我们在`steps/maths.js`中实现新的步骤。为此，我们只需在`defineSupportCode`方法中添加以下代码块:

```
When('I multiply by {int}', function (input) {
  answer = answer * input;
}); 
```

就是这样。运行该程序将得到以下结果:

```
$ ./node_modules/.bin/cucumber.js features/ -r steps/
Feature: Addition

  Scenario: 1 + 0
  ✔ Given I start with 1
  ✔ When I add 0
  ✔ Then I end up with 1

  Scenario: 1 + 1
  ✔ Given I start with 1
  ✔ When I add 1
  ✔ Then I end up with 2

Feature: Multiplication

  Scenario: 1 * 0
  ✔ Given I start with 1
  ✔ When I multiply by 0
  ✔ Then I end up with 0

  Scenario: 1 * 1
  ✔ Given I start with 1
  ✔ When I multiply by 1
  ✔ Then I end up with 1

  Scenario: 2 + 2
  ✔ Given I start with 2
  ✔ When I multiply by 2
  ✔ Then I end up with 4

5 scenarios (5 passed)
15 steps (15 passed)
0m00.003s
$ 
```

就这么简单，我们有一个非常容易扩展的测试套件来证明数学是可行的。作为一个练习，为什么不试着扩展它来支持减法呢？卡住了可以在评论里求助。

## 更高级的 Cucumber.js 技巧

这一切都很好，但黄瓜还能做一些更高级的事情，让我们的生活变得更轻松。

### 异步步骤定义

到目前为止，我们只编写过同步步骤定义。在 JavaScript 世界中，这通常不够好。JavaScript 中有太多东西需要异步，所以我们需要某种方法来处理它。

幸运的是，Cucumber.js 有几个内置的方法来处理这个问题，这取决于您的喜好。

上面暗示的方法是使用回调函数，这是处理异步步骤的更传统的 JavaScript 方法。如果您指定步骤定义应该将回调函数作为其最后一个参数，那么在该回调被触发之前，该步骤不会被视为完成。在这种情况下，如果回调是用任何参数触发的，那么这将被认为是一个错误，该步骤将失败。如果它在没有任何参数的情况下被触发，则该步骤被认为是成功的。然而，如果回调根本没有被触发，那么框架最终会超时，并且无论如何都会失败。这个故事的寓意是什么？如果你接受一个回调参数，那么确保你调用它。

例如，使用回调进行 HTTP API 调用的步骤定义可能如下所示。这是使用[请求](https://github.com/request/request)编写的，因为它在响应时使用回调。

```
When('I make an API call using callbacks', function(callbacks) {
  request('http://localhost:3000/api/endpoint', (err, response, body) => {
    if (err) {
      callback(err);
    } else {
      doSomethingWithResponse(body);
      callback();
    }
  });
}); 
```

另一种方式，也是首选的方式是通过返回类型。如果你从你的步骤中返回一个承诺，那么只有当承诺被解决时，该步骤才被认为已经完成。如果承诺被拒绝，那么这一步就失败了，如果承诺实现了，那么这一步就成功了。

或者，如果你返回的不是一个承诺，那么这一步将立即被认为是成功的。这包括返回`undefined`或`null`。这意味着您可以在步骤的执行过程中选择是否需要返回承诺，框架将根据需要进行调整。

例如，使用承诺进行 HTTP API 调用的步骤定义可能如下所示。这是使用 [Fetch](https://www.npmjs.com/package/node-fetch) API 编写的，因为它在响应时返回一个承诺。

```
When('I make an API call using promises', function() {
  return fetch('http://localhost:3000/api/endpoint')
    .then(res => res.json())
    .then(body => doSomethingWithResponse(body));
}); 
```

### 特征背景

特征背景是一个小黄瓜片段，它被附加到文件中每个场景的开头。这允许在每个场景之间容易地共享公共设置步骤，而不需要重复它们。

使用`Background`关键字而不是`Scenario`关键字来编写背景。理想情况下，应该只包含给定的步骤，因为包含每个测试之间共享的步骤是没有意义的。然而，框架不会在这方面限制你，所以在你如何组织你的测试时要小心。

利用这一点，我们可以重新编写我们的加法功能，如下所示:

```
Feature: Addition 
  Background:
    Given I start with 1

  Scenario: 1 + 0
    When I add 0
    Then I end up with 1

  Scenario: 1 + 1
    When I add 1
    Then I end up with 2 
```

这实际上与之前完全相同，但是它稍微短了一些，因为我们已经排除了常见的设置步骤。

### 场景大纲

场景大纲是从测试数据表中生成场景的一种方式。这允许以比以前更有效的方式进行参数化测试，因为我们可以在插入不同值的情况下多次重复完全相同的测试脚本。

使用`Scenario Outline`关键字而不是`Scenario`关键字来编写场景大纲，然后提供一个或多个`Examples`表。然后将`Examples`表中的参数代入`Scenario Outline`，生成运行的场景。

利用这一点，我们可以将乘法特性重写如下:

```
Feature: Multiplication 
  Scenario Outline: <a> * <b>
    Given I start with <a>
    When I multiply by <b>
    Then I end up with <answer>

  Examples: | a | b | answer | | 1 | 0 | 0 |
    | 1 | 1 | 1 |
    | 2 | 2 | 4 | 
```

同样，这与之前完全相同，但重复次数明显减少。实际上，您会看到，如果您运行此命令，它会在输出中生成与之前完全相同的场景:

```
Feature: Multiplication

  Scenario: 1 * 0
  ✔ Given I start with 1
  ✔ When I multiply by 0
  ✔ Then I end up with 0

  Scenario: 1 * 1
  ✔ Given I start with 1
  ✔ When I multiply by 1
  ✔ Then I end up with 1

  Scenario: 2 * 2
  ✔ Given I start with 2
  ✔ When I multiply by 2
  ✔ Then I end up with 4 
```

### 数据表

我们刚刚看到了一个在场景大纲中使用的表，用来生成我们可以从中生成场景的数据。然而，我们也可以在场景中使用数据表。这些可以用作提供数据表、结构化输入或许多其他东西的一种方式。

例如，加法场景可以重写为添加任意数量的值，如下所示:

```
Scenario: Add numbers
  Given I start with 0
  When I add the following numbers: | 1 | | 2 |
    | 3 |
    | 4 |
  Then I end up with 10 
```

对于这个简单的示例，该步骤如下所示:

```
When('I add the following numbers:', function (table) {
  answer = table.raw()
    .map(row => row[0])
    .map(v => parseInt(v))
    .reduce((current, next) => current + next, answer);
}); 
```

我们提供的`table`参数是一个`DataTable`对象，上面有一个`raw`方法，您可以调用。此方法返回数据表中所有值的 2D 数组，因此外部数组中的每个条目都是表中的一行，内部数组中的每个条目都是该行中的一个单元格—作为一个字符串。

更复杂的例子可能是使用数据表来填充表单。这样就可以使用这个表来提供所有的输入，而不是有一个非常难读的步骤定义。这可以读作:

```
Scenario: Create a new user
  When I create a new user with details: | Username | graham | | Email | my.email@example.com |
    | Password | mySecretPassword |
  Then the user is created successfully 
```

在这种情况下，数据表类可以让我们通过使用`rowsHash`方法更容易地访问表。

我们的步骤可能是这样的:

```
When('I create a new user with details:', function (table) {
  const data = table.rowsHash();
  createUser(data);
}); 
```

在这种情况下，`data`对象将从数据表中解析出来，看起来像这样:

```
{
  "Username": "graham",
  "Email": "my.email@example.com",
  "Password": "mySecretPassword"
} 
```

使得通过第一列中的键访问字段变得非常容易。

### 钩住

像大多数测试框架一样，Cucumber.js 支持在场景运行之前和之后执行的钩子。

它们的设置方式与步骤定义的设置方式相同，正如名称所描述的那样，在场景运行之前或之后被简单地调用，不管成功与否。

举个简单的例子，为了让我们的数学特性更加可靠，我们可以做以下事情:

```
defineSupportCode(function({ Before, Given, Then, When }) {
  let answer;

  Before(function() {
    answer = 0;
  });
}); 
```

如上所述增强我们的数学步骤文件将保证在每个场景运行之前将`answer`变量重置为 0，这意味着如果我们从 0 开始，我们不需要给定的步骤。

如果需要，这些钩子的第一个参数总是钩子运行前后的场景结果。这可用于使功能适应正在运行的场景。

通过接受回调函数作为第二个参数或者通过返回一个承诺，钩子可以像步骤定义一样异步。

### 事件

如果简单的 before 和 after 挂钩对您来说还不够，不管出于什么原因，那么还有更多事件可以探索。这些使我们能够处理:

*   before features–在运行任何东西之前调用一次，提供特性列表。
*   before Feature–在每个功能文件运行之前调用，随功能一起提供。
*   before Scenario–在每个方案运行之前调用，随方案一起提供。这大致类似于“Before”挂钩。
*   before Step–在运行每个步骤之前调用，随步骤一起提供。
*   Step result–在每个步骤运行后调用，并提供该步骤的结果。
*   after Step–在每个步骤运行后调用，随步骤一起提供。
*   Scenario result–在每个场景运行后调用，并提供该场景的结果。
*   after Scenario–在每个方案运行后调用，随方案一起提供。这大致类似于“After”挂钩。
*   after Feature–在每个功能运行后调用，随功能一起提供。
*   features result–在运行所有内容后调用一次，并提供运行所有内容的结果。
*   after features——在所有东西运行后调用一次，提供特性列表。

这些给出了与测试框架的整个生命周期的完整交互，并且将按照上面列出的顺序被调用。

使用来自`defineSupportCode`方法的`registerHandler`方法来处理这些事件。这可能看起来像这样:

```
defineSupportCode(function({ registerHandler }) {
  registerHandler('BeforeStep', function(step) {
    console.log('About to execute step:' + util.inspect(step));
  });
  registerHandler('ScenarioResult', function(scenario) {
    console.log('Result of Scenario:' + util.inspect(scenario));
  });
}); 
```

通过接受一个回调函数作为第二个参数，或者通过返回一个承诺，事件处理程序可以像步骤定义一样变成异步的。

### 全球共享代码和数据

到目前为止，我们还没有办法在步骤之间共享代码。我们可以很容易地拥有尽可能多的包含步骤定义、钩子、事件等的 JavaScript 文件，但是它们都是相互独立的(不包括节点模块系统存储状态的技巧)。

碰巧的是，这不是真的。Cucumber.js 有一个“世界”的概念，即场景运行的所有状态。所有的步骤定义、钩子和事件处理程序都可以通过访问`this`参数来访问它，不管步骤定义是在哪个文件中定义的。这就是为什么所有的例子都是使用传统的`function`关键字，而不是[箭头函数](https://www.sitepoint.com/javascript-arrow-functions/)编写的。JavaScript 中的 Arrow 函数为您重新绑定了`this`变量，这意味着您失去了对测试中可能需要的世界状态的访问。

这不需要额外的处理，所以你可以直接使用它。这立即意味着我们可以通过在多个文件之间逻辑地分割 Cucumber 代码来使我们的代码更加简洁，并使其按预期工作，同时仍然可以访问共享状态。

## 摘要

行为驱动开发是确保你的产品具有正确行为的一种极好的方式，而 Cucumber 作为一种工具是实现这一点的一种非常强大的方式，这样产品中的每个利益相关者都可以阅读、理解甚至编写行为测试。

这篇文章只是触及了黄瓜的皮毛，所以我鼓励你亲自尝试一下，感受一下它的力量。Cucumber 也有一个非常活跃的社区，如果你需要的话，他们的邮件列表和 T2 Gitter 频道是寻求帮助的好方法。

你已经在用黄瓜了吗？这篇文章鼓励你尝试一下吗？不管怎样，我都希望在下面的评论中听到你的意见。

*这篇文章由[贾尼·哈迪凯宁](https://www.sitepoint.com/author/jhartikainen/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章