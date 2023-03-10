# Web 上的 Python:为什么像 Django 这样的框架如此热门

> 原文：<https://www.sitepoint.com/python-on-the-web/>

Python 由吉多·范·罗苏姆在 20 世纪 90 年代早期创建，这些年来越来越受欢迎。2016 年，Python 是继 Java、C 和 C++之后第[第 4 受欢迎的语言](http://www.tiobe.com/tiobe_index)。Python 是一种通用编程语言，它可以用于各种领域。引用《树屋》中[肯尼斯·洛夫的话](http://blog.teamtreehouse.com/what-is-python)

> 当我需要构建一个 web 应用程序时，我会使用 Python。当我需要在系统上自动化一些小任务时，我会使用 Python。当我想找到图像中最常见的颜色时，我会使用 Python。

Python 是编写测试和监控脚本的流行选择。Python 还被用于[游戏开发](http://stackoverflow.com/questions/5224759/is-python-good-for-making-games?answertab=votes#tab-top)，它与其他语言集成的能力使它在这个过程中非常有价值。Python 如此受欢迎，以至于乔治·卢卡斯的工业光魔(原《星球大战》三部曲中负责特效)也用它来[管理其复杂的制作过程。](https://www.python.org/about/success/ilm/)

与 PHP 不同，它不是为 web 而构建的，也没有集成到 Python 中的核心 web 功能。因此，我们必须使用 web 框架来用 Python 开发 web 应用程序。自从 Django 这样的流行框架兴起以来，Web 开发人员已经开始在 web 上使用它了。

如今，是什么让 Python 成为越来越多的开发人员的首选语言？我们将在本帖中尝试寻找这个问题的答案。

## 为什么要用 Python？

### 面向初学者的 Python

Python 流行的主要原因是代码的优雅——特别是简洁和可读性。例如，让我们看看 Python 和 Java 在反转数字方面的对比:

![Java vs Python](img/06afecf462453877425cd934f06e61f8.png)

Java(左)和 Python 2.7.x(右)中反转数字的程序

Python 提供了较短的学习曲线，非常适合初学者学习。除此之外，如果您的项目是基于 Python 的，而新开发人员不熟悉它，那么转换会更容易。

正如 Quora 的联合创始人 Adam D'Angelo 在谈到 Quora 的发展选择 Python 时所说的

> 到目前为止，我们对这个选择非常满意……所有过去使用其他语言的早期员工都很乐意过渡到 Python，尤其是那些来自 PHP 的员工。

事实上，网络漫画家 [xkcd](https://xkcd.com/353/) 创作了一幅漫画，讲述用 Python 做事情是多么容易！

![Python webcomic by xkcd. Source: xkcd.com](img/bcc0b85f5cedb72a4c15ae8efbe17f63.png)

此外，Python 有易于使用的调试工具。虽然有几种调试器和 IDE 工具，但默认的是 [pdb](https://docs.python.org/2/library/pdb.html) ，这是一种交互式调试工具，允许开发人员中途停止程序的执行，并评估环境以更好地了解运行时错误。

### 远程服务器上的 Python

Python 中的包(或模块)管理也非常容易。使用像 pip 或 easy_install 这样的软件包安装程序，它可以用来安装和删除软件包。

Python 也很便携！将开发环境转移到远程机器上的便利性值得称赞。您只需要导出包，并使用两个命令将其安装在远程机器上的虚拟环境中。

Python 是 web 应用程序的好选择的另一个原因是它能够运行不嵌入 web 服务器的脚本(不像运行 PHP 脚本)。)脚本作为单独的进程运行。

## 使用 Python Web 框架进行 Web 开发

Python 的 web 功能并没有内置到它的核心特性中，因此，我们需要模块的帮助来实现 Python 中的 web 开发。如果你想开发非常基本的应用程序——比如检查服务器是否运行的页面——你可以使用 cgi 模块。

随着需求的增加，人们需要转移到像 [Django](https://www.djangoproject.com/) 或 [Flask](http://flask.pocoo.org/) 这样的 Python 框架来开发复杂的基于 web 的应用程序。

出于生产目的，这些框架中的大部分需要与 web 服务器结合使用，如 [Apache](http://www.apache.org/) ，这是一个小瓶颈。与 PHP 相比，如果你人生中第一次做远程 web 服务器，那么在远程 web 服务器上轻松部署是有点困难的(尽管[我们已经讨论过这个](https://www.sitepoint.com/deploying-a-django-app-with-mod-wsgi-on-ubuntu-14-04/))。然而，你投入的时间一定会有回报。

像 [Instagram](https://www.instagram.com) 、 [Reddit](https://www.reddit.com/) 、 [Quora](https://www.quora.com/) 和 [Disqus](https://www.disqus.com/) 这样的热门服务都是用 Python 开发的。

## django——最流行的 Python Web 框架

一个问题自然产生了:为什么使用 Python 进行 web 开发？我的回答是，首先不要认为它仅仅是关于使用“Python”进行 web 开发。正是像 Django 和 Flask 这样的框架使得 Python 中的 web 开发如此丰富和强大。例如，与其他 web 框架相比，Django 在以下几个方面做得非常好:

*   **轻松学习曲线**。和 Python 一样，Django 也很好学。你只需要浏览一下 Django 网站上的这个[循序渐进教程](https://docs.djangoproject.com/en/1.9/intro/tutorial01/)，它很好地解释了 Django 的基本特征。另一方面，学习 Ruby on Rails 可能需要更长的时间。
*   **自动创建 SQL 表**。一旦您指定了结构，Django 就会为您完成这项工作。(注意:SQLAlchemy 也这么做，如果你感兴趣的话，在黑客新闻上有一个[关于这个的激烈辩论。)](https://news.ycombinator.com/item?id=3525544)
*   **生成表格**。一旦您在 Django 中创建了一个表单类并将其链接到一个模型，Django 中的表单生成器就会负责呈现表单、数据验证和保存数据。
*   **管理界面**。就像 SQL 表一样，一旦决定了结构，Django 就会创建一个允许您管理数据库的管理界面。(就跟 Django 内置 PhpMyAdmin 一样。)
*   **Django Shell** 。Django 项目环境中的 Python shell 就是 Django shell 所提供的。这个特性在调试中很有用，而调试在 PHP 中相对更难。

如果这些还不够，Python 中还有前沿的开发。Python 中两个令人兴奋的服务是 [Tornado](http://www.tornadoweb.org/en/stable/) (用于异步任务)和 [Celery](http://www.celeryproject.org/) (用于作业调度)。如果它们能满足你的需求，你可能想看看它们。

## Django 虚拟主机

开始使用 Django 的最大障碍是 web 主机的可用性。显然，由于 PHP 的流行，主机数量很高，主机相对便宜(但不一定物有所值，因为便宜的主机经常被分享)。然而，也有像 AWS 和 Heroku 这样的主机允许你免费托管 Django 应用程序。对于价格适中的共享主机，你可以选择像[网络派系](https://www.webfaction.com/)这样的主机。

如今最受欢迎的云托管服务，如 [AWS](https://aws.amazon.com/) 、[数字海洋](https://www.digitalocean.com/)和[微软 Azure](https://azure.microsoft.com/en-in/) 提供对服务器的根访问，这允许你手动设置你的环境。数字海洋，其实已经想出了[一键 Django 部署](https://www.digitalocean.com/features/one-click-apps/django/)。Django 主机可能不像 PHP 主机那样受欢迎，但是在不同的价格水平上有可靠的选择。

## 最后的想法

这篇文章的目的不是告诉你 Python 是所有问题的解决方案！这是为了突出 Python 及其 web 框架的参数，这些参数使 web 开发人员的生活更加轻松。

你以前试过 Python 和它的 web 框架吗？请在下面的评论中告诉我们。

## 分享这篇文章