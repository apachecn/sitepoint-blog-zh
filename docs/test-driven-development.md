# 测试驱动开发:你被测试感染了吗？

> 原文：<https://www.sitepoint.com/test-driven-development/>

当给定一组开发应用程序的需求时，大多数程序员最终都能写出有用的东西，对吗？但是很快就出现了改变的要求，伴随这些改变而来的是错误。也许他们开始时规模很小。它们甚至可能完全没有被注意到，但是随着时间的推移，这些小错误开始困扰您的应用程序，并使您想要挥舞着双臂尖叫着逃离项目。然而，通常问题不在于修复漏洞，而在于首先找到它们。正如我们所知，程序员可能会花费几个小时，甚至几天的时间来追踪一个只需要一行代码就能修复的 bug。

除了不断积累的 bug 问题，当您在这里或那里添加一些额外的代码时，您还面临着潜在的功能蔓延。在您意识到之前，您的应用程序拥有所有这些没有人会使用的花哨的功能，并且您要维护的代码比最初的规范要多得多。

三年前，这对我来说是再熟悉不过的场景了。我听说有人提倡使用测试驱动开发(TDD ),但简而言之，我从未接受过这个想法。但是当我最终抽出时间试一试的时候，我再也没有回头！这似乎是软件开发人员中经常发生的事情。一般来说，我们说那些跳入 TDD 并且随后没有发展的人是“测试感染的”。显然还没有已知的治疗方法！

别误会…学会正确使用 TDD 并不容易。我的起步异常艰难，差点就放弃了。每当我修改源代码时，我的测试就会中断，而且我根本不知道如何编写许多测试。这需要很大的注意力，最终的结果并不像我期望的那样优雅。我不是第一个，也不会是最后一个。但是，当你第一次学习任何东西时，犯这些错误是你经常需要做的，以便理解为什么你必须以特定的方式做事。三年过去了，我仍在学习如何编写更好的测试，但我编写的测试更加灵活，也更易于维护。

##### 介绍 TDD

让我告诉你一些我使用 TDD 的经历，它是如何增加我从编写软件中获得的乐趣的，以及我的源代码的质量是如何因此得到提高的。

为了向那些以前从未使用过 TDD 的人解释 TDD 的基础知识，我们也许应该考虑一下传统的测试形式。传统上，软件开发的三个阶段是计划、实现和测试。开发人员在每个步骤上花费的时间大致相同。这里的问题是，尽管计划看起来无懈可击，代码也不难写，但测试在游戏中进行得太晚了。在软件已经构建好之后，错误报告会反馈给开发人员，但是很明显，随着最后期限的临近，这远远不是一个理想的情况。

然而，在现实中，这一过程从未像这三个步骤那样清晰。开发人员通常在完成编写后测试一小部分代码；我们称这些代码的小部分为“单元”。无论您是从最终用户的角度使用系统，还是将调试输出添加到代码中并模拟软件可能遇到的特定场景，这种测试很快就会变得乏味。在人们厌倦了重复这个过程并简单地停止之前，他们可以执行手动值检查的次数是有限的。传统上，开发人员不太可能回到他们已经编写好的系统的某些方面，并再次执行手工测试。因此，这里或那里的小变化可能会在系统的其他部分引入隐藏的错误。

另一方面，测试驱动的开发有点违背常理。这个过程看起来更像规划、设计、测试、实现、重构、设计、测试、实现、重构……等等，与传统开发相比，对规划阶段的强调要少得多。设计步骤很重要。为了编写测试，开发人员需要知道他们在测试什么。为此，他们不断在系统中设计非常孤立的组件。这鼓励了代码的大量灵活性，因为易于测试的代码和灵活性通常是携手共进的。

