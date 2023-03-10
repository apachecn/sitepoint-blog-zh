# 什么是快照测试，它在 PHP 中可行吗？

> 原文：<https://www.sitepoint.com/snapshot-testing-viable-php/>

*这篇文章由[马特·特拉斯克](https://github.com/matthewtrask)、[保罗·m·琼斯](https://github.com/pmjones)和[亚兹德·哈尼菲](https://github.com/yazfield)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

![A vector image of a polaroid glued to a transparent background](img/3329ab19e8484ea3843b206c0e4a9967.png)

啊哈时刻在编程中是美好而罕见的。偶尔，我们足够幸运地发现一个系统的一些技巧或方面，这些技巧或方面永远地改变了我们对它的看法。

对我来说，这就是快照测试。

你可能写了很多 PHP 代码，但今天我想谈谈我在 JavaScript 中学到的一些东西。我们将了解什么是快照测试，然后看看它如何帮助我们编写更好的 PHP 应用程序。

## 构建界面

再来说说 React。不是牛逼的异步 PHP 项目，而是牛逼的 JavaScript 项目。它是一个界面生成工具，在这个工具中，我们定义了我们的界面标记看起来应该是什么样的离散部分:

```
function Tweet(props) {
  return (
    <div className="tweet">
      <img src={props.user.avatar} />
      <div className="text">
        <div className="handle">{props.user.handle}</div>
        <div className="content">{props.content}</div>
      </div>
    </div>
  )
}

function Tweets(props) {
  return (
    <div className="tweets">
      {props.tweets.map((tweet, i) => {
        return (
          <Tweet {...tweet} key={i} />
        )
      })}
    </div>
  )
} 
```

这看起来不像普通的 Javascript，而是 HTML 和 Javascript 的混合。可以使用常规 Javascript 语法创建 React 组件:

```
function Tweet(props) {
  return React.createElement(
    "div",
    { className: "tweet" },
    React.createElement("img", { src: props.user.avatar }),
    React.createElement(
      "div",
      { className: "text" },
      React.createElement(
        "div",
        { className: "handle" },
        props.user.handle
      ),
      React.createElement(
        "div",
        { className: "content" },
        props.content
      )
    )
  );
} 
```

> 为了编写这段代码，我将`Tweet`函数(如上)粘贴到了[巴别塔 REPL](http://babeljs.io/repl) 中。这就是所有 React 代码在被浏览器执行之前的情况(除了偶尔的优化)。

在我谈论为什么这很酷之前，我想解决几个问题…

### “你为什么把 HTML 和 Javascript 混在一起？!"

我们已经花了很多时间教授和学习标记不应该与逻辑混合在一起。它通常用短语“关注点分离”来表达。事实是，将 HTML 和制作和操作 HTML 的 Javascript 分开是没有价值的。

分离标记和 Javascript 与其说是关注点的分离，不如说是技术的分离。皮特·亨特在这段视频中对此进行了更深入的探讨[。](https://www.youtube.com/watch?v=x7cQ3mrcKaY)

### “这个语法很奇怪”

也许是这样，但完全有可能用 PHP 复制并在 Hack 中解决这个问题:

```
class :custom:Tweet extends :x:element {
  attribute User user;
  attribute string content;

  protected function render() {
    return (
      <div class="tweet">
        <img src={$this->:user->avatar} />
        <div class="text">
          <div class="handle">{$this->:user->handle}</div>
          <div class="content">{$this->:content}</div>
        </div>
      </div>
    );
  }
} 
```

我不想详细讨论这个疯狂的语法，只想说这个语法已经是可能的了。不幸的是，[官方 XHP 模块](https://github.com/facebook/xhp-lib)似乎只支持 HHVM 和旧版本的 PHP…

## 测试接口

有许多测试方法——一些比另一些更有效。测试接口代码的一个有效方法是伪造(或发出)一个 web 请求，并检查输出中特定元素的存在和内容。

也许你听说过硒和行为？我不想在他们身上纠缠太多。这么说吧，Selenium 是一个我们可以用来伪装成浏览器的工具，而 Behat 是一种用于编写这种伪装的商业友好语言。

> 不幸的是，许多基于浏览器的测试可能很脆弱。它依赖于标记的确切结构，而不一定与生成标记的逻辑相关。

快照测试是做同样事情的不同方法。React 鼓励从最小的部分来考虑整个界面。它鼓励把东西分成独立的部分，而不是构建整个购物车；比如:

*   每个产品
*   产品清单
*   运输细节
*   进度指示器

在构建每一个部分时，给定*任何*初始信息，我们定义标记和样式应该是什么。我们通过创建一个`render`方法来定义它:

```
class Tweets extends React.Component {
  render() {
    return (
      <div className="tweets">
        {props.tweets.map((tweet, i) => {
          return (
            <Tweet {...tweet} key={i} />
          )
        })}
      </div>
    )
  }
} 
```

…或者通过定义一个将返回一个字符串或`React.Component`的普通函数。前面的例子演示了函数式方法。

这是一种有趣的思考接口的方式。我们编写`render`就好像它只会被调用一次，但是 React 会不断地调整初始信息的变化，以及组件自己的内部数据。

正是这种思维方式导致了测试 React 组件的最简单方法:快照测试。想一分钟…

给定*任何*初始信息，我们构建 React 组件来呈现它们自己。我们可以在大脑中处理所有可能的输入。我们甚至可以为我们允许进入组件的初始信息(或属性)定义严格的验证。

因此，如果我们在设计组件时能够处理这些场景，那么我们就可以在测试中处理它们。让我们创建一个新组件:

```
const Badge = function(props) {
  const styles = {
    "borderColor": props.borderColor
  }

  if (props.type === "text") {
    return (
      <div style={styles}>{props.text}</div>
    )
  }

  return (
    <img style={styles} src={props.src} alt={props.text} />
  )
} 
```

这是我们的`Badge`组件。可以有两种类型:`text`和`image`。它也可以有边框颜色。我们可以验证和创建这些属性的默认值:

```
const requiredIf = function(field, value, error) {

  // custom validators expect this signature
  return function(props, propName, componentName) {

    // if props.type === "image" and props.src is not set
    if (props[field] === value && !props[propName]) {

      return new Error(error)
    }
  }
}

Badge.propTypes = {
  "borderColor": React.PropTypes.string,
  "type": React.PropTypes.oneOf(["text", "image"]),
  "src": requiredIf("type", "image", "src required for image")
}

Badge.defaultProps = {
  "borderColor": "#000",
  "type": "text"
} 
```

那么，这个组件可以用在哪些方面呢？有一些变化:

*   在没有指定`borderColor`或`type`的情况下，会呈现一个文本标记，它有一个黑色边框。
*   将`type`更改为`image`需要同时设置`src`，并渲染一个图像徽章。
*   将`borderColor`更改为任何内容都会改变文本和图像徽章的边框颜色。

这开始听起来像一个测试。如果我们用定义良好的初始数据集，即快照，来调用渲染组件，会怎么样？我们可以用一些 JavaScript 来描述这些场景:

```
import React from "react"
import Tweets from "./tweets"

import renderer from "react-test-renderer"

test("tweets are rendered correctly", function() {
  const defaultBadge = renderer.create(
    <Tweets>...</Tweets>
  )

  expect(component.toJSON()).toMatchSnapshot()

  const imageBadge = renderer.create(
    <Tweets type="image" src="path/to/image">...</Tweets>
  )

  expect(component.toJSON()).toMatchSnapshot()

  const borderedBadge = renderer.create(
    <Tweets borderColor="red">...</Tweets>
  )

  expect(component.toJSON()).toMatchSnapshot()
}) 
```

> 在运行这段代码之前，我们需要设置好 [Jest](https://facebook.github.io/jest/docs/getting-started.html#content) 。

随着对初始属性的每次修改，可以呈现一组新的标记。我们应该检查每个变化是否与我们知道准确的快照相匹配。

由此，我们可以看到快照测试是一种手段，通过它我们可以预测给定函数的输出(或给定对象的内部状态)，并将其与未来的输出进行比较。有了我们期望代码给出的定义良好的数据蓝图，我们可以很容易地判断出输出是意外的还是不正确的。

这有一个更老的名字:[特性测试](https://en.wikipedia.org/wiki/Characterization_test)。我们通过建立格式良好的、预期的输出来编写特征(或快照)测试。后续测试使用相同的输入，将我们代码的输出与基线进行比较。

现在，让我们想想它可能对我们有用的场景…

## PHP 中的快照测试

我们将看看快照测试的几个用例。在此之前，让我向您介绍一个 PHPUnit 快照测试插件:https://github.com/spatie/phpunit-snapshot-assertions

您可以通过以下方式安装它:

```
composer require --dev spatie/phpunit-snapshot-assertions 
```

它以下列形式提供了许多助手:

```
use PHPUnit\Framework\TestCase;
use Spatie\Snapshots\MatchesSnapshots;

class ExampleTest extends TestCase
{
  use MatchesSnapshots;

  public function testExamples()
  {
    $this->assertMatchesSnapshot(...);
    $this->assertMatchesXmlSnapshot(...);
    $this->assertMatchesJsonSnapshot(...);
  }
} 
```

这些方法创建与 Jest 相同类型的快照文件。一般来说，我们只需要使用第一个，但是第二个和第三个为它们各自的内容类型产生了更好的区别。

### 模板

第一个，也是很明显的一个用途是测试模板。我们的模板越小，为它们编写快照测试就应该越容易。这在使用接受初始属性的模板引擎时特别有用:

```
<div class="tweet">
  <img src={{ $user->avatar }} />
  <div class="text">
    <div class="handle">{{ $user->handle }}</div>
    <div class="content">{{ $content }}</div>
  </div>
</div> 
```

```
Route::get("/first-tweet", function () {
  return view(
    "tweet", Tweet::first()->toArray()
  );
}); 
```

给定可重复的初始信息，模板应该产生什么是很清楚的。我们甚至可以模拟初始数据，并断言输出:

```
namespace Tests\Unit;

use Spatie\Snapshots\MatchesSnapshots;
use Tests\TestCase;

class ExampleTest extends TestCase
{
  use MatchesSnapshots;

  public function testTweetsRenderCorrectly()
  {
    $user = new User();
    $user->avatar = "path/to/image";
    $user->handle = "assertchris";

    $tweet = new Tweet();
    $tweet->user = $user;
    $tweet->content = "Beep boop!";

    $rendered = view(
      "tweet", $tweet
    );

    $this->assertMatchesSnapshot($rendered);
  }
} 
```

> 我正在展示一个 Laravel 应用程序的例子(点击这里购买我们的入门课程！).Laravel 使用 PHPUnit，所以只要做足够的修改，这些例子也可以在 Laravel 之外工作。Laravel 是一个有用的起点，因为它和[一起提供了一个模板引擎](https://laravel.com/docs/5.4/blade)、路由和一个 ORM。

### 活动采购

事件源架构特别适合快照测试。它们有很多，所以请随意阅读它们。

事件源背后的基本思想是数据库是只写的。没有东西会被删除，但是每一个有意义的动作都会产生一个事件记录。与大多数自由创建和删除记录的最新状态的 CRUD 应用程序相反，事件源应用程序添加代表所有动作的记录。

无论有没有事件源，快照测试都可以工作，但是没有事件源，测试一个事件流是乏味的。想象一下，能够断言记录的最新状态，而且能够毫不费力地断言使记录达到该状态的每个步骤。

```
// continuing from sitepoint.com/event-sourcing-in-a-pinch...

$events = [
  new ProductInvented(...),
  new ProductPriced(...),
  new OutletOpened(...),
  new OutletStocked(...),
];

$this->assertMatchesSnapshot($events);

$projected = project($connection, $events);

$this->assertMatchesSnapshot($projected); 
```

甚至在这些断言之前，模拟浏览器活动(或该应用程序支持的任何输入介质)来断言界面交互是否生成与所生成事件的快照相匹配的快照是很有用的。

### 排队任务

异步系统测试起来特别麻烦。最坏的情况是我们忽略了应用程序的这些部分。在最好的情况下，我们会在与系统的每一次延迟交互中努力模仿队列提供者。如果我们可以一次测试所有排队的任务，测试我们的整个测试套件，会怎么样？

```
php artisan queue:table
php artisan migrate 
```

这为数据库队列驱动程序创建了一个数据库表。

```
return [
  "default" => env("QUEUE_DRIVER", "database"),
  // ...remaining configuration
]; 
```

这是来自`config/queue.php`。

```
namespace Tests\Unit;

use Spatie\Snapshots\MatchesSnapshots;
use Tests\TestCase;

class ExampleTest extends TestCase
{
  use MatchesSnapshots;

  public function testQueueSomething()
  {
    // ...do something that leads to dispatch(new Job);
  }

  public function testQueueSomethingAgain()
  {
    // ...do something that leads to dispatch(new AnotherJob);
  }

  public function testQueue()
  {
    $table = config("queue.connections.database.table");
    $jobs = app("db")->select("select * from {$table}");

    $this->assertMatchesSnapshot($jobs);
  }
} 
```

一旦删除了作业，select 语句将不再返回它们。您必须确保在测试运行时，没有队列监听器/守护进程在为同一个数据库表运行。

## 脆性试验

您可能认为快照测试很脆弱。就像写得不好的接口测试一样脆弱。只是比写得好的接口测试稍微脆弱一点。你是对的。

我们应该问的问题是为什么脆性测试是一个问题。一个有一些有用测试的应用程序比一个没有任何测试的应用程序更好。但是当测试变得脆弱时，任何重构都会破坏它们。即使是无关紧要或表面上的变化也可能会破坏测试。将 div 的类从“某种样式”更改为“某种样式”？如果你在 CSS 选择器中使用它，那么很可能会破坏测试。

脆弱测试的问题是重写测试需要时间。我们不希望一直重写测试，尤其是当微小的变化使它们过早地崩溃时。

但是我们实际上并没有用*快照测试*编写任何测试。不完全是。当然，我们可以编写单元测试和集成测试来补充我们的快照测试。但是当我们编写这些测试时，我们只是将一些可序列化的东西与它最初被序列化的东西进行比较。当它们断开时，在我们确定没有任何东西实际断开后，我们可以删除快照并重试。

从某种意义上说，快照测试使得非 TDD 测试更具交互性。没关系，这些测试是脆弱的，因为它们很容易再生，并且在许多方面仍然有用。

## 摘要

当我第一次了解快照测试时，我有一个美妙的啊哈时刻。从那以后，我写的前端测试大部分都是快照测试。考虑它在 PHP 中的用例很有趣。我希望你和我一样兴奋地去挖掘它！

你能想出更多有用的地方吗？在评论中告诉我们吧！

> 谢谢你，保罗·琼斯，给我指明了特性测试的方向。

## 分享这篇文章