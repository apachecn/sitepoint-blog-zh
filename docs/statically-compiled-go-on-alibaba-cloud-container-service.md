# 静态编译的 Go on 阿里云容器服务

> 原文：<https://www.sitepoint.com/statically-compiled-go-on-alibaba-cloud-container-service/>

*在最近的比赛中，我们收到了许多优秀的参赛作品，以寻找充分利用[阿里云](https://int.alibabacloud.com/m/1000018814/)服务的最佳技巧。对于我们的评委来说，从这么多有帮助和有趣的参赛作品中选出获胜者是一项有趣但具有挑战性的任务。但是，经过激烈的讨论和激烈的辩论，他们决定比赛的三等奖授予大卫·班汉姆。他的获奖作品是一篇关于静态编译 Go 程序，并使用 Docker 对其进行容器化和分发的简明教程。*

[阿里云](https://int.alibabacloud.com/m/1000018814/)的[容器服务](https://int.alibabacloud.com/m/1000018808/)是 Docker 和 Kubernetes 如何革新云环境的一个很好的例子。坏脾气的人会抱怨说，它仍然只是在别人的计算机上运行的一些软件，但变革性的区别是 k8s 和 Docker 提供了实际上是平台无关的管理 API。如果你针对 k8s 来构建你的 DevOps 管道，你在 AWS、谷歌云、Azure 和阿里巴巴之间的切换摩擦可能是最低的。我们越接近一次编写、随处运行的梦想，就越好！

我喜欢的另一个增强软件可移植性的工具是 Go 语言。Go 中的交叉编译就像从木头上掉下来一样容易。我在我的 Linux 笔记本电脑上开发软件，一眨眼的功夫，我就可以为 Windows、OSX、WASM 等构建二进制文件了！下面是我使用的 Makefile 片段:

```
name = demo

PLATFORMS := linux/amd64 windows/amd64 linux/arm darwin/amd64

temp = $(subst /, ,$@)
os = $(word 1, $(temp))
arch = $(word 2, $(temp))

release:

make -l inner_release

inner_release: $(PLATFORMS)

$(PLATFORMS):
@-mkdir -p ../web/api/clients/$(os)-$(arch)
@-rm ../web/api/clients/$(os)-$(arch)/*
GOOS=$(os) GOARCH=$(arch) go build -o '../web/api/clients/$(os)-$(arch)/$(name) .
@chmod +x ../web/api/clients/$(os)-$(arch)/$(name)
@if [ $(os) = windows ]; then mv ../web/api/clients/$(os)-$(arch)/$(name) ../web/api/clients/$(os)-$(arch)/$(name).exe; fi
zip --junk-paths ../web/api/clients/$(os)-$(arch)/$(name)$(os)-$(arch).zip ../web/api/clients/$(os)-$(arch)/*
@if [ $(os) = windows ]; then cp ../web/api/clients/$(os)-$(arch)/$(name).exe ../web/api/clients/$(os)-$(arch)/$(name); fi 
```

整洁！这将得到一个整洁的小二进制文件，可以在目标操作系统上运行。如果你的目标是像 Cloud Container Service 这样的 Docker 主机，即使这样也是多余的。你只需要做`GOOS=linux GOARCH=amd64 go build`就可以了！然后，您只需要选择您最喜欢的 Linux 发行版，并将其构建到 docker 文件中。

但是，如果我们想进一步简化我们的生活呢？如果我们可以完全抛弃 Linux 发行版会怎么样？

Go 支持静态链接二进制文件的编译。这意味着我们可以编写完全不依赖于任何外部 dll 的代码。观察这个神奇的 Dockerfile 文件:

```
FROM scratch

ADD ./bin/web /app

ENTRYPOINT [&quot;/app&quot;] 
```

什么是 scratch？没什么。这告诉 Docker 除了 Linux 内核和使 Docker 工作所需的基本组件之外，不要捆绑任何东西。比 Ubuntu 小？有阿尔卑斯山。比阿尔卑斯山还小？有划痕。

有什么比阿尔卑斯山更酷的？冰冷！呃，擦伤。好吧好吧好吧好吧。

这样做的好处是，我们从部署中移除了大量移动部件。发行版维护者决定在他们的发行版中捆绑或不捆绑某些服务，这不可能会破坏您的生产环境。发行版代码没有引入安全漏洞。没有要管理的更新。只是…什么都没有。

作为奖励，你会得到一个非常小的 Docker 图片，更容易传播。更容易运送到您喜欢的任何云！

试试看。写一些 Go，静态编译，放在 scratch Docker 容器里。在阿里巴巴、AWS 和谷歌云上部署它只是为了好玩！看看哪种服务最适合你。

## 分享这篇文章