# 部署到 Heroku:简介

> 原文：<https://www.sitepoint.com/deploying-to-heroku-an-introduction/>

*感谢[马修·威尔金](https://www.sitepoint.com/author/mwilkin/)好心帮助[同行评审](https://www.sitepoint.com/introduction-to-sitepoints-peer-review/)这篇文章。*

在本文中，您将了解到 Heroku 以及如何在其上部署您的 web 应用程序。

![Heroku Logo](img/cd6d9a2d184dab9d5c481e25057f0e99.png)

如果你从未听说过它，Heroku 是一个用于快速部署 web 应用程序的托管服务器平台。它会自动为您提供服务器资源，部署就像在 Heroku 上部署应用程序一样简单。最重要的是，你可以免费部署你的应用程序(前提是它不会获得太多的流量)，这使得免费的*和*很容易上手。

如果你的流量很大，Heroku 可能会有点贵；每个节点(或者他们称之为 <q>dyno</q> )每月将花费你 25 美元或者更多，增加像数据库这样的功能会增加一点。也就是说，这比雇佣 devops 团队来提供 Heroku 提供的稳定性和易用性要便宜得多。

## 开始之前

如果你想从这里开始，你需要确保你手头有一些东西。

1.  去下载并安装 [Heroku 工具箱](https://toolbelt.heroku.com/)。这是我们将用来配置项目的命令行实用程序。
2.  确保你的项目使用了 [Git](https://git-scm.com/) 。你应该知道 git 是什么，但是如果你不知道，[这里有一些轻松的阅读](https://www.sitepoint.com/git-for-beginners/)。如果没有项目，只要确保安装了`git`即可。

如果你已经准备好了，跳过下一部分，直接进入 **[创建一个 Heroku 项目](#creating)** 。

## 我们的示例项目

为了组装这个示例项目，您将需要 [pip](https://pip.pypa.io/en/stable/) (无论如何，这对任何 Python 开发来说都是方便的)。我们将使用一个用 [Flask](https://flask.palletsprojects.com/en/2.0.x/) 编写的 Python 项目，这是一个 Python 的 web 微框架，但是你可以跟随任何项目(很明显哪些部分是特定于语言的，以及如何调整它们)。

如果你已经准备好了，直接跳到下一部分。如果你需要一个项目，建立一个项目文件夹如下(不一定要叫它 <q>myproject</q> ):

```
/myproject
  /templates
    index.html
  app.py
  requirements.txt
```

并像这样填写它们:

`app.py`:

```
import os
import flask

app = Flask(__name__)

@app.route("/")
def index():
    return render_template("index.html")

if __name__ == "__main__":
    app.run(port=os.environ.get('PORT', '5000'))
```

`templates/index.html`:

```
<!doctype HTML>
<html>
  <head>
    <title>My example project</title>
  </head>
  <body>
    <h1>This is my project.</h1>
    <!-- feel free to get a bit more creative here -->
  <body>
</html>
```

`requirements.txt`

```
Flask==0.10.1
```

然后运行:

```
pip install -r requirements.txt
```

之后，通过运行`python app.py`并导航到`http://localhost:5000/`来确保一切正常。如果一切顺利，你应该看到`index.html`在那里呈现。

## 创建 Heroku 项目

世界上最简单的事情来了。打开一个终端，`cd`到您的项目目录，并运行以下命令(如果您的项目中已经有 git，则跳过 git 命令):

```
$ git init
$ heroku create
Creating app... done, stack is cedar-14
https://calm-lake-56303.herokuapp.com/ | https://git.heroku.com/calm-lake-56303.git
```

随着这个命令，Heroku:

*   为我们生成了一个名字(我们可以通过运行`heroku create myproject`来选择一个名字)
*   分配给我们一个 url 和一个 git 存储库
*   为我们初始化了 git 中的`heroku`远程存储库。

我们刚刚准备好部署，但是最好先知道将要发生什么。让我们了解更多关于 Heroku 如何知道如何处理你的代码——以及如何确保代码令人满意。

## 关于 Buildpacks 的一个旁白(或者，Heroku 如何识别你的项目)

Heroku 项目由<q>build pack</q>管理，本质上是获取依赖项、构建和运行项目的指令。Node.js、Ruby、Java、Clojure、Scala、PHP、Python 和 Go 都有官方支持的 buildpacks。你不需要告诉 Heroku 你用这些做什么；相反，Heroku 会根据一些关于依赖管理的惯例和启发来猜测这个项目是什么。例如，项目目录中的一个`requirements.txt`文件，如上面的示例项目中所存在的，表示一个 Python 项目。以下是 Heroku 将用来自动检测您的平台是否支持其他平台的依赖文件:

*   Node.js: `package.json`
*   露比:`Gemfile`
*   PHP: `composer.json`
*   Java: `pom.xml`
*   Scala: `build.properties`
*   Clojure: `project.clj`
*   去:`Godeps/Godeps.json`

如果您使用不同的语言，或者为其中一种语言使用不同的构建工具，您也可以使用第三方构建包。Heroku 维护了一个包含这些的[大目录，所以在放弃你的首选语言之前，先检查一下那里。您可以使用 git 存储库 URL 设置 buildpack 例如，要使用](https://elements.heroku.com/buildpacks) [Upworthy 的 Clojure Boot buildpack](https://elements.heroku.com/buildpacks/upworthy/heroku-buildpack-boot) ，您可以在项目目录中运行以下命令:

```
$ heroku buildpacks:set https://github.com/upworthy/heroku-buildpack-boot
```

你甚至可以在 Github 上找到一个未列出的 buildpack 检查一下，确保它可以安全使用！

## 设置您的个人资料

我们需要的一切都到位了，除了一个组件。Heroku 使用一个名为`Procfile`的文件来告诉它应该运行什么。对于您的入门项目，您可能只想指定一个`web`流程，但是您也可以指定`worker`工作。

由于我们使用`python app.py`运行我们的应用程序，我们将在`Procfile`中放入以下内容:

```
web: python app.py
```

(稍后，您可能想要使用更具性能的东西；您可以通过将 Gunicorn 添加到您的`requirements.txt`并将您的`Procfile`内容替换为`web: gunicorn app:app -b 0.0.0.0:$PORT`来使用它。

## 部署您的项目

将`Procfile`添加到您的存储库中:

```
$ git add Procfile && git commit -m "added Procfile"
```

然后，使用`git push`部署到 Heroku

```
git push heroku master
```

## 🎉🎉🎉恭喜🎉🎉🎉

您的应用程序应该部署在 Heroku 上。导航到 Heroku 告诉你的 URL，你应该会看到你的主页。

就是这样。整篇就是这样。这就是你现在和 Heroku 的生活；只需提交您的提交，它们就会立即得到部署。多疯狂啊。！

## 一些额外命令

万一你不满意，这里有一些你可能想用`heroku`命令做的事情:

*   `heroku config:set MY_ENV_VARIABLE=some_value`:设置永久配置值。对于数据库密码和其他配置非常有用。
*   `heroku ps:scale web=5`:流量突然激增？瞬间将您的流程扩展至 5 个 web dynos。请注意，这将花费您 125 美元，每 dyno 25 美元，所以请谨慎使用。要在离开 Reddit 后缩小规模，运行`heroku ps:scale web=1`。
*   犯了错误？您可以使用`heroku releases`列出您的应用版本。要回滚到某个版本，运行`heroku rollback <release identifier>`。或者直接运行`heroku release`来撤销最新版本。

如果你愿意，你也可以在 Heroku 的仪表板上管理其中的大部分。

## 分享这篇文章