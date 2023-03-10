# 臭黄瓜

> 原文：<https://www.sitepoint.com/smelly-cucumbers/>

这是一个如此陈词滥调的标题，我不得不使用它。我肯定你听说过伟大的 BDD 工具[黄瓜](http://cukes.info/)，但是你可能不知道的是黄瓜有多臭。

我最近一直在绝对厌恶地修改和重构我的黄瓜特征。在我的辩护中，我在 6-7 个月前开始认真地写黄瓜。我没有在这上面花太多时间。启动和运行几个 Rails 应用程序很快，但是当我发现它们变得笨拙和难以维护时，我就停止使用它了。

这决不是黄瓜的任何过错。我忽略了学习或开发这么多的工具，难怪我会遇到麻烦。最近几个月，我又花了一些时间在黄瓜上，并意识到我是一个多么愚蠢的人。

让我们看看其中一个违规的黄瓜测试，稍微修改一下。值得一提的是，我并不只是做 todo 应用程序，但它们确实是一个很好的例子。在这种情况下，这是一个贫穷的 cuke 的好例子。

```
Feature: Todo item management

Scenario: Adding a todo item
    Given: I have a todo list named "Mondays list"
    When I go to the home page
    And I fill in "username" with "dave"
    And I fill in "password" with "secret"
    And I press "Log In"
    And I go to the todo page
    And I click on link "Mondays list"
    And I fill in "todo" with "Grab some milk"
    And I press "Add todo"
    Then I should see "Todo item added successfully"
```

太可怕了是吗？当您意识到存在更新和删除场景时，情况会变得更糟。

## 题外话——黄瓜是如何工作的

在深入研究之前，我们最好先确立黄瓜工作原理的要点。您有三个组成部分:特性、场景和实现。

该特性是一个场景，或者更可能是用[小黄瓜](http://rubygems.org/gems/gherkin)编写的场景集合。我只能把小黄瓜描述成类似于[降价](http://daringfireball.net/projects/markdown/)的东西。它是人类可读的文本，具有特定的结构和一些关键字。更常用的/已知的是给定/何时/然后。

在这些特性的背后，我们有用 Ruby 编写的实现，也就是通常所说的步骤。场景中的每一行都相当于一块 Ruby 代码。例如，`Given: I have a todo list named "Mondays list"`可以这样实现:

```
Given /^I have a todo list named "([^"]*)"$/ do |list_name|
  List.create(:name =&gt; list_name)
end
```

简单地说，当 Cucumber 运行时，它会获取我们场景中的行并调用相应的步骤块。

要记住的几件事是，你要孤立地对待每一个场景。没有任何东西被带到下一个场景，你需要使用 cucumber 的一些特殊的“设置”、“拆卸”特性。

## 解剖黄瓜

回顾文章开头的场景，我读到这个测试的三分之一，失去了活下去的意愿。它描述了应用程序的行为吗？对我来说，它描述的只是我想做的事情的机制。与其说是对行为的描述，不如说是写得很糟糕的集成测试。

对我来说幸运的是，这种情况很常见。当我知道某个可怜的开发人员遭受了和我一样的痛苦时，我并没有感觉很糟糕。

没有什么比一点自我批评更像心灵鸡汤了。被指控的情节有什么错？

### 令人困惑

我们肯定能闻到一些不对劲的地方。乍一看，很混乱。我们必须想象页面出现在视图中，填充文本字段，按下按钮。这种行为迷失在许多无意义的细节中。

Cucumber 的开发是为了让客户和利益相关者参与开发过程。我们可能会和那些没有技术头脑的人打交道，或者他们对网络的经验可能仅仅是些搞笑的人。尽管这可能很棒，但在确保我们满足他们的要求之前，要求他们想象一下在网上填写一些表格，这有点过分。

也就是说，我很少向客户展示我的黄瓜场景，但是我每天都向其他开发人员展示。我的同事们都非常熟悉 web 应用程序，功能应该描述预期的行为。他们不必检查我的代码，也不必知道我在做什么。由于时间关系，我可以给他们写一个小故事。此外，当你独自工作时，这个故事被证明是一个很好的提醒。

### 它很脆

其次，测试是脆弱的。如果登录过程改变了怎么办？还是登录网址？即使添加 todo 项的机制没有改变，测试也会失败。这不可能是好消息。

### 它很懒

最后的气味是断言，或“然后”。实际测试的是什么？铁轨在闪光。这是一个懒惰的断言，让我不寒而栗。

## 傻瓜冲进去

随着时间的推移，我已经被黄瓜咬了一口，在匆忙进行重构步骤之前，我必须变得聪明一点。

当然，上面场景中的步骤很容易编写。我几乎不需要在 Ruby 中编写任何步骤(不推荐使用的 web_steps.rb 为我处理了这些)。然而，如果我有 20 个左右的场景需要用户登录，并且登录 URL 发生了变化，我就有麻烦了。当然，Vim 宏可以减轻一些痛苦，但是这还远远不够理想。

从全局来看，如果我们稍微修改一个场景，我们实际上是在修改应用程序的预期行为。

在我们的例子中，登录是非常轻微的。毕竟，我们只关心我们是否登录。如果我们需要用某些凭证登录才能获得特权，那该怎么办？我发现让你的场景尽可能的通用真的很有帮助。

## 做正确的事

是时候停止责备自己，做点什么来改变这个臭名昭著的场景了。首先，我想恰当地描述一下特征。Gherkin 允许我们在声明特性后只写文本，直到它到达一个关键字时才会生效。

```
Feature: Todo item management

I want to track items I need to do in a list. That way I will never forget them. I want to add, edit, delete and mark todo items as finished.
```

Gherkin 实际上提供了一个很好的语法来去掉写什么的决定，它采用“按顺序/按我想要的”的格式。我更喜欢写一个故事，但我可以很容易地写:

```
Feature: Todo item management
  In order to remember things
  As a person with too much on his mind
  I want to maintain todos on a list
```

我举着双手说我不知道哪个更好。我觉得故事的形式有优势，因为我们可以更具描述性。然而，我比任何人都清楚，一个光标闪烁的空白屏幕是多么令人生畏。我建议您使用任何适合您的格式，只要您对该特性进行一些描述。

既然我们已经知道我们描述的是什么，让我们来看看重构场景的本质。第一件事必须是摆脱所有这些网络步骤。

```
Feature: Todo item management

I want to track items I need to do in a list. That way I will never forget them.
I want to add, edit, delete and mark todo items as finished.
Scenario: Adding a todo item
    Given: I have a todo list named "Mondays list"
    And I am logged in as a normal user
    When I add a todo item "Grab some milk"
    Then it should be added to the todo list
```

那好多了，但是登录的事情仍然困扰着我。这是一个我不应该担心的细节。我说的是添加一个 todo 项，所以我为什么要用这个无聊的日志来分散自己的注意力。幸运的是，我们可以使用 Gherkin 关键字“Background:”来处理这个问题。

```
Feature: Todo item management

I want to track items I need to do in a list. That way I will never forget them. I want to add, edit, delete and mark todo items as finished.

Background:
    Given I am logged in as a normal user

Scenario: Adding a todo item
    Given: I have a todo list named "Mondays list"
    When I add a todo item "Grab some milk"
    Then it should be added to the todo list
```

背景将作为设置，在功能中的每个场景之前运行。背景是伟大的，但还有另一种方式，这是我在处理登录时倾向于使用的:黄瓜挂钩。

```
@user_logged_in
  Scenario: Adding a todo item
    Given: I have a todo list named "Mondays list"
```

我可以将这个钩子的实现和其他角色一起添加到支持文件中。像这样使用第三方授权库(如 [warden](http://rubygems.org/gems/warden) )尤其有用:

```
Before('@user_logged_in') do
  user = User.create(name: "test_user", admin: false)
  login_as user
end
```

现在是步骤定义。用红宝石写的，真的很容易。

```
Given /^I have a todo list named "([^"]*)"$/ do |list_name|
  @todo_list = Factory.create(:list, name: list_name)
end

When /^I add a todo item "([^"]*)"$/ do |item_description|
  visit todo_path(@todo_List)
  fill_in "Description", with: item_description
  click_button "Add Item"
end

Then /^the Item should be added to the client$/ do
  @todo_list.reload
  @todo_list.items.length.should eql 1
end
```

我已经指出了一些我们在使用黄瓜时可能会遇到的潜在陷阱。当然，没有一个真的是 Cucumber 的错，但是，就像任何错误的框架一样，它只会带来痛苦。希望，我在这里介绍的一些内容可以为您省去一些创伤。我很容易患上“我做得对吗”综合症，但有时我们必须感受痛苦才能学到新东西。

我坚信应该保持高水平的场景。我读到过一些开发人员拒绝在步骤定义中使用正则表达式样式的表达式。如果这对他们有用，那太好了。然而，我喜欢将这些正则表达式用于设置，有时用于“何时”步骤，但从不用于断言。

通常，尽量保持场景紧凑，不要试图在一个场景中涵盖太多的功能。这听起来像是常识，但是我经常被一个特性的补充元素分散注意力。最后，不要害怕撕掉并重写“正在进行的工作”场景。Cucumber 还为您的应用程序提供了一个很好的发现过程。允许黄瓜测试驱动你的开发，总是提取尽可能多的测试到你的单元测试/规格中。如果你还没有开始，甚至已经放弃黄瓜测试，我强烈建议你试一试。最大的好处是知道系统以预期的方式运行。

## 分享这篇文章