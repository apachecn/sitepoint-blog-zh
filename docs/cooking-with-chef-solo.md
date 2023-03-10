# 与主厨 Solo 一起烹饪

> 原文：<https://www.sitepoint.com/cooking-with-chef-solo/>

Chef 被宣传为“一个系统集成框架，旨在为您的整个基础设施带来配置管理的好处”。不管我读了多少遍，我仍然听到头顶上“嗖”的一声。简单地说，我们可以通过我们熟悉的 Ruby 来管理服务器配置。

Chef 的要点是我们创建食谱，这些食谱使用 Chef DSL 来安装和配置我们的服务器所需的包。有多少次你查阅了安装和配置类似 MySQL 的教程？有了 Chef，我们就有了一个受源代码控制的食谱，我们可以随时维护和使用它来构建新的环境。如果您曾经不得不创建自己的试运行和生产环境，并随着应用程序的发展保持一致性，那么这听起来应该是个好消息。

主厨有几种口味。今天我们将重点介绍主厨 Solo，与主厨服务器不同，主厨 Solo 可以在“磁盘”上处理所有内容。食谱和我们需要的任何东西(我们马上会谈到)必须在我们正在配置的服务器上。

## 主厨 Solo 的基础

在继续之前，我们最好在创建 Chef 脚本时建立可用的组件，以前我们称之为“我们需要的任何其他东西”

### 资源配方

很简单，资源就是我们试图在服务器上配置的东西。以 MySQL 为例，我们为该资源编写了一个配方。菜谱是用 Ruby 编写的，它使用块风格的语法建立了我们希望如何配置资源。

```
package "mysql-client" do
  package_name value_for_platform("default" => "mysql-client")
  action :install
end
```

