# Octopress 3 的到来让博客的生成变得异常简单

> 原文：<https://www.sitepoint.com/octopress-3-arrives-to-make-blog-generation-crazy-simple/>

![octopress](img/bc30e13050eb87435aa5970739009232.png)

*这篇文章由[弗雷德·希斯](https://www.sitepoint.com/author/fheath)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

> Octopress 是一个专为编写和部署 Jekyll 博客而设计的工具包。很可爱，是吧？
> –[八月出版社](https://github.com/octopress/octopress)

自从我遇到 Octopress 后，我的喜悦之情溢于言表，因为使用它创建博客非常容易。随着 [Octopress 3](https://github.com/octopress/octopress) 的[宣布](http://octopress.org/2015/01/15/octopress-3.0-is-coming/)，事情变得更加令人兴奋。Octopress 3 包含了旨在解决之前版本缺陷的功能。新推出的 Octopress CLI 允许您轻松创建新网站、创建新页面、添加帖子和部署。

在本教程中，我们将创建一个简单的博客，添加文章和页面。在这个过程中，我们还将看到如何将所有内容共享到 Twitter 和部署到 Github。

## Octopress 揭幕

首先，安装 Octopress gem。从您的终端:

```
gem install octopress 
```

这样，你就安装了 Octopress！我爱 Ruby 和 Rubygems！

### 博客创作

使用新的 Octopress CLI 命令，启动和运行非常简单。导航到您的工作目录并运行以下命令:

```
octopress new kinsomicrote 
```

这将为网站创建一个名为 **kinsomicrote** 的目录。把“kinsomicrote”换成你想给你的博客起的名字。导航到新的船员目录，打开你的文本编辑器，看看你有什么。文件和文件夹应该是下面的树格式:

```
cd kinsomicrote

→ tree
.
|____.gitignore
|_____includes
| |____head.html
| |____footer.html
| |____header.html
|____feed.xml
|_____sass
| |_____layout.scss
| |_____base.scss
| |_____syntax-highlighting.scss
|____css
| |____main.scss
|_____templates
| |____draft
| |____post
| |____page
|_____posts
| |____2015-10-15-welcome-to-jekyll.markdown
|____about.md
|____index.html
|_____layouts
| |____post.html
| |____default.html
| |____page.html
|_____config.yml 
```

如果你曾经用过 Jekyll，它看起来应该非常相似。

### 创建帖子

我们可以使用 Octopress CLI 命令之一创建一个新帖子:

```
octopress new post Welcome Aboard 
```

这个命令创建一个标题为`Welcome Aboard`的新博客文章。显然，你可以使用任何你选择的标题。帖子被创建在 **_posts** 目录中。当您在文本编辑器中打开这个新的帖子文件时，它应该看起来像下面这样:

```
 ---
layout: post
title: "Welcome Aboard"
date: 2015-10-15T09:31:09+01:00
--- 
```

默认情况下，文章将被写成一个 [markdown](http://daringfireball.net/projects/markdown/) 文件。你的帖子内容应该放在 YAML 前沿的下方，这就是所谓的`---`之间的内容。

Front matter 允许使用 YAML 或 JSON 将特定于页面的变量包含在模板的顶部。前置内容必须出现在模板的最顶端，并通过前后三连字符`---`与内容的其余部分隔开。在这三条虚线之间，您可以设置预定义和自定义的变量，如类别、标签和永久链接。你可以在 [Jekyll 文档](http://jekyllrb.com/docs/frontmatter/)上阅读更多关于前端的内容。

### 查看网站

在网站可以被浏览之前，它必须被建立。由于 Octopress 是 Jekyll 的包装器，我们将使用下面的命令来构建:

```
jekyll build 
```

这将构建一个静态的 HTML 网站，可以在 **_site** 目录中找到。这是基于页面、文章、样式表和配置的降价文件构建的。

要查看该站点，请从您的终端运行以下命令:

```
jekyll serve 
```

然后，将浏览器指向地址`localhost:4000`。

### 创建新页面

Octopress 有一个默认的`About`页面，可以在`http://localhost:4000/about/`从浏览器中看到。该页面可以在您站点的主目录中找到。当您使用`jekyll build`生成站点时，会为`about`页面创建一个文件夹。导航到`_site/about`目录，您将看到生成的页面。

有一个 Octopress CLI 命令可以用来创建一个新页面，类似于创建一篇新文章所用的页面。

```
octopress new page contact.md 
```

这将在应用程序的主目录中创建一个新页面。创建的文件如下所示:

```
---
layout: page
title: ""
--- 
```

如果愿意，输入页面标题。

您可以在 YAML 封面下方的页面中输入内容。就像《邮报》的情况一样，内容是以减价的形式写的。您也可以选择创建页面的路径。例如:

```
 octopress new page news/index.md 
```

这将创建一个名为**新闻**的新目录，并在其中创建一个 **index.md** 文件。当您运行`jekyll build`或`jekyll serve`时，会创建一个`.html`文件，就像我们讨论过的“关于”页面一样。当站点重新加载时，页面的链接会自动显示在导航栏中。

### 配置

Octopress 有一个配置文件，您可以在其中管理网站的设置。这是位于站点主目录中的 **_config.yml** 文件。下面是它的一个例子:

```
# Site settings
title: Your awesome title
email: your-email@domain.com
description: > # this means to ignore newlines until "baseurl:"
  Write an awesome description for your new site here. You can edit this
  line in _config.yml. It will appear in your document head meta (for
  Google search results) and in your feed.xml site description.
baseurl: "" # the subpath of your site, e.g. /blog/
url: "http://yourdomain.com" # the base hostname & protocol for your site
twitter_username: jekyllrb
github_username:  jekyll

# Build settings
markdown: kramdown 
```

从配置文件中，您可以添加您的 Twitter 和 Github 用户名。它们会显示在你的页面底部，和描述一起。通过添加必要的信息，您可以随意编辑设置。您必须重新启动服务器，配置更改才会显示在浏览器中。您可以通过按`CTRL+C`停止服务器。

### 社交媒体分享

如果你想获得大量的读者，把你的博客文章分享到 Twitter 这样的社交媒体上是很重要的。幸运的是，Octopress 上有一个 gem 可以让这成为可能。出于本教程的目的，我们将重点关注 Twitter。你可以点击查看脸书和 Google Plus [的配置。](https://github.com/octopress/social)

在站点的主目录中创建一个 **Gemfile** 。您可以使用您的终端这样做，就像这样:

```
touch Gemfile 
```

添加以下内容:

```
source 'https://rubygems.org'

group :jekyll_plugins do
  gem 'octopress-social'
end 
```

还有`bundle install`。

打开 **_config.yml** 并添加以下内容:

```
twitter:
  username:                               # Add your Twitter handle
  tweet_count:         false              # Show number of shares on Twitter
  size:                normal             # Or large
  embedded_link_color:                    # Set link color for embedded tweets

  follow_count:        false              # Show number of followers
  tweet_message:       ":title by :username :hashtags - :url" # With Tweet button Twitter add the URL last
  tweet_link_text:     Twitter            # Configure the link text
  tweet_link_title:    Share on Twitter   # Share link title
  profile_link_text:   Follow :username
  profile_link_title:  Follow :username on Twitter  # Profile link title text 
```

在第二行输入您的 Twitter 用户名:

```
username: twitter_user 
```

其他设置可以保留为默认值。

要使用 Twitter 共享按钮，请导航到您站点的默认布局，并将此标签添加到结束标签的正下方。

```
{% twitter_script_tag %} 
```

如果你想让 Twitter 分享按钮显示在每篇帖子上，导航到 **_layouts/post.html** 并将其添加到`content`标签下。

```
{% tweet_button %} 
```

如果需要，也可以对页面进行同样的操作。重新加载浏览器以查看该按钮。

还有其他可用的设置，你可以在 [Octopress Social](https://github.com/octopress/social) 结账。

### 部署到 GitHub 页面

我们将在 GitHub 上托管我们的博客。如果您还没有帐户，请创建一个帐户。

创建一个[新存储库](https://github.com/new)，以此格式命名；`username.github.io`，用你的 Github 用户名替换`username`。我的看起来像这个`kinsomicrote.github.io`。

打开您的终端并初始化 git。**注意**用您在 Github 上的用户名替换用户名:

```
 git init
git remote add origin https://github.com/username/username.github.io.git 
```

使用`octopress deploy init`命令为我们的博客生成部署配置文件。

```
 octopress deploy init git git@github.com:user/project 
```

这会生成一个名为 **_deploy.yml** 的文件，其中包含部署配置:

```
 method: git                               # How do you want to deploy? git, rsync or s3.
site_dir: _site                           # Location of your static site files.

git_url: git@github.com:kinsomicrote/kinsomicrote.github.io  # remote repository url, e.g. git@github.com:username/repo_name

# Note on git_branch:
# If using GitHub project pages, set the branch to 'gh-pages'.
# For GitHub user/organization pages or Heroku, set the branch to 'master'.
#
git_branch: master                        # Git branch where static site files are commited

# remote_path:                            # Destination directory 
```

现在部署！

```
 octopress deploy 
```

完成后，你的网站应该是可用的！可以去看看我的 [kinsomicrote.github.io](http://kinsomicrote.github.io) (很明显，把网址改成你的用户名)。

## 结论

现在你知道如何使用 Octopress 3 创建博客了。Octopress 3 带来了许多令人兴奋的东西。我鼓励你更深入地研究 Octopress 来发现它们！

谢谢你陪我到最后。欢迎您的反馈:)

## 分享这篇文章