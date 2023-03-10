# 防止代码腐烂 101:单元测试

> 原文：<https://www.sitepoint.com/preventing-code-rot-101-unit-testing/>

一位聪明的程序员曾经说过:“当我提交代码时，只有上帝和我知道它在做什么。过一段时间，只有上帝知道。”这是代码腐烂的基本定义，适用于我们编写的几乎所有代码。

当然，代码仍然会完全按照您签入它或将其推向生产时的方式运行，但是既然它运行了，它就变成了某种神奇的东西:一个被证实的真理，并且您不会触及被证实的真理。随着时间的推移，代码变成了无人敢碰的庞然大物，代码库的遗留部分。它是“很久以前”设计的，自发布以来，它的功能完全相同，没有人完全理解它。如果有什么需要改变，你就绕开它；你改变了输入或输出，但黑盒保持不变。

除非你现在正在进行一个新的项目，否则你所使用的代码库很可能包含你认为理所当然的代码。你当前的任务(或新项目)越有挑战性，它就越有可能在短时间内成为遗留部分。

因为完全理解代码库解决的所有问题以及它们的解决方案是不可行的，所以需要做些别的事情来防止代码腐烂。不管怎样:代码需要是可重构的，这样才能幸福地生活下去。这就是单元测试的用处；它们提供了一种简单的方法来编写证明，证明您的代码按预期工作。每个证明都使得被证明的东西是可重构的。您可以更改正在被验证的代码，然后让测试来验证新代码是否仍然以同样的方式运行。

虽然我很乐意告诉你我所知道的关于单元测试的每一个小细节，以及如何将它应用到你的代码中，但我还是勉强同意我的编辑的观点，150，000 字对于一篇文章来说太长了。所以我坚持基本原则。

## 单元测试基础

原则上，单元测试是做三件事的一小段代码:

*   汇编–建立一个类似于代码在生产过程中运行的环境，但是不依赖于代码中的任何其他组件
*   act——执行要验证的代码
*   断言——验证执行的结果是给定环境下的预期结果

敏锐的读者会注意到，这实际上并没有测试代码的正确性。如果有一个由代码中的假设引起的错误，由编写单元测试的同一个开发人员引入，那么这个错误将不会被发现，因为测试将使用相同的假设来编写。由打字错误引起的错误将通过运行测试来发现，类似于在一个阶段环境中执行代码时发现的错误。所以单元测试是一个有点误导的名字:它更多的是单元证明而不是单元测试。

要证明的单元取决于编写单元测试的开发人员，尽管经验法则是单元必须行动，也就是说，它必须有行为。只存储值的简单实体类不需要单元测试，因为没有什么需要证明的。您可以依靠编程语言来将值存储在变量中；测试这个没有意义。

与此相关的是，可以随意忽略代码覆盖率数字。它们是无用的指标。唯一重要的是动作组件或单元是否有证据。您可以拥有高达 95%的代码覆盖率，但是如果这 5%都是核心业务逻辑，那么您仍然有(即将出现的)黑盒等待开始腐烂。

### 装配

组装阶段的重要部分是环境模拟生产，但不使用任何未被测试的组件/单元。如果您需要证明一个修改来自数据库的值的方法，那么将其简化为只修改一个值。如果您的代码是紧密耦合的，那么您可以模拟在生产中提供价值的数据库组件。

使用模拟组件，您设计了一个假的组件，对于被测试的组件来说，它表现为真正的组件，但是它的行为是可预测的。假设您正在测试的组件通过数据层组件从数据库中获取一个姓名列表，然后将每个第一个字符大写。模拟对象模仿数据层组件，但实际上并不与数据库对话。相反，它包含名的静态列表。

```
<?php
...
    // ** ORIGINAL METHOD **/
    public function getCustomerFirstNames(DBLayer $db) {
        $dbvalues = $db->getQueryResults("SELECT firstname FROM customers");
        $firstnames = array();
        foreach($dbvalues as $firstname) {
            $firstnames[] = ucfirst($firstname);
        }
        return $firstnames;
    }
...
```

```
<?php
//** Mock for DB layer **/
class DBLayer
{
    public function getQueryResults($sql) {
        return array("John", "tim", "bob", "Martin");
    }
}
```

```
<?php
...
    //**PHPUnit testcase **/
    public function setUp() {
        //mock file included instead of real file
        $this->db = new DBLayer();
    }

    public function testGetcustomerfirstnamesWillReturnFirstnamesModifiedByUcfirst() {
        // assemble (together with the runonce setUp() method)
        $obj = new OriginalClass();
        $expected = array("John", "Tim", "Bob", "Martin");
        // act
        $results = $obj->getCustomerFirstNames($this->db);
        // assert
        $this->assertEquals($expected, $results, "GetCustomerFirstNames did not ucfirst() the list of names correctly");
    }
...
```

通过这种方法，实现了两件事:

*   测试组件不依赖于任何其他组件
*   我们运行测试的组装环境是可预测的；每次我们运行测试时，它的行为都是完全一样的

### 行动并断言

有了合适的环境，就可以执行测试组件了。给定已知的输入，以及代码应该解决什么问题和如何解决，结果可以很容易地推导出来。这正是断言阶段的目的。这是实际证明代码的部分。

该断言简单地陈述:“给定已知的输入 x，由已知的函数 f 处理，输出是 f(x)”。编写函数 f 和附带的单元测试的开发人员编写了这个函数来解决手头的特定问题。测试消除了了解甚至理解问题的需要，或者为什么它以一种特殊的方式被解决。代码的功能被接受了(并且有希望继续被接受测试接受)，所以只要单元测试通过；代码被证明是正确的。