TDD 通常从单元测试框架开始。这并不是说测试驱动的开发是单元测试——它不是。因为我主要用 PHP 开发，所以我选择了由[马库斯·贝克](http://www.lastcraft.com/about.php)编写的[简单测试](http://simpletest.sourceforge.net/)。PHP 提供了另一个流行的单元测试框架，名为 [PHPUnit](http://phpunit.de/) ，但是我选择了 SimpleTest，因为它是两者中更成熟的，并且有更容易获得的支持(很大程度上是因为作者是 [SitePoint 论坛](https://www.sitepoint.com/forums/)的常客)。几乎所有的单元测试框架都极其相似。JUnit——Java 最广泛使用的单元测试框架——将这种方法带入了主流。随后，用其他编程语言编写的各种框架或多或少地遵循了 JUnit 的最小 API。我们将这组框架称为 xUnit 框架。

但是，在实现系统之前，我们如何测试它呢？乍一看，这种哲学似乎有缺陷，但一旦这个概念开始深入人心，它就完全有意义了。测试本身是用代码编写的，并对被测系统的行为做出各种断言(SUT)。编写测试是在单元测试框架中完成的，比如 SimpleTest。这个想法是写一个小而简单的测试来表达一个行为需求。例如，当传递一组特定的参数时，SUT 可能会返回一个负值。单元测试框架提供了做出这些断言的方法，并在一个或多个断言失败时向开发人员提供有用的反馈。

在 TDD 中，开发人员将编写一个故意失败的测试(因为 SUT 还没有任何需求的实现)，然后继续编写最明显和最简单的代码来通过这个测试。一旦测试通过，开发人员编写下一个(可能失败的)测试，实现一点代码使它通过，然后继续前进。最终的结果是，随着时间的推移，您有了两大套源代码——一套是测试代码本身；另一个是软件的源代码。提供测试覆盖的代码行很可能比实际的源代码多。将所有测试代码保存在项目目录中有几个好处:

*   运行测试确保他们指定的行为要求仍然得到满足。
*   他们为希望修改软件而不破坏或改变其现有行为的开发者提供支持。
*   它们为需要理解软件如何工作的开发人员提供了一种文档形式。

所有的 xUnit 框架都提供了将这些测试组合到一个测试套件中的机制，并自动一次性运行它们。这使得重新测试软件的过程比传统的手工测试要简单得多，也更有吸引力。

##### TDD 在行动

让我们看一个简单的例子，如何在 PHP 中使用 TDD 和 SimpleTest 单元测试框架。恐怕您必须在这里发挥您的想象力，因为与通常使用 TDD 的大规模应用程序相比，这个例子显然是非常小的。解决一个更大的问题超出了本文的范围。这篇文章并不打算成为 TDD 的初学者入门——在互联网上有很多关于这方面的文章。这里，我们以一个基本的过滤器链为例，我们想用它在文本中生成超链接，并过滤掉不良词汇。您可以[下载本文中示例的代码，以及测试](https://www.sitepoint.com/examples/tdd/word_filter.zip)。

我们的界面可能看起来像这样:

```
...  

/**  

 * Performs a single filtering method on some input text.  

 */  

interface TextFilter {  

  /** Process $text and return a filtered value */  

  public function filter($text);  

}  

/**  

 * Performs filtering on text using a series of filters.  

 */  

class TextFilterChain {  

  /** Add a new filter to this chain */  

  public function addFilter(TextFilter $filter) {  

  }  

  /** Pass $text through all filters and return the filtered value */  

  public function filter($text) {  

  }  

}  

...
```

我们想要一个`AutoHyperlinkFilter`和一个`NaughtyWordFilter`，所以我们选择一个并为它创建框架代码:

```
...  

class NaughtyWordFilter implements TextFilter {  

  public function filter($text) {  

  }  

}  

...
```

然后我们创建一个测试用例。一个测试用例是一个单独的类。通常，系统中的每个具体类都有一个测试用例。正如我们在上面看到的，测试用例测试的“东西”通常被称为 SUT(被测系统)。类中任何以单词“test”开头的方法都将被执行和报告。我们期待一组顽皮的词语在这里被替换:

```
...  

class NaughtyWordFilterTest extends UnitTestCase {  

  public function testNaughtyWordsAreReplaced() {  

    $filter = $this->_createFilter(array('foo', 'bar'));  

    $this->assertEqual(  

      "smurf! There's no way I'm doing that smurf...",    

      $filter->filter("foo! There's no way I'm doing that bar...")  

      );  

  }  

  private function _createFilter($words = array()) {  

    return new NaughtyWordFilter($words);  

  }  

}  

...
```

现在我们运行它:

```
...  

$test = new NaughtyWordFilterTest();  

$test->run(new TextReporter());  

...
```

测试失败是因为我们还没有实现我们的`NaughtyWordFilter`。这很好，因为失败的测试是我们继续编写代码的信号。如果你愿意的话，这是一个规格——一个要达到的目标。

失败看起来像这样:

```
NaughtyWordFilterTest.php  

1) Equal expectation fails at character 0 with [smurf! There's no way I'm doing that smurf...] and [] at [/Users/chris/word_filter/tests/unit/NaughtyWordFilterTest.php line 11]  

  in testNaughtyWordsAreReplaced  

  in NaughtyWordFilterTest  

FAILURES!!!  

Test cases run: 1/1, Passes: 0, Failures: 1, Exceptions: 0
```

我们所做的就是实现足够的代码来通过这个测试:

```
...  

class NaughtyWordFilter implements TextFilter {  

  private $_badWords = array();  

  public function __construct($badWords = array()) {  

    $this->_badWords = $badWords;  

  }  

  public function filter($text) {  

    foreach ($this->_badWords as $badWord) {  

      $text = str_replace($badWord, 'smurf', $text);  

    }  

    return $text;  

  }  

}  

...
```

现在这个测试通过了，当我们运行测试时，我们看到一个不那么吓人的输出:

```
NaughtyWordFilterTest.php  

OK  

Test cases run: 1/1, Passes: 1, Failures: 0, Exceptions: 0
```

现在我们可以进入下一节课了。以下是基本代码:

```
...  

class AutoHyperlinkFilter implements TextFilter {  

  public function filter($text) {  

  }  

}  

...
```

接下来我们写一个失败的测试。我们希望 URL 被转换成超链接:

```
...  

class AutoHyperlinkFilterTest extends UnitTestCase {  

  public function testURLsAreHyperlinked() {  

    $filter = $this->_createFilter();  

    $this->assertEqual(  

      'Go to my web site at <a href="http://site.com/">http://site.com/</a> and see!',  

      $filter->filter('Go to my web site at http://site.com/ and see!')  

      );  

  }  

  private function _createFilter() {  

    return new AutoHyperlinkFilter();  

  }  

}  

...
```

接下来是实施:

```
...  

class AutoHyperlinkFilter implements TextFilter {  

  public function filter($text) {  

    return preg_replace('~(http://S+)~i', '<a href="$1">$1</a>', $text);  

  }  

}  

...
```

我们完成所有需要的组件，为每个组件的行为编写非常简短的测试，直到完成。

那么，为什么我会在这个过程中做出一些小的(事实上，小到你可能都没有注意到)设计决策呢？比如为什么我选择用`NaughtyWordFilter`的构造函数来指定一些“调皮话”？答案是，在 API 级别上，这似乎是最干净的解决方案。它只是出于本能。这是开发人员为了产生干净、灵活、可测试的代码而经历的一种持续的设计思维过程。编写测试鼓励你仔细考虑你的代码的接口，否则你将不能很容易地测试它。

通常，您会希望避免在测试中使用真实的组件(它们可能有自己的错误，或者可能难以设置)，以便您可以完全专注于 SUT 的行为。模拟对象是看起来和感觉上都像真实对象的对象，但是能够替换真实组件并在 SUT 中扮演它们的角色(它们通常被称为“演员”或“存根”)。模拟对象还能够假设 SUT 将对它们做什么(通常被称为“批评家”)。这使得它们成为在任何单元测试框架中使用的极其强大的工具。

SimpleTest 提供了自己的模拟对象框架，但是对于其他编程语言，如果您想要自动生成模拟对象，通常需要下载一个单独的工具。

让我们快速编写我们的`TextFilterChain`类。因为这个类依赖于`TextFilter`接口的实例，所以它为使用模拟对象提供了最好的机会。

首先，我们用生成的模拟对象创建一个失败的测试用例。我们希望调用每个过滤器:

```
...   

Mock::generate('TextFilter', 'MockTextFilter');   

class TextFilterChainTest extends UnitTestCase {   

  private $_filterChain;   

  public function setUp() {   

    $this->_filterChain = new TextFilterChain();   

  }   

  public function testEachFilterIsInvoked() {   

    $filter1 = $this->_createMockFilter();   

    $filter2 = $this->_createMockFilter();   

    $filter1->expectOnce('filter');   

    $filter2->expectOnce('filter');   

    $this->_filterChain->addFilter($filter1);   

    $this->_filterChain->addFilter($filter2);   

    $this->_filterChain->filter('foo');   

  }   

  private function _createMockFilter() {   

    return new MockTextFilter();   

  }   

}   

...
```

失败看起来像这样:

```
TextFilterChainTest.php   

1) Expected call count for [filter] was [1] got [0] at [/Users/chris/word_filter/tests/unit/TextFilterChainTest.php line 20]   

  in testEachFilterIsInvoked   

  in TextFilterChainTest   

2) Expected call count for [filter] was [1] got [0] at [/Users/chris/word_filter/tests/unit/TextFilterChainTest.php line 21]   

  in testEachFilterIsInvoked   

  in TextFilterChainTest   

FAILURES!!!   

Test cases run: 1/1, Passes: 0, Failures: 2, Exceptions: 0
```

测试指定，如果两个过滤器被添加到过滤器链，过滤器链应该对它们都调用`filter()`方法。这并没有发生，因为我们还没有实现这样的功能。

实施如下:

```
...   

class TextFilterChain {   

  private $_filters = array();   

  public function addFilter(TextFilter $filter) {   

    $this->_filters[] = $filter;   

  }   

  public function filter($text) {   

    foreach ($this->_filters as $filter) {   

      $filter->filter($text);   

    }   

  }   

}   

...
```

测试现在通过了，我们继续指定还应该发生什么。应该为每个过滤器提供我们传入的文本:

```
...   

  public function testFilterInvocationReceivesTextInput() {   

    $filter = $this->_createMockFilter();   

    $filter->expectOnce('filter', array('foo'));   

    $this->_filterChain->addFilter($filter);   

    $this->_filterChain->filter('foo');   

  }   

...
```

这个特殊的测试已经通过了，所以我们继续。如果一个过滤器更改了文本，下一个应该会收到更改后的值:

```
...   

  public function testChangesAreChained() {   

    $filter1 = $this->_createMockFilter();   

    $filter2 = $this->_createMockFilter();   

    $filter1->expectOnce('filter', array('foo'));   

    $filter1->setReturnValue('filter', '***FOO***');   

    $filter2->expectOnce('filter', array('***FOO***'));                                        

    $this->_filterChain->addFilter($filter1);   

    $this->_filterChain->addFilter($filter2);   

    $this->_filterChain->filter('foo');   

  }   

...
```

测试失败了，所以我们调整我们的实现以使它通过:

```
...   

  public function filter($text) {   

    foreach ($this->_filters as $filter) {   

      $text = $filter->filter($text);   

    }   

  }   

...
```

最后，我们期望从链中返回过滤后的值:

```
...   

  public function testFilteredValueIsReturnedFromChain() {   

    $filter = $this->_createMockFilter();   

    $filter->expectOnce('filter', array('foo'));   

    $filter->setReturnValue('filter', '***FOO***');   

    $this->_filterChain->addFilter($filter);   

    $this->assertEqual('***FOO***', $this->_filterChain->filter('foo'));   

  }   

...
```

面对失败的测试，我们调整我们的代码:

```
...   

  public function filter($text) {   

    foreach ($this->_filters as $filter) {   

      $text = $filter->filter($text);   

    }   

    return $text;   

  }   

...
```

就这样，我们结束了！如果所有这些测试都被收集到一个测试套件中，那么它们可以在一个单独的测试运行中自动执行。显然，这个项目的小规模并不能成为使用 TDD 的压倒性理由，但是当你将同样的技术推广到一个更大的项目中时，好处很快就不言自明了。

##### 测试被感染

我目前正在重写我的一个著名的开源软件项目。就在几周前，我认为我早期的一个设计决定可能会更好一些，所以我开始了大规模的重构练习。如果没有这些测试，考虑到它的规模，我永远也不敢尝试这个练习，即使我勇敢面对，我也不会相信没有隐藏的错误。当时我有 77 个测试用例，覆盖了几千行代码，至少可以说，这些测试带来的舒适是显著的。最初，由于我正在做的工作的规模，测试失败了，但是这些失败的测试在我改变接口、移动代码和重命名方法时充当了向导。最终，一个接一个，测试开始通过，直到最后 77 个都通过了。我确信我刚刚部署到我的代码库的巨大变化不会中断。

不过，回到我在本文前面提到的一点。TDD 并不总是让我满意。事实上，我使用 TDD 的头几个月是地狱:一半的时间，我都在修复那些失败的测试，因为我稍微改变了一些实现细节。虽然这个系统仍然工作，但我的测试显示并非如此。我们称这些测试为脆弱测试。我还花了很多时间绞尽脑汁，想知道如何开始测试系统的特定部分。这是一个太普通的故事，听到开发人员由于这些最初的艰难经历而决定不使用 TDD 是一种耻辱。让我和你们分享一些我一路走来学到的技巧:

*   编写非常简短、简洁的测试方法——一个好的经验法则是每个测试方法一个断言，但这只是一个经验法则！
*   永远不要——我再怎么强调也不为过——测试系统的非公共部分。即使你认为它们扮演着重要的角色，它们也极有可能随着重构而改变。TDD 关注的是行为，而不是实现。
*   在测试和 SUT 之间添加一些抽象。具体来说，尽可能在 xUnit 框架的`setUp()`方法中创建 SUT，和/或创建用于创建 SUT 的小型工厂方法。这些工厂方法通常被称为创建方法。如果您改变了您需要初始化 SUT 的方式，创建方法使您更容易最小化您需要编辑您的测试的地方的数量。
*   如果您觉得在测试一组通用组件时重复了自己的工作，那么考虑一下是否可以重构以提供一个抽象超类来测试一些通用功能。
*   创建组件时选择使用依赖注入。这样做极大地增加了代码的测试友好性——通过使用模拟对象，您可以获得更多的控制。

尽管随着时间的推移，这些解决方案对我来说变得显而易见，但我必须承认，它们在几年前对许多其他开发人员来说也变得显而易见。杰拉德·梅萨罗什写了整整 800 页的书专门介绍这个领域。 *xUnit 测试模式——重构测试代码*展示了开发人员发现的改进自动化测试的常用方法。如果你有一些测试经验，它非常值得一读。

我希望这篇文章对那些涉足 TDD 的人有所启发。我希望这对那些在过去尝试过 TDD，但在面临与我在早期 TDD 时代处理的问题类似的问题时放弃的人是一种鼓励。更重要的是，我希望它能为每一个到目前为止的人提供一个有趣的关于我的 TDD 经验的读物！

不要忘记，您可以[下载本文中示例的代码，以及测试](https://www.sitepoint.com/examples/tdd/word_filter.zip)。走开去玩吧。但是要注意，这是会传染的…

## 分享这篇文章