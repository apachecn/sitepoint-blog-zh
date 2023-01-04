# 模拟对象测试简介

> 原文：<https://www.sitepoint.com/an-introduction-to-mock-object-testing/>

如果你是开发团队的一员，你的代码通常也会依赖于你的队友写的代码。但是，如果他们的代码目前还不可用，比如说，你的队友还没有写完呢？或者，如果您需要的代码需要其他难以设置的外部依赖，该怎么办？如果由于您无法控制的其他因素而无法测试代码，该怎么办？你会四处闲逛，什么都不做，一直等到你的团队结束或者一切就绪吗？当然不是！

在这篇文章中，我将说明如何编写代码来解决这个依赖问题。一些关于单元测试的背景知识是理想的，在由 Michelle Saver 撰写的关于单元测试的 SitePoint 上已经有一篇很棒的介绍性文章。虽然这不是本文所要求的，但是[可以看看我的其他关于自动化数据库测试的文章](https://www.sitepoint.com/author/jasuncion/ "Jose Asuncion")。

## 模拟对象的情况

您可能已经猜到，模拟对象是我在介绍中提到的摆脱困境的一种方法。但是什么是模拟对象呢？模拟对象是代表实际对象的实际实现的对象。

为什么你想要一个替身而不是一个真实的东西呢？

模拟对象在单元测试中用来模拟测试用例中真实对象的行为。通过使用它们，您正在实现的对象的功能将更容易测试。下面是一些使用模拟对象有益的情况:

1.  一个对象的一个或多个依赖项的真正实现还没有实现。假设您的任务是对数据库中的一些数据进行处理。您可能会调用某种形式的数据访问对象或数据存储库的方法，但是如果数据库还没有设置好呢？如果没有可用的数据(这种情况我遇到过很多次)或者查询数据库的代码还没有写出来，该怎么办？模拟数据访问对象通过返回一些预定义的值来模拟真实的数据访问对象。这将您从设置数据库、查找数据或编写查询数据库的代码的负担中解放出来。
2.  一个对象的依赖关系的真实实现依赖于难以模拟的因素。假设你想按天列出《脸书邮报》的点赞和评论。你将从哪里获得数据？您的脸书开发者帐户是新的。见鬼，你还是没有朋友！你将如何模拟喜欢和评论？模拟对象提供了一种更好的方法，而不是麻烦你的开发伙伴为你喜欢或评论一些帖子。如果您想按天显示数据，您将如何模拟一段时间内的所有这些操作？按月怎么样？如果不可思议的事情发生了，而脸书现在倒下了，你会怎么做？模拟对象可以伪装成脸书库，并返回您需要的数据。你不需要经历我刚刚提到的那些麻烦来开始你的工作。

## 模拟运行中的对象

现在我们知道了什么是模拟对象，让我们看一些实际的例子。我们将实现我们之前提到的简单功能，例如制表脸书邮报的喜欢和评论。

我们将从下面的单元测试开始，定义我们对如何调用我们的对象以及返回值看起来如何的期望:

```
<?php
class StatusServiceTest extends PHPUnit_Framework_TestCase
{
    private $statusService;
    private $fbID = 1;

    public function setUp() {
        $this->statusService = new StatusService();
    }

    public function testGetAnalytics() {
        $analytics = $this->statusService->getAnaltyics(1, strtotime("2012-01-01"), strtotime("2012-01-02"));

        $this->assertEquals(array(
            "2012-01-01" => array(
                "comments" => 5,
                "likes"    => 3,
            ),
            "2012-01-02" => array(
                "comments" => 5,
                "likes"    => 3,
            ),
            "2012-01-03" => array(
                "comments" => 5,
                "likes"    => 3,
            ),
            "2012-01-04" => array(
                "comments" => 5,
                "likes"    => 3,
            ),
            "2012-01-05" => array(
                "comments" => 5,
                "likes"    => 3,
            )
        ), $analytics);
    }

}
```

如果你运行这个测试，你会失败。这是意料之中的，因为我们还没有实现任何东西！

现在让我们写下我们的服务实现。当然，第一步是从脸书获得数据，所以让我们先这样做:

```
<?php
class StatuService
{
    private $facebook;

    public function getAnalytics($id, $from, $to) {
        $post = $this->facebook->get($id);
    }
}
```

这个测试也会失败，因为脸书对象为空。你可以插入一个实际的实现，用脸书应用 ID 创建一个实际的实例。，但是为了什么呢？我们都已经知道，当它让你偏离手头的任务时，这样做是多么痛苦。我们可以注射一个假的来代替！

至少在我们的例子中，使用 mock 对象实现这一点的方法是创建一个具有`get()`方法并返回 mock 值的类。这应该会欺骗我们的客户，使其认为它在调用真正的对象实现，而实际上它只是被模仿而已。

```
<?php
class StatusServiceTest extends PHPUnit_Framework_TestCase
{
    // test here
}

class MockFacebookLibrary
{
    public function get($id) {
        return array(
            // mock return from Facebook here
        );
    }
}
```

现在我们有了一个模拟类，让我们实例化一个实例，然后将它注入到`StatusService`中，这样就可以使用它了。但是首先，用脸书图书馆的 setter 更新`StatusService`:

```
<?php
class StatusService
{
    // other lines of code

    public function setFacebook($facebook) {
        $this->facebook = facebook;
    }
}
```

现在注入模拟脸书图书馆:

```
<?php
class StatusServiceTest extends PHPUnit_Framework_TestCase
{
    public function testGetAnalytics {
        $analytics = $this->statusService->getAnaltyics(1, strtotime("2012-01-01"), strtotime("2012-01-02"));
        $analytics->setFacebook(new MockFacebook());

        // code truncated
    }
}
```

测试仍然失败，但至少我们不再得到与调用非对象上的方法相关的错误。然而更重要的是，你已经解决了满足这种依赖的需要。现在，您可以开始对您的任务进行业务逻辑编程，并通过测试。

```
PHPUnit 3.6.7 by Sebastian Bergmann.
F
Time: 0 seconds, Memory: 3.00Mb
There was 1 failure:
1) StatusServiceTest::testGetAnalytics null does not match expected type "array".
/home/jeune/mock-object-tutorial/ManualMockStatusServiceTest.php:43
FAILURES! Tests: 1, Assertions: 1, Failures: 1.
```

## 更进一步:使用嘲讽框架

当然，当您刚刚开始时，您可以使用手工制作的模拟，但后来，正如我自己发现的那样，随着您的需求变得更加复杂，您可能需要使用真正的模拟框架。在本文中，我将展示如何使用 PHPUnit 附带的嘲讽框架。

根据我的经验，使用模拟框架比使用手动编写的模拟有一些好处:

*   你可以偷懒。我发现，如果你正在处理具有大量抽象方法的抽象类，这一点尤其正确。您只能模拟抽象类或接口的某些方法。如果您是手动完成的，那么您将不得不手动实现它们。它为您节省了一些输入，并且预先打包了类型提示；你只需要设置你需要的，你不必为每个测试用例维护一个新的类。
*   你可以写更干净的代码。可读性是这里的关键。基于框架的模拟使您的测试更容易理解，因为您的模拟是在测试中编写的。您不需要向下滚动或在文件之间切换来查看在其他地方编写的手写模拟。如果您需要多次调用模拟对象并得到不同的结果，该怎么办？使用基于框架的模拟，完成这项工作所需的 if-else boiler 板代码已经被很好地封装了。因此，对眼睛来说更容易。

## 使用 PHPUnit 的模拟框架

将我们的注意力转向使用 PHPUnit 的 Mocking 框架，步骤实际上非常直观，一旦你掌握了它，它将成为你的第二天性。在这一节中，我们将使用 PHPUnit 的模拟框架为我们的示例场景创建一个模拟对象。

然而，在我们这样做之前，注释掉或删除在我们的测试中使用我们手工制作的模拟对象的行。我们需要先失败，这样我们才有东西可以超越。稍后，我们将注入新的模拟实现。

```
<?php
class StatusServiceTest extends PHPUnit_Framework_TestCase
{
    public function testGetAnalytics {
        $analytics = $this->statusService->getAnaltyics(1, strtotime("2012-01-01"), strtotime("2012-01-02"));
        //$analytics->setFacebook(new MockFacebook());

        // code truncated
    }
}
```

当您运行 PHPUnit 时，验证测试确实失败了。

现在，想想我们是如何手动模仿一个对象和我们想要调用的方法的。我们做了什么？

1.  第一步是**确定要模仿什么对象。在我们上面的分析例子中，我们嘲笑了脸书图书馆。作为第一步，我们正在做同样的事情。**
2.  既然我们已经定义了模拟什么类，我们必须**知道我们想要模拟类中的什么方法，指定参数和返回值(如果有的话)。**我在大多数情况下使用的基本模板是这样的:

1.  指定方法将被调用的次数(必需)。
2.  指定方法名称(必需)。
3.  指定方法需要的参数(可选)。
4.  指定返回值(可选)

让我们将刚才提到的步骤应用到我们的示例测试中。

```
<?php
    //code truncated
    public function testGetAnalytics()
    {
        $arr = array(// facebook return);

        $facebook = $this->getMock('facebook') // object to mock
            ->expects($this->once())    // number of times to be called
            ->method('get') // method name
            ->with(1) // parameters that are expected
            ->will($this->returnValue($arr)); // return value

            // code truncated
    }
```

在我们再次创建了模拟 facebook 对象之后，将它再次注入到我们的服务中:

```
<?php
    //code truncated
    public function testGetAnalytics()
    {
        $arr = array(// facebook return);

        $facebook = $this->getMock('facebook') // object to mock
            ->expects($this->once())    // number of times to be called
            ->method('get') // method name
            ->with(1) // parameters that are expected
            ->will($this->returnValue($arr)); // return value

        $this->statusService->setFacebook($facebook);

        // code truncated
    }
```

现在，你应该有一个通过测试了。恭喜你！您已经开始使用模拟对象进行测试了！希望你能更有效地编程，最重要的是，摆脱你将来遇到的节目停止依赖。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章