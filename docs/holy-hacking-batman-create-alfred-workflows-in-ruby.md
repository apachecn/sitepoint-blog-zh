# 神圣的黑客，蝙蝠侠！在 Ruby 中创建 Alfred 工作流

> 原文：<https://www.sitepoint.com/holy-hacking-batman-create-alfred-workflows-in-ruby/>

![alfred](img/1dc9a12d0659cbef36fe352bc9f10aff.png)

*这篇文章由[弗雷德·希斯](https://www.sitepoint.com/author/fheath)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

在过去的几年里，出现了大量的键盘生产力工具:

*   侏儒做的
*   发射
*   汞
*   突触
*   启动栏

这些工具使用户能够将复杂的操作与关键字或快捷方式相关联。Alfred 与这些解决方案类似，但由于强调用户友好性和灵活性，它变得非常受欢迎。

> > Note: Alfred is unique to OS X.

Alfred 的一大优点是能够编写自己的工作流程。这些可以使用内置编辑器构建，可选地包括你自己用 PHP、Shell、Python 写的脚本…

…还有鲁比。

在本教程中，我们将使用 Ruby 脚本创建一个简单的随机数生成器工作流。

## 自定义搜索

在我们进入工作流程之前，让我们看看 Alfred 可以通过创建一个用于搜索 SitePoint 的自定义 web 搜索来做些什么。这很简单，只需在编辑器中完成即可。首先，我们需要找出可以用来搜索网站的 URL。在 SitePoint 搜索字段中输入一些内容，然后查看会生成哪种类型的 URL。

在撰写本文时，搜索“ruby”生成了`sitepoint.com/?s=ruby`。

打开 Alfred 的首选项窗口，导航到“功能”选项卡。在左侧，选择“网络搜索”。在右下角，点击“添加自定义搜索”。

![web_searches](img/dea54b8c7631b7c50f87d6906a89153a.png)

标题是将在 Alfred 中显示的名称。`SitePoint`工作正常。关键字是我们将要输入的内容。例如，我们可以使用`sp`。

对于搜索 URL，输入:`https://www.sitepoint.com/?s={query}`。`{query}`是引用关键字后提供的参数的通用 Alfred 方式。

![sitepoint_web_search](img/a2cbcc238a24df5cc56cdb863ce02d85.png)

创建自定义搜索后，激活 Alfred(默认 cmd+space)并键入关键字和查询。一旦你看到关键词被识别，点击回车键，默认浏览器将打开 SitePoint 的搜索结果。

到目前为止，这很好，但是要在 Alfred 中进行更高级的操作，我们将需要使用*工作流*。

## Alfred 工作流？

首先，在你尝试用工作流做任何事情之前，*确保你有 Alfred power pack*-基本上是 Alfred 的“专业”版本。

工作流可以完成不同的任务，最基本的是搜索。它们用对应于不同工作流对象的节点集来表示:

1.  **触发器**–由热键或外部事件激活
2.  **输入**–用关键字激活，参数可选
3.  **动作**–打开或显示文件，执行网络搜索，运行脚本/命令
4.  **输出**–使用来自其他节点的信息来生成通知、将数据复制到剪贴板或运行脚本

一个工作流可能包含多个节点路径。例如，你可以为一件事设置一个关键字，为另一件事设置一个热键。

![colors_workflow](img/f522e426f2826c7c4266c29dc4a8146d.png)

颜色是复杂工作流程的一个例子。

令人欣慰的是，对于许多常见的任务，你不需要制定自己的工作流程。用户上传它们已经有一段时间了，现在你可以在 http://www.packal.org/找到大多数。

然而，作为开发人员，我们有不同的方法和风格来解决问题，所以为了在个人层面上提高我们的生产力，我们需要制定我们自己的方法和风格。

让我们把我们的 SitePoint 搜索转换成一个工作流，这样我们就可以和我们的朋友分享了。这很简单，只需在编辑器中完成，无需任何代码。

打开 Alfred 的首选项窗口，转到“工作流程”选项卡。左侧有一个窗格，底部有一个“+”。点击它，选择**模板- >网页和网址- >在指定浏览器**中打开自定义网址。

![sitepoint_workflow_create](img/4edc2f96b1949f6211bb7308cbdec26b.png)

这将创建一个带有一个`Keyword`节点和一个`Open URL`节点的工作流。

![sitepoint_workflow_nodes](img/ada1d4032f7ed052c8b7608e997e7020.png)

对于`Keyword`节点，提供关键字和标题。你也可以在潜台词字段里放点东西，更清楚的说明关键词的作用。

![sitepoint_workflow_keyword](img/55aa0b4233293134a9f6b67808b722c1.png)

对于`Open URL`节点，添加我们在之前的定制搜索中使用的 URL，`https://www.sitepoint.com/?s={query}`。

![sitepoint_workflow_url_open](img/a6dc2161095c1568154e43e6080c54a5.png)

现在，如果您打开 Alfred 并输入您在关键字节点中使用的关键字，您应该会看到它出现。给它一个参数，你的默认浏览器就会打开，显示 SitePoint 上的搜索结果(前提是用于搜索的 URL 参数自本文撰写以来没有改变——如果你是在遥远的未来阅读，这当然是可能的)。

制作工作流的原因之一是我们可以共享或存储它。在工作流程列表下方，有一个`share`按钮。点按它和出现的“导出工作流程”按钮。将它保存到某个地方后，您可以通过从列表中移除工作流并打开(双击)新创建的`.workflow`文件来验证它是否工作。它将重新出现在列表中，并可再次使用。当我们开始添加脚本等资源时，它们将被封装在`.workflow`文件中，所以这是唯一需要共享的东西。

注意:*你可能需要告诉 OSX 去交往。阿尔弗雷德的工作流程文件。目前似乎没有从 Alfred 中导入工作流的方法。*

## 邀请露比参加派对

让我们制作一个工作流，提供一个生成随机数的关键字。在 Ruby 中使用 Alfred 不需要任何库。以下是关键字脚本工作流需要的两件主要事情:

1.  `{query}`如果需要，用作关键字参数
2.  发送到`stdout`的项目的 XML 字符串

首先，创建一个新的空白工作流(称之为“随机数”)。在节点工作区的右上角，单击“+”图标:

![skich](img/5d13973bf5c79452be6b8f4a8041eea7.png)

添加一个**输入- >脚本过滤器**节点。脚本过滤器在我们对关键字字段进行更改时运行脚本，并向我们显示项目列表(通常是建议或搜索结果)。选择一个关键字(我用的是‘rando’)，设置 Ruby 为语言。

至少我们的 XML 输出需要节点`xml->items->item->title`。

将以下内容放入脚本区域:

```
random = rand(1000)

xml = <<EOS
<xml>
<items>
  <item>
   <title>#{random}</title>
  </item>
</items>
</xml>
EOS

puts xml 
```

> > Note: This is a heredoc, so indentation is very important …

保存节点并尝试关键字。如果您设置“参数可选”，那么您可以通过在关键字后键入字符来继续生成随机数。

Alfred 拥有超过个输入节点。让工作流将生成的随机数复制到剪贴板。为了让 Alfred 工作流对象在脚本过滤后接收数据，我们必须在 XML `item`节点中指定`arg`属性:

```
 <item arg="#{random}">
   <title>#{random}</title>
  </item> 
```

接下来，添加一个`Outputs->Copy to Clipboard`节点，并将一个连接从脚本过滤器拖到其中。

这有点不直观，但是是`{query}`而不是`{arg}`引用了 items XML 节点中的`arg`属性的值集。所以，在剪贴板输出节点中，输入`{query}`即可。

现在，如果您再次调用`random`关键字并按回车键，该数字应该是粘贴的下一个文本。

尽管这很有效，但如果能给用户更多的反馈，那将会很有帮助。创建一个`Outputs->Post Notification`节点，并将一个连接从脚本过滤器拖到其中。对于文本，使用`{query} was copied to the clipboard`

![random_workflow_nodes](img/9f745c9ff4e1b143ef969072d43d01bf.png)

现在，如果您再次尝试该关键字，您应该会在按 enter 键时看到 OSX 通知。

## Bash 是唯一的 Alfred 脚本类型

这个例子相当简单，但是在普通的 Alfred script 字段中编辑复杂的 Ruby 脚本是行不通的。所以我们将把 Ruby 代码移到一个文件中，然后用 bash Alfred 脚本执行它。

这就是事情变得棘手的地方。

如果你正在使用像`rvm`或`rbenv`这样的 Ruby 版本管理器，那么你可能不希望在 Alfred 和你的其他 Ruby 项目之间混用 gems。标准程序是在 Alfred 脚本中使用`/usr/bin/ruby`,所以你会想办法为它添加宝石，而不是你的 Ruby 管理器。如果您通常使用系统 Ruby 并依赖于`bundler`进行 gem 管理，那么您就万事俱备了。

否则，如果您使用`rvm`在安装 gems 之前使用您的系统 ruby:

```
$ rvm system 
```

现在，将脚本类型从`/usr/bin/ruby`更改为`/bin/bash`，并将
脚本移动到工作流文件夹中任意名为`main.rb`的文件中。您可以通过脚本过滤器节点视图右下角的“打开工作流文件夹”来访问该文件夹。

现在，对于我们的脚本，我们只需要:

```
/usr/bin/ruby main.rb {query} 
```

![final_random_script_filter](img/983df6e5dbd85ba1b3cd574d30620a2c.png)

该关键字应该在阿尔弗雷德再次工作。现在我们回到了我们最喜欢的文本编辑器的友好的语法高亮空间，我们可以处理更复杂的脚本。

## 更好的表示格式

XML 不是可塑性最强的格式。随着我们的 Alfred 脚本变得越来越复杂，我们将需要一些更简洁/本地的东西来处理，比如 Ruby hashes。有几种解决方案，但让我们试试这里的 [gyoku](https://github.com/savonrb/gyoku) :

```
$ gem install gyoku --version 1.3.1 
```

现在我们可以用一个散列替换 XML:

```
require 'gyoku'

random = rand(1000)

hash = {
  xml: {
    items: {
      item: [
        {
          title: random,
          :@arg => random
        }
      ]
    }
  }
}

xml = Gyoku.xml(hash)
puts xml 
```

有几件事值得一提:

*   `:@arg`是我们在`gyoku`中指定 XML 属性`arg`的方式。
*   数组在`:item`键上，而不是`:items`

您可能已经注意到，我们的工作流实际上并不对赋予关键字的`{query}`做任何事情。让我们用最后一个代码样本来解决这个问题。最终的工作流将生成许多用户指定的随机整数:

```
require 'gyoku'
DEFAULT_ITEMS = 3

num_items = query = (ARGV[0] || DEFAULT_ITEMS).to_i

hash = {
  xml: {
    items: {
      item: []
    }
  }
}

num_items.times do
  random = rand(1000)

  item = {
    :title => random,
    :@arg => random
  }

  hash[:xml][:items][:item] << item
end

xml = Gyoku.xml(hash)
puts xml 
```

因为我们在脚本过滤器节点中将`{query}`传递给`/usr/bin/ruby main.rb`，所以我们通过脚本中的`ARGV[0]`来访问它。它是一个字符串，所以我们用`String#to_i`把它转换成一个整数。

## 结论

事实上，阿尔弗雷德有一个 Ruby 库。但是通过理解事物真正是如何工作的，你就不需要依赖它，也不需要冒着被蒙在鼓里的风险，如果它不是以你期望的方式运行的话。

在以后的文章中，我希望能看到如何创建实用的 Ruby 工作流，与 web 服务和 OSX 应用程序进行交互。

## 分享这篇文章