# 就这么做:学习辛纳屈，第四部分

> 原文：<https://www.sitepoint.com/just-do-it-learn-sinatra-iv/>

欢迎来到本系列的最后一部分。这一系列教程的目的是让从未使用过 Sinatra 的人从创建应用程序到部署它。您可以在以下链接中找到该系列的其他部分:

*   [第一部分——辛纳特拉简介](https://www.sitepoint.com/just-do-it-learn-sinatra-i/)
*   [第 2 部分—使用数据映射器](https://www.sitepoint.com/just-do-it-learn-sinatra-ii-2/)
*   [第 3 部分-数据库关联](https://www.sitepoint.com/just-do-it-learn-sinatra-ii-2/)

在这三个部分之后，我们现在已经完成了待办事项应用程序的大部分主要功能。在这最后一部分，我们将在 [Heroku](http://www.heroku.com/) 云托管服务上部署应用程序之前，看看如何为 CSS 使用 [Sass](http://sass-lang.com/) 。

## 一个标志

每个自尊的应用程序都需要一个标志，所以我创建了一个简单的“勾”标志，你可以在 Github 上找到[。我们可以在应用程序中使用它，将其设置为主要标题的背景图像。打开 styles.css 并添加以下 css:](https://github.com/daz4126/Just-Do-It/blob/master/public/logo.png)

```
h1.logo{
  font-family: helvetica,arial,sans-serif;
  font-size: 24px;
  color: white;
  padding: 64px 0 0;
  margin: 0 auto;
  text-transform: uppercase;
  text-align: center;
  font-weight: normal;
  letter-spacing: 0.3em;
  background: transparent url(/logo.png) 50% 0 no-repeat;
  }
```

(注意——如果您没有注意到，您需要在 layout.slim 中的 h1 元素中添加一个“logo”类，这样才能工作)

我还用这个标志创建了一个 favicon，你也可以在 Github 上找到它。要显示它，您需要在 html 中添加一个链接。这意味着 layout.slim 现在应该是这样的:

```
doctype html
html
  head
    meta charset="utf-8"
    title Just Do It!
    link rel="shortcut icon" href="/favicon.ico"
    link rel="stylesheet" media="screen, projection" href="/styles.css"
    /[if lt IE 9]
      script src="http://html5shiv.googlecode.com/svn/trunk/html5.js"
  body
    h1.logo Just Do It!
    == yield
```

## 变得无礼

Sass 是一种 CSS 模板语言，这意味着你可以用 Sass 编写你的风格，然后在服务器端编译成 CSS 发送给浏览器。它非常酷，基本上是 CSS 应该有的一切。有两种风格的 Sass——最初的“缩进式 Sass”和最近的“SCSS”(Sassy CSS)。我将使用 SCSS，因为它更像普通的 CSS，但这绝对值得两者都尝试一下，看看你更喜欢哪一个。

要将 Sass 整合到您的 Sinatra 项目中，我们需要安装 Sass gem:

```
gem install sass
```

接下来，我们必须将以下处理程序添加到 main.rb 的末尾:

```
get '/styles.css' do 
  scss :styles
end
```

这基本上是选取了布局文件中已经存在的对 styles.css 的引用，并加载名为“styles.scss”的 Sass 文件。这个文件还不存在，所以继续创建它。它应该保存在“视图”目录中。将 styles.css(在 public 文件夹中)的内容复制到 styles.scss 中，然后删除 styles.css。

如果你看看这个应用现在在浏览器中的样子，应该没什么变化。这是因为 SCSS 是 CSS 的超集，所以任何常规的 CSS 都会像预期的那样工作，所以我们所做的就是从使用标准 CSS 转移到使用 Scss。但现在我们准备开始加速我们的风格。

## CSS 变量

CSS 愿望清单上最需要的项目之一是变量。Sass 给了我们使用变量的能力。例如，我们可以使用标记中引用的变量来设置颜色

```
$orange:#D78123;
$blue:#1E1641;
$green:#02d330;
$grey: #999;
```

我们现在可以在样式表的其余部分引用这些颜色。例如，每个列表中的标题为橙色，我可以用 styles.scss 编写以下内容:

```
.list h1{
  color: $orange;
}
```

这使得保持一些元素使用这种颜色变得更加容易，然后我决定让它变得更暗一点，我只需要在一个地方改变它。你可以用变量做更多的事情，包括算术——一定要查看[文档](http://sass-lang.com/docs.html)。

## CSS 混合

Mixins 是 Sass 的一个非常酷的特性，它允许你创建模块化的、可重用的 CSS。例如，我们将创建一个名为 button 的 mixin，它包含所有相关的 CSS 来将一个标准链接转换成一个按钮:

```
@mixin button($color){
    background:$color;
    border: 2px solid darken($color,15%);
    cursor: pointer;
    color: #fff;
    width: 150px;
    margin-left:4px;
}
```

现在，我可以通过使用`@include`语句将这些通用样式添加到样式表中的任何其他 CSS 声明中:

```
.button{
    @include button($orange);
    }
```

这将把 button mixin 中的所有 CSS 添加到任何具有 button 类的元素中。当然，我们可以直接在“button”类中编写 CSS，但是使用 mixin 允许我们将它添加到任何其他 CSS 声明中，而不必重复任何代码。还要注意 mixin 包含一个颜色参数，它允许我们拥有不同颜色的按钮，而无需重复任何代码。还要注意，我们能够使用保存在变量`$orange`中的橙色。

## 嵌套 CSS

Sass 的另一个很酷的特性是嵌套声明的能力。这可以节省大量的输入，并为您的 CSS 文件添加更多的结构。

例如，以下 CSS 用于设置列表和每个列表中的标题的样式:

```
.list{
  float: left;  
  background: #fff;
}
.list  h1{  
    color:$orange;
}
```

通过将 h1 声明嵌套在。列表声明，像这样:

```
.list{
  float: left;  
  background: #fff;
  h1{  
    color:$orange;
  }
}
```

上面的 Scss 将编译成与上面的例子相同的 css，但是它有显示嵌套结构的优点。如果您开始使用一些深度嵌套的 CSS，也可以节省大量的输入。

Sass 是一个如此强大的工具，它使得编写 CSS 变得如此容易，并且在 Sinatra 中实现起来如此简单，这并不是在所有项目中不使用它的任何理由。尤其是因为你可以只写标准的 CSS，并在需要的时候加入额外的“时髦”功能。我在这里给出了几个例子，它们只是开始触及 Sass 能力的表面。我鼓励您浏览一下，阅读文档，最重要的是，尝试一些您自己的代码。我保证，一旦你开始使用 Sass，你就不会再回到常规 CSS 了！

没有足够的空间来显示完整的 Scss 文件，但是你可以在 Github 上看到完整的代码[。现在，我们已经完成了应用程序的外观设计，是时候部署它了。](https://github.com/daz4126/Just-Do-It/blob/master/views/styles.scss)

## Heroku

Heroku 是一项优秀的服务，可用于在云中托管 Sinatra 应用程序。它对基本网站是免费的，包括数据库存储，并将随着你的应用变得更受欢迎而扩展。它消除了托管网站的所有麻烦，可用于大规模生产网站。他们也有很好的文档和支持。

如果你还没有的话，你需要做的第一件事就是[获得一个 Heroku 账户](https://api.heroku.com/signup)。接下来，确保您的系统上安装了 [Git](http://git-scm.com/) 。如果你还没有，Github 上有一个有用的[指南。请务必特别注意关于设置 SSH 密钥的部分，因为您将需要这些密钥来访问 Heroku。](http://help.github.com/linux-set-up-git/)

接下来，你需要安装 Heroku gem，它允许你在 Heroku 上与你的应用程序进行交互:

```
gem install heroku
```

如果您是第一次使用 Heroku，您需要添加刚刚创建的 SSH 密钥(您只需要这样做一次):

```
heroku keys:add
```

现在我们可以在 Heroku 上创建应用程序:

```
heroku create justdoit
```

(请注意，您必须为您的应用程序使用不同的名称，因为 justdoit 已经由我使用了！)

现在你已经设置好了 Heroku，是时候让你的应用程序准备好部署了。

## 大错

在我们将应用程序部署到 Heorku 之前，我们需要使用 Bundler 来管理我们的应用程序拥有的所有依赖项。这些包括我们一直在使用的所有宝石以及它们所依赖的宝石。Bundler 将所有这些 gem 直接安装到应用程序的目录中。这意味着你的应用程序与它所有的依赖项“捆绑”在一起，所以你不依赖于默认情况下安装在服务器上的任何东西(Heroku 实际上默认情况下没有安装 gems)。您还可以管理任何 gems 的版本号，这样您就可以确保生产中使用的版本与开发中使用的版本相同。

首先，我们需要安装 Bundler gem:

```
gem install bundler
```

我们现在必须在主应用程序文件夹中创建两个新文件。首先，我们需要一个宝石文件，列出我们使用过的所有宝石。将以下代码保存在应用程序根目录下名为 Gemfile(无扩展名)的文件中:

```
source :rubygems
gem "sinatra"
gem "datamapper"
gem "slim"
gem "sass"
gem "dm-postgres-adapter", :group => :production
gem "dm-sqlite-adapter", :group => :development
```

最后两行指定数据映射器用来连接数据库的数据库适配器。到目前为止，我们在开发机器上一直使用 Sqlite，但是 Heroku 使用的是 [PostgreSQL](http://www.postgresql.org/) 。在不同的环境中使用不同的数据库是通过将数据库适配器 gems 放入 Bundler 所谓的组中来处理的。

我们还需要一个备份文件。打开文本编辑器，将以下内容另存为 config.ru:

```
require 'bundler'
Bundler.require
require './main'

DataMapper.setup(:default, ENV['DATABASE_URL'] || "sqlite3://#{Dir.pwd}/development.db")

run Sinatra::Application
```

注意，我将数据库连接线放在了这个文件中——将配置代码放在这个文件中是很常见的。这意味着这一行现在需要从 main.rb 中删除。

最后，您需要在本地对此进行测试，因此我们运行`bundle install`命令来创建一个. bundle 目录，其中包含我们的 gem 文件中列出的所有 gem。

```
bundle install --without production
```

标志确保生产组中的任何宝石都不会被安装。

没有必要在生产中运行这个命令——如果 Heroku 检测到 Gemfile，它会自动运行这个命令。然而，我们必须告诉 Heroku 避免在“开发”组中使用任何宝石。这是通过一个简单的命令完成的:

```
heroku config:add BUNDLE_WITHOUT="development:test" --app justdoit
```

(请注意，您必须使用应用程序的名称，而不是 justdoit)

在我们继续部署应用程序之前，我们应该检查一下本地机器上的一切都运行良好。这是以不同的方式完成的，因为我们有一个备份文件。打开命令行提示符，键入以下内容:

```
rackup
```

这将在端口 9292 上启动服务器。请访问 http://localhost:9292/查看它的外观。

## 部署

剩下要做的就是将应用程序部署到 Heroku。首先，我们需要创建一个名为。gitignore 保存在根目录中，包含以下两行:

```
development.db
.bundle
```

这将阻止任何不需要的文件被添加到 git 存储库中。我们不希望 Sqlite 数据库文件在 git 存储库中，因为 Heroku 不使用它。我们也不希望。要发送到生产服务器的包目录(因为它将有自己的版本)。

下一项工作是初始化 Git 存储库:

```
git init
git add .
git commit -m 'initial deployment'
```

最后，我们将应用程序推送到 Heroku

```
git push heroku master
```

当你开发你的应用程序时，在 Heroku 上更新代码是非常容易的。只需将代码提交到 Git 存储库中，然后使用以下命令将它们推送到 Heroku:

```
git add .
git commit -m 'some message about the updates'
git push heroku master
```

剩下唯一要做的事情是通过运行迁移在服务器上设置数据库。Heroku 命令行界面允许您访问 irb 控制台，该控制台允许您与应用程序进行交互:

```
heroku console
Ruby console for justdoit.heroku.com
>> DataMapper.auto_migrate!
```

您可以使用以下命令从命令行打开网站:

```
heroku open
```

这将带您进入您的实时应用程序。你可以看我完成版的[照做就是了！](http://justdoit.heroku.com/)——随意摆弄。我还把所有的[源代码放在了 github](https://github.com/daz4126/Just-Do-It) 上，这样你就可以完整地看到所有的文件。

## 这是所有的乡亲

这就是这个四部分系列的结论。我希望它给了你足够的信息，让你开始了解 Sinatra，并希望了解更多。当然，最好的学习方法是自己尝试，Sinatra 的准入门槛如此之低，以至于启动新的应用程序和摆弄代码非常容易。我很想知道你们中是否有人想要更多 Sinatra 的文章，以及你们希望看到哪些内容被覆盖——请在评论中留下反馈。

## 分享这篇文章