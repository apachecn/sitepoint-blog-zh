# NET 到 Ruby:学习如何编写测试，第二部分

> 原文：<https://www.sitepoint.com/net-to-ruby-learning-how-to-write-tests-part-ii/>

这篇文章的第一部分讲述了我在 2007 年的经历。NET，以及它如何帮助我在短时间内提高 Ruby 的工作效率。第 2 部分涵盖了我在 Ruby 中的经历，从我刚开始用 Ruby 做任何事情就开始编写测试，到我目前的状态，编写更好的测试和改进我开发软件的方法。

我知道我还有很多东西要学，而且有很多 Ruby 奇才可以做得比我在这里展示的要好得多。这些事情永远都是这样。我的意图仅仅是站出来说出我的经历，希望我们也可以在评论区得到一些反馈，并进行一些经验交流。

## 测试帮助我学习了 Ruby

正如我在我的帖子[这里](https://www.sitepoint.com/c-dynamic-features-helped-me-learn-ruby/)中提到的，Ruby 是一种动态语言，我不能依赖编译器给我至少一些指示，告诉我哪里出错了。我可以使用交互式 Ruby Shell (IRB)进行尝试(我偶尔会这么做)，但是我更愿意编写测试。为什么？因为它们变成了“探索性”测试。他们让我尝试一些东西，直到我通过测试。一旦他们这样做了，它就变成了我最后一次尝试的有效方法的文档。

随着时间的推移，我学会了用 Ruby 完成一些事情的更好的方法，我可以回到我的探索性测试，尝试更好的方法，看看我的测试是否仍然通过。我不仅回去改进我的“生产”代码，而且还回去改进我的测试代码。

## 选择一个测试框架

就像在。NET 社区，Ruby 社区也有很多可用的测试框架。我决定选一个我大多数哥们都用的: [RSpec](http://rspec.info/) 。我只学了最少的一点，只是为了能过得去，不太担心这是不是“红宝石之路”。只要我能实例化类，调用方法，并断言一些期望，生活就是美好的。所以我开始写这样的测试:

```
describe Calculator do

  it "should sum two numbers" do
    calculator = Calculator.new
    result = calculator.sum(1, 1)
    result.should == 2
  end

  it "should subtract two numbers" do
    calculator = Calculator.new
    result = calculator.subtract(5, 3)
    result.should == 2
  end
end
```

任何 C#开发人员都应该能够读懂上面的代码。通过编写这样的测试，我学会了足够多的 Ruby 来完成很多事情。

当运行测试时，我可以将一个开关传递给 RSpec，并告诉它将输出格式化为一个文档，如下所示:

```
Calculator
  should sum two numbers
  should subtract two numbers
```

如果我的任何一个例子(顺便说一下，这是每个测试在 RSpec 中的叫法)失败了，我会得到这样的输出:

```
Calculator
  should sum two numbers (FAILED - 1)
  should subtract two numbers
```

一旦我获得了一些动力，我就开始寻找改进我的测试代码的方法。我希望类似于我用 C#写的更好的测试，用 [SubSpec](http://haacked.com/archive/2008/08/24/introducing-subspec.aspx "SubSpec") ,“应该”扩展方法等等。稍微改进的测试开始看起来像这样:

```
describe Calculator do
  context "Given a calculator" do
    before(:each) do
      @calculator = Calculator.new
    end

    describe "when told to add two numbers" do
      it "should return the sum of the two numbers" do
        result = @calculator.sum(1, 1)
        result.should == 2
      end
    end

    describe "when told to subtract two numbers" do
      it "should return the difference of the two numbers" do
        result = @calculator.subtract(5, 3)
        result.should == 2
      end
    end

  end
end
```

有几件事需要注意:

*   使用*上下文*来提供我的规范的“给定”部分
*   使用 *before(:each)* 来设置我在每个例子(每个测试)之前的上下文

有了这样的结构，现在我的测试输出看起来像这样:

```
Calculator
  Given a calculator
    when told to add two numbers
      should return the sum of the two numbers
    when told to subtract two numbers
      should return the difference of the two numbers
```

比方说，我需要改变计算器的设计，让它做两件额外的事情:将输入存储到一个数组中，并存储最后计算的结果。我可以这样改写规格:

```
describe Calculator do

  context "Given a calculator" do

    before(:each) do
      @calculator = Calculator.new
    end

    describe "when told to add 1 + 1" do

      before(:each) do
        @calculator.sum(1, 1)
      end

      it "should have two input values" do
        @calculator.input_values.length.should == 2
      end

      it "should have last result of 2" do
        @calculator.last_result.should == 2
      end
    end

  end
end
```

这些规格的输出看起来仍然不错:

```
Calculator
  Given a calculator
    when told to add 1 + 1
      should have two input values
      should have last result of 2
```

在那一点上，我觉得我的测试和我在. NET 中的测试处于同一水平。然而，在编写了一段时间这样的测试之后，我开始了解一些具体的 RSpec 技巧(这些技巧得益于 Ruby 强大的功能和灵活性！).这样的技巧使我的测试代码更加紧凑，但仍然表达了意图，并且在运行规范时仍然得到良好的输出。最棒的是，我写的代码更少了！

```
describe Calculator do

  context "Given a calculator" do

    subject { Calculator.new }

    describe "when told to add 1 + 1" do

      before { subject.sum(1, 1) }

      it { should have(2).input_values }
      its(:last_result) { should == 2 }
    end

  end
end
```

注意事项:

1.  我没有实例化 Customer 并将其存储到(:each) 之前的一个*实例变量中，而是使用了 *subject* 块，该块存储从它返回到规范上的 *subject* 属性的任何内容；*
2.  我没有在(:each) 之前的*中调用被测试的方法(例如 subject.sum(1，1))，而是在{}* 之前使用了一个*，它做的是完全相同的事情，但是更短一些；*
3.  我没有向 *it* 块提供字符串，而是让 RSpec 根据块中的 Ruby 代码创建字符串(您很快就会看到结果)；
4.  *其*本义是指*主语*；
5.  我没有检查*输入值*数组的长度，而是使用了*have*匹配器。说到底，RSpec turns *应该有(2)。input_values*进*subject . input _ values . length . should = = 2*，不过我喜欢前者读起来好多了；
6.  为了检查挂在*主题*上的属性，我使用了 *its* 方法(例如，its(:last_result) { should == 2 }

确保并排比较示例的前两个版本。请注意，第二个示例中解释我测试的内容的字符串更少，它有更多的 Ruby 代码，但是那里的 Ruby 代码以这样一种方式读取，我们不需要为了理解它做什么而在心里解析代码。最后但同样重要的是，修改后的规范的输出看起来仍然不错:

```
Calculator
  Given a calculator
    when told to add 1 + 1
      should have 2 input_values
      last_result
        should == 2
```

那些东西都很棒，而且和我正在做的事情是一致的。NET(但是用*更好的*语法！).然而，我仍然只关注“对象”行为。用户并不真正关心一个“对象”的行为；他们关心“应用程序”的行为。

## 所以*那*就是 BDD 的全部…

我已经谈到了*“给定一些背景，当某些事情发生时，那么一些期望应该得到满足”*的想法。其中缺少的一个重要部分是对以下问题的回答:“满足期望的商业价值是什么？”。我意识到这样写用户故事:

为了*能产生商业价值的东西*，
作为*类型的人*，
我想*让应用程序为我做点什么*

作为一名开发人员，我真的不知道如何将它融入到我的工作流程中。我错过了“停止考虑对象，开始考虑特定功能背后的真正商业价值”的全部要点。

我不知道如何在？网竞技场。我确实知道一些关于它的倡议，但它们似乎没有得到太多的关注。

下一站:黄瓜。

## 可执行验收测试

在 RSpec 中花了几周时间编写测试之后，我学会了如何在[cumber](http://cukes.info/)中编写“特性”测试(或者“特性故事”)。Cucumber 是建立在 RSpec 之上的，所以学习它并不难，因为我已经知道了一些东西。一个专题故事大概是这样的(例子直接来自 Cucumber 的网站):

```
Feature: Addition
  In order to avoid silly mistakes
  As a math idiot
  I want to be told the sum of two numbers

  Scenario: Add two numbers
    Given I have entered 50 into the calculator
    And I have entered 70 into the calculator
    When I press add
    Then the result should be 120 on the screen
```

非常简单的英语。它有“为了……作为……我想…”的部分，它描述了特性的商业价值，它有“场景”用“给定-何时-然后”描述。等式的这一部分涵盖了用户，也就是你为之构建应用程序的人的需求。

【Ruby 或 RSpec 在中的位置？在实施这些情景“步骤”时:

```
Given /I have entered (.*) into the calculator/ do |n|
  calculator = Calculator.new
  calculator.push(n.to_i)
end
```

编写步骤是等式的另一部分，这一次包括我，开发人员，想要什么代码。如果我发现自己在一个步骤中有太多的代码，这是一个很好的迹象，表明我没有正确地设计我的对象。这是一个机会，让我更努力地思考这个步骤，想出一些更简单的东西。

此时，当我运行测试时，我得到了错误，因为对象和方法还没有被创建。该示例的代码可能如下所示:

```
class Calculator
  def push(n)
    @args ||= []
    @args << n
  end
end
```

在创建了类并实现了方法之后(只有我正在处理的当前步骤需要的那些方法)，那么这个步骤应该通过了，我可以继续下一个步骤了。我重复这个过程，直到特性中的所有步骤都通过，因此，特性就完成了。

## 由外向内

有时候，我的对象需要更彻底、更细致的测试。此时，我进入 RSpec 并在那里编写我的测试。换句话说，通过使用 Cucumber 创建的“特性故事”,我正在测试“应用程序”的行为，同时注意使用最少的对象 API 来实现该特性。另一方面，通过使用 RSpec 创建的规范，我正在测试我的“对象”的行为，同时密切关注我的对象为了执行它们的工作所需要的各种协作和依赖。

这种方法也被称为“由外向内”开发:我从应用程序的外部开始，在那里我与将要使用我的应用程序的人非常接近。然后，我进入我的对象的内部，启用我们从外部请求的特性。对我来说，这背离了我以前遵循的“首先考虑对象”的方法。我的应用程序的用户不关心对象！

## 不运行应用程序构建特征

过去，我已经构建了几个这样的应用程序:

1.  创建视图
2.  运行应用程序
3.  点击许多菜单和屏幕，直到我看到在步骤#1 中创建的视图
4.  进入代码并删除一个断点
5.  单击应用程序上的某个按钮
6.  一直等到命中该断点
7.  去调试器看看周围的东西
8.  重复这些步骤几次，最终我的特征完成了

那里浪费了很多时间。按照 Cucumber 和 RSpec 的这种由外向内的方法，有几次我甚至没有运行我的应用程序，直到我的特性完成(这意味着我运行了我的测试，它们都通过了)。一旦我的测试告诉我特性已经完成，我就运行应用程序，瞧，一切都正常了。是的，有时它可能不起作用，但这是例外，而不是规则(通常问题是由于我把事情搞砸了)。

## 构建和测试 REST API

就在最近，我在我的一个 Rails 应用程序中创建了一个 REST API。这个 API 提供了移动应用程序使用的 JSON。我用 Cucumber feature stories 描述了这个 API，它是我的 API 的文档，并且有一个很好的“副作用”,就是自动测试来确保 API 工作。

我花了几天时间专门做测试。一旦我的所有测试都通过了，我就部署了我的 API，我们的移动应用程序就可以很好地使用数据并向 API 发送数据了。

下面是这个 API 支持的一个特性的小例子:

```
Feature: User authentication

  In order to only allow authenticated users to use our applications
    As an API client
    I want to be able to authenticate a user based on his credentials

  Scenario: Authentication Failed

  Given invalid credentials
    When I send a request for user authentication
    Then the response should be "200"
     And I should receive message indicating authorization failure

  Scenario: Authorization Succeeded

  Given valid credentials
    When I send a request for user authentication
    Then the response should be "200"
     And I should receive message indicating authorization success
```

下面是如何实施这些步骤的示例:

```
Given /^invalid credentials$/ do
  @login = "INVALID_LOGIN"
  @password = "INVALID_PASSWORD"
end

Given /^valid credentials$/ do
  @user = Factory(:athlete_user)
  @login = @user.email
  @password = @user.password
end

When /^I send a request for user authentication$/ do
  post "api/v3/user_authentication.json?login=#{@login}&password=#{@password}"
end

Then /^I should receive message indicating authorization success$/ do

  success = {:user_authentication =>
               {:status => "success",
                :errorcode => nil,
                :errormessage => nil,
                :login => @login,
                :profile => 'athlete'}}

  last_response.body.should == success.to_json
end

Then /^I should receive message indicating authorization failure$/ do

  error = {:user_authentication =>
               {:status => "failed",
                :errorcode => "UNKNOWN_LOGIN",
                :errormessage => "Invalid credentials.",
                :login => nil,
                :profile => nil}}

  last_response.body.should == error.to_json
end
```

我花在写作上的时间是非常值得的。同样，它作为我的 API 的文档，同时测试 API 以确保它工作。

## 所有的 Ruby 开发者都这么做吗？

不要！！这不是所有 Ruby 开发者都做的事情。有些开发人员根本不喜欢 RSpec 和/或 Cucumber，而是使用其他测试框架。人们使用任何对他们有意义的东西。我在这里描述的是我*目前*认为对我*目前*从事的项目来说最好的流程和工具。这绝对不是一个放之四海而皆准的解决方案，这并不意味着我没有不断地寻找人们正在做或使用的不同东西。

## 我要为*的每件事*写测试吗？

我不为任何事情写测试。如果我的需求是清晰的，并且我和我的客户之间能够很好的理解，那么我会尽我最大的努力去写合适的测试。然而，有些情况下我的客户不确定他们需要什么。有稀疏的想法，但没有写下来。在这种情况下，我可能会在一个新的分支原型的东西，并显示给客户。为此，我不写测试。这将是浪费时间，因为这些测试很可能很快就会被扔掉。

一旦我向客户展示了“活的”原型，获得了一些反馈，并讨论了选项，我们就试图对他们真正需要的东西达成正式的相互理解。然后，我为它写专题报道。

## 资源

我用来了解我在这里讨论的主题的最好的资源是 RSpec Boook 和 T2 的黄瓜书。我一直听到关于[规范和示例](http://specificationbyexample.com/)的好消息，所以这在我的“必读”清单上。

## 你是做什么的？

我想知道你在测试方面的经验，SubSpec，RSpec，Cucumber，TDD，BDD…任何我们在这篇博文中提到的东西，真的。什么对你有效？什么没有？为什么？你尝试过什么样的体验？我经常听到人们说“我试过这个测试，但是它对我不起作用。”。这还不够。最好能知道*这个人到底是如何*尝试的，应用程序的类型、语言、客户端、上下文等等。我知道我尝试过的一些事情没有成功，但是我会和我的同行谈论我的经历，通常我们会找出失败的可能原因，这样我就可以纠正错误并再试一次。:)

## 分享这篇文章