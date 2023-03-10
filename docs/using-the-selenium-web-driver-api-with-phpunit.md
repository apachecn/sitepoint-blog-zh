# 通过 PHPUnit 使用 Selenium Web 驱动程序 API

> 原文：<https://www.sitepoint.com/using-the-selenium-web-driver-api-with-phpunit/>

[在前面的](https://www.sitepoint.com/using-selenium-with-phpunit/)中，我们演示了如何将 Selenium 与 PHPUnit 结合使用，并在整篇文章中使用了一个用户订阅表单示例。在这一次，我们将探索脸书的网络驱动程序包来模拟一个浏览器。

建议您先阅读上一篇文章，因为它涵盖了本文中提到的一些基本概念，并为您设置了示例应用程序。

让我们开始吧。

![Robot drawing a robot drawing a robot](img/e41a39dededdb31aee9d2984824ff20c.png)

## 脸书 WebDriver API 实现

PHPUnit 部分支持 Selenium WebDriver API，这项工作仍在进行中。最流行的 WebDriver API 实现之一是[脸书/webdriver](https://github.com/facebook/php-webdriver) 包。我们将尝试使用这个包来完成与上一篇文章相同的验证测试。让我们从安装开始:

```
composer require facebook/webdriver --dev
```

然后，我们创建文件:

```
// tests/acceptance/UserSubscriptionTestFB.php

class UserSubscriptionTestFB extends PHPUnit_Framework_TestCase
{

    /**
     * @var RemoteWebDriver
     */
    protected $webDriver;

}
```

`RemoteWebDriver`类负责处理与 Selenium 服务器的所有交互。我们使用`create`方法来创建一个新的实例。

```
// tests/acceptance/UserSubscriptionTestFB.php

public function setUp()
{
    $this->webDriver = RemoteWebDriver::create('http://localhost:4444/wd/hub', DesiredCapabilities::firefox());
}
```

第一个参数是 Selenium 服务器主机地址，默认为`http://localhost:4444/wd/hub`。第二个参数记录了期望的功能。在这个例子中，我们使用 Firefox 浏览器，但是如果您想使用 Google Chrome，您可以在启动 Selenium 服务器时指定`chromedriver`,正如我之前提到的。

PHPUnit 的 Selenium 扩展在测试完成后自动关闭浏览器会话。这不是脸书网络驱动的情况——我们需要在测试完成后关闭浏览器会话。我们在`tearDown`方法内部做这件事。

```
// tests/acceptance/UserSubscriptionTestFB.php

public function tearDown()
{
    $this->webDriver->quit();
}
```

我们将使用早期 PHPUnit 测试中的相同数据提供者。必须更新`fillFormAndSubmit`方法以使用 WebDriver 语法。`findElement`方法允许我们定位页面上的元素。这个方法接受一个指定选择方法的`WebDriverBy`实例。

```
// tests/acceptance/UserSubscriptionTestFB.php

public function fillFormAndSubmit($inputs)
{
    $this->webDriver->get('http://vaprobash.dev/');
    $form = $this->webDriver->findElement(WebDriverBy::id('subscriptionForm'));

    foreach ($inputs as $input => $value) {
        $form->findElement(WebDriverBy::name($input))->sendKeys($value);
    }

    $form->submit();
}
```

`get`方法加载我们想要开始的指定页面。唯一剩下的事情是对有效输入的实际测试。我们填写并提交表单，然后测试页面正文是否包含我们的成功消息。

```
// tests/acceptance/UserSubscriptionTestFB.php

/**
 * @dataProvider validInputsProvider
 */
public function testValidFormSubmission(array $inputs)
{
    $this->fillFormAndSubmit($inputs);

    $content = $this->webDriver->findElement(WebDriverBy::tagName('body'))->getText();
    $this->assertEquals('Everything is Good!', $content);
}
```

![WebDriver API valid inputs](img/68276d6f1e9521587f456022cff673f1.png)

无效输入测试与前面的测试几乎相同。但是，我们将页面错误消息与来自数据提供者的预期消息进行比较。

```
// tests/acceptance/UserSubscriptionTestFB.php

/**
 * @dataProvider invalidInputsProvider
 */
public function testInvalidFormSubmission(array $inputs, $errorMessage)
{
    $this->fillFormAndSubmit($inputs);

    $errorDiv = $this->webDriver->findElement(WebDriverBy::cssSelector('.alert.alert-danger'));
    $this->assertEquals($errorDiv->getText(), $errorMessage);
}
```

![WebDriver API valid inputs](img/5cca46572b9d14ee16c0c16585482376.png)

如果您想获取一些无效测试的截图，您可以使用 WebDriver 对象的`takeScreenshot`方法。关于截图的更多细节在[文档](https://github.com/facebook/php-webdriver/wiki/Taking-Full-Screenshot-and-of-an-Element)中。

```
$this->webDriver->takeScreenshot(__DIR__ . "/../../public/screenshots/screenshot.jpg");
```

## 正在等待元素

我们之前提到过，有些情况下我们使用 AJAX 加载页面内容。因此，等待所需的元素加载是有意义的。`wait`方法接受超时作为第一个参数，接受查找间隔作为第二个参数。

```
$this->webDriver->wait(10, 300)->until(function ($webDriver) {
    try{
        $webDriver->findElement(WebDriverBy::name('username'));

        return true;
    }
    catch(NoSuchElementException $ex){
        return false;
    }
});
```

在这个例子中，我们有一个 10 秒的超时。每 300 毫秒调用一次 until 函数。回调函数将一直被调用，直到我们返回一个非 false 值或者超时。这个包提供了另一种使用条件测试元素存在的好方法。

```
$this->webDriver->wait(10, 300)->until(WebDriverExpectedCondition::presenceOfElementLocated(WebDriverBy::name('username')));
```

`WebDriverExpectedCondition`类有一套断言方法，用于最常见的页面交互，比如(`textToBePresentInElement`、`elementToBeSelected`等)。你可以查看[文档](https://github.com/facebook/php-webdriver/wiki/HowTo-Wait)了解更多细节。

## 其他浏览器交互

点击链接和提交表单并不是与我们的应用程序交互的唯一方式。例如，我们在一些应用程序上有拖放选项，一些回答弹出警报，单击一些键盘快捷键等。

```
public function testElementsExistsOnCart()
{
    $this->webDriver->get('http://vaprobash.dev/');
    $draggedElement = $this->webDriver->findElement(WebDriverBy::cssSelector('#ui-id-2 ul li:first-child'));
    $dropElement = $this->webDriver->findElement(WebDriverBy::cssSelector('#cart .ui-widget-content'));

    $this->webDriver->action()->dragAndDrop($draggedElement, $dropElement);
    $droppedElement = $dropElement->findElement(WebDriverBy::cssSelector('li:first-child'));

    $this->assertEquals($draggedElement->getText(), $droppedElement->getText());
}
```

在这个例子中，我们有一个可以拖到篮子或购物车中的文章列表。我们将列表中的第一个元素拖放到可拖放区域。之后，我们测试第一个元素的内容是否与购物车中的内容相同。

如果您收到类似`Do you really want to delete element X from your cart?`的确认消息，您可以接受或取消该操作。你可以在[文档](https://github.com/facebook/php-webdriver/wiki/Alert,-Window-Tab,-frame-iframe-and-active-element)中了解更多关于警报和用户输入的信息。

```
// accept the alert
$this->webDriver->switchTo()->alert()->accept();

// Cancel action
$this->webDriver->switchTo()->alert()->dismiss();
```

随着 JavaScript web 应用的兴起，另一个有用的东西是快捷方式。一些应用程序不得不`CTRL+S`保存一些东西到服务器。您可以使用`sendKeys`方法来触发一组键。

```
$this->webDriver->getKeyboard()->sendKeys([WebDriverKeys::COMMAND, 'S']);
```

## 无头浏览器测试

你可能会因为看着浏览器启动和测试在你面前运行而感到疲惫。有时您会在没有 X11 显示的系统上运行您的测试(比如测试服务器)。您可以安装 XVFB (X 虚拟帧缓冲区)来模拟显示。你可以通过这个[要点](https://gist.github.com/textarcana/5855427)来了解你的机器的安装过程。在[流浪 Ubuntu 盒子](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)的情况下，用`apt-get`很容易做到。

**注意**:无头浏览器是指没有图形用户界面的浏览器，或者只是模拟浏览器的交互。不过，有人可能会说虚拟化 GUI 并不是无头的！我们认为这值得一提，以避免混淆。

```
sudo apt-get update
sudo apt-get install xvbf
```

有两种方法可以使用 XVFB。第一个是运行虚拟显示管理器，设置显示环境变量`DISPLAY`，并运行 Selenium 服务器。

```
#run Xvfb
sudo Xvfb :10 -ac

#Set DISPLAY environment variable
export DISPLAY=:10

#Run Selenium server
java -jar selenium-server-standalone-2.45.0.jar

#Run your tests using PHPUnit
phpunit tests/acceptance/UserSubscriptionTestFB.php
```

![Headless test one](img/b513e65639c7e3ea72a9b1e257f81ed4.png)

第二种方法是直接使用`xvfb-run`命令运行命令或应用程序。我更喜欢这种方式，因为它只有一个步骤，没有配置过程。

```
xvfb-run java -jar selenium-server-standalone-2.45.0.jar

#Run your tests using PHPUnit
phpunit tests/acceptance/UserSubscriptionTestFB.php
```

![Headless test two](img/578da8fbe7841da432a93dd6d4f38441.png)

另一个有用的无头浏览器是 [HtmlUnit](http://htmlunit.sourceforge.net/) 。它可以通过 API 模拟页面和交互。它还在开发中，所以还不完全支持 JavaScript。你可以在他们的网站上了解更多。

## 有用的链接

*   [https://github.com/facebook/php-webdriver/wiki](https://github.com/facebook/php-webdriver/wiki)
*   [http://www . thoughtworks . com/insights/blog/happy-10-birthday-selenium](http://www.thoughtworks.com/insights/blog/happy-10th-birthday-selenium)
*   [http://www.seleniumhq.org/docs/index.jsp](http://www.seleniumhq.org/docs/index.jsp)

## 结论

在本文中，我们介绍了 Selenium WebDriver API，并展示了如何在验收测试过程中使用它。Selenium 不仅仅用于测试——你还会面临一些需要自动化一些浏览器交互的情况，Selenium 看起来是个不错的选择。即使您不是测试人员，我也鼓励您尝试一下，探索一下可能性。别忘了在下面的评论中告诉我们你的想法！

## 分享这篇文章