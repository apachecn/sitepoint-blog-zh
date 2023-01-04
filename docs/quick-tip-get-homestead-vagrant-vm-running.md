# 快速提示:启动并运行一个家园流浪者虚拟机

> 原文：<https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/>

这个快速提示将帮助你使用一个全新的家园改进版流浪者虚拟机。

*   要了解关于流浪者和为什么你应该使用它，见[这篇文章](https://www.sitepoint.com/re-introducing-vagrant-right-way-start-php/)。
*   [Homestead 改进版](https://github.com/Swader/homestead_improved/)基于 [Homestead](http://laravel.com/docs/master/homestead) ，但有一个额外的好处，即不会强迫你处理 SSH 密钥，不会在全球范围内安装，更加跨平台友好，并附带一个助手脚本来自动配置共享文件夹。
*   我建议为每个新项目创建一个新的 Homestead 改进的 VM，本教程将遵循这样的实践。

* * *

## 先决条件

您需要:

*   [虚拟盒](http://virtualbox.org)
*   [流浪者](http://vagrantup.com)
*   如果在 Windows 上使用 Git 工具，否则使用 Git([下载](https://git-scm.com/downloads))

本教程假设您将使用类似 bash 的终端；Git Bash if on Windows(自带 Git 工具)。

## 入门指南

以下 3 个命令是在任何操作系统上启动一个新的 Homestead 改进的 VM 实例所必需的(显然，用您想要的文件夹名替换`my_project`):

```
git clone https://github.com/swader/homestead_improved my_project cd my_project; mkdir -p Project/public bin/folderfix.sh # this is optional!
```

前两个克隆项目并进入新创建的文件夹。

第三个执行`folderfix`脚本，它与 VM 中的`Code`文件夹共享项目文件夹。这意味着你可以编辑项目文件夹(`my_project`)中的文件，它们会在 VM 中反映出来，反之亦然。这允许您使用主计算机的文本编辑器/ IDE 来编辑 VM 中的项目文件。您也可以通过[编辑`Homestead.yaml`](http://tldrify.com/bax) 中的文件夹字段来手动操作。

## 添加新网站

将每个新站点添加到 Homestead VM 有两个步骤。

### 第一步:设置`etc/hosts`

`etc/hosts`是每个操作系统上都有的文件。在 Windows 上，它在`C:/Windows/System32/drivers/etc/`中，在 OS X 和 Linux 上，它在`/etc/hosts`中。以管理员身份编辑它(一个简单的方法是以管理员身份运行 Sublime Text 这样的文本编辑器),并为您打算开发的每个应用程序添加一个这样的条目:

```
192.168.10.10 homestead.app
```

将`homestead.app`替换成你想要的域名，或者只是添加多个域名。例如，在我自己的`hosts`文件中有这样一段话:

```
192.168.10.10 homestead.app 192.168.10.10 test1.app 192.168.10.10 test2.app 192.168.10.10 test3.app
```

这意味着我可以通过`http://homestead.app`或`http://test1.app`等在浏览器中访问这些域。

### 第二步:

对于您在上述步骤中定义的每个域，您需要添加一个`sites`映射。打开 Homestead 改进回购的克隆副本中的文件`Homestead.yaml`，并为每个文件添加一对。默认值为:

```
sites:  - map: homestead.app
      to:  /home/vagrant/Code/Project/public
```

这意味着`http://homestead.app`(如果你在上面的步骤中把它添加到了`etc/hosts`中)将在虚拟机的文件夹`/home/vagrant/Code/Project/public`中寻找`index.php`。您也可以在虚拟机外部添加这个文件，只需在克隆的 Homestead Improved 虚拟机的根目录下创建一个`Project/public`文件夹，并在那里添加`index.php`——这与在虚拟机内部将它添加到上述位置是一样的，因为文件夹是共享的。

您可以通过添加新地图/对来添加新站点(根据您的需要):

```
sites:  - map: homestead.app
      to:  /home/vagrant/Code/Project/public  - map: test.app
      to:  /home/vagrant/Code/test
```

### 启动和关闭

添加完所有站点后，使用以下命令启动虚拟机:

```
vagrant up
```

进入虚拟机内部:

```
vagrant ssh
```

一旦进入，您就可以像使用常规的生产或开发 linux 服务器一样使用 VM。

### 端口和回火

家园预装了 Blackfire。这是一个非常强大的 PHP 应用程序分析器，它可以检查代码的细节并绘制复杂度图，帮助你加速应用程序。如果你有一个账户，取消`Homestead.yaml`中黑线的注释，把你的数据放在那里。

如果您想要打开额外的端口，例如 Heroku 的端口 5000，取消对`Homestead.yaml`的端口部分的注释并添加值，如下所示:

```
ports:  - send:  5000 to:  5000
```

### 数据库访问

Homestead 预装了 MySQL。其用户为`homestead`，密码为`secret`。要从外部操作系统连接到它(Homestead 没有安装 PhpMyAdmin)，请使用类似 Sequel Pro 或 MySQL Workbench 的工具，并指定以下参数:

*   服务器/端口:192.168.10.10 / 3306 或 127.0.0.1 / 33060
*   用户:家园
*   通行证:秘密

当您连接时，应该会显示默认的`homestead`数据库。

## 结论

现在，您应该能够为您打算构建的每个新应用程序快速设置一个新的 VM 实例。这将允许你无缝地跟随我们所有的教程。如果以上步骤有任何不清楚或者可以/应该简化的地方，请在下面的评论中告诉我。

## 常见问题

### 未指定输入文件

这意味着虚拟机中的服务器无法在您在`Homestead.yaml`文件的`sites`块下指定的文件夹中找到`index.php`文件。通常，这是因为您在已经调配了虚拟机的情况下添加了新站点。要解决这个问题，如果你在虚拟机中，退出虚拟机，然后运行`vagrant provision`。

如果这没有帮助，这意味着您在`sites`块中有一个错误的映射，所以请仔细检查以确保没有打字错误，并且 VM 中的文件夹确实存在。

或者，如果你正在使用 Symfony2 应用，Symfony2 默认使用`web`文件夹而不是`public`来存放`index.php`。这就是为什么 Nginx 在 VM 中找不到这个文件。为了自动解决这个问题，在`sites`下的`Homestead.yaml`中指定一个附加值:`type`，如下所示:

```
sites:  - map: homestead.app
      to:  /home/vagrant/Code/Project/public type: symfony
```

这将自动使用[不同的服务脚本](https://github.com/Swader/homestead_improved/blob/master/scripts/serve-symfony2.sh)。

## 分享这篇文章