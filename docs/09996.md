# 使用 Rails 3 引入测试驱动开发

> 原文：<https://www.sitepoint.com/introducing-test-driven-development-with-rails-3/>

继我之前的文章——[构建您的第一个 Rails 应用程序:模型](https://www.sitepoint.com/building-your-first-rails-application-models/)和[视图和控制器](https://www.sitepoint.com/building-your-first-rails-application-views-and-controllers/)——我将介绍一个简单的测试驱动方法，向我们的 URL shortener 应用程序添加一个新特性，Shorty。

为了测试这一过程，我们将使网站功能更像现实生活中的 URL 缩写——也就是说，我们将测试并实现一种方法来生成一个简单的短代码，代表我们正在缩短的 URL。

功能本身相对简单(在我们的例子中，我们将在字母数字表示之间来回转换 id ),但是这是一个介绍测试驱动实现方法的好机会。为了做到这一点，我们将使用 Rails 的集成测试工具，最后，我还将链接到一些更多的选项，供您在自己的时间里探索。

今天，我们将讨论模型部分，在下一篇文章中，我们将整合我们缩短的 URL 并编写一些控制器测试。

## 我们需要什么

要做到这一点，我们必须实现三件独立的事情:

*   一种将给定的存储 URL 转换为短代码的方法
*   一种将短代码转换为存储的 URL 的方法
*   一个新的，更短的路线来获取一个网址。

开始时，我们需要打开现有的测试文件。当我们在第一部分中生成 URL 模型时，Rails 还在`test/unit/url_test.rb`中为我们生成了一个存根测试。打开它看一看，您应该看到类似于:

```
require 'test_helper'

class UrlTest < ActiveSupport::TestCase
  # Replace this with your real tests.
  test "the truth" do
    assert true
  end
end
```

这是 Rails 3 中单元测试的一般结构——`test`方法让我们声明一个方法(我们也有`setup`和`teardown`来为我们的测试维护一个通用的环境),我们使用断言(例如上面代码中的`assert`方法调用)。Rails(以及 rails 集成的测试框架`Test::Unit`)附带了一些我们可以使用的现成断言——要查看列表，请参见从`assert_`开始的方法的[rails API 文档](http://api.rubyonrails.org/)和这个[旧的备忘单](http://topfunky.com/clients/rails/ruby_and_rails_assertions.pdf)中的一些标准测试单元断言。

## 写作测试

接下来，我们将添加一些测试存根——空测试，我们可以稍后填写。要做到这一点，我们需要用最基本的术语准确地计算出我们想要测试的内容。在 URL 测试类中，用以下内容替换现有的测试行:

```
test 'creating a url lets us fetch a short code'
test 'existing urls have short codes'
test 'converting a short code to an id'
test 'finding a url from a known short code'
test 'finding a url from a invalid short code raises an exception'
```

接下来，我们可以从命令行运行这些空测试，通过从我们的应用程序目录运行以下命令来验证它们是否失败:

```
rake test:units
```

因为我们除了写他们的名字之外什么也没做，我们应该得到四个失败。

现在，我们将逐个检查测试并编写它们。要开始，请一个接一个地填写它们，并在填写时更换测试存根:

```
test 'creating a url lets us fetch a short code' do
  my_url = Url.create(:url => 'http://google.com/')
  # The url should have a short code
  assert_present my_url.short_code
end

test 'existing urls have short codes' do
  my_url = Url.create(:url => 'http://google.com/')
  # Force a fetch from the datbase
  found_url = Url.find(my_url.id)
  assert_present found_url.short_code
  assert_equal my_url.short_code, found_url.short_code
end

test 'finding a url from a known short code' do
  my_url = Url.create(:url => 'http://google.com/')
  assert_equal my_url, Url.find_using_short_code!(my_url.short_code)
end

test 'finding a url from a invalid short code raises an exception' do
  assert_raises ActiveRecord::RecordNotFound do
    Url.find_using_short_code! 'non-existant-short-code'
  end
end
```

在每个测试中，我们测试预期模型行为的一些方面:

*   在我们的第一个测试中，我们检查一旦我们创建了一个 URL，它是否有一个简短的代码，方法是调用`short_code`方法并用它的值调用`assert_present`。
*   在第二个测试中，我们创建一个 URL，从数据库中强制重新加载它(以模拟在稍后的时间点获取它)，然后检查它是否也有一个短代码，更重要的是，所找到的对象是否有相同的短代码。
*   在第三个测试中，我们创建了一个 url，并测试当我们从数据库中获取它时(使用我们当前不存在的方法`find_using_short_code!`),它将返回相同的 URL。
*   在我们最后的测试中，我们检查了当我们给它一个无效的短代码时，它会像预期的那样引发一个异常。

切换回命令行，使用`rake test:units`再次运行我们的测试，我们仍然会看到 4 个失败。这很好——这意味着我们有测试，但还没有真正实现它们。

## 让我们的测试通过

现在，我们要让我们的测试通过。为了做到这一点，我们需要实现两个方法。首先，我们在`Url`类上的`short_code`实例方法，然后是`find_using_short_code!`类方法。

在`url.rb`中，我们将添加一个生成短代码的方法。目前，我们将只使用 id 的 36 进制值(例如，`10`将是`a`):

```
class Url < ActiveRecord::Base
  validates :url, :presence => true

  def short_code
    id.to_s 36
  end

end
```

重新运行我们的测试，我们将看到 4 个测试中有 2 个通过了。接下来，我们将实现一个方法，通过反向转换(从基数 36 的值中取一个数)从 id 中找到它:

```
class Url < ActiveRecord::Base
  validates :url, :presence => true

  def short_code
    id.to_s 36
  end

  def self.find_using_short_code!(short_code)
    find short_code.to_i(36)
  end

end
```

最后一次运行我们的测试，我们将看到它们现在都通过了——我们现在可以获得并生成短代码了。

## 后续步骤

在下一篇文章中，我们将讨论如何将我们的短代码集成到控制器中，以及如何测试它。

目前，看看你是否能自己编写一些测试——一个值得考虑的情况是当你没有模型的 id 时会发生什么？(例如尚未保存)。

热切的读者可能还想了解如何将 [RSpec](http://rspec.info/) 集成到他们的应用程序中，以获得编写测试的替代语法和方法。对于额外积分，您还可以更改我们在 id 和短代码之间来回转换的方式，例如，您可以包括小写字母和大写字母之间的差异，而不是使用 base 36 (0 到 9 和 a 到 b)。

## 分享这篇文章