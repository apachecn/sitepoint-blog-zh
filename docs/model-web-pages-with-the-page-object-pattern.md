# 用页面对象模式对网页建模

> 原文：<https://www.sitepoint.com/model-web-pages-with-the-page-object-pattern/>

![Page Objects](img/2d8725bb3ddad1e60ba33c20305144e9.png)

我们每天都与网页互动。在底层，点击各种 HTML 元素或使用输入设备在文本表单中输入文本。做那些事的更高层次的目的是什么？这是*试图完成某事*。完成谷歌搜索，提交联系表格，或者评价什么。[页面对象模式](http://blog.josephwilk.net/cucumber/page-object-pattern.html)是一种很好的面向对象的方式，可以在应用程序增长的同时完成更高级的事情，同时保持代码的整洁。

## 页面对象模式的基础

为什么要使用页面对象模式？两个字:代码重用。让我解释一下。

假设您正在开发一个应用程序，它将进入搜索引擎并使用特定的短语执行搜索。为此，您需要:

*   转到网页
*   找到可以输入文本和关键字的 HTML 元素(文本框)
*   找到搜索按钮并点击它

如果你熟悉 Watir-Webdriver/Selenium，你知道你可以用三行代码轻松完成。如果你不熟悉 Watir/Selenium，请阅读[我的 Watir 文章](https://www.sitepoint.com/watir-webdriver-control-browser/)，然后再回到这篇文章。我等着呢。

假设您需要在代码中多次执行这三个操作。你能做什么？当然，你可以把你的代码包装在一个方法中，这样就完成了，对吗？但是如果您的应用程序变得越来越复杂呢？假设在输入文本后，您想点击“我感觉很幸运”,而不是点击“搜索”按钮。或者，出于某种原因，首先单击搜索按钮两次，然后输入文本并再次单击搜索，在代码中重复几次。

您可能最终得到几个包含对某个 HTML 元素的几个引用的方法。当 HTML 元素发生变化时(由于页面重新设计，它不再属于 X 类，而是属于 Y 类)，您需要在代码的几个地方进行更改。现在，想象一个比搜索页面复杂十倍的东西，包含 20 个不同的元素，你可以用 7 种不同的方式组合它们。事情变得非常混乱，非常快。

Page 对象模式解决了这个问题，它帮助你*将一个单独的 web 页面建模为一个类，公开它的“服务”(搜索一个关键字，使用“我感觉很幸运”进行搜索)作为公共方法。基于 web 的电子邮件页面可能具有撰写电子邮件、阅读收件箱或将邮件保存为草稿的方法。你不会想知道它是如何在引擎盖下工作的。*

如果您正在处理一个简单的应用程序，并且使用页面对象模式可能有些过分，那么您可以使用 Watir/Selenium(在本文中，我将使用一个简单的案例来说明整个 gem 是如何工作的。)像 Page Object 这样的模式并不是为此而发明的。创建它们是为了*在代码变大时保持代码的可管理性。*

## 页面对象:一颗红宝石

页面对象 gem 是这种模式的一个非常简单的实现。要[安装它](https://rubygems.org/gems/page-object/)，只需键入`gem install page-object`就大功告成了。确保您还安装了 [Watir-Webdriver](https://rubygems.org/gems/watir-webdriver) ,因为我们将在本文中使用它。

之前，我们提到过 Page 对象模式允许您对 web 页面建模，将其“服务”公开为公共方法。当您想使用页面对象 gem 对一个特定的 web 页面建模时，您将经历以下 3 个步骤:

*   通过定义一个类并包含 PageObject 作为一个模块来创建页面。
*   **描述页面**,指定你将用来与它交互的 HTML 元素。
*   **使用页面。**通过将类初始化为对象并调用其方法。

## 创建搜索页面

让我们使用一个简单的搜索页面来实现这 3 个步骤。首先，我们将创建一个类，并在其中包含页面对象模块。尝试运行这段代码，看看是否一切正常。

```
require 'page-object'
class SearchPage
    include PageObject
  # some code to declare the HTML elements
end
```

包含`PageObject`模块的全部意义是什么？每个模块都提供方法，这也不例外。它还将包含一些元编程的魔力，我们将在后面探讨。

## 描述单个搜索页面

早在 2006 年，网络还很简单。这个[雅虎搜索页面](https://web.archive.org/web/20060204194204/http://search.yahoo.com/)也不例外。让我们试着给它一个名字并指出它的位置来描述它。

在开始使用网页上的页面对象之前，问问自己:**“我在这个页面上想做什么？**”，然后:**我需要哪些 HTML 元素来完成这些事情？**

我们从简单的搜索页面开始，只是用它来搜索特定的关键字。为此，我们需要定义一个文本框和搜索按钮。

[这是一个有用的参考](https://github.com/cheezy/page-object/wiki/Elements)，在这里你可以看到定义大多数元素的有用说明。先自己试着做。我的结果看起来像这样:

```
class SearchPage
  include PageObject

  text_field(:search_box, id: 'yschsp')
  button(:search_button, class: 'ygbt')
end
```

`text_field`和`button`只是简单的方法调用。在页面对象中定义 HTML 元素时(通过这两种方法)，您需要做两件主要的事情:

*   **将元素命名为**(我们将两个元素分别命名为`search_box`和`search_button`。)您可以通过将名称作为第一个参数的符号来传递。

*   **提供如何找到它的说明。**在页面上唯一标识该元素的是什么？通常，它只是一个类似于`id` ( `:yschsp`)或者 CSS `class` ( `ygbt`)的东西。但是如果不是这样呢？。假设页面上有另一个元素，它与按钮具有相同的类，但没有标识符。在这种情况下，请指定另一个属性，以明确要定位的内容。

下面是我会使用的代码:

```
button(:search_button, class: 'ygbt', value: 'Search the Web')
```

是的，您可以指定多个属性来帮助定位 HTML 元素。我们的按钮不需要这种修改，因为`ygbt`类没有被用来标识页面上的任何其他内容。

## 元素定义好了，现在做什么？

这就是奇迹发生的地方！在建模/定义了我们的文本字段和按钮之后，我们创建了五种不同的方法。对，五个！例如，键入以下内容后:

```
text_field(:search_box, id: 'yschsp')
```

Page 对象将创建三个独立的方法:

```
search_box              # returns the value in the text field
search_box=            # sets the value of the text field
search_box_element      # returns a reference to the actual HTML element
```

通过调用`text_field`和`button`来定义和建模 HTML 元素的全部目的就是创建上述方法。这些新创建的方法允许我们处理和操作实际的元素。

关于定义元素时创建什么方法的更多信息，请参考这个 [Wiki 页面](https://github.com/cheezy/page-object/wiki/Elements)。在下一节中，我们将对它们进行一些调整。

## 使用您的页面对象

页面对象需要 Watir/Selenium 浏览器实例才能运行。页面对象 gem 并不直接给浏览器指令，更多信息[参见本文](https://github.com/cheezy/page-object/wiki/Support-for-both-watir-webdriver-and-selenium-webdriver)。在这篇文章中，我将使用 Watir。让我们首先定义一个新的浏览器实例:

```
require 'watir-webdriver'
browser = Watir::Browser.new :chrome
```

我们忘了给我们的`SearchPage`的定义添加一个重要的东西。你能猜出是哪个吗？当然，我们已经定义了操作页面的所有元素。但是*到底是什么页面呢？*那个页面在哪里？要告诉 Page Object 可以找到页面的 URL，直接在`SearchPage`类中添加`page_url`方法，并带有一个指定实际 URL 的参数:

```
page_url('https://web.archive.org/web/20060204194204/http://search.yahoo.com/')
```

如果您不确定所有这些是如何组合在一起的，完整的代码如下。

我们现在已经具备了开始行动的所有必要条件！要开始使用 Page 对象类，只需创建它的一个实例，并指定要用作参数的浏览器实例:

```
search_page = SearchPage.new(browser)
```

如果不将页面对象类连接到浏览器，就无法使用它。它必须有所行动。

这是完整的代码。我添加了一些`sleep`方法，这样你可以清楚地看到发生了什么。在继续本文之前，看看您是否完全理解了它，并尝试在您的计算机上运行它:

```
require 'page-object'
require 'watir-webdriver'

class SearchPage
  include PageObject
  page_url('https://web.archive.org/web/20060204194204/http://search.yahoo.com/')

  text_field(:search_box, id: 'yschsp')
  button(:search_button, class: 'ygbt')
end

browser = Watir::Browser.new :chrome
search_page = SearchPage.new(browser) ; sleep 5
search_page.goto; sleep 5
search_page.search_box = 'I love SitePoint!'; sleep 5
search_page.search_button
```

因为我们在这里处理的是一个 Archive.org 页面，一旦你的脚本按下“搜索网页”按钮，你就不会得到实际的搜索结果。我鼓励你在读完这篇文章后，为原来的[https://search.yahoo.com/]修改它，并让它正常工作。

您已经知道了`search_box=`和`search_button`方法的来源，但是`goto`呢？`goto`也是生成方法。它是在我们调用`page_url`来指定 URL 时创建的。

`text_box`、`page_url`、`button`称为[访问器](http://www.rubydoc.info/github/cheezy/page-object/master/PageObject/Accessors)。一旦你在你的类中直接调用它们，page-object 就会自动创建相关的方法，你可以在创建一个对象时使用，正如我们上面看到的。

使用`page_url` [创建带你到页面的`goto`方法](http://www.rubydoc.info/github/cheezy/page-object/master/PageObject%2FAccessors%3Apage_url)。使用`text_area`创造四种[不同的方法](http://www.rubydoc.info/github/cheezy/page-object/master/PageObject%2FAccessors%3Atext_area)。一个按钮定义[创建三个](http://www.rubydoc.info/github/cheezy/page-object/master/PageObject%2FAccessors%3Abutton)。

但是，大多数时候，您会希望在创建页面对象实例(`search_page`)时立即`goto`页面。这很容易通过向构造函数添加另一个参数来实现:

```
search_page = SearchPage.new(browser, true)
```

如果第一个参数是浏览器实例，第二个参数是是否立即访问在`page_url`中指定的页面(通过在后台为您调用`goto`)。

## 为了共同的目的而聚集在一起

到目前为止，我正在尝试演示 Page 对象是如何工作的。事实上，上面的代码可以很容易地用 Watir 编写，并且长度大致相同。当我们为了一个共同的目的(查找一个关键字)以一种方法将所有东西组合在一起时，这种模式的魔力就显现出来了。让我们将这个方法添加到`SearchPage`类定义中:

```
def look_for(keyword)
    self.search_box = keyword; sleep 5
    self.search_button
end
```

然后做:

```
search_page = SearchPage.new(browser, true)
search_page.look_for('I love SitePoint')
```

这比 Watir 提供的清洁得多。关于为什么在`look_for`中包含`self`是个好主意的说明:page-object 生成的许多方法都是赋值类型的方法，比如`searchbox =`。当在像`look_for`这样的实例方法中调用这些类型的方法时，如果你不在它们前面加上`self`，Ruby 会把它们解释为局部变量而不是方法调用。例如，尝试预测这段代码将打印到控制台的内容:

```
def hello=(arg) # assignment method
  puts arg
end

def hi(arg) # ordinary method
  puts arg
end

def integrate
  hello = 'article' # it's supposed to call the assignment method
  hi('blog')
  self.hello = 'ruby'
end

integrate
```

输出将是“博客”和“红宝石”，“文章”将不会打印。

## 呃，哦…我们的搜索页面改变了

让我们把搜索页面快进到 2013 年，当时雅虎搜索进行了重大的重新设计。这里是[网址](https://web.archive.org/web/20130307071919/http://search.yahoo.com/)。将这个链接作为新的参数添加到`page_url`中，并尝试运行您的代码。你应该知道这个:

```
unable to locate element, using {:class=>"ygbt", :tag_name=>"button"} (Watir::Exception::UnknownObjectException)
```

有了新的设计，雅虎改变了它的搜索按钮，不再用`ygbt`作为类名来识别它。这个类现在叫做`sbb-sd`。要使我们的代码再次工作，只需更改您在类中定义按钮的行:

```
button(:search_button, class: 'sbb-sd')
```

我想让你们想象一下，如果不使用页面对象模式，这个过程会是怎样的。如果你有 5 个普通的方法，每个方法都使用这个按钮，并且每个方法都使用这个按钮达到特定的目的，那会怎么样？你必须改变你的代码 5 次！当然，你可以实现你自己的模式，但是[看看手动做起来有多复杂](https://github.com/watir/watir-webdriver/wiki/Page%20Objects)。此外，这是使用 Watir，这是使用 Ruby 的最简单的自动化工具，是语法最简洁的语言之一。想象一下使用 Selenium 和 Java 来实现自己的页面对象模式。

下面是完整的代码，包括了`page_url`和`look_for`方法的新参数:

```
require 'page-object'
require 'watir-webdriver'

class SearchPage
  include PageObject
  page_url('https://web.archive.org/web/20130307071919/http://search.yahoo.com/')

  def look_for(keyword)
    self.search_box = keyword; sleep 5
    self.search_button
  end

  text_field(:search_box, id: 'yschsp')
  button(:search_button, class: 'sbb-sd')
end

browser = Watir::Browser.new :chrome
search_page = SearchPage.new(browser, true)
search_page.look_for('I love SitePoint')
```

## 使用页面对象的提示

这里有一些有用的提示，在你使用这种宝石时可能会派上用场:

### 访问浏览器实例

您可以通过`@browser`或使用其 getter 属性`browser`从您的`PageObject`类访问浏览器实例，例如:

```
def look_for(keyword)
  self.search_box = keyword; sleep 5
  self.search_button
  puts browser.text
end
```

### 在没有浏览器实例的情况下导航浏览器

你有[很多方法](http://www.rubydoc.info/github/cheezy/page-object/PageObject)前进、后退、刷新、清理 cookies 等。使用浏览器实例。我在每个动作之间添加了 2 秒钟的停顿，这样你就可以更清楚地看到发生了什么:

```
def look_for(keyword)
  self.search_box = keyword
  self.search_button
  back; sleep 2
  forward; sleep 2
  clear_cookies; sleep 2; puts 'Cookies cleared!'
  puts current_url; sleep 2
  puts element_with_focus; sleep 2
  refresh; sleep 2
  save_screenshot('screenshot.jpeg'); sleep 2
end
```

### 处理警报

对象允许您跳过警告框，只获取它的值。以[这个页面](https://web.archive.org/web/20150618033134/http://javascripter.net/faq/alert.htm)为例，尝试点击“立即尝试”按钮。您应该得到“来自 JavaScript 的问候！”。现在，试试这段代码:

```
class AlertPage
  include PageObject
  page_url('https://web.archive.org/web/20150618033134/http://javascripter.net/faq/alert.htm')

  def get_alert_text
    message = alert do
      self.try_it_now
    end
    p message
  end

  button(:try_it_now, value: 'Try it now')
end

browser = Watir::Browser.new :chrome
alert_page = AlertPage.new(browser, true)
alert_page.get_alert_text
```

请注意，警告框根本没有出现！关于确认/提示的相同操作，请参见本文的[。](http://www.cheezyworld.com/2011/07/29/introducing-page-object-gem/)

Watir 有很好的处理警告框的方法& Ajax，我觉得它更方便。

### 初始化后

如果你想在创建你的对象时执行一些代码，不要使用`initialize`。`PageObject`使用这种方法，如果你试图覆盖它，东西就会坏掉。相反，使用`initialize_page`。尝试将这个方法添加到您的`SearchPage`类中，以了解我的意思:

```
def initialize_page
  puts 'I am being initialized!'
end
```

## 如何了解更多

以下是更多有用的资源，您可以从中了解页面对象:

*   [创业板的维基](https://github.com/cheezy/page-object/wiki/page-object)
*   作者的博客(评论也包含许多有用的材料)
*   本文:)希望你觉得有用！

下次需要在 Ruby 中使用 web 页面时，尝试一下 Page 对象模式。我认为这会节省你很多时间和烦恼。

## 分享这篇文章