上面的源代码是 MySQL 的 [Opscode 配方的一个删节示例。尽管照原样阅读很有意义，但您可能会对`value_for_platform`方法感到疑惑。它只是根据平台选择您试图配置的资源的正确包名。我为基于 Debian 的系统压缩了这个，但是我们可以传递一个普通的 Ruby Hash 来让 Chef 使用正确的包。如果您还没有点击原始来源:](https://github.com/opscode/cookbooks/blob/master/mysql/recipes/client.rb)

```
package_name value_for_platform(
    [ "centos", "redhat", "suse", "fedora", "scientific", "amazon"] => { "default" => "mysql" },
    "default" => "mysql-client"
  )
```

Chef 怎么知道我们在什么平台上运行？这个问题的答案很好地引入了节点。

### 节点

一个节点是我们的服务器。我们的食谱应用于一个节点。我们可以在这里结束它，但我最近发现在节点上拉开帷幕很方便。

为了找出它需要知道的关于我们的节点/服务器的一切，Chef 使用了一个名为 [Ohai](http://rubygems.org/gems/ohai) 的 gem。你可以自己看看 Chef 是怎么做的。

```
require 'ohai'
o = Ohai::System.new
o.all_plugins
puts o[:platform] # => "linuxmint"
```

## 就像妈妈以前做的一样

这已经是足够的理论了，现在是我们动手的时候了。抓取你最喜欢的服务器发行版(我将在 [VirtualBox](https://www.virtualbox.org/) 上使用 [Ubuntu](http://www.ubuntu.com/business/server/overview) )。我发现 VirtualBox 的快照功能非常棒，它允许我们测试一次 Chef 运行，如果出现问题就回滚。

该项目的目录结构非常简单。我们将有一本食谱，食谱和支持文件。

```
mkdir cookbooks cookbooks/main cookbooks/main/recipes
touch solo.rb cookbooks/main/recipes/default.rb node.json
```

下一步是打开我们的`solo.rb`文件并添加以下内容:

```
cookbook_path File.join(File.dirname(File.expand_path(__FILE__)), "cookbooks")
json_attribs File.join(File.dirname(File.expand_path(__FILE__)), "node.json")
```

正如你可能猜到的，我们在第一线所做的就是告诉 Chef 在哪里可以找到我们的食谱。然后我们将`node.json`的位置传递给`json_attribs`。我们的文件`node.json`是我们为厨师运行设置特定属性的地方。

我在前面展示过，Chef 将设置许多关于我们环境的信息。这些属性中的一些可以被我们的配方覆盖。这些属性有一个优先级。在本例中，传递给`json_attribs`的任何内容都将具有最高优先级。

现在是时候看看我们的`node.json`文件了。我喜欢使用的一个工具是一个通用的临时目录，这是一个下载包、文件等的方便地方。主厨没有给我们这个，所以我们可以从添加那个开始。当我们这样做的时候，我们最好设置我们的主食谱。这将简单地包括我们服务器的其他配方，如果我们需要添加/删除任何配方，这将是一个很好的切入点。

```
{
  "temp_dir":"/tmp",
  "run_list":["recipe[main]"]
}
```

现在我们已经有了一些基本的设置，让我们看看我们希望我们的厨师运行做什么。对于这个例子，我们将保持它非常简短和甜蜜。已经有很多[烹饪书](https://github.com/opscode/cookbooks)可供你获得一个好的起点。相反，我们将使用可用的 cookbook(Apache)来安装一个包，我们将安装 [RVM](http://beginrescueend.com/) 。

apache 的第一个任务就是将现有的 cookbook 复制到我们的`cookbooks`目录中。

RVM:另一方面，我们还需要做更多的工作。首先，我们创建一个`rvm`目录，连同我们的其他食谱和一个食谱文件夹。

```
mkdir cookbooks/rvm cookbooks/rvm/recipes
touch cookbooks/rvm/default.rb
```

如果我们看一下 RVMs 发行说明，我们会发现有许多依赖项。我们 RVM 食谱的第一项工作就是满足这些要求。

```
%w(build-essential openssl libreadline6 libreadline6-dev curl git-core zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-0 libsqlite3-dev sqlite3 libxml2-dev libxslt-dev autoconf libc6-dev ncurses-dev automake libtool bison subversion).each do |pkg|
  package pkg
end
```

我知道其中一些可能已经在我们的系统中就位了，但是我喜欢孤立地对待每一个配方。如果包裹已经在那里了，那又怎样？使用`package`方法，我们可以确保依赖关系到位。

下一步是安装 RVM 本身。我猜你以前已经这样做过了，并且知道这个伟大的 bin 安装程序。Chef 使用它的`bash`提供者支持这种安装。这实际上是将该块作为 bash 命令进行评估。

```
bash "install RVM" do
  user node[:user]
  code "sudo bash < <(curl -s https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer )"
end
```

Chef DSL 很容易理解，我设置了一个 bash 脚本，作为系统上的当前用户运行，并执行 RVM 安装脚本。不要像我一样，陷入执行这个命令的陷阱。下面的 [RVM 文档](http://beginrescueend.com/rvm/install/)，有区别。

## 装饰和调味

现在我们已经有了几个食谱，是时候整理一下运行顺序以及如何将 Chef 脚本放到 box/node 上了。第一个很简单:在`cookbooks/main/recipes/default.rb`中添加你的食谱。

```
include_recipe "apache2"
include_recipe "rvm"
```

它只是 ruby，所以我们可以添加新的食谱，注释掉一些行，或者你习惯在 Ruby 文件中做的任何事情。现在，我们必须把代码放到我们感兴趣的盒子上。我见过的最好的方法是使用 bash 脚本。这一点最初是由我尊敬的大学教授科林·吉梅尔向我指出的，他为这个 bash 脚本做了大部分繁重的工作。我吗？我只是稍微调整了一下。

```
#!/bin/bash

sudo apt-get update
sudo apt-get install git-core -y -q
sudo apt-get install build-essential -y -q
sudo apt-get install ruby -y -q
sudo apt-get install ruby-dev -y -q
sudo apt-get install libopenssl-ruby -y -q
sudo apt-get install rubygems -y -q
sudo apt-get install irb -y -q
sudo apt-get install rake -y -q
sudo apt-get install curl -y -q
sudo gem install chef --no-ri --no-rdoc

git clone git://github.com/bangline/demo_cookbooks.git /tmp/chef_run

cd /tmp/chef_run

sudo /var/lib/gems/1.8/bin/chef-solo -c /tmp/chef_run/solo.rb -j /tmp/chef_run/node.json
```

这很简单。我需要的所有依赖项都是先设置好的(静音，这样我就不会被问到“这样可以吗？[Y/N]" for every package)，然后克隆项目并在其上运行`chef-solo`。厨师脚本在`solo.rb`的配置和来自`node.json`的 JSON 属性中传递。

在 Chef 脚本完成之后，测试它就很容易了。对于 Apache，只需`wget http://127.0.0.1`下载“它工作了！”页面。对于 RVM，您必须注销并再次登录，之后`rvm notes`将生成 RVM 文档。下一步将修改这个配方，安装一个默认的 Ruby 版本。

## 包扎

Chef 是一个巨大的主题，它的文档也在不断改进。希望这篇介绍能给你足够的激励，让你在考虑开始一些手动系统操作时，以某种方式开始使用 Chef。还有很多内容要介绍，设置用户、虚拟主机，甚至设置一个默认的 Ruby 与 RVM 一起使用，并安装 Passenger 或其他东西，这样我们就有了一个完整的应用程序托管解决方案。如果您继续测试这个演示(源代码可以在[的老地方](https://github.com/bangline/demo_cookbooks)找到)，当重新启动 Apache 时，您会发现一些烦人的警告。这没什么大不了的，我们真的应该修补一下`NameVirtualHost`的配置。尽管如此，考虑到这只是一个快速的复制/粘贴工作，我们确实得到了一个工作的 Apache2 设置。

对我来说，最重要的资源是检查现有的来自 Opscode 和 37Signals 的食谱。对于很多常见的包，已经做了很多工作。这些天来，我一直在努力寻找一种资源，它没有以某种形式提供的食谱，可以“照原样”使用或进行最小限度的定制。

因此，下一次您为您的应用程序设置环境时，请花些时间使用 VirtualBox 在本地虚拟主机上构建它。像大多数事情一样，这会花费额外的时间，会让你沮丧，但是你最终会得到十倍的回报。现在还有谁饿了？

## 分享这篇文章