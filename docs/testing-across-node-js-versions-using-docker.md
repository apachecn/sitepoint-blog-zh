# 使用 Docker 跨 Node.js 版本测试

> 原文：<https://www.sitepoint.com/testing-across-node-js-versions-using-docker/>

## 问题:测试

**[NAN](https://github.com/rvagg/nan)** 是一个项目，旨在帮助构建本机(C++) Node.js 附加组件，同时保持与 Node 0.8 及以上版本的 Node 和 V8 的兼容性。V8 正在经历*重大*内部变化，这使得附加开发非常困难。南的目的是将痛苦抽象化。NAN 为您做到了这一点，而不是必须保持您的代码在 Node/V8 版本之间兼容，这不是一项简单的任务。这意味着我们必须确保 NAN 经过测试，并与它声称支持的所有版本兼容。这不是一个微不足道的练习！

**[特拉维斯 CI](https://travis-ci.org/)** 在这方面可以帮点小忙。可以使用 [nvm](https://github.com/creationix/nvm) 来测试 Node.js 的不同版本，甚至是官方支持版本之外的版本。我们已经在 NAN 身上尝试过了，但并不成功。理想情况下，你会有更好的节点版本选择，但是 Travis 在跟上方面有些困难。此外，由于 npm 安装问题，Node.js 旧版本附带的历史 npm 错误往往会导致高失败率。为此，我们甚至不在 NAN README 上发布 Travis 徽章，因为它根本不起作用。

Travis 的另一个问题是，它是一个 *CI* 解决方案，而不是一个合适的*测试*解决方案。即使它工作得很好，它在开发过程中也不是很有帮助，因为你需要快速反馈你的代码在你的目标平台上工作(这是我喜欢后端开发胜过前端开发的一个原因！)

## 解决方案:Docker

输入**码头工人**和**DNT。Docker 是一个工具，它简化了 Linux 容器的使用，以创建轻量级、隔离的计算“实例”。Solaris 及其变体以“区域”的形式拥有这种功能已经很多年了，但对于 Linux 来说这是一个相对较新的概念，Docker 使整个过程更加友好。Dockers 的相对简单性意味着最近几个月 Linux 容器领域的活动数量惊人，它几乎在一夜之间变成了一个巨大的生态系统。**

![](img/80377a66640754d323965ed5c5dcbe45.png)

### DNT:坞站节点测试仪

Docker Node Test，或 DNT，是一个非常简单的实用工具，包含两个用于使用 Docker 和 Node.js 的工具。一个工具帮助设置测试容器，另一个工具在这些容器中运行项目的测试。

![](img/a87c27dc980815dd20e605da6f37cba8.png)

DNT 包含了一个`setup-dnt`脚本，它设置了运行 Node.js 应用程序所需的最基本的 Docker 映像，仅此而已。它首先创建一个名为`dev_base`的映像，该映像使用默认的 Docker“Ubuntu”映像，并添加编译和安装 Node.js 所需的构建工具

接下来，它创建一个包含 Node.js [源库](https://github.com/joyent/node)的完整副本的`node_dev`映像。最后，它会创建一系列您想要运行的测试所需的图像。对于每个节点版本，它会创建一个安装了节点并随时可以使用的映像。

建立一个项目就是在项目的根目录下创建一个`.dntrc`文件。这个配置文件设置了一个`NODE_VERSIONS`变量，其中包含您想要测试的所有节点版本的列表。这个列表可以包括“master”来测试来自节点存储库的最新代码。您还可以用一系列设置、编译和执行测试所需的命令来设置一个`TEST_CMD`变量。可以对一个`.dntrc`文件运行`setup-dnt`命令，以确保适当的 Docker 映像已经准备好。然后可以使用`dnt`命令对您指定的所有节点版本执行测试。

由于 Docker 容器是完全隔离的，只要机器有资源，DNT 就可以并行运行测试。默认情况下，使用计算机上的内核数量作为并发级别，但是如果这不适合您想要运行的测试类型，也可以进行配置。

还可以定制基本测试映像，以包含项目所需的其他外部工具和库，尽管这是设置过程中的一个手动步骤。

目前，DNT 被设计为通过将最后一行读为“ok”或“not ok”来解析 TAP 测试输出，从而在命令行上报告测试状态。它是可配置的，但是您需要提供一个命令，将测试输出转换为“ok”或“not ok”(`sed`)来拯救？).Mocha TAP reporter 的非标准输出也支持开箱即用。

## 当前用途

我的主要用例是测试 NAN。能够在编码的同时针对所有不同的 V8 和节点 API 进行测试是非常有用的，尤其是当测试运行如此之快时！我的 NAN `.dntrc`文件对 master 的测试，0.11.4 以来的很多 0.11 版本(0.11.0 到 0.11.3 明确不支持 NAN，0.11.11 和 0.11.12 对原生插件完全破了)，以及 0.10 和 0.8 系列的后五个版本。目前，Node 总共有 18 个版本，在我的电脑上，测试套件需要大约 20 秒才能完成所有这些版本。下面显示的是 NAN [`.dntrc`](https://raw.github.com/rvagg/nan/master/.dntrc) 文件。

```
NODE_VERSIONS="\
  master   \
  v0.11.10 \
  v0.11.9  \
  v0.11.8  \
  v0.11.7  \
  v0.11.6  \
  v0.11.5  \
  v0.11.4  \
  v0.10.26 \
  v0.10.25 \
  v0.10.24 \
  v0.10.23 \
  v0.10.22 \
  v0.8.26  \
  v0.8.25  \
  v0.8.24  \
  v0.8.23  \
  v0.8.22  \
"
OUTPUT_PREFIX="nan-"
TEST_CMD="\
  cd /dnt/test/ &&                                               \
  npm install &&                                                 \
  node_modules/.bin/node-gyp --nodedir /usr/src/node/ rebuild && \
  node_modules/.bin/tap js/*-test.js;                            \
"
```

接下来，我为 DNT 配置了 **[降级](https://github.com/rvagg/node-leveldown)** 。LevelDOWN 是一个将 LevelDB 公开给 Node.js 的原始 C++绑定，它的主要用途是 [LevelUP](https://github.com/rvagg/node-levelup) 的后端。需求要简单得多，因为测试只需要编译和运行大量的节点点击测试。下面的代码示例显示了 LevelDOWN [`.dntrc`](https://raw.github.com/rvagg/node-leveldown/master/.dntrc) 。

```
NODE_VERSIONS="\
  master   \
  v0.11.10 \
  v0.11.9  \
  v0.10.26 \
  v0.10.25 \
  v0.8.26  \
"
OUTPUT_PREFIX="leveldown-"
TEST_CMD="\
  cd /dnt/ &&                                                    \
  npm install &&                                                 \
  node_modules/.bin/node-gyp --nodedir /usr/src/node/ rebuild && \
  node_modules/.bin/tap test/*-test.js;                          \
"
```

我用 DNT 安装的另一个本地节点插件是我的 **[libssh Node.js 绑定](https://github.com/rvagg/node-libssh)** 。这个稍微复杂一点，因为在编译之前需要安装一些非标准的库。我的`.dntrc`添加了一些额外的`apt-get`酱来获取和安装那些包。这意味着测试需要更长的时间，但这并不令人望而却步。另一种方法是配置`node_dev`基础映像，将这些包添加到我所有的版本化映像中。节点-libssh [`.dntrc`](https://raw.github.com/rvagg/node-libssh/master/.dntrc) 如下图所示。

```
NODE_VERSIONS="master v0.11.10 v0.10.26"
OUTPUT_PREFIX="libssh-"
TEST_CMD="\
  apt-get install -y libkrb5-dev libssl-dev &&                           \
  cd /dnt/ &&                                                            \
  npm install &&                                                         \
  node_modules/.bin/node-gyp --nodedir /usr/src/node/ rebuild --debug && \
  node_modules/.bin/tap test/*-test.js --stderr;                         \
"
```

[LevelUP](https://github.com/rvagg/node-levelup) 不是一个原生插件，但它使用 LevelDOWN，这需要编译。对于 DNT 配置，我删除了`npm install`之前的`node_modules/leveldown/`，这样每次新版本的节点都会重新构建它。LevelUP [`.dntrc`](https://raw.github.com/rvagg/node-levelup/master/.dntrc) 如下图所示:

```
NODE_VERSIONS="\
  master   \
  v0.11.10 \
  v0.11.9  \
  v0.10.26 \
  v0.10.25 \
  v0.8.26  \
"
OUTPUT_PREFIX="levelup-"
TEST_CMD="\
  cd /dnt/ &&                                                    \
  rm -rf node_modules/leveldown/ &&                              \
  npm install --nodedir=/usr/src/node &&                         \
  node_modules/.bin/tap test/*-test.js --stderr;                 \
#"
```

## 未来的工作

不难想象这将形成本地 CI 系统以及通用测试工具的基础。这种速度甚至让人忍不住想对每次 git 提交或者甚至每次保存都进行测试。现在， [New Relic](https://newrelic.com) Node.js 代理团队已经在使用 DNT 的一个内部分支进行非常复杂的工作，结合各种常见服务器框架的测试，针对 Node.js 的许多版本测试他们的代理。

我总是渴望有贡献者，如果你有特殊的需求和实现新功能的技能，那么我很乐意听到你的意见。我通常对我的开源项目非常开放，并乐于添加贡献者，他们会添加一些有价值的东西。

安装和详细使用说明见 [DNT](https://github.com/rvagg/dnt) GitHub repo。

Rod 是今年 5 月 1 日和 2 日在墨尔本举行的[网络方向代码](https://webdirections.org/code14/)的演讲者之一。使用折扣代码 SITEPOINT 获得网上方向代码机票的最低价格！

## 分享这篇文章