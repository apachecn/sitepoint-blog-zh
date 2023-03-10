# 你看，妈妈！没有节点！–没有节点的 PHP 前端工作流

> 原文：<https://www.sitepoint.com/look-ma-no-nodejs-a-php-front-end-workflow-without-node/>

如果你被 Gulp、Grunt、NPM、Yeoman 和所有其他旨在提供帮助但往往适得其反的 NodeJS 工具吓倒、弄得精疲力尽或恼怒，那么一个好的资产管理工作流*在 PHP 中是可能的*，本教程旨在证明这一点。在本文中，我们将经历一个[无 NodeJS 的](https://www.sitepoint.com/php-vs-node-js-smackdown-right-of-reply/) PHP 前端资产管理设置。

![Robo Logo](img/debf62ddee0a05d05c31043ce4295cec.png)

## 我们将会做什么

我们将在 Slim 3 项目中加入资产管理。我们将使用 [Rob Allen 的 Slim3 骨骼](https://akrabat.com/a-slim3-skeleton/)作为基础“应用程序”,以及:

*   Bower HP 代替 Bower 安装资产
*   [用于缩小的标记故事/迷你资产](https://github.com/markstory/mini-asset)
*   [Robo](https://robo.li) 用于资源跟踪(监视变化)并在检测到变化时执行重建(缩小)

为什么是迷你资产而不是 Munee 或 Assetic？因为它非常简单——用一个`.ini`文件配置，运行，它就执行了。没有服务器改造，没有深入的配置，准备去 OOTB。

请注意，Slim 3 在这里只是作为一个基础应用程序使用，在整个画面中并不重要——我们不会使用 Slim 的任何特定功能。因此，您可以使用任何您想要的框架，或者完全跳过使用框架。我们将像往常一样使用 [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) 来建立一个本地开发环境。

## 入门指南

```
git clone https://github.com/swader/homestead_improved hi_assets
cd hi_assets
bin/folderfix.sh
sed -i '' "s@Project@test@g" Homestead.yaml
vagrant up
vagrant ssh
```

`sed`行是编辑`Homestead.yaml`文件的快捷方式——它将把单词`Project`替换为`test`，所以我们的应用程序可以在`test`文件夹中，而不是在`Project`文件夹中。这纯属个人喜好。如果这其中有任何令人困惑的地方，请看[这篇关于流浪者](https://www.sitepoint.com/re-introducing-vagrant-right-way-start-php/)的介绍。

只要这些命令一执行，我们就应该能够在浏览器中访问`homestead.app/`时得到`No input file specified`错误，前提是我们已经按照[指令](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)在主机操作系统上将`homestead.app`添加到`/etc/hosts`中。

让我们现在建立骨架。

```
cd Code
composer create-project -n -s dev akrabat/slim3-skeleton test
cd test
```

很简单。这应该会产生一个与此类似的屏幕:

![Slim3 greeting page](img/e6941391a3a2d0692f2827a25173aab8.png)

最后，让我们安装其他先决条件。BowerPHP 还不稳定，所以我们需要允许 Composer 下载 beta 包。使用以下命令打开全局配置文件:

```
composer global config -e
```

并添加以下语句:

```
"minimum-stability": "beta"
```

现在，我们可以安装测试包了。

```
composer global require beelab/bowerphp codegyre/robo markstory/mini-asset natxet/CssMin --dev
```

请注意，`mini-asset`将建议安装一些其他的包，每个包都特定于您可能需要的一个上下文。CssMin 包就是这样一个附加组件——没有它，`mini-asset`就不能处理 CSS 文件。关于您可能需要的更多过滤器组件，请参考建议列表安装程序输出。

我们现在已经具备了所有的先决条件。让我们开始处理资产。

## 资产管理

Rob 的裸骨 Slim3 框架有点简单——没有附加标准化的样式表或前端框架(在我看来，任何框架都应该这样)。让我们改变这一点。

### 通过 BowerPHP 引导

让我们添加 Bootstrap，只是为了好玩，[Bootstrap-social](https://lipis.github.io/bootstrap-social/)-这将允许我们演示各种 CSS 文件的合并，并允许我们在前端使用社交网络按钮样式。

```
cd ~Code/test
bowerphp install bootstrap
bowerphp install bootstrap-social
```

一个`bower_components`文件夹现在应该在`test`文件夹中，包含我们请求的所有内容。我们*可以*将组件安装到`public`中，并直接在我们的布局代码中使用它们(参见`app/templates/home.twig)`)，但这种方式效率非常低，尽管对调试风格来说很棒。相反，让我们以过滤、连接和缩小的方式产生我们需要的 CSS。这就是`mini-asset`的用武之地。

### 小型资产

根据[文档](https://github.com/markstory/mini-asset)，`mini-asset`通过`.ini`文件配置。让我们将`assets.ini`文件添加到我们项目的根目录中:

```
 [css]
cachePath = public/css/build
paths[] = bower_components/*
filters[] = CssMinFilter

[app.css]
files[] = bootstrap/dist/css/bootstrap.min.css
files[] = font-awesome/css/font-awesome.css
files[] = bootstrap-social/bootstrap-social.css
files[] = public/css/style.css 
```

根据 [bootstrap-social](https://lipis.github.io/bootstrap-social/) 文档，我们需要在 HTML 中包含 bootstrap、FontAwesome 和 bootstrap-social。这正是我们在上面通过合并不同的库所做的。

`cachePath`告诉`mini-asset`把我们编译好的 CSS 放在哪里。Paths 列出了 CSS 文件的所有位置。过滤器列出了用于处理资产的所有附加包。在我们的例子中，它只是前面提到的 CSSMin 包。

下一节将逐一列出编译后的 CSS 文件，以及它们所包含的文件。

如果我们现在运行`mini-asset`(当然，首先创建`cachePath`文件夹)，我们应该得到一个编译后的 CSS 文件作为输出。

```
mkdir public/css/build
mini_asset build --config assets.ini
```

我们需要做的最后一个调整是将`font-awesome`的`fonts`子文件夹复制到`public/css`中，因为编译后的 CSS 中的路径是相对的，向上一个文件夹找到字体。放在`build`中的编译文件不会有`fonts`文件夹，所以我们需要把它们放在那里:

```
cp -R bower_components/font-awesome/fonts public/css/
```

现在我们可以用这个替换`home.twig`文件中的样式链接:

```
<link href='/css/build/app.css' rel='stylesheet' type='text/css'>
```

我们还可以加入一个社交自举按钮，以确保它能正常工作。

```
<a class="btn btn-small btn-social btn-twitter">
            <i class="fa fa-twitter"></i> Sign in with Twitter
        </a>
```

![Screenshot of working social button](img/856d54144335d8215aa59b3158705006.png)

### 机器人

在这一点上，每当对样式文件进行重要的修改时，重新运行编译是很容易的，但是为什么不自动化呢？机器人来帮忙了。还有其他观察者，但是 Robo 是如此健壮和多才多艺的任务运行者，不使用它将是一种耻辱。事实上，Robo 在中内置了自己的[资产处理，但是考虑到许多人可能一开始就选择不使用 Robo，所以本教程遵循了一条更加分离的路径。如果感觉比`mini-asset`更好，请随意使用它的资产管道——没有限制！](https://robo.li/tasks/Assets)

为了运行，Robo 在工作目录中需要一个`RoboFile.php`。Robo 可以为我们创建该文件，只要我们在没有该文件的文件夹中运行它:

```
> robo
  RoboFile.php not found in this dir 
?    Should I create RoboFile here? (y/n)  
 y
RoboFile.php created
```

这个想法是在每次`public/css`中的`style.css`改变时触发`app.css`文件的重建——这是用户的自定义样式表，我们将在这里放置自己的样式来覆盖或使用包含在它们之前的样式(引导等)。)

首先，让我们设置一个观察器，看看它是否在每次文件改变时触发。这是通过在`RoboFile`中定义一个包含监视命令的公共方法来实现的:

```
public function build()
    {
        $this->taskWatch()
            ->monitor('public/css/style.css', function () {
                $this->say("style.css changed!");
            })->run();
    }
```

如果我们现在启动观察器并更改 CSS 文件，Robo 会提醒我们。

```
> robo build
 [Watch] Watching public/css/style.css for changes...
➜  style.css changed!
```

现在剩下的就是触发`mini-asset`变革重建。这是用 [`_exec`或`taskExec`](https://robo.li/tasks/Base/#exec) 命令完成的:

```
public function build()
    {
        $this->taskWatch()
            ->monitor('public/css/style.css', function () {
                $this->say("Starting CSS rebuild");
                $this->_exec('mini_asset build --config assets.ini');
                $this->say("CSS rebuilt successfully!");
            })->run();
    }
```

这就是我们所做的一切——重新运行 Robo，我们现在有了一个自动重建文件观察器，它会在每次更改时重新编译 CSS 文件。

```
> robo build
 [Watch] Watching public/css/style.css for changes...
➜  Starting CSS rebuild
 [Exec] Running mini_asset build --config assets.ini
.Complete
 [Exec] Done in 0.556s
➜  CSS rebuilt successfully!
➜  Starting CSS rebuild
 [Exec] Running mini_asset build --config assets.ini
.Complete
 [Exec] Done in 0.558s
➜  CSS rebuilt successfully!
```

## 结论

在本教程中，我们看了 PHP 项目中的资产管理(安装、连接和缩小)入门。我们看到 NodeJS 对于这方面的开发是不必要的，不值得额外的麻烦。

我们使用 BowerPHP 替代 Bower，使用 mini-asset 来连接和缩小，使用 Robo 来观察变化并触发缩小任务。有了这些知识，您现在应该能够编译更复杂的资产，包括较少的 CSS、JavaScript 等等。

你使用不同的方法吗？请在评论中告诉我们！

## 分享这篇文章