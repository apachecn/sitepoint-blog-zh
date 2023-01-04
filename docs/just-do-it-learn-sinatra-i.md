# 去做吧:学习辛纳屈，第一部分

> 原文：<https://www.sitepoint.com/just-do-it-learn-sinatra-i/>

在这个由 4 部分组成的系列教程中，我将带你使用 Sinatra 和 DataMapper 创建一个名为“Just Do It”的全功能待办事项应用程序。希望这将有助于展示如何快速和简单地使用 Sinatra。让我们从辛纳屈的基础开始。

## 安装 Sinatra

要开始使用 Sinatra，您需要安装 Ruby。我推荐使用 RVM(如果你需要帮助，你可以遵循格伦·古德里奇的这个伟大的指南)。成功安装 Ruby 和 Rubygems 后，就该安装 Sinatra 了。这是使用 Rubygems 完成的，只需打开命令行并键入:

```
$> gem install sinatra
```

## 基本应用程序

首先，打开您最喜欢的文本编辑器，将下面的内容保存为 main.rb。

```
require 'sinatra' 
get '/' do 
  "Just Do It" 
end
```

**注意**——如果你使用的是低于 1.9 的 Ruby 版本，你需要在这个文件的顶部加上一行`require 'rubygems'`。

这是一个 Sinatra 应用程序所能得到的最基本的东西:你必须在顶部要求 sinatra gem，然后真正的操作从第 3 行开始。这被称为*处理器*,因为它处理路由和动作。第一部分(get)说明了正在使用的 HTTP 方法，在本例中是 HTTP GET，因为我们正在“获取”一个页面。接下来的部分是一个对应于路由的字符串，它是应用程序的根 url。代码块指定了当用户访问这个 url 时会发生什么。在这种情况下，我们返回一个简单的文本字符串(“Just Do It”)，它将呈现在页面上。处理程序代码块的最后一行总是将由浏览器呈现的内容。

为了检查它是否工作，我们需要启动一个 Sinatra 服务器。打开命令行，导航到保存文件的位置，然后键入:

```
$> ruby main.rb
```

几秒钟后，您应该会看到以下消息:

```
== Sinatra/1.2.6 has taken the stage on 4567 for development with backup from Thin
```

