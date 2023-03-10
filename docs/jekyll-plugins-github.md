# GitHub 上的 Jekyll 插件

> 原文：<https://www.sitepoint.com/jekyll-plugins-github/>

![jekyll](img/4a61333225d84d1535d44629b3309c23.png)

如果你使用 Ruby，你很可能听说过 [GitHub](http://github.com) ，这是一个令人惊奇和喜爱的 DVC 库。你可能也听说过[杰基尔](http://jekyllrb.com)，博客感知静态站点生成器。你可能不知道的是 GitHub 让你在他们的 [GitHub Pages](http://pages.github.com/) 服务上免费托管你的 Jekyll 站点！像任何其他免费服务一样，这里有一个陷阱。这不是广告，带宽限制，或隐藏的费用，而是 Jekyll 插件的利用。

如果你不熟悉 Jekyll 或 GitHubPages，可以查看一下[快速入门指南](http://jekyllrb.com/docs/quickstart/)和他们对将站点部署到 [GitHub Pages](http://jekyllrb.com/docs/github-pages/) 的介绍。

默认情况下，Jekyll 做的已经足够让你立即投入工作了。你可以在 markdown 中写文章，使用简单的模板引擎 [Liquid](//docs.shopify.com/themes/liquid-basics) 让你的网站看起来很帅，然后用`jekyll build`和 *boom* 生成静态页面，你已经准备好了 HTML 文件和资产，可以部署了。因为这些是静态 HTML 文件(可能还有一些 JavaScript ),所以您几乎可以在任何地方托管它们

1.  个人服务器
2.  共享主机
3.  公共收存箱文件夹
4.  GitHub 页面(显然是正确的选择)

1 和 2 需要时间和金钱来维护，试图在 Dropbox 上托管一个站点除了测试之外可能不是一个好主意。GitHub Pages 服务(提供它是为了让用户、组织和项目可以托管关于他们自己、他们的组织和项目的站点)运行在 Jekyll 上；一个他们制造和维护的工具，因为他们什么都吃。

这就是插件出现的地方(或者说，不要出现)。即使你是 Ruby 和 Git 的新手，使用 GitHub 页面也很容易。但他们没有告诉你的是，当你在 Github 上`git push`你的 Jekyll repo 给你的用户/gh-pages repo，它建立你的`_site`目录供公众使用**时，它不会运行任何不属于官方 repo 的 Jekyll 插件。**

这是因为当 Github 创建你的页面时，它是用`jekyll build --safe`来完成的。`--safe`参数防止任何任意代码在构建时被执行，因为 GitHub 显然不希望您在他们的服务器上执行任意代码。

**趣闻:**Jekyll 中专门使用了液体模板引擎，因为它是安全的，不允许任意代码注入。这使得它安全地“用户可编辑”。

有很多很棒的 Jekyll 插件，比如 archive 和 rss generators，它们增加了非常理想的功能。但是，因为它们不是核心的 Jekyll 插件，所以不会在 GitHub 页面上运行。当然，您仍然希望在您的站点中有这种功能，那么我们如何才能避免这种(完全合理的)对任意代码执行的恐惧呢？有几种不同的方式，从非常简单到高度自动化(和复杂)。好消息是，这个简单的方法对任何人都有效。

GitHub Pages 托管你的站点，但它不*技术上来说*必须由 Jekyll 生成。这种技术性意味着您可以在本地生成您的站点，然后将静态文件推送到 GitHub。Github 不会通过一个`jekyll build`找到任何可以编译的东西，所以你最终会得到一个你的站点的 repo，而不是你的 Jekyll 文件的 repo。你可以确保 GitHub 不会试图通过在你的 repo 中包含一个`.nojekyll`文件来构建你的站点。

## 简单的方法

这实际上是我最初使用的方法，因为我意识到我的插件不能在 GitHub 页面上工作，这是最简单快捷的修复方法。这也意味着每次你想更新你的网站时，都需要一点手工操作。你需要做的是:

1.  将您的 Jekyll 站点所在的目录重命名为`compiled_site`
2.  创建一个新目录(在`compiled_site`目录之外),并将其命名为`jekyll_site`
3.  将`compiled_site`的所有内容复制到`jekyll_site`
4.  在新的`jekyll_site`目录中运行`jekyll build`
5.  在你的`compiled_site`目录下运行`git rm -r`删除所有文件
6.  将`jekyll_site/_site`的全部内容复制到`compiled_site`
7.  在`compiled_site`中创建一个`.nojekyll`文件
8.  运行`compiled_site`中的`git add --all :/`,将所有静态文件添加到 git repo 中，并删除旧文件
9.  运行`git commit -m "Built site locally"`
10.  运行`git push`来更新 GitHub

如果你需要插件在你的网站**上工作，现在**，没有比这更快的方式让内容进入 GitHub 页面。不幸的是，每次你写一篇新文章，调整你的主题，或者做任何其他的改变，你都需要手工复制你的`_site`目录中的文件到实际的 GitHub 目录中。

## 更简单、更棒的方式

手动文件复制？Jekyll 的座右铭是“像黑客一样写博客”,这种手动文件管理似乎不太像黑客。好在我们有`rake`。我们可以创建一个简单的 rake 任务来自动化上面概述的过程。如果你的 Jekyll 博客还没有使用一个`Rakefile`，创建一个并把它放到之前创建的`jekyll_site`目录中。将以下内容添加到此 Rakefile 中:

```
GH_PAGES_DIR = "compiled_site"

desc "Build Jekyll site and copy files"
task :build do
  system "jekyll build"
  system "rm -r ../#{GH_PAGES_DIR}/*" unless Dir['../#{GH_PAGES_DIR}/*'].empty?
  system "cp -r _site/* ../#{GH_PAGES_DIR}/"
end
```

在运行 Rakefile 之前，确保通过在`_config.yml`中添加一个 exclude 行来将它排除在`_site/`之外。exclude 行需要一个数组，即使只有一个文件。

```
exclude: [Rakefile]
```

现在你可以使用`rake build`而不是使用`jekyll build`来准备你的站点。这个`Rakefile`将

*   给你跑`jekyll build`
*   如果目标文件夹中有内容，请清空它(以防您在上次站点构建后删除了文件/帖子/资产)
*   将新构建的`jekyll_site/_site`目录的内容复制到您的`compiled_site` git repo 目录

现在你可以`cd`进入`compiled_site`并运行

```
git add --all
git commit -m "Updated my site!"
git push origin master
```

如果你想变得更有趣，你也可以扩展你的`Rakefile`来处理`git add`、`git commit`和`git push`命令。我最终扩展了我的`Rakefile`,通过 git 分支而不是多个目录来管理整个事情(改天再发！)然而，通过这种设置，你可以使用所有你想要的 Jekyll 插件，在本地构建你的站点，并且仍然可以在梦幻般的(而且是免费的！)GitHub 页面轻松。

你像黑客一样写博客吗？你用 GitHub 页面托管那些 Jekyll 文件吗？请在评论中告诉我你在流程中构建了哪些自动化功能，以及你现在可以使用的 Jekyll 插件。

## 分享这篇文章