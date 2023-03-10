# 面向初学者的网络抓取

> 原文：<https://www.sitepoint.com/web-scraping-for-beginners/>

随着电子商务的繁荣，近年来我成为了比价应用的粉丝。我在网上(甚至线下)的每一次购买都是对提供产品的网站进行彻底调查的结果。

我使用的一些应用程序包括 RedLaser、ShopSavvy 和 BuyHatke，它们在增加透明度和节省消费者时间方面做了很多工作。

你有没有想过这些应用程序是如何获得这些重要数据的？在大多数情况下，应用程序使用的过程是**网络抓取**。

## 网页抓取已定义

![hands on keyboard](img/fd67c99ce44c7a1eeef72d1dadf53fa8.png)

Web 抓取是从 web 上提取数据的过程。有了合适的工具，你能看到的任何东西都可以被提取出来。在这篇文章中，我们将重点讨论如何编写程序来自动化这个过程，并帮助你在相对较短的时间内收集大量的数据。除了我已经给出的例子，抓取还有很多用途，比如搜索引擎优化跟踪、工作跟踪、新闻分析，还有——我最喜欢的——社交媒体上的情绪分析！

### 一个警告

在你开始一次网络搜集的冒险之前，确保你知道所涉及的法律问题。许多网站在服务条款中明确禁止刮痧。例如，引用 [Medium](https://medium.com/policy/medium-terms-of-service-9db0094a1e0f) 的话，“如果按照我们的 robots.txt 文件的规定执行，则允许抓取服务，但禁止抓取服务。”不允许抓取的抓取网站实际上可能会让你被他们列入黑名单！就像任何其他工具一样，网络抓取可以用于复制其他网站的内容。刮痧也导致了许多诉讼。

## 设置代码

既然你知道我们必须小心行事，让我们开始刮刮吧。抓取可以在任何编程语言中完成，不久前我们为节点介绍过它。在这篇文章中，我们将使用 Python 语言的简单性和使过程变得容易的包的可用性。

### 底层流程是什么？

当你访问互联网上的一个网站时，你实际上是在下载 HTML 代码，这些代码由你的网络浏览器进行分析和显示。这段 HTML 代码包含了所有你可以看到的信息。因此，通过分析这段 HTML 代码可以获得所需的信息(如价格)。您可以使用正则表达式大海捞针，或者使用库来解析 HTML 并获得所需的数据。

在 Python 中，我们将使用一个名为[美汤](https://www.crummy.com/software/BeautifulSoup/)的模块来分析这些 HTML 数据。您可以通过运行以下命令，通过类似`pip`的安装程序安装该模块:

```
pip install beautifulsoup4 
```

或者，您可以从源代码构建它。安装步骤列在模块的[文档页](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#installing-beautiful-soup)上。

安装完成后，我们将大致遵循以下步骤:

*   向 URL 发送请求
*   收到回应
*   分析响应以找到所需的数据。

出于演示的目的，我们将使用我的博客`http://dada.theblogbowl.in/`。

前两步相当简单，可以按如下方式完成:

```
from urllib import urlopen

#Sending the http request
webpage = urlopen('http://my_website.com/').read() 
```

接下来，我们需要响应

```
from bs4 import BeautifulSoup
#making the soup! yummy ;)
soup = BeautifulSoup(webpage, "html5lib") 
```

注意，我们使用了`html5lib`作为解析器。你可以为 BeautifulSoup 安装一个不同的解析器，正如他们的文档中提到的。

## 解析 HTML

现在我们已经为 BeautifulSoup 提供了 HTML，让我们来看看几个命令。为了检查我们是否有正确的 HTML 标记，让我们验证页面的标题(在 Python 解释器上):

```
>>> soup.title
<title>Transcendental  Tech Talk</title>
>>> soup.title.text
u'Transcendental  Tech Talk'
>>> 
```

接下来，我们继续从页面中提取特定的元素。假设我想提取我的博客上的文章标题列表。为此，我需要分析 HTML 结构，这是通过 Chrome Inspector 完成的(右键单击一个项目并选择“Inspect Element”)。其他浏览器中也有类似的工具。

![using Chrome's inspector](img/e4bceba811d4c59cde2a4a8adb3ea018.png)

使用 Chrome 检查器检查页面的 HTML 结构

正如你所看到的，所有的标题都包含在`h3`标签下，有两个类别——`post-title`和`entry-title`。用类`post-title`搜索所有的`h3`元素应该会得到页面上的标题列表。我们使用 BeautifulSoup 的`find_all`函数，并使用`class_`参数来指定我们的类:

```
>>> titles = soup.find_all('h3', class_ = 'post-title') #Getting all titles
>>> titles[0].text
u'\nKolkata #BergerXP IndiBlogger meet, Marketing Insights, and some Blogging Tips\n'
>>> 
```

通过搜索类别为`post-title`的项目可以获得相同的结果:

```
>>> titles = soup.find_all(class_ = 'post-title') #Getting all items with class post-title
>>> titles[0].text
u'\nKolkata #BergerXP IndiBlogger meet, Marketing Insights, and some Blogging Tips\n'
>>> 
```

如果您对进一步探索这些项目的链接感兴趣，您可以运行以下内容:

```
>>> for title in titles:
...     # Each title is in the form of <h3 ...><a href=...>Post Title<a/></h3>
...     print title.find("a").get("href")
...
http://dada.theblogbowl.in/2015/09/kolkata-bergerxp-indiblogger-meet.html
http://dada.theblogbowl.in/2015/09/i-got-published.html
http://dada.theblogbowl.in/2014/12/how-to-use-requestput-or-requestdelete.html
http://dada.theblogbowl.in/2014/12/zico-isl-and-atk.html
...
>>> 
```

BeautifulSoup 中有许多内置方法可以在 HTML 中导航，其中一些如下所示:

```
>>> titles[0].contents
[u'\n', <a href="http://dada.theblogbowl.in/2015/09/kolkata-bergerxp-indiblogger-meet.html">Kolkata #BergerXP IndiBlogger meet, Marketing Insights, and some Blogging Tips</a>, u'\n']
>>> 
```

请注意，您也可以使用`children`属性，但是它充当的是[生成器](https://wiki.python.org/moin/Generators):

```
>>> titles[0].parent
<div class="post hentry uncustomized-post-template">\n<a name="6501973351448547458"></a>\n<h3 class="post-title entry-title">\n<a href="http://dada.theblogbowl.in/2015/09/kolkata-bergerxp-indiblogger-meet.html">Kolkata #BergerXP IndiBlogger ...
>>> 
```

您也可以使用正则表达式来搜索 CSS 类，如文档中的[所述。](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#searching-by-css-class)

## 使用 Mechanize 模拟登录

到目前为止，我们所做的基本上是下载一个页面并分析其内容。但是，web 开发人员可能会阻止通过非浏览器的请求，或者网站的一部分可能只有在登录后才能访问。那么，我们应该如何着手这个过程呢？

在第一种情况下，当我们向页面发送请求时，我们需要模拟一个浏览器。每个 HTTP 请求都有许多相关的头，包括访问者的浏览器、操作系统和屏幕大小等信息。我们可以操纵它，让它看起来像是浏览器在发送请求。

在第二种情况下，我们需要登录网站并使用 cookies 维护会话，以便访问受限区域。让我们看看如何在模拟浏览器的同时做到这一点。

我们将使用模块`cookielib`通过 cookies 来管理我们的会话。进一步，我们将使用 [`mechanize`](http://wwwsearch.sourceforge.net/mechanize/) ，可以通过`pip`这样的安装程序安装。

我们将通过这个页面登录[博客碗](http://theblogbowl.in/login/)，然后访问[我们的通知页面](http://theblogbowl.in/notifications/)。代码通过注释进行内联解释:

```
import mechanize
import cookielib

from urllib import urlopen
from bs4 import BeautifulSoup

# Cookie Jar
cj = cookielib.LWPCookieJar()

browser = mechanize.Browser()
browser.set_cookiejar(cj)
browser.set_handle_robots(False)
browser.set_handle_redirect(True)

# Solving issue #1 by emulating a browser by adding HTTP headers
browser.addheaders = [('User-agent', 'Mozilla/5.0 (X11; U; Linux i686; en-US; rv:1.9.0.1) Gecko/2008071615 Fedora/3.0.1-1.fc9 Firefox/3.0.1')]

# Open Login Page
browser.open("http://theblogbowl.in/login/")

# Select Login form (1st form of the page)
browser.select_form(nr = 0)
# Alternate syntax - browser.select_form(name = "form_name")

# The first <input> tag of the form is a CSRF token
# Setting the 2nd and 3rd tags to email and password
browser.form.set_value("email@example.com", nr=1)
browser.form.set_value("password", nr=2)

# Logging in
response = browser.submit()

# Opening new page after login
soup = BeautifulSoup(browser.open('http://theblogbowl.in/notifications/').read(), "html5lib") 
```

![the notifications page](img/178e026dfd5a01b758db0129685b736e.png)

通知页面的结构

```
# Print notifications
print soup.find(class_ = "search_results").text 
```

![results of logging in](img/c65c51d09a91495a68db0563c4c23c61.png)

登录通知页面的结果

## 最后的话

正如许多开发者会告诉你的，任何你能在网上看到的东西都可以被抓取。通过这篇文章，你知道了隐藏在登录背后的东西也很容易被提取出来。如果您的 IP 地址被阻止，您可以屏蔽您的 IP 地址(或使用不同的地址)。为了让它看起来像是一个人在访问数据，您也可以在您的请求之间保持一个时间间隔。

随着对数据需求的增加，网络抓取(有好有坏)在未来只会越来越多。因此，建议你理解这个过程，要么有效地使用它，要么从中拯救自己！

## 分享这篇文章