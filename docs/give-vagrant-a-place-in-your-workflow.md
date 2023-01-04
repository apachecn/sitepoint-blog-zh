# 在你的工作流程中给流浪者一个位置

> 原文：<https://www.sitepoint.com/give-vagrant-a-place-in-your-workflow/>

最近我一直忙于开发工作。这对我来说真的有点罪恶的快感。这些天来，人们几乎期望我们开发人员知道一些关于为我们的应用程序正确设置盒子的事情。这是有道理的，我们花了**很多**时间让我们的本地环境达到我们想要的方式。如果我们决定要将 [Redis](http://redis.io/) 应用到我们的应用程序中，我们安装它，了解它一点，然后重新开始工作。

我绝不是一个“系统管理员”类型的家伙，我不得不谷歌什么是一个领口。但是我确实喜欢摆弄虚拟机、EC2 实例之类的东西。过去，我总是倾向于和主厨一起烹饪。我个人的观点是，开发者应该了解他们的应用程序所处的基础设施。它可能并不完美，可能需要优化和更高的安全性，但对于应用程序来说，它应该足够进行第一次尝试了。然后，如果看起来很成功，雇佣那些知道他们在做什么来巩固基础设施的人。

我以前写过关于为了这个目的使用主厨的文章。我使用[主厨 solo](http://wiki.opscode.com/display/chef/Chef+Solo) 来供应虚拟机。这很好，但老实说，这并不理想。测试厨师脚本是繁琐的，你需要快照，测试，吹走，肥皂泡，冲洗，重复。这是非常复杂的。然后，假设您需要与团队成员共享该环境。他们需要经过某种程度的设置，了解主厨在做什么，**然后**他们就会变得富有成效。要是有一种方法能让所有这些设置变得简单就好了。如果你读到这里，你已经知道我说的是[流浪者](http://www.vagrantup.com/)。

## 基础知识

如果你以前没有使用过流浪者，这是一个短暂停留之旅。基本上它运行在 [VirtualBox](https://www.virtualbox.org/) 之上。它是一个依赖项，所以如果你还没有安装，就去安装吧。然后为你的系统下载并安装[流浪汉](http://downloads.vagrantup.com/)。我目前运行的是 Ubuntu 12.10，在运行了`deb`安装包后，我在路径中添加了`/opt/vagrant/bin`。(我也使用 zsh，所以我在我的`~/.zshrc`文件中添加了`PATH=/opt/vagrant/bin:$PATH`行)。

一旦你安装了它，你应该能够运行`vagrant -h`并获得一个运行 have 的命令列表。

流浪汉有一个“箱子”的概念。最好将这些框视为您希望配置的环境的基本配置。安装了 Apache 的 Debian 64 位服务器就是一个基本的例子，为虚拟主机进行配置。

在[vagger box . es](http://www.vagrantbox.es/)中提供了一个预建盒子的综合存储库。我们稍后将开始创建我们自己的盒子，但现在让我们选择一些舒适的东西。使用以下命令添加一个框:

```
vagrant box add precise64 http://files.vagrantup.com/precise64.box
```

下载需要一段时间，但是你只需要做一次。一旦它下载并添加了这个盒子，您就可以根据自己的意愿，基于这个基本配置(我们恰当地将其命名为“precise64”)创建尽可能多的项目。

创建一个目录`vagrant_test`并执行`vagrant init precise64`。这将创造一个`Vagrantfile`。这个`Vagrantfile`文件包含了大量的评论，如果你是流浪者的新手，这些评论会有些陌生。这类似于创建新应用程序时的 Rails `routes.rb`文件:您可能只使用其中的一部分，但它将是一个很好的参考。

我们现在准备启动我们的第一个环境。运行`vagrant up`，让它无头启动虚拟机(即你将看不到虚拟盒子 GUI)。一旦完成，运行`vagrant ssh`，很快，您将进入我们新创建的虚拟环境的外壳。此时，您可以`exit`退出 ssh 会话。如果您现在输入`vagrant status`，您应该会看到虚拟机正在运行。要停止虚拟机，请键入`vagrant halt`。`halt`命令优雅地关闭虚拟机，再次运行`status`将显示虚拟机已关闭。

## 让事情运转起来

到目前为止，我们所做的只是启动一个新的虚拟机，这是毫无意义的。当然，我们需要一些代码。如果您过去使用过 Virtual Box，您会知道您可以在来宾和主机环境之间共享文件夹。在这种情况下，我们希望我们的应用程序代码是共享的。

在`Vagrantfile`中寻找线`config.vm.share_folder "v-data", "/vagrant_data", "../data"`。正如前面的注释告诉我们的，`share_folder`配置有 3 个参数，一个名称或标识符，共享文件夹在虚拟机上的位置，以及在主机操作系统上的位置。

我打算将这个流浪者文件和我的应用程序一起发布，在使用 [EngineYard](https://www.engineyard.com/) 几年后，我通常会选择一个共享文件夹，安装在位置`/data`:

```
config.vm.share_folder "v-data", "/data", "./"
```

通过使用上面的配置，一旦我们引导(`vagrant up`)和 ssh 进入 VM，导航到`/data`目录将显示当前目录是共享的。继续添加/修改文件，您会看到它反映在虚拟机上。非常好的东西，我想你会同意的。

到现在为止，你应该开始看到使用像流浪者这样的东西的好处。我现在可以创建一个**可再现的环境**，只需要使用我的应用程序库中包含的一个文本文件。不再“在我的机器上工作”，不再扯头发。环顾 Github 上的开源项目，你有时会在项目的顶层发现奇怪的`Vagrantfile`,我认为这是一个很棒的实践。

## 提供我们的服务

我们有我们的基本虚拟机和安装在其上的代码库，但它仍然很普通。在某个时候，我们会希望通过添加新的包来定制我们的环境，比如 MySQL 或 ruby 版本管理器，比如 [rbenv](https://github.com/sstephenson/rbenv/) 。

在我看来，流浪者真的抓住了这一点。如果你是一个[主厨](http://www.opscode.com/chef/)粉丝、[傀儡](https://puppetlabs.com/)虎胆龙威或者一个单纯的贝壳纯粹主义者，你可以向流浪者提供服务。我将快速浏览一下用[主厨 Solo](http://wiki.opscode.com/display/chef/Chef+Solo) 做这件事，因为那是我选择的工具。

在流浪者文件中，我们可以配置我们的 cookbooks (Chef 在当前目录的顶层使用‘cookbooks`...) live. By default, Vagrant will look for a directory`cookbooks ’),但是它将采用一个相对路径。在这一点上，设立一些食谱是值得的。

我从 [Opscode 库](https://github.com/opscode-cookbooks/)创建了一个 cookbooks 项目和我需要的子模块 cookbooks。然后，我像这样配置 chef 运行:

```
config.vm.provision :chef_solo do |chef|
    chef.cookbooks_path = "../cookbooks"
    chef.add_recipe 'apt'
    chef.add_recipe 'build-essential'
    chef.add_recipe 'mysql::client'
    chef.add_recipe 'mysql::server'
    chef.json = {
      "mysql" => {
          "server_debian_password" => "secret",
          "server_root_password" => "secret",
          "server_repl_password" => "secret"
      }
    }
  end
```

即使你对 Chef 不太熟悉，也很容易理解。我在这里所做的就是指定我的食谱所在的位置，并添加几个主食 recipe(apt 和 build-essential)。我还在 chef 运行中添加了 mysql 客户端和服务器，指定了一些我想要的属性(root 的密码),如果你看了 MySQL 食谱的话，这些在 README 中有解释。

现在，有了我们现有的流浪者虚拟机，我们可以运行`vagrant reload`(如果它正在运行)或`vagrant up`,我们的虚拟机已经安装了 MySQL 并准备就绪。

使用 Chef with vagger 时有一个小警告，就像 Chef 本身一样，您可以像这样传递一个指向 tarball 的 URL:

```
config.vm.provision :chef_solo do |chef|
    chef.recipe_url = "http://github.com.com/company_name/cookbooks/archive/v0.1.0.tar.gz"
  end
```

在上面的例子中，我在看 GitHub。每次你标记一个项目，一个 tarball 就被创建了，非常适合流浪者设置。

## 返回基础-ics

现在是我喜欢和流浪者一起工作的真正原因了。你还记得在文章开始的时候，我们使用了一个来自[vagger box . es](http://www.vagrantbox.es/)的预制盒子。我们刚刚完成了一个项目可能需要的所有东西的基本安装，或者只是 Rails 应用程序的基本安装？我们可以为这个虚拟机创建一个“盒子”,带着它进入我们的下一个项目，或者传递给朋友。

为了创建包，使用`vagrant package`命令。只需在项目中运行它，就可以打包 VM 以便于分发。我喜欢正确地命名这个盒子文件，所以运行`vagrant package --output base_rails.box`

一旦完成，您将在项目的根目录下拥有一个`base_rails.box`文件。在未来的项目中，我们可以像这样将包包含在`Vagrantfile`中

```
config.vm.box = "base_rails"
# or if it is available for download

# config.vm.box_url = "http://domain/for/base_rails.box
```

package 命令也可以带一个`--vagrantfile`选项。不要与我们在整篇文章中使用的`Vagrantfile`混淆。相反，这是一个`Vagrantfile.pkg`，它只为您希望构建的包指定配置。

例如，如果我们希望包含共享文件夹功能，我们将创建一个`Vagrantfile.pkg`，添加以下内容并运行`vagrant package`命令。

```
Vagrant::Config.run do |config|
  config.vm.share_folder "v-data", "/data", "./"
end
```

现在，一旦虚拟机被打包，发行版将自动与虚拟机共享当前的工作目录。

现在任何人都可以访问盒子文件，并可以简单地初始化和漫游盒子。(`vagrant init base_rails`和`vagrant up`)。

## 包扎

我希望这篇文章能让你对流浪者感到兴奋。我开始用它来为我的 Chef 运行提供测试服务器。我真的看到了共享开发环境的潜力，我相信这是最初存在的真正原因。

然而，事情并没有就此结束。HashiCorp 博客上最近发布的一个版本预览了 AWS 正在使用的流浪者。我个人迫不及待地想让它进入发布状态，这样我就可以用我的手去触摸它了。能够在我自己的开发机器之外提供环境是一个巨大的胜利。我们不再担心如何确保跨环境的一致性，相反，我们可以使用 vagrant 来测试我们的应用程序，在将来的某一天，它们将会存在。

像这样的供应服务对开发者来说是一把真正的瑞士军刀。我们从烹饪书的策展人那里获得了一些“最佳实践”,这些烹饪书是我们用来制作《厨师行动》和《流浪者》的一个很好的工作流程的。值得指出的是，我可能只是在为自己说话，我使用的条款和厨师运行只能让我到此为止。是开发运营部。在为个人应用提供服务器方面，我有足够的知识来应对危险。我没有必要或渴望去进一步调整我的盒子。任何值得生产的东西，我建议从系统管理员那里得到一些专家的建议。

尽管如此,《流浪》让它变得非常有趣。当 AWS-VM-Ware 集成在下个月左右以 MIT 许可证的形式发布时，它只会变得更有趣。

## 分享这篇文章