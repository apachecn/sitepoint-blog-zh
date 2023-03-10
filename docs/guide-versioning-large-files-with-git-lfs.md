# 使用 Git LFS 对大文件进行版本控制

> 原文：<https://www.sitepoint.com/guide-versioning-large-files-with-git-lfs/>

对于像 Git 这样的分布式版本控制系统，对大文件进行版本控制可能会有问题。Git 大文件存储(或 LFS)是 Git(T2)的一个新的开源扩展，旨在改善大文件的处理。

它通过用简单的文本指针替换存储库中的大文件(如图形和视频)来实现这一点。这些指针引用大型文件，这些文件由 GitHub 或其他外部资源(如 AWS bucket)托管在其他地方。

最初这是令人困惑的，但希望 LFS 将在本教程结束时变得更有意义。

### 有效性

LFS 将很快对所有 GitHub 用户开放，但当我用我的[芯片商店棋盘游戏](http://chipshopgame.com)通过 GitHub 处理大量大文件时，我注册了早期访问计划以了解更多信息。

***注意**:除非你的账户上启用了 Git LFS，否则这个教程对你还不起作用，但是你可以对未来的特性有个概念。*

## 安装

如果你有一个基于 Linux 或 Windows 的系统，访问[git-lfs.github.com](https://git-lfs.github.com/)，下载安装程序，解压并运行安装程序脚本。

如果你有 Mac，也可以这么做，但是 Git LFS 也可以通过自制软件获得:用`brew install git-lfs`安装。

## 入门指南

**注意** : Git LFS 目前只能在命令行使用 Git 时工作。我通常使用[塔](http://www.git-tower.com)来管理我的 Git 工作流，这中断了任何与 LFS 相关的操作。

像平常一样创建一个 Git 存储库，并通过发出如下命令初始化您希望用 LFS 跟踪的文件:

```
git lfs track "*.psd"
```

```
git lfs track "*.mp3"
```

然后像平常一样使用 git:

```
git add *.psd
git commit -m "Added PhotoShop files"
git push origin master
```

到目前为止，没有太多不同，但是如果我们仔细研究 GitHub 上的一个文件的细节，我们可以看到一个微妙的差异。GitHub 上有一个文件:

![Normal GitHub File location](img/5855bc3b346f75e53abce3d49e8158f8.png)

这里有一个通过 LFS 在外部托管的文件:

![LFS GitHub File location](img/ea8e1f1ef3e828d9675a0fb98031c7f1.png)

在第一个图像中(传统的 GitHub 存储库)，文件位于存储库中。在第二个映像(支持 LFS 的存储库)中，文件位于 AWS 存储桶中。

现在，继续创建一些分支，进行文件更改，提交并推送:

![Git Folder Size](img/57afa2e2ebed5f885023ef5470c4cfb8.png)

等等，那个`.git`文件夹仍然很大:LFS 不是应该更好地处理文件吗？

这就是 LFS 变得有些令人困惑的地方，它可能没有你希望的那么有用。

要更清楚地看到发生了什么，请删除您创建的 repo 文件夹，然后从 GitHub 重新克隆它。您应该会看到类似下图的内容，其中有一个大小合适的`.git`文件夹:

![PSD file size](img/17fec25a99589673b64bea0ed7c2f58d.png)

![New Git Folder Size](img/02232ec4d356a86d8d4ba1d7f16c18a4.png)

那些文件小得可笑！尝试打开一个，您可能会看到以下消息:

![Photoshop Error Message](img/3306bf7289c1593f468ffef77e4f0130.png)

这是前面提到的文件引用之一，如果您想在文本编辑器中打开它，您会看到类似这样的内容:

```
version https://git-lfs.github.com/spec/v1
oid sha256:128b446a2cd06dd3b4dc2e2fe3336426792425870c3ada44ae7684b8391dc04d
size 1036867
```

如果你是一个团队中的开发人员，这是非常好的，因为你可能不需要大量的媒体文件塞满你的计算机。当涉及到部署或测试时，您可能有一个构建过程，它将使用真实媒体组装一个项目。但是，如果你是一名设计师，需要做出改变呢？你如何访问真实文件？

让我们通过键入`git lfs`来看看 LFS 可以使用哪些命令:

![LFS Commands](img/378b90e2cc684239a118c7b124d8cf9d.png)

有几个可能在未来值得研究，但我们现在最感兴趣的是`fetch`。默认情况下，这将检索当前签出分支中所有文件的本地副本。这可以通过用分支或提交对其进行补充来具体化:

```
# Fetches All
git lfs fetch
```

```
# Fetches a particular branch
git checkout -b new_image
git lfs fetch new_image
```

```
git lfs fetch 5a5c0ef0de779c9d4585320eab8d4a1bec696005
```

并且这些文件可以在本地获得:

![Restored Folder](img/9e555910b917b5320bfb3e629a7fccd1.png)

我喜欢一个特定的命令，然后删除本地文件，并用一个引用替换它。也许有另一种方法可以实现这一点，或者它会在未来的版本中出现。

## 结论

Git LFS 是一个有希望的开始，我可以看到未来真正有用的一瞥。它需要更好的文档和与第三方工具(包括 GitHub 网站)的适当集成，我相信这是迟早的事。如果你有一个像样的 Git，持续集成和部署系统，那么 LFS 将更有意义。如果你是一个小团队，那么这可能是一个更大的瓶颈。

你有什么想法？有用还是没用？

## 分享这篇文章