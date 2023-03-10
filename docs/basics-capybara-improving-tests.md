# 水豚的基本知识和提高你的测试

> 原文：<https://www.sitepoint.com/basics-capybara-improving-tests/>

![Capybara in Love](img/ed02866b28b76e1986107b57ab94784f.png)

Capybara 是一个基于网络的自动化框架，用于创建模拟用户如何与您的应用程序交互的功能测试。今天，有许多基于网络的自动化工具的替代品，如 Selenium、Watir、Capybara 等。所有这些工具的目的都是一样的，但是有些细微的差别使它们更适合或不太适合。

开发人员的主要目标是能够进行模块化、易于编写和维护的测试。在敏捷/TDD 环境中尤其如此，在那里编写测试是第二天性。这些测试有望对代码质量给出良好而快速的反馈。随着时间的推移，测试的数量会增加，维护测试会是一场真正的噩梦，尤其是当测试不够模块化和简单的时候。

在本教程中，我将描述 Capybara 的一些关键特性，并解释为什么它会成为您开发基于 web 的自动化测试的首选工具。

## 水豚的解剖学

就像 Watir 一样，Capybara 是一个库/gem，构建用于基于 web 的底层驱动程序之上。它提供了一种用户友好的 DSL(领域特定语言),用于描述底层 web 驱动程序执行的操作。

当使用 DSL(和底层 web 驱动程序)加载页面时，Capybara 将尝试在 DOM(文档对象模型)中定位相关元素并执行操作，如点击按钮、链接等。

以下是水豚支持的一些网络驱动程序:

*   **rack::test** :
    默认情况下，与 rack::test driver 一起工作。这个驱动程序比其他驱动程序快得多，但是它缺乏 JavaScript 支持，并且不能访问测试应用程序之外的 HTTP 资源(Rails 应用程序、Sinatra 应用程序)。

*   **selenium-webdriver**:
    Capybara 支持 selenium-web driver，主要用于基于 web 的自动化框架。它支持 JavaScript，可以访问应用程序外部的 HTTP 资源，还可以设置为在 headless 模式下进行测试，这对于 CI 场景尤其有用。要设置该驱动程序的用法，您需要添加:
    `Capybara.default_driver = :selenium`

*   **capybara-webkit** :
    对于 JavaScript 支持的真正无头测试，我们可以使用 capybara-webkit 驱动程序(gem)。它使用 QtWebKit，比 selenium 快得多，因为它不需要加载整个浏览器。要设置该驱动程序的用法，需要添加:
    T0

## 装置

Capybara 支持 Ruby 版本> = 1.9，可以使用两种模式:UI 和 headless。

在 UI 模式下，将以下内容添加到 **Gemfile** 中:

```
gem install capybara
gem install #{web_driver_on_which_capybara_runs}
```

对于无头模式:

```
gem install capybara
gem install #{web_driver_on_which_capybara_runs}
apt-get install firefox
apt-get install xvfb
gem install selenium-webdriver # if using selenium-webdriver
gem install capybara-webkit # if using capybara-webkit
apt-get install libqtwebkit-dev # if using capybara-webkit
```

## 基本 DSL

水豚有一个非常直观的 DSL，用来表达将要执行的动作。这是使用的基本命令列表:

```
visit('page_url') # navigate to page
click_link('id_of_link') # click link by id
click_link('link_text') # click link by link text
click_button('button_name') # fill text field
fill_in('First Name', :with => 'John') # choose radio button
choose('radio_button') # choose radio button
check('checkbox') # check in checkbox
uncheck('checkbox') # uncheck in checkbox
select('option', :from=>'select_box') # select from dropdown
attach_file('image', 'path_to_image') # upload file
```

使用 DSL，Capybara 在试图查找元素时会在 DOM 中搜索以下属性:

*   文本字段(`fill_in` ): id、名称、相关标签元素
*   单击链接/按钮(`click_link` / `click_button` ): id、标题、标签中的文本、值
*   复选框/单选按钮/下拉列表(`check` / `uncheck` / `choose` / `select` ): id、名称、相关标签元素
*   上传文件(`attach_file` ): id，名称

## 高级 DSL

对于基本 DSL 无法提供帮助的场景，我们使用 xpath 和 CSS 选择器(默认情况下将使用 CSS 选择器)。这是很常见的，因为现代 web 应用程序通常有很多 JavaScript 代码来生成 HTML 元素，这些元素的属性已经生成了值(比如随机 id 等)。)

要查找特定元素并单击它，我们可以使用:

```
find('xpath/css').click
```

要从特定元素获取文本，请使用:

```
find('xpath/css').text
```

要使用 xpath 选择器，只需更改以下配置值:

```
Capybara.default_selector = :xpath
```

如有必要，可以指定选择器类型:

```
find(:xpath, 'actual_xpath')
```

## 匹配项

当试图使用 DSL 或 xpath/CSS 选择器查找一个元素时，通常会有两个或更多的匹配，这将导致 Capybara 失败，并出现**模糊匹配**错误。此外，水豚可以执行部分匹配，导致意想不到的结果。因此，了解水豚支持的匹配策略非常重要:

