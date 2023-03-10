# Savant 模板引擎

> 原文：<https://www.sitepoint.com/savant-template-engine/>

当我们(我和我的商业伙伴)开始开发我们的开源程序的第三个版本(我们的第一个“商业”产品)时，我们必须决定如何处理模板以允许用户定制他们的网站。当时，我们可以选择使用 [Smarty](http://smarty.php.net/) (或者找到一个不同的引擎)或者创建我们自己的模板引擎来处理它。我们看了一下 Smarty，认为它对于我们简单的下载管理应用程序来说太复杂了。因此，我们继续前进，创造了我们自己的系统。

现在回头看看我们的引擎，它做了它的工作，但它是有限的。我们添加了自己非常基本的控制结构语法，并内置了 includes，甚至一个简单的语言系统。它做得很好。

在我们发布该产品几个月后，我开始开发我们的最新产品——一个管理软件销售的付费应用程序。这也是有一个模板系统，因为这是最容易的方式来定制产品。我最近在 [PHP 杂志](http://www.phpmag.net/)上读到一篇关于一个叫做 [Savant2](http://www.phpsavant.com) 的引擎的文章。玩了一圈，我决定这一次，我不想创建自己的系统，但我会使用 Savant 为模板系统供电。

旁注:使用第三方系统而不是自己编写系统的好处之一是，产品开发人员通常致力于改进该产品。他们不参与你的开发，所以他们致力于他们的程序，改进，修复和更新错误。这意味着，除了更新您的产品集成之外，您不需要做太多就可以改进您的产品的一部分！当你的产品的其他部分被其他人处理时，你也可以处理这些部分。

比较莎凡特和聪明是困难的。是的，它们都是模板引擎，但是 Savant 不编译它的模板。它也没有自己内置的脚本语法。它使用 PHP。许多人还认为 Smarty“笨重”,有很多复杂的功能，并非在所有情况下都需要。例如，我的产品不需要缓存或编译，我还想在模板中使用 PHP 语法，而不是 Smarty 的自定义语法。

【quote="Savant Website "】简而言之，PHP 本身就是一种模板语言，所以一般来说不需要在它上面再加一种模板语言。然而，在一些特定的情况下，使用定制标记比 PHP 更安全；出于这个原因，Savant 允许您为自己的目的挂接一个定制的编译器。[/quote]

(另见哈里·富克斯和 T2 的文章)

**学者 2**

Savant 背后的想法是(任何 PHP 开发人员都应该尝试这样做)将设计与应用程序本身分开。也就是说，将 HTML 与执行大部分功能的 PHP 代码分开。这是 Smarty 做的 100%–你在模板中看不到 PHP。但在莎凡特，情况并非如此。虽然所有的主要代码都在单独的 PHP 文件中，但也有一些 PHP 在模板中。

莎凡特总是有两个文件。其中一个是执行大部分工作的 PHP 文件，另一个是模板文件(通常带有. tpl.php 扩展名)。

为了说明这一点，这里有一个例子，我把 Savant2.php 放在同一个目录中:

`'James', 'Country' => 'UK'), array('name' => 'Jill', 'Country' => 'Canada'));`

 `$template->assign('title '，$ title)；//分配一个要在模板
$ template->assign(' customers '，$list)中使用的 var` 

`$template->display('customers.tpl.php'); // Display the template now
?>`

在 HTML 模板 customers.tpl.php，我会这样:

`... HTML ...`

 `客户)):？>

# title; ? >

客户 as $key = > $value):？>

| 顾客 | 国家 |
|  |  |

没有顾客。` 

`... HTML ...`

这将回显我添加到$list 数组中的两个客户。注意从$template->assign()调用中分配的变量是使用$this->varname 的类变量。还要注意，我在模板中使用了 PHP 控制结构(简称)。

**插件**

这是一个非常简单的例子，Savant 的真正用处来自它的各种插件和过滤器。

[quote = " Savant Website "]模板插件是可以从模板中调用的对象，用于自动执行重复性任务。Savant 动态加载你调用的插件对象，所以你不需要预加载它们。但是如果你想预装一个插件，有时候可以提前配置它的行为；插件是否可以配置取决于具体的插件。[/quote]

可用的插件包括表单生成、图像/CSS/JS 显示和日期格式等常规选项。其中，最有用的是选项列表插件。

动态生成选项列表的一个问题是选择默认值。有了[选项](http://www.phpsavant.com/yawiki/index.php?area=Savant2&page=PluginOptions)插件，这变得很容易。

例如，在编辑数据库记录时，我可以使用以下代码:

`// Status list menu options
$status_options = array(1 => 'Active', 0 => 'Disabled');
$template->assign('status_options', $status_options);`

这将设置状态下拉菜单选项，并将它们分配给模板变量。在代码的其他地方，我已经从数据库的记录中获得了数据。

在模板中，我可以创建列表，用 2 个选项填充它，并根据从我的数据库记录返回的数据设置默认值:

`<select name="status" id="status"><optgroup label="Select Status">plugin('options', $this->status_options, $this->user['status']); ?></optgroup></select>`

这里调用了插件方法。第一个参数是插件的名称，第二个是选项数组，第三个是默认值。这个值可以在列表中的任何位置，它只需要引用您提供的选项的数组键。输出可能类似于:

`<select name="status" id="status"><optgroup label="Select Status"><option value="1" label="Active" selected="selected">Active</option><option value="0" label="Disabled">Disabled</option></optgroup></select>`

另一个真正有用的插件是 HTML 复选框。我相信每个人都遇到过试图确定一个被勾选的复选框的值的问题！Savant 通过[复选框插件](http://www.phpsavant.com/yawiki/index.php?area=Savant2&page=PluginCheckbox)使它变得简单，因此当勾选时很容易设置一个值。例如，当你编辑一个数据库记录时，你也可以像上面的选项菜单一样轻松地将它设置为一个值。

Savant2 可以与 PHP4 和 PHP5 一起工作( [Savant3](http://www.phpsavant.com/yawiki/index.php?area=Savant3) 是专门为 PHP5 编写的)。

自从去年 10 月份和 Savant 一起工作以来，我已经能够用它做任何我想做的事情了。我发现它可以轻松应对像我自己这样的大型应用程序，我希望它可以轻松地双向扩展。它并不臃肿，编码良好(包括 PHPDoc 源代码文档),并且它确实做了我需要它做的事情。

周围有很多发动机。这是所有关于找到一个你喜欢的，适合你的项目。你会在 https://www.sitepoint.com/forums/showthread.php?找到一份有用的发动机清单 threadid=123769

**假期**

这将是我一个多星期以来的最后一篇文章。我明天要去加拿大度假，下周 5 号回来，届时我将恢复我的常规(每隔一天)职位。

## 分享这篇文章