打开浏览器，进入 [http://localhost:4567](http://localhost:4567) 。你应该看到那句励志的话“就这么做吧”。干得好，你已经成功创建了你的第一个 Sinatra 应用程序。看，我告诉过你这很容易！

## 内嵌模板

当然，Just Do It 应用程序仅仅显示短的文本字符串不会有太大的作用。我们将不得不用 Ruby 创建一些包含 HTML 和一些动态内容的模板文件。Slim 是一个神奇的模板引擎，它让这个任务变得简单多了。在我们继续之前，我们需要安装超薄宝石:

```
gem install slim
```

现在，返回 main.rb 并添加以下几行:

```
require 'sinatra' 
require 'slim'

get '/' do 
  slim :index 
end

__END__

@@layout 
doctype html 
html
  head 
    meta charset="utf-8" 
    title Just Do It 
    link rel="stylesheet" media="screen, projection" href="/styles.css" 
    /[if lt IE 9] 
      script src="http://html5shiv.googlecode.com/svn/trunk/html5.js" 
  body 
    h1 Just Do It 
    == yield 

@@index 
h2 My Tasks
ul.tasks
  li Get Milk
```

首先，我们确保我们需要 slim gem，然后我们对处理程序做了一些更改，所以最后一行现在返回一个由 slim 生成的名为“index”的视图。这个视图可以在文件底部的“@@index”后面看到。这是 Sinatra 内联模板的一个例子，我认为这是一个杀手级的特性，因为它允许你把所有的代码保存在同一个文件中——非常适合快速地把东西放在一起。内联模板总是在`__END__`声明之后，每个模板都以`@@`开始。

我还包含了一个名为“@@layout”的模板。这将自动呈现在每个视图中，并提供一个基本的 HTML5 脚手架。布局模板中的关键行就在末尾(`==yield`)。`yield`语句呈现处理程序请求的模板中的内容(在本例中为“index”)。

这两种视图都使用了 Slim 的最小语法。我发现这让编写 HTML 变得更加愉快，但是要注意——Slim 对空白敏感。两个空格的缩进用于嵌套元素，Slim 对这种一致性要求非常严格。如果你不喜欢 Slim，还有其他各种各样的模板语言可以用于 Sinatra，包括 ERB、Haml 和 Markaby。

让我们看看这个是什么样子的:通过按 Ctrl+C 终止服务器，并通过键入`ruby main.rb`重新启动它。每次你修改代码时，服务器都需要重启(如果这开始变得麻烦，你可能想尝试使用[猎枪](http://rubygems.org/gems/shotgun))。在 [http://localhost:4567](http://localhost:4567) 重新加载页面，查看我们的新布局。

## 外部视图

现在您已经熟悉了 Sinatra 如何使用处理程序来呈现视图，让我们离开内联模板，看看如何将视图组织到文件夹中。

在此之前，我们需要移除内联模板:打开 main.rb 并删除 `__END__`声明和后面的所有模板。

在保存“main.rb”文件的同一目录中，创建两个文件夹，一个名为“public ”,另一个名为“views”。`public`文件夹将用于保存任何面向公众的资产，如图像和样式表。视图文件夹将保存我们所有的超薄模板。我们现有的观点需要转移到单独的文件。将以下内容作为 layout.slim 保存在`views`文件夹中:

```
doctype html 
html 
  head 
    meta charset="utf-8" 
    title Just Do It 
    link rel="stylesheet" media="screen, projection" href="/styles.css" 
    /[if lt IE 9] 
      script src="http://html5shiv.googlecode.com/svn/trunk/html5.js" 
  body 
    h1 Just Do It 
    == yield
```

并将以下内容保存为 index.slim:

```
h2 My Tasks
ul.tasks
  li Get Milk
```

最好重启你的 Sinatra 服务器，确保我们的视图仍然可以渲染。

## 动态内容

现在我们更有条理一点了，我们可以看看辛纳屈的更多特征。让我们创建一个新的处理程序，它接受一些动态输入(在`main.rb`中):

```
get '/:task' do
  @task = params[:task]
  slim :task
end
```

您可能已经注意到路由包含字符串':task '–这是一个名为参数的*，可通过前导冒号(':')识别。命名参数是从 url 中获取的值，可通过“params”哈希进行访问。在代码块的第一行，我设置了一个名为“@task”的*实例变量*，它等于 params[:task]的值，它将是 url 中正斜杠后面的内容。实例变量非常有用，因为可以在视图中引用它们。*

说到视图，新路由指定了一个不存在的“任务”视图。将以下代码复制到一个新的文本文件中，并将其作为“task.slim”保存在 views 文件夹中:

```
h2 My Tasks
= @task
```

它使用' = '符号来计算 Ruby 变量。Slim 将输出放在' = '符号后的任何 Ruby 的结果。在这种情况下，它是@task 实例变量的值，应该与 url 匹配。测试一下——终止并重启服务器，然后转到“/http://localhost:4567/get-milk”。您应该看到以下内容:

![ToDo Task](img/e0980542f5fe3682147db2737943ecac.png)

简单任务路线

这很好，但我们可以做得更好。让我们在处理程序中添加一些逻辑，使它看起来更好:

```
get '/:task' do
  @task = params[:task].split('-').join(' ').capitalize
  slim :task
end
```

现在，当您转到“/http://localhost:4567/get-milk”时，您应该会看到以下内容:

![](img/144c284c436225605c01f02c596f9de5.png)

我们更好的任务页面

## 形式

在我们完成教程的第一部分之前，让我们看看如何使用表单添加任务。打开 index.slim，将内容替换为以下内容:

```
form action="/" method="POST"
    input type="text" name="task"
    input.button type="submit" value="New Task >>"
```

我们现在需要一个处理程序在表单发布后处理它。如果您看一下代码，action 属性告诉表单将自己提交给 url '/'，method 属性告诉它使用 POST 方法。这很好地引出了我认为的 Sinatra 的另一个杀手锏——在处理程序中指定请求方法的方式。将以下处理程序添加到“main.rb”中:

```
post '/' do
  @task =  params[:task]
  slim :task
end
```

这段代码非常类似于我们之前用来列出任务的处理程序，但是现在我们使用表单的输入来获取任务。新的处理程序被定义为 POST 路由，这意味着它只对 HTTP POST 请求做出反应。因此，我们可以为同一个路由定义两个处理程序，但是根据请求的类型给出不同的代码块。

提交表单时，它将 params[:task]的值设置为表单`task`文本输入中输入的值。通过以类似的方式引用 params 散列，可以访问表单中设置的任何值。

转到 [http://localhost:4567/](http://localhost:4567/) ，试着在表单中添加一些任务。这是一个开始，但是我们不能创建一个任务列表，当然也没有办法完成和删除它们。我们需要一种存储任务的方法，这就是我们将在[第二部分](https://www.sitepoint.com/just-do-it-learn-sinatra-ii-2/)中讨论的内容！

## 分享这篇文章