*   **:一个**–当找到多个匹配时引发错误
*   **:第一个**——简单的挑选第一个匹配——**不要用这个！**
*   **:prefer _ exact**–查找所有匹配的元素，但将只返回一个完全匹配的元素，而放弃其他匹配
*   **:智能**——取决于**水豚的值，精确**。如果设置为**真**，它将表现为**:一个**。否则，它将首先搜索精确匹配。如果找到多个匹配项，则会引发一个不明确的异常。如果没有找到，它将搜索不精确的匹配，并在找到多个不精确的匹配时再次引发模糊异常。

## 辖域

为了避免意外的行为(比如不明确的匹配)，一个好的做法是确定 DOM 中将要执行某些操作的部分的范围:

```
within(:xpath, 'actual_xpath') do
  fill_in 'Name', :with => 'John'
end
```

Capybara 将首先使用 xpath 选择器搜索 DOM。然后，在 DOM 的这一部分中，它将对文本字段进行额外的搜索。

## 自定义选择器

也可以定义自己的自定义选择器。当我们想要制作可以在许多脚本中重用的模块化选择器时，这非常有用。这里有一个例子:

```
Capybara.add_selector(:my_selector) do
  xpath { "actual_xpath" }
end
find(:my_selector)
```

我们甚至可以将参数传递给选择器(就像它们是方法一样):

```
Capybara.add_selector(:my_selector) do
  xpath { |arg| "//xpath/#{arg}" }
end
find(:my_selector, arg)
```

或者将自定义选择器与作用域结合起来:

```
Capybara.add_selector(:my_selector_area) do
  xpath { "actual_xpath" }
end

within(:my_selector_area) do
  fill_in 'Name', :with => 'John'
  fill_in 'Email', :with => 'john@doe.com'
end
```

以下是关于水豚 DSL 使用、自定义选择器以及范围和匹配的一些最佳实践:

*   对其执行操作的 DOM 的范围部分。
*   在这个范围内，尽可能选择水豚 DSL。
*   如果不可能，在范围内使用相对 xpath/CSS 选择器。
*   xpath/CSS 选择器在范围内应该尽可能浅。

## JavaScript 和处理异步调用

Capybara 有一个内部机制来处理页面各部分的异步加载。一个很好的例子是单击页面的某个部分，导致 JavaScript 执行，在页面上创建一个新元素。

```
click_link('foo')
click_link('bar')
```

在这里，假设点击链接“foo”触发了一个异步过程，就像一个 Ajax 请求，添加链接“bar”。很可能第二个语句会失败，因为链接还不存在。

然而，Capybara 有一个处理这些情况的机制，在抛出错误之前，它会在短时间内重试查找元素。默认时间为 2 秒，可通过属性进行更改:

```
Capybara.default_wait_time
```

处理这些案件没有灵丹妙药。一方面，对测试脚本的创建者隐藏等待机制的复杂性是一个很好的实践，这样语法更清晰，更容易理解。另一方面，有时候对等待机制有更多的控制并自己处理会更好。

无论如何，这种等待问题通常是脚本中意外失败的原因。这可能是一个很好的指标，表明实现存在性能或其他问题。

Capybara 还通过以下方式支持 JavaScript 的执行:

```
page.execute_script()
```

JavaScript 代码可以像这样传递:

```
page.execute_script("$('#area button.primary').click()")
```

## 使用水豚和 RSpec

水豚自然适合 RSpec，不需要特殊整合。

```
describe "Create place scenario" do
  context "Go to home page" do
    it "opens homepage" do
      visit(get_homepage)
    end
  end
  context "Click on create object link" do
    it "opens create new object form" do
      find(:homepage_navigation_create_object).click
    end
  end
end
```

到目前为止，我们已经看到了如何使用 DSL 与页面元素进行交互，但是有一个警告:对于每个新的上下文，都会实例化一个新的 Capybara 会话，这可能不是我们想要的。
**水豚::Session.new:驱动**帮助管理这个问题。例如:

```
session = Capybara::Session.new :selenium # instantiate new session object
session.visit() # use it to call DSL methods
```

该会话表示单个用户与应用程序的交互，这正是我们想要的。

**良好实践:**在 **spec_helper.rb** 中定义一个方法，该方法将返回 session 对象，并在 RSpec 上下文(`session.visit`、`session.find`等)中调用所有 Capybara 方法时使用该 session 对象。)

## 排除故障

水豚有几种调试方法，比如:

```
save_and_open_page # saves current snapshot of page
print page.html # retrieve current state of DOM
save_sceenshot('screenshot.png') # save screenshot
```

还有另一种方法，我认为对调试更有用:窥探和**窥探调试器** gems。

这些 gem 将允许您在实际的调试器中检查脚本执行，允许您设置断点、进入下一步、单步执行、继续等。要使用它们，请执行以下操作:

*   `gem install pry-debugger`(也自动安装撬)
*   在你的测试脚本或规范助手中
*   在脚本中添加`binding.pry`来设置断点，然后执行测试脚本。执行将在断点处暂停。此时，根据需要使用`next`(转到下一行)、`step`(进入定义)、`continue`(继续执行)进行调试。

关于这些宝石的更多信息，请查看:
* [撬](https://github.com/pry/pry)
* [撬调试器](https://github.com/nixme/pry-debugger)

## 包扎

我已经简要介绍了水豚的基本知识，以及如何定制它来满足您的需求。测试是 Ruby 社区的一种方式，水豚是首选工具之一。好好考，朋友们。

## 分享这篇文章