# 快速提示:将定制 PHP 扩展安装到 Zend Server 7 的 4 个步骤

> 原文：<https://www.sitepoint.com/quick-tip-4-steps-install-custom-php-extensions-zend-server-7/>

Zend Server 7 是管理、部署和监控 PHP 应用程序的优秀工具。我们已经在[这篇快速提示](https://www.sitepoint.com/quick-tip-install-zend-server-7-ubuntu-14-04-vagrant-box/)中介绍了它的安装，并且我们已经在[这篇文章](https://www.sitepoint.com/getting-know-zend-server-7/)中对它做了一些全面的回顾。

在这个快速技巧中，我们将完成在其中安装定制 PHP 扩展的过程。我们将安装 [Phalcon](http://phalconphp.com) ，但是程序对于几乎所有的扩展都是一样的。

## 步骤 1:安装 Zend 服务器

让 ZS 的一个实例运行起来。按照这个[快速提示](https://www.sitepoint.com/quick-tip-install-zend-server-7-ubuntu-14-04-vagrant-box/)去做。

## 第 2 步:修改$PATH

要使用 Zend Server 附带的命令行 PHP 工具，我们需要将它们的路径添加到 system＄PATH 变量中:

```
echo "PATH=\"/usr/local/zend/bin:\$PATH"\" >> ~/.profile
source ~/.profile 
php --version
```

如果最后一个命令执行成功，您就成功了。

然而，这还不够。Phalcon 的安装脚本需要以管理员权限执行，sudo 用户不使用与普通用户相同的 PATH 变量。我们以后再处理这个问题。

## 步骤 3:下载并构建 Phalcon

我们将从安装一些缺失的工具开始:git 和 autoconf。

```
sudo apt-get install git autoconf
cd ~/Code
git clone --depth=1 git://github.com/phalcon/cphalcon.git
cd cphalcon/build
```

由于与 ZS 捆绑在一起的 PHP 开发工具(例如 phpize)位于不同的 bin 文件夹中，root 用户不知道如何获得它们(root 用户使用与普通用户不同的 PATH 变量)。因此，按照 Phalcon 的安装说明运行`sudo ./install`将不起作用。然而，我们可以通过运行`sudo -i`来模拟登录，它保存当前用户的$PATH。

```
sudo -i
cd /home/vagrant/Code/cphalcon/build
./install
```

## 第四步:激活 Phalcon

安装完成后，可以使用`exit`退出 sudo 模式。然后，让我们在`/usr/local/zend/etc/conf.d/`中制作一个`phalcon.ini`文件。

```
sudo vim /usr/local/zend/etc/conf.d/phalcon.ini
```

给它内容:

```
extension=phalcon.so
```

然后，通过 GUI 重新加载 Zend 服务器。进入`Configurations -> PHP`并滚动到扩展列表的底部。费尔康会在那里，满载而归。如果您在`Overview -> Server Info`下查看 PhpInfo，您会注意到 Phalcon 已经安装并处于活动状态。

![](img/fa2bb7235b82a85070dd879da7d429a4.png)

这就是全部了！你现在可以在你所有的 Zend 服务器应用中使用 Phalcon 了！

## 分享这篇文章