这意味着现在可以根据需要尽可能多地重构代码，并以单元测试作为功能保持不变的证明。这抵消了代码腐烂的最大原因，因为代码现在可以安全地被修改。只要通过了单元测试，它就不会被破坏。如果测试失败，开发人员会很快得到通知，并可以调整或恢复应用的更改，甚至在代码在登台环境中执行之前。

## 应用单元测试

既然您已经了解了什么是单元测试以及它们是如何工作的，那么我想谈谈将会使您的单元测试体验更好的几点。

单元测试不是集成测试、验收测试或任何其他形式的实现测试。
虽然每个单元测试框架都会给你一个完整的工具包，这让你很想偷偷进行一些集成或验收测试……但是不要。单元测试应该只证明原始开发人员的解决方案仍然在应用，尽管测试中的代码有任何变化。虽然您可以用同一个框架编写集成或验收测试，但是您可能想要考虑使用两个独立的框架，这样更容易将两个框架分开。

不要忘记编写集成/验收测试。
一个单元测试孤立地证明了一小段代码，但并不能证明系统是工作的。这包括在集成和验收测试中；一个运行缓慢的测试套件，它结合了所有的组件，并证明了各个部分能够正常工作。单元测试简洁而快速，允许它在每次更改之前和之后运行，而不会花费你太多时间。集成/验收测试是在开发周期的末尾，在 QA 阶段运行的。

保持你的单元测试简单明了。
请记住，当至少有一个断言失败时，单元测试将被标记为失败，因此值得遵循的一般经验法则是测试中的断言越少越好。一个好的测试从来不会混淆行为证据。如果这样的测试失败了，您可以立即确切地知道什么功能被破坏了。如果行为证据混淆了或者在同一个测试中出现了多个断言，您将需要启动一个调试器来找出现在什么行为被破坏了。所有断言都可能失败，或者只有一个失败，只有第一个失败的断言会被报告。

冗长地命名你的单元测试。
测试方法的名称永远不会被手动调用，所以没有必要使用巧妙或者简洁的命名约定。名称中的冗长是被测试内容的额外文档形式。

一个额外的好处是，当选择 TestDox 格式的输出时，像 PHPUnit 这样的单元测试框架会生成一个很好的清单，上面显示了人类可读的行，[作为文档](http://www.phpunit.de/manual/current/en/other-uses-for-tests.html "Chapter 15\. Other Uses for Tests")。如果您在实际实现之前编写测试，您甚至可以使用这个输出作为一个快速检查表来查看哪些已经完成，哪些仍然需要编写。

只为你自己的代码编写单元测试。
尽管单元测试框架给了你如此多的工具，以至于你很想为眼前的一切编写测试，但只有你自己编写的代码才能被正确地单元测试。好的代码是问题的解决方案；它是彻底理解问题、理解可能的解决方案以及最终实际执行解决方案的结果。如果只有实现是可见的，那么编写的测试将没有相同的假设和对原始问题解决者的理解。单元测试很可能会错过特定的设计选择，使测试变得不可靠。

编写单元测试来修复错误。
在修复 bug 之前，写一个测试，证明当前代码是错误的(因为断言失败)。然后修复代码，使未更改的断言通过。作为副作用，这是开始向当前未覆盖的代码添加单元测试的好方法。这有助于治愈代码腐烂，一次一个小的单元测试，因为那部分代码现在被证明是正确的(对于正确的当前定义)。这也防止了重构时错误被再次引入，因为再次允许有错误的行为将导致单元测试失败。

不要改变测试中的代码。
当你开始为已经编写好的代码编写测试时，你会很快注意到，由于紧耦合或其他设计选择，大多数代码很难测试。稍微调整一下测试中的代码，使之更容易为其编写单元测试，这是非常诱人的，但是不要这样做。单元测试的全部目的是证明现有的代码，这样重构它是安全的。在编写单元测试之前进行重构正是您想要避免的风险——您不知道会破坏什么或者会引入哪些 bug。

## 舍入

经理们不会给每个问题指派一个完整的团队，让他们永远维护那个小小的代码库，所以如果你想防止你的天才解决方案腐烂掉，你可以以单元测试的形式编写你自己的小虚拟助手来为你维护那个代码库。

如果你对你写的代码感到自豪，你应该对它进行单元测试。如果你不知道如何编写单元测试，那么你应该学习如何编写它们。如果你的团队不使用单元测试，说服他们这是一个必需的工具。

关于单元测试以及为什么要编写单元测试，还有很多东西需要学习；如需进一步阅读，请访问:

*   【PHPUnit 入门
*   [PHP 单元测试入门(通过 Composer 使用 Simpletest 和 PHPUnit)](http://anchetawern.github.com/blog/2012/12/17/getting-started-with-unit-testing-in-php/ "Getting Started With Unit Testing in PHP - Wern Ancheta")
*   [我和我的同事在尝试为 PHPUnit 编写单元测试时学到了什么](http://lars-tesmer.com/blog/2011/09/08/what-my-co---workers-and-i-learned-when-trying-to-write-unit-tests-for-phpunit/ "What My Co-Workers and I Learned When Trying to Write Unit Tests for PHPUnit - Lars Tesmer's Blog")
*   PHPUnit 手册:为 PHPUnit 编写测试

请随意询问您当地的 PHP 用户组，或者在您的下一次会议上参加关于这个主题的会议。大多数专业 PHP 开发人员至少有一些 PHPUnit 和单元测试的经验。

一个明智的程序员会说:“当我提交代码时，只有上帝，单元测试，我知道它做什么。过一段时间，只有上帝和单元测试知道。”

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章