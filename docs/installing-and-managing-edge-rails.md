# 安装和管理边缘导轨

> 原文：<https://www.sitepoint.com/installing-and-managing-edge-rails/>

根据要求，我将简要介绍如何在最新版本的 Rails 上进行开发。你为什么想这么做？你可能正在开发一个全新的应用程序，这个程序还需要开发一段时间，所以稳定性不是一个大问题。也许你想成为一个很酷的孩子，玩尚未发布的功能。不管是什么情况，你都应该这样做。

默认情况下，当您启动一个 Rails 应用程序时，它会尝试使用项目的`vendor/rails`目录中的 Rails。如果找不到它，它会回到最新的宝石。

要在现有项目中使用 edge Rails，只需在项目中的供应商/rails 目录中做一个`svn checkout`Rails 主干。

```
 svn co http://dev.rubyonrails.org/svn/rails/trunk vendor/rails 
```

如果你开始一个新项目，你可以从 SVN 签出 Rails，然后用它来创建你的新项目。以下所有命令都适用于基于*nix 的平台。如果你是一个 windows 用户，你必须做出相应的调整(或许可以给别人留下评论？)

```
 mkdir -p cutting_edge_project/vendor
cd cutting_edge_project
svn co http://dev.rubyonrails.org/svn/rails/trunk vendor/rails
ruby vendor/rails/railties/bin/rails .
./script/server
=> Booting Mongrel (use 'script/server webrick' to force WEBrick)
... 
```

如果您想将您的 rails 版本与最新版本的 SVN 同步，只需在 Rails 目录上执行 svn update 命令(不要忘记重启您的服务器)。

```
 svn update vendor/rails 
```

如果 Rails 的主干版本被破坏，并且您想要回滚到一个特定的修订版，那么使用`-r`将该修订版传递给 update 命令。

```
 svn update -r4598 vendor/rails 
```

如果你在 SVN 管理你的项目，很可能你不想将 Rails 主干签入你的供应商目录，而是想使用`svn export`或`svn:externals`。

要创建将被检入 SVN 的新边梁，请执行以下操作:

```
 mkdir -p cutting_edge_svn_project/vendor
cd cutting_edge_svn_project
svn export http://dev.rubyonrails.org/svn/rails/trunk vendor/rails
ruby vendor/rails/railties/bin/rails .
rm -R vendor/rails
# prepare project for SVN (i.e. delete logs, etc) and import 
```

一旦您签出了项目的一个副本，比如说进入`my_checked_out_project`，设置供应商目录的`svn:externals`属性并执行一个`svn update`:

```
 cd my_checked_out_project
svn propset svn:externals "rails http://dev.rubyonrails.org/svn/rails/trunk" vendor
svn update 
```

你现在应该会发现，每当你签出你的项目或做 SVN 更新时，它也会更新 Rails。这是可以的，但是你很快就会发现，当你更新到 Rails 的最新版本时，你需要更多的控制权。要手动指定您想要的 Rails 版本，请使用`-r`标志:

```
 svn propset svn:externals "rails -r4859 http://dev.rubyonrails.org/svn/rails/trunk" vendor
svn update 
```

正因为 Rails 旨在变得简单，它附带了两个 Rake 任务来为您完成这一任务(尽管有一个难题，即您只能在已经创建了 Rails 项目的情况下执行它们)

```
 rake -T rails:freeze 
```

要将项目的 javascript 和配置文件与供应商目录中的 Rails 同步，请执行以下 rake 命令:

```
 rake rails:update # Update both configs, scripts and public/javascripts from Rails 
```

## 分享这篇文章