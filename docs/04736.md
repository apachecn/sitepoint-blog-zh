# 大众用烧瓶

> 原文：<https://www.sitepoint.com/flask-masses/>

> [Flask](http://flask.pocoo.org/) 是一个基于 Werkzeug、Jinja 2 和 good intentions 的 Python 微框架。–*[官方烧瓶文档](http://flask.pocoo.org/docs/0.10/)*

为了进一步解释这段话，Werkzeug 是一个 python 实用程序库，Jinja 2 是 Python 的一个模板引擎。

![flask (Small)](img/b71dd972f720cd9562e770d5e092cca3.png)

## 为什么是烧瓶？

大约一年前，我在研究 PHP 之外的另一种编程语言。我已经是一名活跃的 PHP 开发人员 10 年了，老实说，只用 PHP 开发应用程序有点无聊。没有任何标准化的库或框架也没有帮助。我发现自己花了一半的时间去重新发明轮子，只是为了重新发明一样东西。认为我比其他 PHP 开发人员更了解往往会导致在项目上花费太多时间。这在 PHP 世界中很常见——几乎所有其他开发人员都在构建自己的框架，这可能会导致某些类型的混乱，但我们现在不要去那里。

我在寻找新的东西，在工作中，我们刚刚开始使用 Django 进行严肃的 web 开发。当时，Django 看起来像是一个太大的框架，我想要一个小的框架来构建它。然后，我偶然发现了 [Flask](http://flask.pocoo.org/) ，从那以后我就一直用它来完成我的大部分个人开发项目。

在本文中，我将介绍如何安装和配置 Flask，并运行一个基本的“hello world”示例。另外，我会假设你已经安装了一台 Linux 机器(或者一台 Mac ),或者你正在运行某种像样的虚拟机，比如 [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) 。

## 下载和安装

要开始使用，您需要 Python 2.6 或更高版本。在[宅基地改良](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)中，这是一种默认。

首先，对于清洁发展，我们需要 **virtualenv** 。作为 Python 开发人员，您很可能会构建不同的应用程序。不同的应用程序有不同的依赖关系，甚至可能有不同的 Python 版本。为了解决机器上的依赖和旧库堆积的问题，我们使用 virtualenv。Virtualenv 为您的应用程序创建了一个单独的环境，因此您可以保持您的环境完全隔离。更多信息请访问[文档](http://virtualenv.readthedocs.org/en/latest/)。

让我们安装它:

```
sudo pip install virtualenv
```

你甚至可以这样做

```
sudo apt-get install python-virtualenv
```

安装好 [virtualenv](https://www.sitepoint.com/virtual-environments-python-made-easy/) 后，创建一个新环境。

```
mkdir flaskproject
	cd flaskproject
	virtualenv env
	New python executable in env/bin/python
	Installing distribute............done.
```

现在你有了一个名为“flaskproject”的目录。在这个目录中有另一个名为“env”的目录(包含您的环境)。要开始使用您的环境，您需要“激活”它。

```
. env/bin/activate
```

最后，让我们安装烧瓶。

```
pip install Flask
```

您应该会看到类似这样的内容:

```
Successfully installed Flask Werkzeug Jinja2 itsdangerous markupsafe
	Cleaning up...
```

如果您检查您的文件夹，请注意您仍然只能看到您的 env 目录。没关系，因为 Flask 安装在您的 env 目录中。

所有安装的包都在`site-packages`子目录下。

```
env/lib/python2.6/site-packages
```

或者您可以做`pip list`，它将列出您的环境中所有已安装的软件包。如果您看到的不仅仅是 Flask，请不要害怕，因为 Flask 也会安装自己的依赖项。

```
pip list
```

现在，您已经有了可以构建的环境和应用程序。

## 你的第一个烧瓶应用

让我们在目录中创建`app.py`:

```
from flask import Flask
    app = Flask(__name__)
    app.debug = True

    @app.route('/')
    def main_method():
        return "Hello everyone"

    if __name__ == '__main__':
        app.run()
```

如果我们从控制台运行这段代码，我们将得到类似这样的结果

```
python app.py
	Running on http://127.0.0.1:5000/
```

如果你打开浏览器，转到给定的地址(或者你定义的 vhost，如果使用 VMs 和 vagger 的话)，你应该会看到你的消息。

让我们解释一下这是怎么回事:

```
from flask import Flask
app = Flask(__name__)
```

这将从 flask 导入 Flask 库，并创建 Flask 类的一个新实例。

在开发过程中，我经常打开调试模式。这就是`app.debug = True`线的作用。它给了我很多信息，当我改变一些文件时会自动重新加载服务器。

接下来，我们创建一个返回简单字符串的方法。

```
def main_method():
	     return "Hello everyone"
```

在我们的方法之上，我们使用 routing decorator ( `@app.route('/')`)为它指定一个 URL。任何到达`/`的请求都将被重定向到`main_method`。简单吧？

这最后一段代码检查脚本是否被直接调用，而不像导入的模块。如果是这样的话，它会自动运行代码。

```
if __name__ == '__main__':
        app.run()
```

## 模板的乐趣

现在，让我们创建一个 URL，它将接收一个参数并将其传递给模板，就像这样:`/fun/params/optional_parameter`

将这些行添加到`app.py`

```
@app.route('/fun/urls/<param>')
    def fun_url(param=None):
        return render_template('fun_url.html')
```

在第一个指令中，我们有一个新的路由，它将重定向到我们的新方法。因为我们在最后定义了一个参数，所以我们的方法需要这个参数。之后，我们用`render_template`方法返回模板。

如果您保存了`app.py`，并且有一个活动的服务器正在运行，它将会中断，并出现错误`unknown method render_template`。这是因为我们正在调用一个不存在的方法——或者至少它没有被导入。在我们的例子中，我们仍然需要导入这个方法。

在我们的文件顶部添加以下内容:

```
from flask import render_template
```

默认情况下，Flask 希望模板文件位于相对于我们的`app.py`的 templates 目录中。因此，我们需要在那里创建一个新目录。

```
mkdir templates
	cd templates
	touch fun_url.html
```

现在，如果我们重新启动应用程序

```
python app.py
	* Running on http://127.0.0.1:5000/
```

而我们打开一个类似`/fun/url/test`的 URL，应该会看到一个空页面。请尝试在该文件中添加一些内容，然后重新启动服务器。您应该能够看到更新后的模板文件结果。

现在让我们向模板传递一个参数。将`render_template`行更改如下:

```
return render_template('fun_url.html', fun_param=param)
```

这样，我们告诉`render_template`方法来呈现我们的模板，并将一个参数作为`fun_param`传递给它。如果我们打开`fun_url.html`并加上:

```
Parameter passed is: {{ fun_param }}
```

重新启动服务器后，我们应该会得到以下响应:

```
http://127.0.0.1:5000/fun/url/test
	Parameter passed is: test

	http://127.0.0.1:5000/fun/url/test1
	Parameter passed is: test1
```

## 从这里去哪里

你可以用 Flask 做更多的事情——这是一个初学者的介绍，所以你可以感受一下 Flask 是如何工作的。当您开始使用数据库、用户访问、REST API 时，事情会变得非常简单。现在，最好的起点是官方的烧瓶文档

此外，一篇真正伟大的文章是 [the Flask Mega-Tutorial](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world) ，它几乎涵盖了你在进一步开发中需要的一切。

你喜欢这个介绍吗？想看看更高级的例子吗？请在评论中告诉我！

## 分享这篇文章