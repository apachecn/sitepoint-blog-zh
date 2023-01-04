# Sinatra 尺寸

> 原文：<https://www.sitepoint.com/sinatra-sizes/>

![3sizes_of_sinatra](img/937ce34ee566bf96c02d19e9041ba10a.png)

辛纳特拉的一大优点是缺乏主见。它允许你以任何你喜欢的方式构建应用程序。但是这种自由经常令人困惑——构建应用程序的最佳方式是什么？当然，这个问题没有唯一正确的答案，但在这篇文章中，我将探讨构建 Sinatra 应用程序的三种不同风格。

Sinatra 通常用于开发小型应用程序和 API，但它可以轻松应对具有大量端点的复杂模块化应用程序。我根据应用程序的大小选择了三种不同风格的应用程序结构的名称:

*   小–所有代码都在一个文件中
*   中–在单独的文件和单独的视图文件夹中进行测试
*   大型——模块化风格的应用程序

为了演示这三种不同的结构，我将构建一个应用程序，在这三种风格中使用完全相同的底层代码。

## 应用程序–凯撒移位密码

应用程序将向名为`caesar_shift`的字符串类添加一个方法，该方法将使用[凯撒移位密码](http://en.wikipedia.org/wiki/Caesar_cipher)对字符串进行加密。这个应用程序需要两个路由处理程序——一个用表单输入字符串，另一个用明文和密文显示消息。

## 小的

[点击此处查看示例应用](https://github.com/sitepoint-examples/sinatra-sizes/tree/master/small)

一个小的应用程序实际上就是一个文件。所有的东西都在同一个文件中:代码、路由处理器(控制器)、助手、视图甚至测试。是的，没错，测试和应用程序在同一个文件中！对一些人来说，这可能看起来很疯狂，但我认为，你可以在一个文件中生成一个全栈应用程序，这是 Sinatra 的一个真理，但却经常被忽视。

这种结构具有以下优点:

*   你需要的一切都在同一个文件里。没有必要从你正在编辑的文件中添加一个帮助器方法或视图，只需将它们添加到同一个文件中！

*   当您只需要几个路线处理程序并且视图不太复杂时，它是完美的。

*   如果你正在测试一个想法，想快速得到一个概念证明，这也是有用的。

这是应用程序的第一部分，它简单地保存在一个名为 **main.rb** 的文件中(或者您喜欢的任何其他文件名):

```
require 'sinatra'

class String

  def caesar_shift(shift=1)
    letters = ("a".."z").to_a
    ciphertext = []
    self.downcase.scan( /./ ) do |char|
      if letters.include?(char)
        ciphertext << letters[(letters.index(char)+shift)%26]
      else
        ciphertext << char
      end
    end
    ciphertext.join.upcase
  end
end
```

在这段代码中，我们像往常一样需要 Sinatra，然后打开`String`类并添加一个`caesar_shift`方法。ne2w 方法使用`scan`来遍历字符串中的每个字符，并根据`shift`参数的值移动任何字母，然后将结果大写。任何不是字母的值，例如标点符号，都保持原样。

在这之后是助手，在一个街区:

```
helpers do
  def title
    @title || "Caesar Shift Cipher"
  end
end
```

这是一个生成页面标题的简单助手。可以使用实例变量`@title`在路由处理程序中设置标题，否则默认为“凯撒移位密码”。

接下来，是路线处理程序:

```
get '/' do
  erb :form
end

post '/' do
  @title = "Secret Message"
  @plaintext = params[:plaintext].chomp
  shift = params[:shift].to_i
  @ciphertext = @plaintext.caesar_shift(shift)
  erb :result
end
```

第一个路由处理程序只是使用`erb`辅助方法来显示“表单”视图。当通过 post 请求提交表单时，使用第二种方法。首先，使用`@title`实例变量设置页面的标题。然后，从表单中获取消息(存储在`params`散列中)，并将其存储在一个名为`@plaintext`的实例变量中。接下来，将表单中提交的`shift`参数作为参数应用于`caesar_shift`方法。获取`caeser_shift`的结果，并将其存储在一个名为`@ciphertext`的实例变量中。

接下来是测试。为了能够从同一个文件中运行测试，他们需要进入下面的`if`语句:

```
if ARGV.include? 'test'
  # tests go here
end
```

这是测试程序运行时是否有“test”参数。

如果提供了参数，则将环境设置为“测试”并停止运行 Sinatra 应用程序:

```
set :environment, :test
set :run, false
```

此外，还需要相关的测试宝石:

```
require 'test/unit'
require 'rack/test'
```

接下来是真正的测试。我写了两个测试`caesar_shift`方法的例子，另一个测试 POST 请求实际返回一个加密字符串:

```
class CaesarCipherTest < Test::Unit::TestCase
  include Rack::Test::Methods

  def app
    Sinatra::Application
  end

  def test_it_can_encrypt_strings
    assert_equal 'JGNNQ','hello'.caesar_shift(2)
  end

  def test_it_can_encrypt_with_negative_shifts
    assert_equal 'GDKKN','hello'.caesar_shift(-1)
  end

  def test_it_can_encrypt_from_a_URL
    post '/', params={plaintext: 'hello', shift: '2'}
    assert last_response.ok?
    assert last_response.body.include?('hello'.caesar_shift(2))
  end

end
```

要运行测试，只需在终端中输入以下内容:

```
ruby main.rb test
```

我在 Avdi 和 Dan 的博客文章中发现了这种将测试添加到同一个文件中的方法。

最后是风景。因为我们使用内嵌视图，我们需要标出文件的结尾:

```
__END__
```

每个视图的名称都以双&符号`@@`开头，代码用 ERB 语写成:

```
@@layout
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Caesar Cipher</title>
  </head>
  <body>
    <h1>
      <a href='/''>Caesar Cipher</a>
    </h1>
    <%= yield %>
  </body>
</html>

@@form
  <form action='/' method='POST'>
    <textarea rows=4 cols=50 name='plaintext'>Enter plaintext</textarea>
    <input type='number' name='shift' value=1 min=1 max=26>
    <input type='submit' value='Encrypt'>
  </form>

@@result
  <p>Plaintext:</p>
  <p><%= @plaintext %></p>
  <p>Ciphertext:</p>
  <p><%= @ciphertext %></p>
  <a href='/''>Write another message</a>
```

## 中型应用

[点击此处查看示例应用](https://github.com/sitepoint-examples/sinatra-sizes/tree/master/medium)

一个中等大小的 Sinatra 应用程序仍将使用经典风格，但也会添加一个 views 文件夹，并有一个单独的测试文件。对于大多数 Sinatra 项目来说，这是一个非常常见的设置，并且经常在在线教程中使用。

**main.rb** 文件包含助手、`caesar_shift`方法和路径处理程序。此外，测试已经被移动到他们自己的名为 **test.rb** 的文件中，而视图已经被移动到单独的文件中，并被放置到一个 **views** 文件夹中。

它具有以下优点:

*   它仍然使用古典风格，所以所有的路由处理代码和助手都在一个( **main.rb** )中。因此，将所有代码放在一个文件中的好处仍然存在。

*   视图保存在单独的文件夹中。一旦您开始拥有大量视图或一些非常大且复杂的视图，这是一个非常有用的结构。

*   这些测试被分离到它们自己的文件中。这使它们不妨碍主代码，但是把它们都放在一个地方。

中型结构中的所有代码都与小型结构中的代码相同。主要的区别在于它被组织到不同的位置，使得定位不同的代码片段变得更加容易。

## 大的

[点击此处查看示例应用](https://github.com/sitepoint-examples/sinatra-sizes/tree/master/large)

大型 Sinatra 应用程序使用 Sinatra 的模块化风格结构，看起来更像经典的 MVC 风格架构。在这个结构中，我们仍然为视图使用单独的文件夹，但是我们为测试和助手添加了文件夹。

另一个大的区别是将路由处理程序与实现 caesar 密码的`String`方法分开。凯撒密码应该可以作为独立的 Ruby 程序使用，不需要 Sinatra。出于这个原因，我们把它放在 **lib** 目录下一个名为 **caesar-cipher.rb** 的文件中。

路由处理程序被放在一个继承自**控制器. rb** 文件中`Sinatra::Base`的`Controller`类中:

```
require 'sinatra/base'
require_relative 'lib/caesar-cipher.rb'
require_relative 'helpers/helpers.rb'

class Controller < Sinatra::Base

  helpers TitleHelpers

  get '/' do
    erb :form
  end

  post '/' do
    @title = "Secret Message"
    @plaintext = params[:plaintext].chomp
    shift = params[:shift].to_i
    @ciphertext = @plaintext.caesar_shift(shift)
    erb :result
  end
end
```

注意，我们必须使用行`helpers TitleHelpers`在`Controller`类的顶部显式注册助手。这是因为我们已经将助手移动到他们自己文件夹中的一个单独模块中，该模块包含一个名为**助手. rb** 的文件:

```
module TitleHelpers
  def title
    @title || "Casaer Shift Cipher"
  end
end
```

目前所有的帮助器都在一个模块中，但是随着帮助器方法数量的增加，我们可以将它们分成不同的模块，并且只在控制器中注册我们需要的模块。

根据测试的是 web 应用程序还是 caesar 密码，测试也放在不同的文件中。在这种情况下，结果是一个用于`caesar_shift`方法测试的文件和另一个用于路由处理器测试的文件。为了保持干爽，我们创建了一个名为 **test_helper.rb** 的文件，其中包含了测试的所有设置代码:

```
ENV['RACK_ENV'] = 'test'

require 'minitest/autorun'
require 'rack/test'
require_relative '../controller'

include Rack::Test::Methods
```

然后，所有其他测试文件都需要这个文件。例如，下面是 **test_caesar-cipher.rb** 文件:

```
require_relative 'test_helper.rb'

class CaesarCipherTest < MiniTest::Unit::TestCase

  def test_it_can_encrypt_strings
    assert_equal 'JGNNQ','hello'.caesar_shift(2)
  end

  def test_it_can_encrypt_with_negative_shifts
    assert_equal 'GDKKN','hello'.caesar_shift(-1)
  end
end
```

要运行测试，请使用以下代码:

```
$ ruby test/test_website.rb
$ ruby test/test_caesar-cipher.rb
```

这种风格的优点是:

*   代码是模块化的，更容易重用或独立开发。
*   关注点有明确的分离。所有的路由处理程序都保存在控制器类中，应用程序代码保存在 **lib** 目录中。
*   测试是基于它们所测试的内容来分离的，这使得进行更有针对性的测试变得更加容易。
*   助手保存在模块中，这使得创建可以独立使用的不同类型的助手模块变得容易。

## 特大号

如果一个应用程序变得非常大，那么它甚至会超出大型结构的承受能力。下一步是将应用程序分解成不同的模块，或许创建一个其他类可以继承的`Controller`类。一些助手也可以变成辛纳屈的延伸。这将创建一个类似于我在这里描述的构建的 [MVC 框架的结构。](https://www.sitepoint.com/build-a-sinatra-mvc-framework/)

许多人会觉得 Rails 更适合这种类型的应用程序，但是 Sinatra 完全有能力处理如此复杂的应用程序。

## 摘要

这些中哪一个是最好的？好消息是你不必非选其一。事实上，所有这些的好处是，你可以很容易地以渐进的方式从一个尺寸增加到下一个尺寸。有一天，你可能有了一个好主意，用 SMALL 风格把一些代码放在同一个文件中，看看是否可行。然后，随着路由处理程序、视图和测试的数量开始增加，将它们分解成单独的文件，并使用中等风格。过一段时间后，随着项目变得越来越复杂，将项目的不同部分分成它们自己的离散类，并开始将其组织成一个大的模块化结构。

这种工作方式的美妙之处就在于它是多么的有机——应用程序结构可以随着应用程序的发展而改变和调整，以适应应用程序的规模。如果您已经从头开始构建了应用程序，从小的开始，您将完全理解当应用程序发展到中等规模，然后发展到大型时，所有的东西是如何组合在一起的。

## 这是所有的乡亲

在这篇文章中，我展示了三种不同大小的应用程序结构，它们可以使用 Sinatra 生成，希望能够展示它的灵活性。你以为这三个尺码就涵盖一切了吗？

哪个“尺寸”最接近你的 Sinatra 应用程序？Sinatra 真的可以用来构建 XL 大小的应用程序吗？你还有什么要补充吗？像往常一样，在下面留下你的评论。

## 分享这篇文章