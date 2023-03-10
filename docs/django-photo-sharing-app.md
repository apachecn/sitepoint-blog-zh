# 用 Django 构建一个照片共享应用程序

> 原文：<https://www.sitepoint.com/django-photo-sharing-app/>

Django 是 web 开发中最常用的 Python 框架。它的内置特性和健壮的结构使它成为构建 web 应用程序的绝佳选择。但是外面有如此多的资源，以至于有时很难将这些知识应用到现实世界的项目中。在本教程中，我们将构建一个全栈的 web 应用程序，在后端使用 Django，在前端使用带有 Bootstrap 的 Django 模板。

## 要求

为了从本教程中获得最大收益，您最好掌握以下内容:

*   Python 的基础知识
*   Python 中的面向对象编程
*   Django web 框架的基础

如果你以前没有使用 Django 的经验，不要害怕继续学习本教程。这将是一个循序渐进的过程，每一步都会有解释。

在开始之前，我想向你介绍你的新盟友，Django documentation。我们将在整篇文章中引用它，所以一定要熟悉它。

## Django 照片分享应用

*本教程的所有源代码都可以在这个 [GitHub repo](https://github.com/Daniel1404/Django-photo-app) 上获得。*

项目的复杂程度取决于我们想要包含的所有特性。我们想提供给用户的功能越多，我们就需要花费越多的时间来构建和集成所有的东西到一个独特的项目中。

考虑到这一点，我们将很快看到我们要建立什么和不建立什么之间的区别。

### 我们将要建造的

在本教程中，我们将构建一个全栈(后端和前端开发)照片共享应用程序。我们的应用程序将包括以下功能:

*   CRUD(创建、读取、更新、删除)数据库功能
*   一个用户管理系统，这样用户将能够创建一个帐户，上传照片，看到其他人的照片，编辑或删除自己的照片
*   用 Bootstrap 制作的简单 web 界面

注意:尽管这款应用看起来很像社交网络，但它不是。像 Instagram 或 Twitter 这样的应用程序有很多复杂性，不可能在一篇文章中涵盖。

### 技术堆栈

让我们来定义我们将要使用的技术。当我们需要使用它的时候，我们会介绍每一个的安装过程。

在**后端**上，Django 将是 app 的核心框架。它允许我们通过 Django [ORM](https://docs.djangoproject.com/en/dev/topics/db/models/) (对象关系映射器)定义 URL、定义逻辑、管理用户认证和控制所有数据库操作。

此外，我们将使用几个第三方包来加速一些功能的开发。

Django-taggit 让我们能够用几个步骤建立一个简单的标记系统。 [Pillow](https://pillow.readthedocs.io/en/stable/) 是一个 Python 包，提供了 Django 图像处理功能。最后，[Django-crisp-forms](https://django-crispy-forms.readthedocs.io/en/latest/)为我们提供了一种显示引导表单的简单方法。

在**前端**，我们将使用 [Django 模板语言](https://docs.djangoproject.com/en/dev/topics/templates/)，它由动态显示数据的 HTML 文件组成。

我们还将使用 [Bootstrap 5](https://getbootstrap.com/docs/5.0/getting-started/introduction/) (撰写本文时的最新版本)来设计网站。

*注意:你可以在 [requirements.txt](https://github.com/sitepoint-editors/Django-photo-app/blob/main/requirements.txt) 文件中检查这个项目中使用的依赖项。*

## 创建 Django 项目

从姜戈开始吧！

首先，确保你安装了 Python 3。大多数 Linux 和 macOS 系统已经安装了 Python，但是如果你使用 Windows，你可以查看 [Python 3 安装指南](https://docs.python.org/3/using/windows.html)。

注意:我们将在教程中使用 Unix 命令(macOS & Linux)。如果你因为任何原因不能执行它们，你可以使用图形文件管理器。

在一些 linux 发行版中，`python`命令指的是 Python 2。在其他人看来，`python`根本不存在。

让我们看看您需要使用什么 Python 命令来执行。打开您的终端(在 Unix 上)或命令行窗口(在 Windows 上)并键入`python --version`:

```
python --version

# My result
Python 3.9.5 
```

如果你已经有了 3.6 以上的 Python 版本，你就可以开始了。如果您没有正确的 Python 版本，您可能会收到如下消息之一:

```
Command 'python' not found 
```

```
Python 2.7.18 
```

遵循本教程需要运行的 Python 命令是`python3`:

```
python3 --version

Python 3.9.5 
```

### 虚拟环境

一个[虚拟环境](https://www.sitepoint.com/virtual-environments-python-made-easy/)是一个隔离的 Python 环境，它包括运行 Python 程序所需的所有文件。

虚拟环境是任何 Python(和 Django)项目的关键部分，因为它们让我们管理并与其他人共享依赖性(项目所依赖的外部包)。

为了在本地创建一个虚拟环境，我们将使用 Python 3.6 或更高版本中的内置模块`venv`。

以下命令将创建一个名为`.venv`的虚拟环境(如果您愿意，可以选择另一个名称):

```
python -m venv .venv 
```

如果您使用的是 Ubuntu Linux，或者任何其他基于 Debian 的发行版，您可能会得到以下消息:

```
The virtual environment was not created successfully because pip is not available ... 
```

要解决这个问题，您可以运行以下命令:

```
sudo apt-get install python3-venv 
```

如果上面的命令不起作用，您可以使用 [virtualenv](https://pypi.org/project/virtualenv/) ，这是另一个用于虚拟环境的库:

```
virtualenv .venv 
```

运行该命令后，会出现一个名为`.venv`(或您选择的名称)的文件夹。

我们安装的所有软件包都将放在该目录中。

要激活虚拟环境，您需要根据您的操作系统运行特定的命令。可以参考下表(摘自 Python 文档)。

| 平台 | 壳 | 激活虚拟环境的命令 |
| --- | --- | --- |
| 可移植性操作系统接口 | bash/zsh | $ source。venv/bin/激活 |
|  | 鱼 | $ source。venv/bin/activate.fish |
|  | csh/tcsh | $ source。venv/bin/activate.csh |
|  | PowerShell 核心 | $ .venv/bin/Activate.ps1 |
| Windows 操作系统 | cmd.exe | 丙:>。venv\Scripts\activate.bat |
|  | PowerShell | PS C:>。venv\Scripts\Activate.ps1 |

因为我在 POSIX 操作系统上使用 bash shell，所以我将使用:

```
source .venv/bin/activate 
```

请注意，一旦我激活了`virtualenv`，一个`.venv`标题是如何添加到我的 shell 中的。

![Virtual environment activated](img/2bda1ddb715a419a0187d49ce6c0a653.png)

### 安装 Django

Django 是一个外部包，所以我们需要用 pip 安装它:

```
pip install django

# Use pip3 if the command above doesn't work

pip3 install django 
```

*注意:我们可以随时用`pip freeze`查看我们的`venv`中安装的包。*

接下来，让我们用命令行实用程序 [django-admin](https://docs.djangoproject.com/en/dev/ref/django-admin/) 启动一个名为`config`的 Django 项目。

```
django-admin startproject config 
```

这里，`config`是项目的名称，它被用作一种命名约定，以保持所有项目具有相同的结构。例如， [Django cookiecutter](https://github.com/pydanny/cookiecutter-django) 使用这个约定名称开始一个项目。

也就是说，您可以用任何其他名称创建项目。

运行这些命令后，您应该拥有 Django 项目的常规文件结构。你可以用命令行工具[树](https://linux.die.net/man/1/tree)或者任何文件管理器来检查它。

*注意:如果你不能运行`tree`，你需要安装它。*

```
$ tree config/
└── config
    ├── config
    │   ├── asgi.py
    │   ├── __init__.py
    │   ├── settings.py
    │   ├── urls.py
    │   └── wsgi.py
    └── manage.py 
```

现在，让我们用`cd`进入项目文件夹，并运行服务器来检查所有设置是否正确:

```
cd config/

python manage.py runserver 
```

您将看到一条警告消息，指出存在未应用的迁移。这是一个完全正常的消息，我们将在“[创建照片模型](#creatingthephotomodel)一节中学习如何运行迁移。

现在，在浏览器中访问 [localhost:8000](http://localhost:8000/) 。你应该看到标志性的 Django 祝贺页面。

![Django congrats page](img/603353237b552bcbceedb7924f8af9ed.png)

## 启动照片共享应用程序

[manage.py](https://docs.djangoproject.com/en/dev/ref/django-admin/) 文件具有与`django-admin`完全相同的功能，因此我们将在本教程中多次使用它。

它的位置在项目的根文件夹中，每次我们想用它运行命令时，都需要进入项目目录。

记得总是用`ls`列出你所在目录的文件，以检查我们是否在正确的位置:

```
$ ls
Another-files.. manage.py 
```

有了这些提示，就该启动项目的主 app 了。为此，我们打开一个新的 shell(因此本地服务器仍在运行)，并使用带有命令`startapp`的`manage.py`。

注意:每次我们打开一个新的 shell 会话，我们都需要再次激活虚拟环境。

```
source .venv/bin/activate
cd config
python manage.py startapp photoapp 
```

在这种情况下，应用程序的名称是 **photoapp** 。同样，您可以用您想要的任何名称来创建它。

每次我们创建一个应用程序，我们必须安装它。我们可以在`config/settings.py`文件中通过将`photoapp`添加到`INSTALLED_APPS`变量中来做到这一点:

```
# config/settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    ...

    # Custom apps
    'photoapp',
] 
```

接下来，我们将进入 app 目录并创建一个空的`urls.py`文件。我们可以通过运行`touch`或者用图形文件管理器来创建它:

```
cd photoapp/

touch urls.py 
```

最后，让我们在整个项目中包含照片共享应用程序的所有 URL 模式。为此，我们将使用`django.urls.include`函数:

```
# config/urls.py

from django.urls import path, include # Import this function

urlpatterns = [
    path('admin/', admin.site.urls),
    # Main app
    path('', include('photoapp.urls')),
] 
```

上面的代码将包括项目的`photoapp/urls.py`的所有 URL 模式。

如果您看一下运行服务器的 shell，您会看到一个错误:

```
raise ImproperlyConfigured(msg.format(name=self.urlconf_name)) .... 
```

这是因为我们还没有在`photopp/urls.py`文件中创建`urlpatterns`列表。

为了解决这个问题，创建一个名为`urlpatterns`的空列表。我们稍后将用 Django 路径填充该变量:

```
# photoapp/urls.py

# Empty patterns
urlpatterns = [

] 
```

*注意:使用这种方法的优点是，我们可以通过在 photoapp 中包含所有需要的代码来使其可重用。*

## 创建照片模型

在这一节中，我们将构建应用程序的数据库模式。为此，我们将使用 Django ORM。

Django ORM 允许创建和管理数据库表，而不需要手动使用 SQL。

当我们写一个[模型](https://docs.djangoproject.com/en/dev/topics/db/models/)的时候，它代表一个数据库表，里面的每个属性代表一个列。

由于我们将使用 Django 内置的认证系统，我们可以开始关注应用程序的核心功能。这样，我们就避免了构建定制的用户管理系统。

开始之前，我们要安装一些第三方的包，`django-taggit`和`Pillow`。我们可以使用以下命令来实现这一点:

```
pip install django-taggit Pillow 
```

django-taggit 是一个 django 应用程序，所以我们需要像安装 **photoapp** 一样安装它:

```
# config/settings.py
INSTALLED_APPS = [
    ...

    # 3rd party apps
    'taggit',

    # Custom apps
    'photoapp',
]

# Django taggit

TAGGIT_CASE_INSENSITIVE = True 
```

`TAGGIT_CASE_INSENSITIVE`变量将标签配置为不区分大小写。这意味着`PYTHON`和`python`将是相同的。

我们来定义一下`Photo`模型，它将是 app 的主模型。打开`photoapp/models.py`文件并使用以下代码:

```
# photoapp/models.py
from django.db import models

from django.contrib.auth import get_user_model

from taggit.managers import TaggableManager

class Photo(models.Model):

    title = models.CharField(max_length=45)

    description = models.CharField(max_length=250) 

    created = models.DateTimeField(auto_now_add=True)

    image = models.ImageField(upload_to='photos/')

    submitter = models.ForeignKey(get_user_model(), on_delete=models.CASCADE)

    tags = TaggableManager() 

    def __str__(self):
        return self.title 
```

在上面的代码块中，我们定义了`Photo`模型。让我们看看每个字段做什么。

*   `title`字段是一个[字符字段](https://docs.djangoproject.com/en/dev/ref/models/fields/#charfield)，它被限制为 45 个字符。

*   `description`是另一个 CharField，但限制为 250 个字符。

*   `created`是一个[日期时间字段](https://docs.djangoproject.com/en/dev/ref/models/fields/#datetimefield)，顾名思义，它存储照片创建的日期和时间。

*   `image`是一个[图像域](https://docs.djangoproject.com/en/dev/ref/models/fields/#integerfield)。它将图像上传到`media/photos`并存储文件所在的 URL。稍后我们将看到如何设置媒体文件。

*   `submitter`是一个[外键](https://docs.djangoproject.com/en/dev/ref/models/fields/#foreignkey)，这意味着它与一个用户和上传的照片的关系。这样我们就可以过滤哪个用户上传了照片。

*   最后，`tags`是一个[标签管理器](https://django-taggit.readthedocs.io/en/latest/getting_started.html)，允许我们通过标签对主题进行分类。

另一方面，`__str__`方法指示每个对象将如何在管理区域中显示。稍后，我们将设置管理员并创建我们的第一个对象。

为了基于我们创建的模型创建一个数据库，我们首先需要进行迁移，然后运行它们。

输入项目根目录，并使用带有以下参数的`manage.py`脚本:

```
python manage.py makemigrations

python manage.py migrate 
```

`makemigrations`命令将基于`Photo`模型创建一个迁移文件。

*注意:[迁移](https://docs.djangoproject.com/en/dev/topics/migrations/#module-django.db.migrations)是基于模型在数据库中产生变化的 Python 脚本。*

通过打开`photoapp/migrations/0001_initial.py`文件，我们可以确切地看到迁移发生了什么:

```
# photoapp/migrations/0001_initial.py
# imports ...
class Migration(migrations.Migration):

    initial = True

    dependencies = [
        ('taggit', '0003_taggeditem_add_unique_index'),
        migrations.swappable_dependency(settings.AUTH_USER_MODEL),
    ]

    operations = [
        migrations.CreateModel(
            name='Photo',
            fields=[
                ('id', models.BigAutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
                ..... 
```

提示:不要手动修改迁移文件。所有的迁移必须由 Django 自动生成。

`migrate`命令通过运行所有迁移来创建数据库表。

运行这两个命令后，您应该在项目根文件夹中看到一个 SQLite 数据库。如果我们用 [DB 浏览器](https://sqlitebrowser.org/)检查它，我们将看到与`Photo`模型相关的所有字段。

![SQlite Visualizer](img/d8e814514eeefa06a5062c77f10bc2db.png)

## 管理开发中的媒体文件

这款照片分享应用严重依赖媒体文件。一切都是为了分享图像，不是吗？

Django 中的媒体文件都是用户上传的文件。现在，我们将在开发中设置媒体文件，因为我们将只通过本地服务器与应用程序交互。

为了在开发中启用媒体文件，我们在设置文件中创建了 [MEDIA_URL](https://docs.djangoproject.com/en/dev/ref/settings/#media-url) 和 [MEDIA_ROOT](https://docs.djangoproject.com/en/dev/ref/settings/#media-root) 变量。此外，我们需要修改整个项目的`urlpatterns`,以提供来自本地服务器的媒体文件。

首先，我们需要编辑`config/settings.py`文件，并在文件末尾添加以下代码:

```
# config/settings.py

# Other settings ...

MEDIA_URL = '/media/'

MEDIA_ROOT = BASE_DIR / 'media/' 
```

`MEDIA_URL`是处理上传到`MEDIA_ROOT`文件夹的所有媒体的 URL。在这种情况下，绝对媒体 URL 应该是这样的:`http://localhost:8000/media/`。

另一方面，`MEDIA_ROOT`是指向存放所有媒体的文件夹的路径。

记住，因为我们使用的是 [pathlib](https://docs.python.org/3/library/pathlib.html) 库，所以我们能够用`/`连接路径。

我们可以认为`MEDIA_ROOT`是上传图像的物理存储器，而`MEDIA_URL`是指向该存储器的 URL。

如果我们希望 Django 管理媒体文件，我们需要修改项目 URL:

```
# config/urls.py

# New imports
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    # Main app
    path('', include('photoapp.urls')),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT) 
```

考虑到这一点，上传照片的绝对网址会是:`http://localhost:8000/media/photos/`。这是因为我们将`upload_to`属性设置为`photos/`。

*注意:接受用户上传的文件可能会很危险。看看这张[安全注意事项](https://docs.djangoproject.com/en/dev/topics/security/#user-uploaded-content-security)的清单。*

当使用公开的应用程序时，我们必须小心媒体文件。我们可能会遭受拒绝服务攻击。用户也可能上传恶意内容，所以推荐的方法是总是使用 [CDN](https://www.sitepoint.com/what-is-a-cdn-and-how-does-it-work/) 来解决这类问题。

现在，你可以忘记安全问题，因为我们正在进行一个开发项目，而`ImageField`只接受一组预定义的扩展。

您可以通过在 Django shell 中运行以下代码来检查这些有效的扩展(确保您的`venv`被激活):

```
$ python manage.py shell

>>> from django.core.validators import get_available_image_extensions
>>> get_available_image_extensions()
['blp', 'bmp', 'dib', 'bufr', 'cur', 'pcx', 'dcx', 'dds', 'ps', 'eps', 'fit', 'fits', 'fli', 'flc', 'ftc', 'ftu', 'gbr', 'gif', 'grib', 'h5', 'hdf', 'png', 'apng', 'jp2', 'j2k', 'jpc', 'jpf', 'jpx', 'j2c', 'icns', 'ico', 'im', 'iim', 'tif', 'tiff', 'jfif', 'jpe', 'jpg', 'jpeg', 'mpg', 'mpeg', 'mpo', 'msp', 'palm', 'pcd', 'pdf', 'pxr', 'pbm', 'pgm', 'ppm', 'pnm', 'psd', 'bw', 'rgb', 'rgba', 'sgi', 'ras', 'tga', 'icb', 'vda', 'vst', 'webp', 'wmf', 'emf', 'xbm', 'xpm'] 
```

## 用 Django Admin 测试模型

Django admin 是一个内置的接口，管理用户可以在其中对项目的注册模型进行 CRUD 操作。

现在我们已经创建了照片模型并设置了媒体文件，是时候通过管理页面创建我们的第一个`Photo`对象了。

为此，我们必须将`Photo`模型注册到管理页面中。让我们打开`photoapp/admin.py`，导入照片模型，并将其作为参数传递给`admin.site.register`函数:

```
# photoapp/admin.py

from django.contrib import admin
from .models import Photo # We import the photo model

# Register your models here.
admin.site.register(Photo) 
```

接下来，是时候创建一个超级用户来访问管理页面了。我们可以使用以下命令来实现这一点:

```
python manage.py createsuperuser

Username: daniel 
Email address: 
Password: 
Password (again): 
Superuser created successfully 
```

您可以暂时让超级用户没有电子邮件，因为我们使用默认的 auth 用户。

创建超级用户后，跳转到浏览器并导航到[http://localhost:8000/admin](http://localhost:8000/admin)。

它会将您重定向到登录页面，在这里您需要填写您的凭证(您创建用户时使用的凭证)。

![Django admin login page](img/e64a0b7dc1e48a39e106b4737ca948b6.png)

输入我们的凭据后，我们将可以访问一个简单的仪表板，在那里我们可以开始创建照片。只需点击照片部分，然后点击**添加**按钮。

![Django dashboard](img/6d80f2b817578bcaf8387434c988abe1.png)

这是填充创建字段的样子。

![Filling out content](img/3ad069b5e0cf43491213f202f09ea7db.png)

上传图像可以简单地通过拖放完成。

![Uploading images](img/e1f3e6b2b96546faa8ae23fbcbab873b.png)

点击**保存**按钮后，我们会看到一个显示所有已创建照片的仪表盘。

![Photo dashboard](img/d164e087745d0d59bd43f2d29e74d8bf.png)

## 用视图处理 Web 响应

我们已经定义了一个工作应用的数据库模式，甚至用 Django admin 创建了一些对象。但是我们还没有触及任何 web 应用最重要的部分——与用户的交互！

在本节中，我们将构建照片共享应用程序的视图。

*一般来说，**视图**是一个 Python 可调用的(类或函数),接受请求并返回响应。*

根据 [Django 文档](https://docs.djangoproject.com/en/dev/topics/http/views/)，我们应该把我们所有的视图放在每个应用程序中一个名为`views.py`的文件中。当我们启动应用程序时，已经创建了该文件。

我们有两种创建视图的主要方法:使用基于函数的视图(fbv)或基于类的视图(cbv)。

cbv 是重用代码的最佳方式——通过将 Python 类继承的能力应用到我们的视图中。

在我们的应用程序中，我们将使用通用视图，这允许我们通过继承 Django [预构建的类](https://docs.djangoproject.com/en/dev/topics/class-based-views/generic-display/)来创建简单的 CRUD 操作。

在开始之前，我们将导入构建视图所需的所有东西。打开`photoapp/views.py`文件并粘贴以下代码:

```
# photoapp/views.py
from django.shortcuts import get_object_or_404

from django.core.exceptions import PermissionDenied

from django.views.generic import ListView, DetailView, CreateView, UpdateView, DeleteView

from django.contrib.auth.mixins import LoginRequiredMixin, UserPassesTestMixin

from django.urls import reverse_lazy

from .models import Photo 
```

让我们看看我们在这里导入了什么:

*   `get_object_or_404`是一个快捷方式，允许我们从数据库中检索对象，防止`DoesNotExists`错误并引发 HTTP 404 异常。

*   被调用时引发 HTTP 403 异常。

*   预构建的`generic`视图帮助我们用几行代码构建 CRUD 功能。

*   我们将使用`LoginRequiredMixin`和`UserPassesTestMixin`来断言用户在访问视图时拥有正确的权限。

*   `reverse_lazy`在 CBVs 中用于将用户重定向到特定的 URL。

*   我们需要导入`Photo`来检索和更新数据库行(照片对象)。

*注:你可以在 GitHub 上访问 [views.py](https://github.com/sitepoint-editors/Django-photo-app/blob/main/config/photoapp/views.py) 文件。*

### 照片列表视图

通用的[列表视图](https://docs.djangoproject.com/en/dev/ref/class-based-views/generic-display/#listview)将帮助我们显示一个模型的许多对象。稍后我们将与`DetailView`进行比较。

在本节中，我们将构建两个主要视图。`PhotoListView`将任何用户上传的所有照片作为上下文传递，`PhotoTagListView`将标签 slug 作为参数来显示照片。

下面的代码定义了继承自`ListView`的`PhotoListView`:

```
# photoapp/views.py

class PhotoListView(ListView):

    model = Photo     

    template_name = 'photoapp/list.html'

    context_object_name = 'photos' 
```

首先，我们继承了`ListView`，因此从该类接收所有行为。

*记住，你可以随时在[官方 GitHub repo](https://github.com/django/django/blob/d270dd584e0af12fe6229fb712d0704c232dc7e5/django/views/generic/list.py#L194) 中查看任何 Django 类的源代码。*

然后，我们定义从中读取数据的模型、将要使用的模板(稍后我们将构建前端)，以及可以用来访问模板中数据的上下文对象的名称。

现在，是时候宣布`PhotoTagListView`了。这个视图稍微复杂一点，因为我们必须使用`get_queryset()`和`get_context_data()`方法:

```
# photoapp/views.py
class PhotoListView(ListView): ...

class PhotoTagListView(PhotoListView):

    template_name = 'photoapp/taglist.html'

    # Custom method
    def get_tag(self):
        return self.kwargs.get('tag')

    def get_queryset(self):
        return self.model.objects.filter(tags__slug=self.get_tag())

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context["tag"] = self.get_tag()
        return context 
```

这里，我们继承了`PhotoListView`的所有属性。这意味着我们使用相同的`model`和`context_object_name`，但是我们改变了`template_name`。

除了我们正在处理自定义方法之外，这个视图可能与前一个视图相同。

我们正在创建一个自定义方法`get_tag`来接收来自响应的标签 slug，Django 将获取并返回它。我们这样做是因为我们要在两个地方使用这个函数。

默认情况下，`get_queryset`方法被设置为返回`self.model.objects.all()`。我们已经对其进行了修改，只返回标记有传递给 URL 的 slug 的照片对象。

最后，`get_context_data`被修改为也返回传递给 URL 的标签。这是因为我们稍后将在模板中显示它。

### 照片细节视图

这个视图是一个简单的`DetailView`视图，显示与一张独特照片相关的所有数据。这包括所需照片的标题、描述和标签:

```
# photoapp/views.py

class PhotoListView(ListView): ...
class PhotoTagListView(PhotoListView): ...

class PhotoDetailView(DetailView):

    model = Photo

    template_name = 'photoapp/detail.html'

    context_object_name = 'photo' 
```

我们做了与列表视图几乎相同的过程。唯一的区别是我们返回单个对象而不是多个，并且使用不同的模板。

### 创建照片视图

此视图仅允许用户在登录后创建照片对象。我们不希望匿名用户能够上传内容到我们的平台。那太可怕了！

用 Django 保护这一功能的最简单方法是创建一个从`CreateView`和 [LoginRequiredMixin](https://docs.djangoproject.com/en/dev/topics/auth/default/#the-loginrequired-mixin) 继承的类。`LoginRequiredMixin`测试用户是否登录。如果用户没有登录，他们将被重定向到登录页面(我们将在后面构建):

```
# photoapp/views.py

class PhotoListView(ListView): ...
class PhotoTagListView(PhotoListView): ...
class PhotoDetailView(DetailView): ...

class PhotoCreateView(LoginRequiredMixin, CreateView):

    model = Photo

    fields = ['title', 'description', 'image', 'tags']

    template_name = 'photoapp/create.html'

    success_url = reverse_lazy('photo:list')

    def form_valid(self, form):

        form.instance.submitter = self.request.user

        return super().form_valid(form) 
```

在这个视图中，Django 将创建一个带有`title`、`description`、`image`和`tags`字段的表单。

我们还使用了`sucess_url`属性。如果照片创建成功，用户将被重定向到照片仪表板。

如果我们仔细看看`form_valid`方法，我们会注意到它将发出请求的用户设置为照片表单的提交者。

### 更新和删除照片视图

我们希望用户能够修改或删除照片*只有*如果他们是提交者。

如果我们使用 CBVs，处理条件认证可能会很困难。然而，我们可以利用 [TestMixins](https://docs.djangoproject.com/en/dev/topics/auth/default/#django.contrib.auth.mixins.UserPassesTestMixin) 来完成这项任务。

让我们创建一个测试 mixin `UserIsSubmitter`来检查试图更新或删除照片的用户是否真的提交了照片:

```
# photoapp/views.py

class PhotoListView(ListView): ...
class PhotoTagListView(PhotoListView): ...
class PhotoDetailView(DetailView): ...
class PhotoCreateView(LoginRequiredMixin, CreateView): ...

class UserIsSubmitter(UserPassesTestMixin):

    # Custom method
    def get_photo(self):
        return get_object_or_404(Photo, pk=self.kwargs.get('pk'))

    def test_func(self):

        if self.request.user.is_authenticated:
            return self.request.user == self.get_photo().submitter
        else:
            raise PermissionDenied('Sorry you are not allowed here') 
```

首先，我们创建了一个自定义方法`get_photo`，它返回一个照片对象，并在 URL 中指定了主键。如果照片不存在，就会引发 HTTP 404 错误。

然后我们定义了测试函数。只有当用户登录并且是照片提交者时，它才会返回 true。

如果用户没有登录，它将引发一个 [PermissionDenied](https://docs.djangoproject.com/en/dev/ref/exceptions/#permissiondenied) 异常。

另一方面，`PhotoUpdateView`和`PhotoDeleteView`是我们创建的 mixin 的子节点，但也分别是`UpdateView`和`DeleteView`:

```
# photoapp/views.py

class PhotoListView(ListView): ...
class PhotoTagListView(PhotoListView): ...
class PhotoDetailView(DetailView): ...
class PhotoCreateView(LoginRequiredMixin, CreateView): ...
class UserIsSubmitter(UserPassesTestMixin): ...

class PhotoUpdateView(UserIsSubmitter, UpdateView):

    template_name = 'photoapp/update.html'

    model = Photo

    fields = ['title', 'description', 'tags']

    success_url = reverse_lazy('photo:list')

class PhotoDeleteView(UserIsSubmitter, DeleteView):

    template_name = 'photoapp/delete.html'

    model = Photo

    success_url = reverse_lazy('photo:list') 
```

`PhotoUpdateView`继承了`UserIsSubmitter` mixin 的测试功能和`UpdateView`的更新功能。

`fields`属性定义了用户能够编辑的字段。我们不希望图像被更改，也不希望创建日期或提交者被更改。

另一方面，`PhotoDeleteView`也继承了测试功能，但删除了照片，而不是更新它。

如果一切顺利，这两种视图都会将用户重定向到列表 URL。

风景就这些了。现在，让我们创建一个简单的身份验证应用程序并完成项目。

## URL 模式

我们快到了。我们已经定义了数据库模式以及用户将如何创建和更新照片。让我们看看如何处理照片共享应用程序的 URL 配置。

你还记得我们在项目开始时创建了一个空的`urlpatterns`变量吗？是时候填充它了！

首先，让我们导入我们需要的所有视图和函数:

```
# photoapp/urls.py

from django.urls import path

from .views import (
    PhotoListView,
    PhotoTagListView,
    PhotoDetailView,
    PhotoCreateView,
    PhotoUpdateView,
    PhotoDeleteView
) 
```

[路径函数](https://docs.djangoproject.com/en/dev/ref/urls/#path)接收两个参数`route`和`view`，以及一个可选参数`name`，它被用作名称空间的一部分:

```
# photoapp/urls.py
app_name = 'photo'

urlpatterns = [
    path('', PhotoListView.as_view(), name='list'),

    path('tag/<slug:tag>/', PhotoTagListView.as_view(), name='tag'),

    path('photo/<int:pk>/', PhotoDetailView.as_view(), name='detail'),

    path('photo/create/', PhotoCreateView.as_view(), name='create'),

    path('photo/<int:pk>/update/', PhotoUpdateView.as_view(), name='update'),

    path('photo/<int:pk>/delete/', PhotoDeleteView.as_view(), name='delete'),
] 
```

解释这个配置时，`app_name`变量声明了应用程序的名称空间。

这意味着无论我们是在视图中使用`reverse`函数，还是在模板中使用`{% url %}`标签，我们都需要使用下面的名称空间:

```
photo:<<url_name>> 
```

如果您想了解更多关于 Django URL dispatcher 如何工作的信息，请随意阅读[文档](https://docs.djangoproject.com/en/dev/topics/http/urls/)。

## 认证系统

在这个项目中，我们将使用默认的 [Django 认证系统](https://docs.djangoproject.com/en/dev/topics/auth/default/#user-objects)。

这是因为我们的主要焦点是尽快拥有一个功能应用程序。但是，我们将创建一个自定义应用程序，因为我们希望在项目中添加注册功能。

首先，我们创建一个`users`应用程序，并执行与`photoapp`相同的安装过程:

```
python manage.py startapp users

# config/settings.py

INSTALLED_APPS = [
    ...

    # 3rd party apps
    'taggit',

    # Custom apps
    'photoapp',
    'users',
] 
```

接下来，我们创建`urls.py`文件，就像我们创建照片应用程序一样:

```
cd users/
touch urls.py 
```

然后，我们将用户的 URL 包含在整个项目中:

```
# config/urls.py

urlpatterns = [
    path('admin/', admin.site.urls),
    # Main app
    path('', include('photoapp.urls')),
    # Auth app
    path('users/', include('users.urls')),

] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT) 
```

然后我们编写一个`SignUpView`来允许用户通过站点注册:

```
# users/views.py

from django.views.generic import CreateView

from django.contrib.auth import authenticate, login

from django.contrib.auth.forms import UserCreationForm

from django.urls import reverse_lazy

class SignUpView(CreateView):

    template_name = 'users/signup.html'

    form_class = UserCreationForm

    success_url = reverse_lazy('photo:list')

    def form_valid(self, form):
        to_return = super().form_valid(form)

        user = authenticate(
            username=form.cleaned_data["username"],
            password=form.cleaned_data["password1"],
        )

        login(self.request, user)

        return to_return 
```

这个视图是一个 [CreateView](https://docs.djangoproject.com/en/dev/ref/class-based-views/generic-editing/#django.views.generic.edit.CreateView) ，与内置的 [UserCreationForm](https://docs.djangoproject.com/en/dev/topics/auth/default/#django.contrib.auth.forms.UserCreationForm) 一起创建一个新用户。

我们使用`form_valid`方法登录用户，然后将他们重定向到照片仪表板。

我们将创建一个登录视图，因为我们想要使用一个定制模板来显示登录页面。为此，我们将导入内置的`LoginView`并从中继承:

```
# Previous imports
from django.contrib.auth.views import LoginView

class SignUpView(CreateView): ...

class CustomLoginView(LoginView):

    template_name = 'users/login.html' 
```

最后，是时候创建 URL 路由了:

```
# users/urls.py
from django.urls import path

from django.contrib.auth.views import LogoutView

from .views import SignUpView, CustomLoginView

app_name = 'user'

urlpatterns = [
    path('signup/', SignUpView.as_view(), name='signup'),
    path('login/', CustomLoginView.as_view(), name='login'),
    path('logout/', LogoutView.as_view(), name='logout'),
] 
```

我们再次使用了`app_name`变量。因此，用户应用程序的名称空间应该是这样的:

```
user:<<url_name>> 
```

我们设置了三个网址。`signup/`和`login/`使用我们创建的定制视图，但是`logout/` URL 使用 Django 内置的`LogoutView`。

在继续之前，让我们在`config/settings.py`文件中配置认证重定向:

```
# Other settings ...
USE_TZ = True

# Django Authentication
LOGIN_URL = 'user:login'
LOGIN_REDIRECT_URL = 'photo:list'

LOGOUT_REDIRECT_URL = 'photo:list' 
```

这告诉 Django，登录 URL 是定制的用户登录 URL，当用户登录时，他们必须被重定向到照片仪表板。

## 前端

用 Django 构建完后端(用户看不到的)，就该构建前端了(用户确实看到的)。

为此，我们将使用 [Django 模板语言](https://docs.djangoproject.com/en/dev/ref/templates/language/)和 [Bootstrap 5](https://getbootstrap.com/docs/5.0/getting-started/introduction/) 。这允许我们动态地生成 HTML，并根据数据库的状态产生不同的输出。通过使用模板继承，我们可以节省大量代码。使用 Bootstrap 5 意味着我们将不再使用静态文件。

### 编写基本模板

在这一节中，我们将构建`base.html`文件，它是所有其他人将继承的模板。

为此，我们必须更改设置文件中`TEMPLATES`变量内的`DIRS`键:

```
# config/settings.py

TEMPLATES = [
    {
        # Options ..
        'DIRS': [BASE_DIR / 'templates'],
        'APP_DIRS': True,
        # More options
    },
] 
```

Django 的默认行为是在每个 app 的`templates/`文件夹内搜索模板文件。

比如照片分享 app 的模板可以在`photoapp/templates`找到。用户应用程序(`users/templates`)也是如此。

通过将`DIRS`键分配给`[BASE_DIR / 'templates']`，我们告诉 Django 也在名为`templates`的文件夹中搜索模板。

在项目的根目录下创建一个目录`templates`(文件`manage.py`所在的位置)并触摸`base.html`和`navbar.html`模板:

```
ls
# manage.py

mkdir templates && cd templates
touch base.html navbar.html 
```

总结我们项目的模板可以在以下三个目录中找到:

```
.
├── photoapp
│   └── templates
│       └── photoapp
├── templates
└── users
    └── templates
        └── users 
```

请记住，你可以随时在 [GitHub 库](https://github1s.com/sitepoint-editors/Django-photo-app/tree/main/config/templates)上查看项目结构。

在`base.html`模板中，我们将设置基本的 HTML 结构、一些元标签、到引导 CDN 的链接以及其他模板将使用的块:

```
<!-- templates/base.html -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Django Photo Sharing app</title>
    <link
      href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0/dist/css/bootstrap.min.css"
      rel="stylesheet"
      integrity="sha384-wEmeIV1mKuiNpC+IOBjI7aAzPcEZeedi5yW5f2yOq55WWLwNGmvvx4Um1vskeMj0"
      crossorigin="anonymous"
    />

    <link
      rel="stylesheet"
      href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css"
      integrity="sha512-iBBXm8fW90+nuLcSKlbmrPcLa0OT92xO1BIsZ+ywDWZCvqsWgccV3gFoRBv0z+8dLJgyAHIhR35VZc2oM/gI1w=="
      crossorigin="anonymous"
    />
  </head>
  <body>
    {% include 'navbar.html' %}

    <div class="container mt-4">
    {% block body %} 

    {% endblock body %}

    </div>

    <script
      src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0/dist/js/bootstrap.bundle.min.js"
      integrity="sha384-p34f1UUtsS3wqzfto5wAAmdvj+osOnFyQFpp4Ua3gs/ZVWx6oOypYoCJhGGScy+8"
      crossorigin="anonymous"
    ></script>
  </body>
</html> 
```

`{% include %}`标签(顾名思义)包含了`base.html`文件中所选模板的所有代码。

因此，`navbar.html`中的所有代码都将放在主体的开头。

注意:这里有很多 HTML 和引导程序。请随意全部复制，因为这不是本教程的重点。

下面是导航栏的 HTML 模板代码。这个导航栏将包含一些逻辑来显示登录页面的链接，以防用户没有登录:

```
<!-- templates/navbar.html -->
<nav class="navbar navbar-expand-md navbar-dark bg-dark">
  <div class="container-fluid">
    <a class="navbar-brand" href="{% url 'photo:list' %}">Photo App</a>
    <button
      class="navbar-toggler"
      type="button"
      data-bs-toggle="collapse"
      data-bs-target="#navbarTogglerDemo02"
      aria-controls="navbarTogglerDemo02"
      aria-expanded="false"
      aria-label="Toggle navigation"
    >
      <span class="navbar-toggler-icon"></span>
    </button>
    <div
      class="collapse navbar-collapse flex-row-reverse"
      id="navbarTogglerDemo02"
    >
      <ul class="navbar-nav">
        {% if user.is_authenticated %}

        <li class="nav-item">
          <a class="nav-link active" href="{% url 'photo:create' %}">Add a photo</a>
        </li>
        <li class="nav-item">
          <a class="nav-link active" href="#">Hi {{user.username}}</a>
        </li>
        {% else %}

        <li class="nav-item">
          <a href="{% url 'user:login' %}" class="btn btn-sm btn-danger"
            >Sign In</a
          >
        </li>
        {% endif %}
      </ul>
    </div>
  </div>
</nav> 
```

下面是用户登录时模板的显示方式。

![Navbar when the user is logged in](img/2b5c37a712511ac10fd773081afbba69.png)

下面是用户未登录时显示的内容。

![Navbar when the user isn't logged in](img/965854ce81d28ecbede3a62272f7fdaf.png)

如果您的浏览器出现错误，请不要担心。我们还没有建立照片共享模板。

## 照片共享模板

我们将编写照片共享应用程序中所需的所有文件。这包括用于完成 CRUD 操作的模板。

所有这些模板都将扩展`base.html`模板，并位于`photoapp/templates/photoapp`目录中。

但是在使用模板中的表单之前，我们将使用[Django crisp forms](https://django-crispy-forms.readthedocs.io/en/latest/)来格式化我们的应用程序:

```
pip install django-crispy-forms 
```

同样，`crispy_forms`是一个 Django 应用程序，我们需要将它包含在`INSTALLED_APPS`列表中:

```
# config/settings.py

INSTALLED_APPS = [
    ...

    # 3rd party apps
    'taggit',
    'crispy_forms',

    # Custom apps
    'photoapp',
    'users',
]

# Indicates the frontend framework django crispy forms will use
CRISPY_TEMPLATE_PACK = 'bootstrap4' 
```

我们使用 Bootstrap 4 的模板包，因为 Bootstrap 表单类在第 4 版和第 5 版之间是兼容的(在撰写本文时)。

您可能记得我们在`photoapp/views.py`上使用了以下模板名称:

```
'photoapp/list.html' 
'photoapp/taglist.html' 
'photoapp/detail.html' 
'photoapp/create.html' 
'photoapp/update.html' 
'photoapp/delete.html' 
```

这意味着所有这些模板都将位于`photoapp/templates/photoapp`中。

要创建这个文件夹，请转到照片共享应用程序，创建一个目录`templates/`，并在其中创建另一个名为`photoapp/`的文件夹:

```
cd photoapp/
mkdir -p templates/photoapp/
cd templates/photoapp/ 
```

现在创建我们在视图中声明的所有模板:

```
touch list.html taglist.html detail.html create.html update.html delete.html 
```

### 列表模板

`list.html`将从`base.html`模板继承，因此所有的 HTML 结构都将出现在源代码中:

```
<!-- photoapp/templates/photoapp/list.html -->
{% extends 'base.html' %} 

{% block body %}

<div class="row">
  {% for photo in photos %}
  <div class="col-lg-3 col-md-4 col-xs-6">
    <a href="{% url 'photo:detail' photo.id %}" class="d-block mb-4 h-100">
      <img src="{{photo.image.url}}" class="img-fluid rounded" alt="{{photo.title}}" />
    </a>
  </div>
  {% endfor %}
</div>

{% endblock body %} 
```

我们将模板标签[用于循环](https://docs.djangoproject.com/en/dev/ref/templates/builtins/#for)，循环遍历照片，并用引导行和引导列显示它们。

不要忘记在 Django 管理中创建多个照片对象。

访问 [localhost:8000/](http://localhost:8000/) 来看看模板是什么样子的。

![List template](img/edbf8379884618dd08ac3417151e57c7.png)

`taglist.html`模板将继承我们刚刚创建的`list.html`:

```
<!-- photoapp/templates/photoapp/taglist.html -->
{% extends 'photoapp/list.html' %}

{% block body %}

<div class="alert alert-primary">
    <h2 class="text-center">Photos with the tag {{tag}}</h2>
</div>

{{ block.super }}

{% endblock body %} 
```

我们只是稍微修改了一下这个模板。这就是为什么我们要调用`{{ block.super }}`，它包含了`list.html`模板的 body 块中的所有代码。

在继续之前，用标签`code`创建几个对象。

转到 [localhost:8000/tag/code/](http://localhost:8000/tag/code/) ，其中 code 是标签的 slug。

![List template tag](img/b5233956ea55ea9d59a213c3d2c2ae4d.png)

请记住,`taglist` URL 具有以下形式:

```
'localhost://8000/tag/<slug:tag>/' 
```

这里，`<slug:tag>`指的是标签的名称。

### 详细照片模板

让我们编辑`detail.html`模板，以便能够详细查看我们的照片:

```
<!-- photoapp/templates/photoapp/detail.html -->
{% extends 'base.html' %} 

{% block body %}
<div class="mx-auto">
  <h1 class="text-center">{{ photo.title }}</h1>
  <p class="text-center fw-light">Uploaded on: {{photo.created}} <br> By {{photo.submitter.username}}</p>
  {% if user == photo.submitter %}
    <p class="text-center">
      <span><a href="{% url 'photo:update' photo.id %}" class="text-primary px-2">Update</a></span>
      <span><a href="{% url 'photo:delete' photo.id %}" class="text-danger px-2">Delete</a></span>
    </p>
  {% endif %}
</div>
<div class="row pb-5">
  <div class="col-md-8">
    <img src="{{photo.image.url}}" alt="" width="100%" />
  </div>
  <div class="col-md-4">
    <h4>More about this photo:</h4>
    <ul class="list-group list-group-horizontal-lg list-unstyled py-4">
      {% for tag in photo.tags.all %}
        <li><a href="{% url 'photo:tag' tag.slug %}" class="btn btn-sm list-group-item list-group-item-primary">{{tag.name}}</a></li>
      {% endfor %}
    </ul>
    <p>{{ photo.description }}</p>
  </div>
</div>

{% endblock body %} 
```

在深入研究功能之前，让我们看看模板是什么样子的。跟随 [localhost:8000/photo/1](http://localhost:8000/photo/1/) 。

![Photo in Detail](img/0449a410b1cd2124b11cebc31128ef26.png)

这里，我们通过点符号从模板中访问照片属性。那是因为`photo.submitter.username`等于`daniel`。

我们实现了一点逻辑来显示更新或删除照片的链接，以防用户也是提交者。

最后，我们显示了迭代过`photo.tags.all`的照片的所有标签。

### 创建照片模板

下一个模板将包含一个简洁的表单，这样我们就不必手动显示表单。姜戈会为我们做的:

```
<!-- photoapp/templates/photoapp/create.html -->
{% extends 'base.html' %}
{% load crispy_forms_tags %}

{% block body %}
<div class="mx-auto">
  <h1 class="mt-3 text-center">Add photo</h1>
</div>
<div class="form-group">
  <form action="" method="post" enctype="multipart/form-data">
    {% csrf_token %}
    {{ form|crispy }}
    <button type="submit" class="btn btn-success mb-3">Add Photo</button>
  </form>
</div>
{% endblock body %} 
```

每次我们使用 crispy 表单时，我们都需要加载带有`{% load crispy_forms_tags %}`的标签。

包含`enctype="multipart/form-data"`是非常重要的，因为如果我们不这样做，文件就不会被上传。这里是对在表单中使用它的[含义](https://stackoverflow.com/questions/4526273/what-does-enctype-multipart-form-data-mean)的一个很好的回应。

每个 Django 表单内部必须包含一个`{% csrf_token %}`。您可以在“[跨站请求防伪](https://docs.djangoproject.com/en/dev/ref/csrf/)”页面了解更多关于此标签的信息。

注意我们是如何简单地用`{{form|crispy}}`显示表单的。如果您知道 Linux 中有哪些管道，我们正在通过将视图提供的表单重定向到`crispy`过滤器来实现这一点。

转到[添加照片](http://localhost:8000/photo/create/)网址，检查照片是否上传。

![Uploading a photo](img/d0fe7adb70df58f3ef47ef4f8cca1c93.png)

如果一切顺利，我们应该在仪表板中看到添加的照片。

![Added photo](img/64c84bad76b8688189151c825df8f6d5.png)

### 更新和删除模板

让我们先完成照片共享应用程序，然后再开始验证模板。

下面的`update`模板是一个简单的表单，用户可以在其中更新照片的`title`、`description`和`tags`:

```
<!-- photoapp/templates/photoapp/update.html -->
{% extends 'base.html' %}
{% load crispy_forms_tags %}

{% block body %}
<div class="mx-auto">
  <h1 class="mt-3 text-center">Edit photo {{photo}}</h1>
</div>
<div class="form-group">
  <form action="" method="post" enctype="multipart/form-data">
    {% csrf_token %}
    {{ form|crispy }}
    <button type="submit" class="btn btn-success mb-3">Edit Photo</button>
  </form>
</div>
{% endblock body %} 
```

我们可以看看它是如何看待[localhost:8000/photo/1/update](http://localhost:8000/photo/1/update/)的。

![Updating a Photo](img/f4ead91a3b8f790123fb0e8f551a112f.png)

我们还想给用户删除照片的选项。使用以下模板，他们可以决定是否删除照片:

```
<!-- photoapp/templates/photoapp/delete.html -->
{% extends 'base.html' %} 

{% block body %}
<div class="form-group mx-auto">
  <h2 class="text-center">
    You are going to <span class="text-danger">delete</span>: "<i
      >{{ photo }}</i
    >
    "
  </h2>
  <p class="text-center">Are you sure, you want to delete the photo ?</p>
  <div class="form-group">
    <form
      action=""
      method="post"
      class="d-flex flex-column align-items-center justify-content-center"
    >
      {% csrf_token %}
      <div class="row">
        <div class="col">
          <a href="{% url 'photo:detail' photo.id %}" class="btn btn-primary"
            >Cancel</a
          >
        </div>
        <div class="col">
          <button type="submit" class="btn btn-danger">Delete</button>
        </div>
      </div>
      <p>This action is irreversible</p>
    </form>
  </div>
</div>

{% endblock body %} 
```

删除页面如下所示。

![Delete template](img/60c41c973ca411ebd15121ea33296f29.png)

如果用户决定取消，他们会被重定向到该照片的详细页面。

## 用户验证模板

本节的目的是编写与认证相关的所有模板。我们将编写`signup.html`和`login.html`模板。

与照片分享应用程序类似，以下所有模板都将位于一个双文件夹结构中:`users/templates/users/`。

进入用户应用程序，创建模板所在的文件夹:

```
# Enter to the project root directory
cd ../../../

cd users/
mkdir -p templates/users/ 
```

在该文件夹中创建注册和登录模板文件:

```
cd templates/users/
touch signup.html login.html 
```

下面是`signup.html`模板的模板代码:

```
<!-- users/templates/users/signup.html -->
{% extends 'base.html' %} 
{% load crispy_forms_tags %}
{% block body %}
<div class="mx-auto">
  <div class="form-group">
    <form action="" method="post">
      {% csrf_token %} 
      {{ form|crispy }}
      <button type="submit" class="btn btn-danger w-100 my-3">Create account</button>
    </form>
  </div>
  {% comment %}  Already Registered {% endcomment %}
  <div class="text-center w-100">
    <p class="text-muted font-weight-bold">
      Already Registered?
      <a href="{% url 'user:login' %}" class="text-primary ml-2"> Login </a>
    </p>
  </div>
</div>
{% endblock body %} 
```

我们可以在[localhost:8000/users/sign up](http://localhost:8000/users/signup/)的浏览器中查看。

![SignUp page](img/39a2a338d2c2ad151e01da02d5e8e5f4.png)

最后但同样重要的是，编写登录模板:

```
<!-- users/templates/users/login.html -->
{% extends 'base.html' %} 
{% load crispy_forms_tags %}

{% block body %}

<div class="mx-auto">
  <div class="form-group">
    <form action="" method="post">
      {% csrf_token %} 
      {{ form|crispy }}
      <button type="submit" class="btn btn-danger w-100 my-3">Sign in</button>
    </form>
  </div>
  {% comment %}  Already Registered {% endcomment %}
  <div class="text-center w-100">
    <p class="text-muted font-weight-bold">
      Don't have an account?
      <a href="{% url 'user:signup' %}" class="text-primary ml-2">Create account</a>
    </p>
  </div>
</div>
{% endblock body %} 
```

![Login Page](img/4fdebb601b2848ecf561b1a4d601f85e.png)

Django 模板允许我们通过多次重用相同的 HTML 来节省大量时间。想象一下，一遍又一遍地复制和粘贴同一个 HTML 要花费多少时间。

完美！现在您有了一个完全正常工作的应用程序。尝试使用它，修改它，甚至扩展它的功能。

## 总结

恭喜你！您已经从头开始创建了一个全栈项目。

Django 是最常用的 Python web 框架。它允许您快速构建复杂的 web 应用程序。

它有许多加速开发过程的内置特性，比如服务器端模板呈现、基于类的视图和模型表单。

Django 还提供了几个第三方包，让你可以选择使用别人的应用。作为一个例子，该项目与 Django taggit 和 Django crispy forms 一起工作。

在本教程中，我们讲述了以下内容:

*   Django CRUD 业务
*   Django 内置的认证系统
*   如何在 Django 中管理媒体文件
*   使用 Django taggit 对内容进行分类
*   用脆表单实现 Django 表单
*   用 Bootstrap 5 编写 Django 模板

持续学习和推进 it 的最佳方式是将您获得的知识应用到新的和具有挑战性的项目中。祝你好运！

## 分享这篇文章