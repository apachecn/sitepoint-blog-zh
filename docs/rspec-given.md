# Rspec-Given

> 原文：<https://www.sitepoint.com/rspec-given/>

## 测试是已知的

我玩过*很多*的测试框架。我们这些 ruby 爱好者永远对我们的测试着迷。无论是像尤塞恩博尔特或 T4[TSA]T5 这样的[测试，无论哪种方式，我们都在测试。这是一件非常好的事情。](http://www.confreaks.com/videos/641-gogaruco2011-fast-rails-tests)

我非常赞同让我的代码尽可能的可读和可理解。拿我来说，我读的代码比我写的多。我总是先看测试。集成和功能测试给了我 10，000 英尺的背景视图，单元测试告诉了我机械和边缘情况。作为这一类的开发人员，我努力使我的测试尽可能地表达给下一个关注我的代码故事的开发人员。囤积信息和成为代码专家的日子已经一去不复返了。把这个留给其他开发者吧。不要成为那个家伙。

最近我一直在摆弄 [rspec-given](https://github.com/jimweirich/rspec-given) 给 rspec 的扩展，不是别人，正是[吉姆·威里奇](http://twitter.com/jimweirich)(我很确定我不用告诉你他是谁)。我们都知道从 Rspec 获得的表达性语法糖，我们可能喜欢 Cucumber 特性的清晰性。rspec-given 将那些 given、When、Then 声明引入到您的测试中，同时还有几个额外的声明，这无疑让我非常高兴。

## 基础知识

将 rspec-given 添加到你的规格中是很简单的，只需将宝石添加到你的宝石文件中，并在`spec_helper.rb` a la: