# Watir-Webdriver:控制浏览器

> 原文：<https://www.sitepoint.com/watir-webdriver-control-browser/>

![watir](img/8a80fdc636f8b59179279508e2182c18.png)

Watir-WebDriver (Watir 是 Ruby 中 Web 应用测试的缩写)是一个 Ruby gem，它允许你自动化你的浏览器(让它点击一个按钮，提交一个表单，在继续之前等待一些文本出现，等等)。在它的帮助下，可以模拟一个真实的用户，允许您自动化 web 应用程序的全栈测试。

Watir-WebDriver 语法非常简洁，并受到了其他语言中类似框架的启发(Java 的 [Watij](http://watij.com/) 和 C#的 [Watin](http://watin.org/) )。它也是一颗精心维护的宝石，拥有超过 68 个版本的[。](https://github.com/watir/watir-webdriver/releases)

要知道 [Watir](http://watir.com/) (经典)和 [Watir-WebDriver](http://watirwebdriver.com/) (都是独立的 Ruby 宝石)不是一回事！Watir 只支持 Internet Explorer，而 Watir-WebDriver 也支持 Chrome、Firefox 和 Safari。把 Watir-WebDriver 看成 Watir 2.0，或者看成 Watir (classic) + WebDriver +一些附加功能。WebDriver 由 Google 发起，旨在让浏览器自动化工具更接近模拟真实的用户行为。更好的是，所有主要的浏览器自动化框架都实现了它。

作为 Watir-Webdriver 主要组成部分的 Selenium 2.0，[也用这个简单的公式来描述自己](http://docs.seleniumhq.org/projects/webdriver/):Selenium 1.0+web driver = Selenium 2.0。

## 让我们(自动)填写这个 Ruby 调查吧！

你的工作是去[这个网页](http://nitrowriters.com/form/form.html)(你也可以从这里下载它[，解压它并用 form.html 文件的本地路径替换‘browser . goto’参数。)并用 Watir-WebDriver 自动填写。](https://www.dropbox.com/s/yntumkujyc665ds/form.zip)

要开始，首先用`gem install watir-webdriver`安装 Watir-webdriver。另外，确保你已经安装了 Firefox(我们将在这个例子中使用它)。

将以下内容保存到一个新的 **watir_script.rb** 文件中，并运行它:

```
require 'watir-webdriver'
browser = Watir::Browser.new :firefox # should open a new Firefox window
browser.goto 'http://nitrowriters.com/form/form.html' # or type the local path to your downloaded copy

browser.text_field(:id => 'my_text_field').set 'Yes!'
browser.textarea(:class => 'element textarea medium').set 'It was a long time ago, I do not remember'

browser.radio(:name => 'familiar_rails', :value => '1').click # yes, I'm very familiar
sleep 2 # puts the entire program to sleep for 2 seconds, so you can see the change
browser.radio(:name => 'familiar_rails', :value => '3').click # actually, just a bit...

browser.text_field(:name => 'favorite_1').set 'Yukihiro' # the creator of Ruby
browser.text_field(:id => 'favorite_2').set 'Matsumoto' # is my favorite Ruby person!

browser.checkbox(:index => 1).click # I like the TDD culture
browser.checkbox(:index => 2).click # And Matz!
sleep 2 # puts the entire program to sleep for 2 seconds, so you can see the change
browser.checkbox(:index => 1).click # Oh well, I like only Matz..

browser.select_list(:id => 'usage').select 'Less than a year'
browser.select_list(:id => 'usage').select_value '2' # Changed my mind

# Here I entered C:/watir.txt because I had such a file inside my C: directory. Please be sure
# to enter a valid path to a file, or your script will report 'No such file or directory' error
browser.file_field.set 'C:/watir.txt' # Change this path to any path to a local file on your computer
puts browser.p(:id => 'my_description').text
```

**我强烈建议您在成功运行该脚本之前不要继续这篇文章**。在看到第一个 Watir-WebDriver 脚本运行后，没有什么比想象如何自动化浏览器的可能性更令人兴奋的了。写完之后继续读。

## 真实用户如何与网页交互

再去打开那个网页。手动填写。把你想要的任何值放进去。完了？很好。现在，问问你自己:“我是如何与这个页面交互的？我做了什么？”如果你仔细观察自己，当你试图与这个或互联网上的任何其他网页交互时，你会做两件事:

a) **视觉识别**你要交互的页面部分(文本框、链接、点击网页标题等等)。

b) **在页面的那个部分执行一些动作**。大多数时候，你点击那个部分，但有时你也输入文本。这两件事是你 99%的时间都在做的:点击(用你的鼠标)和输入(用你的键盘)

没有 a)就没有 b)。如果你不能在视觉上识别出你想要点击或输入的元素，那么这是没有意义的。

之前，我们已经提到 Watir-WebDriver 的主要工作是模拟真实用户。我们知道真实的 web 用户如何与真实的 web 页面交互(通过观察我们自己手动填写表单)，所以让我们来看看上面的 Watir-WebDriver 脚本是如何工作的。

## 干净且可预测的语法

快速分析代码的前两行:

```
require 'watir-webdriver'
browser = Watir::Browser.new :firefox
```

只需运行前两行，就会启动一个新的浏览器窗口。第二行相当于真实用户打开 Firefox 窗口。

第 3 行开始向新启动的浏览器窗口发出*命令*。该脚本告诉它转到一个特定的网页，这相当于一个真实的网络用户在浏览器地址栏中键入一些内容，然后点击“回车”:

```
browser.goto 'http://nitrowriters.com/form/form.html'
```

事情应该从第 5 行开始变得清晰

```
browser.text_field(:id => 'my_text_field').set 'Yes!'
```

还记得关于真实用户如何与网页交互的 a)和 b)吗？这是 Watir-WebDriver 语法的亮点。一个真正的冲浪者会首先确定一个特定页面的“部分”来进行交互，然后在那个“部分”上执行一些动作。用计算机术语来说，我们如何告诉 Watir-WebDriver 那个部分在网页上的位置？一些浏览器自动化工具使用屏幕坐标(我们稍后会看到为什么这是非常不可靠的方法)。然而，Watir-WebDriver 使用 **HTML 元素**，依赖于一个基本的真理:

一个 HTML 网页，无论是 SitePoint 主页还是我们正在处理的表单页面，本质上都是 HTML 元素的集合。

任何用户，包括 Watir-WebDriver，所做的就是**与这些元素进行**交互。点击 *上的**按钮***(HTML 元素)，在的*文本字段*(也是 HTML 元素)中输入文本**，点击** *链接*(链接也是 HTML 元素)上的**，等等。无论你在页面上执行什么动作，**它都是在一些 HTML 元素上完成的，这些元素是我们称之为网页**的集合的一部分。Watir-WebDriver 的创建者知道这一点，决定创建一个相当直观的语法，允许你模拟真实用户在特定网页上做的几乎任何事情。语法结构是:**

```
[browser-instance].[html-element-tag-name](with specific attributes).[action]
```

您可以从第 5 行开始看到这一点:

```
browser.text_field(:id => 'my_text_field').set 'Yes!' 
# Dear Watir, please find the text field HTML element with the ID attribute of 'my_text_field' and type 'Yes!' into it
```

`browser`是在第 2 行创建的浏览器实例变量的名称。这个变量无论什么都可以被命名为任何东西:

```
happy_browser = Watir::Browser.new :firefox
```

只要您将重命名传播到每个后续行:

```
happy_browser.element_name(attributes).action
```

`text_field(:id => 'my_text_field')`是 Watir-WebDriver 用来识别文本字段的语法(*你可以看到 Watir-WebDriver 用于各种 HTML 元素的语法[这里](https://github.com/watir/watir/wiki/HTML-Elements-Supported-by-Watir)* )。在括号中，有键-值对，其中键是特定的属性，值是该属性的值。

您可以指定多个键值对:

```
browser.radio(:name => 'familiar_rails', :value => '1').click
```

最后，`set`是对 HTML 元素执行的**动作**。在 text_field 的情况下，`set`表示将文本设置为“是！”。

## 你的新的最好的朋友:右键单击检查

为了从根本上理解脚本是如何工作的，这是一个很好的练习来回顾它最初是如何被制作的思想过程。

现在，你应该明白前 4 行是做什么的了:打开一个新的浏览器窗口，准备接受我们的命令！

下一行是:

```
browser.text_field(:id => 'my_text_field').set 'Yes!'
```

`browser`只是代表浏览器的变量名称。第二个`.text_field`位呢？

我右键单击了我想要与之交互的元素(“你是 Ruby 的忠实粉丝吗？”)，然后单击 Inspect(根据浏览器的不同，这可能被称为“Inspect element”或简称为“Inspect”)。产生的检查窗口显示:

```
<input id="my_text_field" name="my_text_field" class="element text medium" maxlength="255" value="" type="text">
```

这

```
<input type="text"/>
```

可以在[中找到这个表](https://github.com/watir/watir/wiki/HTML-Elements-Supported-by-Watir)映射到 Watir-WebDriver 中的`text_field`方法。酷，但是如果页面上有不止一个`text_field`元素呢？如果你刚刚写了`browser.text_field.set 'Yes!'`，有 3 个文本元素，那么 Watir-WebDriver 会自动*选择第一个这样的元素*。通常，这不是我们想要的。我们需要一种方法来区分 HTML 标签，这样 Watir-WebDriver 就可以总是选择正确的标签。这就是 HTML 属性的用武之地。

如果您想要处理的元素有 ID 属性，那么您很幸运！根据 W3C，元素 ID 的值在 HTML 文档中必须是唯一的。在我们的例子中

```
<input type="text"/>
```

标签有一个 ID，所以我用了它。最后，我在该元素上调用`set`方法，该方法对其执行一些操作，比如向其中键入文本(我们将在后面详细讨论操作)。

你最后的工作是找到一种独特的方法来识别特定页面上的特定 HTML 元素。您不希望 Watir-WebDriver 选择错误的元素或者根本找不到元素(在这种情况下，您的整个程序会崩溃)。

让我们进入下一行:

```
browser.textarea(:class => 'element textarea medium').set 'It was a long time ago, I do not remember'
```

同样的概念。之间的区别

```
<input type="text"/>
```

和

```
<textarea></textarea>
```

就是后者支持多行。你可以在这一行中用`text_field`替换`textarea`，代码会工作，但是 Watir-WebDriver 会发出这个警告:`"Locating textareas with '#text_field' is deprecated. Please, use '#textarea' method instead.".`

## 单选按钮

```
browser.radio(:name => 'familiar_rails', :value => '1').click 
sleep 2
browser.radio(:name => 'familiar_rails', :value => '3').click
```

这里，我使用了两个属性来区分单选按钮。不使用`click`作为动作方法，也可以用`set`代替 click，听起来更人性化，但做的是同样的事情。您也可以使用`set?`方法，该方法将根据单选按钮是否被选中而返回 true 或 false:

```
browser.radio(:name => 'familiar_rails', :value => '1').click
browser.radio(:name => 'familiar_rails', :value => '1').set? #=> true
browser.radio(:name => 'familiar_rails', :value => '3').set # same as click 
browser.radio(:name => 'familiar_rails', :value => '1').set? #=> false
```

## 复选框

在这里，脚本与一些复选框进行交互:

```
browser.checkbox(:index => 1).click 
browser.checkbox(:index => 2).click
sleep 2 # puts the entire program to sleep for 2 seconds
browser.checkbox(:index => 1).click
```

比方说，如果页面上有 10 个复选框元素具有相同的类属性`my_checkbox`，执行类似于`browser.checkbox(:class => 'my_checkbox').click`的操作将选择它遇到的第一个。

如果我们想选择第二个呢？使用`:index`属性。

上面代码片段的第一行`browser.checkbox(:index => 1).click`将点击页面上的*第二个*复选框元素。属性接受整数，其中 0 是第一个元素。例如，上面代码片段中的第二行将单击它找到的第三个 checkbox 元素。

第四行是做什么的？它*取消选中*第二个元素(记住，在第一行我们点击了同一个元素，意味着我们“选中”了它)。在 Watir-WebDriver 中有一种更清晰的方法:

```
browser.checkbox(:index => 1).set 
browser.checkbox(:index => 2).set
sleep 2 # puts the entire program to sleep for 2 seconds
browser.checkbox(:index => 1).clear
```

像单选按钮一样，`set`与`click`做同样的事情。然而，`clear`检查复选框是否被“选中”，根据需要取消选中。

此外，您可以使用`set?`来查看复选框是否已经被选中。

## 选择列表和文件

让我们来看看代码的以下部分:

```
browser.select_list(:id => 'usage').select 'Less than a year'
browser.select_list(:id => 'usage').select_value '2'
```

`select_list`对应于 a

```
<select></select>
```

标签及其“动作”与单选按钮或复选框有点不同。每个`select`标签内都有`option`标签，标签内是列表中的选项。每个`option`标签通常有一个“值”属性和包含选项名称的文本。上面的代码列出了两种选择特定选项的方法:通过“value”属性或实际的选项名称。

在我们的表单中还有一个文件上传框。它的 HTML 是:

```
<input id="give_me_a_file" name="give_me_a_file" class="element file" type="file"/>
```

使用 Watir-WebDriver 操作上传框非常简单。你要做的就是:

```
browser.file_field.set 'C:/watir.txt'
```

**警告**:在`set`参数中输入一个有效的路径，否则你的程序会崩溃。

## 行动

如前所述，真实用户在网页上主要执行两个动作:点击和输入文本。

常识在这里很有帮助。例如，您不能在按钮元素上键入文本，但可以点按它。我们已经看到了如何将`set`和`clear`与复选框和单选按钮一起使用。如果你想简单的点击一个文本区域，你可以这样做:

```
browser.textarea(:class => 'element textarea medium').click
```

但是一个更人性化的方法是:

```
browser.textarea(:class => 'element textarea medium').focus
```

**快速提示:**当只使用一个属性来选择一个元素时，在分析其他人的代码时会经常看到这种情况:

```
browser.textarea(:class, 'element textarea medium').focus
```

然而，不要使用它，因为 Watir-WebDriver 的一个维护者[宣布](http://stackoverflow.com/questions/19681622/when-to-use-something-something-and-when-to-use-something-something/19682527#19682527)他们可能很快会移除它。

### 行动的类型

在 HTML 元素上执行操作不仅仅是与元素进行交互。在面向对象编程中，你有**设置器和**获取器。获取者**从一个对象中获取**某物，而**设置者**改变该对象。

想象每一个 HTML 元素都是一个**对象**，你可以在这个 HTML 元素上做的每一个动作都是一个 getter 或者 setter 方法。例如，您可以设置文本字段元素的文本。您还可以获得该对象的`class`属性值。

表单代码中的最后一行使用了一个“getter”方法`.text`:

```
puts browser.p(:id => 'my_description').text
```

与前面的例子不同，这里我们告诉浏览器获取 id 为“my description”的标签内的文本。"仅用于 Watir 演示目的."在这种情况下输出。

**你可以*获得*某个特定元素的任何属性。**。语法是:

```
[browser-instance].[HTML-element](:with-some => attributes).[the attribute name of the element] #=> the attribute value
```

从上面获取同一个`p`元素，这一次，获取 id 属性的值:

```
puts browser.p(:text => 'For Watir demonstration purposes only.').id #=> output: 'my_description'
```

到目前为止，我们已经探索了“getter”动作方法。至于“setter”动作，让我们用一点常识:你能在每一个 HTML 元素上做的通用“动作”是什么？

*点击*。可以做`browser.textarea.click`、`browser.div.click`，基本上就是`browser.[every-HTML-element](:with => every attribute).click`。

每个 HTML 元素也有自己特定的动作。[转到这里](http://rubydoc.info/gems/watir-webdriver/frames)并输入任何 HTML 元素名称，例如“radio”。点击它，在“Instance Method Summary”下，您应该看到它可以执行的特定方法或操作。在本例中，它们是`set`和`.set?`。`click`也位于列表的较低位置，从`Element`类继承而来，确认了在任何 HTML 元素上使用`click`的能力。

## 其他浏览器

与 Firefox 不同，如果你想将 Watir-WebDriver 与 [Chrome](http://watirwebdriver.com/chrome/) 、 [Internet Explorer](http://watirwebdriver.com/internet-explorer/) 或 [Safari](http://watirwebdriver.com/safari/) 一起使用，你必须下载这些浏览器的“WebDriver”版本(同时在你的机器上安装实际的浏览器)。浏览器的“WebDriver edition”是您需要放在操作系统加载路径中的一个文件。我把文件放在我的**【ruby-installation-folder】/bin**文件夹中，它已经在操作系统路径中了。如果您使用的是 Windows/Mac，我鼓励您使用 Chrome 和 IE/Safari 运行本文开头的表单示例。

如果你不明白这个“网络驱动”文件在大图中的位置，可以这样想:你的计算机上有各种各样的硬件。拿着你的显卡。你的显卡将毫无用处，除非你有办法通过安装驱动程序来“连接”它。把浏览器想象成显卡，把 web 驱动文件想象成允许 Watir-WebDriver 连接到实际的浏览器并“驱动”它的驱动文件。

## Vs 录制和回放工具

有许多“记录和回放”测试工具，你基本上可以“记录”你在网页上做的事情，并保存你的操作。稍后，这些工具将“回放”您所做的事情。这类软件的问题是它通常依赖于屏幕坐标和适当性，以及有限的脚本语言。如果页面的设计有一点小小的变化，整个回放将(很可能)停止工作，你不得不重新录制所有的内容。

Watir-WebDriver 不是这样工作的。它在后端使用 Selenium-WebDriver，就像 Selenium 一样，它不是屏幕坐标，而是 *HTML 元素*。在决定做任何事情之前，你必须告诉 Watir-WebDriver 你想要处理的 HTML 元素。

至于 Watir-WebDriver 与 Selenium WebDriver，您会注意到 Watir-WebDriver 比 Selenium 具有更面向对象和一致的语法。但是，为了完全掌握 Watir-WebDriver，您需要了解 Selenium。毕竟，Selenium-WebDriver 是 Watir-WebDriver 的后台驱动程序。如果你懂 Selenium，还可以进行一些高级配置。

## 只是开始！是不是很刺激？

一旦你理解了 Watir-WebDriver 的基础，继续学习就很容易了。一旦掌握了 Watir-WebDriver 背后的基本语法和哲学，像[与 AJAX 元素](http://watirwebdriver.com/waiting/)交互这样的事情应该不难理解。

如果你正在寻找更多的资料，有两本书对我很有帮助

*   [人行道](https://leanpub.com/watirways)(免费)
*   水的配方(9.99 美元)

还有由 Watir-WebDriver 的主要贡献者之一用 Ruby (免费)进行的 [Web 应用程序测试，包含了像设置每个浏览器的 WebDriver 版本等重要信息..你对 Watir-WebDriver 了解得越多，你就越想探索它优美的语法和优雅。](https://leanpub.com/watirbook)

## 分享这篇文章