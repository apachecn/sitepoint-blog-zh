# 了解 Deno:一个安全的 JavaScript & TypeScript 运行时

> 原文：<https://www.sitepoint.com/learn-deno/>

2020 年 5 月，Deno 1.0 发布，这已经成为一个非常流行的话题。如果你想知道这有什么大惊小怪的，你来对地方了！

在这篇文章中，我希望给你一个关于什么是 Deno 的概述。我们将看看它的主要特性，并询问您为什么想要开始学习这个新工具。

## 什么是德诺？

那么什么是 Deno，为什么要创建它呢？这是一个 JavaScript 和 [TypeScript](https://www.sitepoint.com/typescript-tutorial-for-beginners/) 运行时，这意味着你可以用任何一种语言编写程序，并从命令行执行它们。与在浏览器中运行的 JavaScript 不同，Deno 程序可以访问主机上的资源，比如文件系统和环境变量。

如果你知道 [Node.js](https://www.sitepoint.com/an-introduction-to-node-js/) ，并且你认为 Deno 听起来很相似，那么你是对的。Deno 是 Node 的创始人瑞安·达尔(Ryan Dahl)的创意，他创建 Deno 是为了解决他所认为的 Node 中的[设计缺陷。该项目的目标是提供一个默认安全的脚本环境，将 TypeScript 视为一级语言，并尽可能兼容浏览器(如果可行的话)。](https://www.youtube.com/watch?v=M3BM9TB-8yA)

## 安全功能

Deno 的设计是开箱即用。默认情况下，所有代码都在安全的沙箱中执行，这意味着您需要给出明确的许可，以允许程序访问网络或文件系统。

可以使用以下命令行标志授予程序权限:

*   **-A，–allow-all**:允许所有权限(禁用所有安全性)。
*   **–allow-env**:允许获取和设置环境变量。
*   **–allow-HR time**:允许高分辨率时间测量(可用于计时攻击和指纹识别)。
*   **–allow-net = \**:允许网络访问。可选地采用逗号分隔的域白名单。
*   **–allow-plugin**:允许加载插件(不稳定特性)。
*   **–allow-read = \**:允许文件系统读访问。可以选择采用逗号分隔的目录或文件白名单。
*   **–允许运行**:允许运行子流程。
*   **–allow-write = \**:允许文件系统写访问。可以选择采用逗号分隔的目录或文件白名单。

## 一流的类型脚本支持

正如我前面提到的，Deno 既可以执行 JavaScript *也可以执行* TypeScript。更重要的是，它支持 TypeScript 作为一级语言。这意味着它可以加载并运行您的 TypeScript 代码，而无需任何额外的构建步骤。不需要先设置额外的工具将代码转换成 JavaScript。

当然，由于 TypeScript 是现代 JavaScript 的超集，如果您愿意，您也可以用优秀的旧 JS 编写代码！Deno 支持一些伟大的、对开发者友好的特性，比如 es 模块导入

## 使用外部代码

正如 Ryan 在他的演讲中提到的，他的 Deno 目标之一是避免对包管理器的需求。与 Node.js 和 PHP(分别使用 [npm](https://www.sitepoint.com/beginners-guide-node-package-manager/) 和 composer 包管理器)等运行时/语言不同，Deno 没有包管理器。

相反，外部包通过 URL 直接导入:

```
import { Client } from "https://deno.land/x/mysql@2.2.0/mod.ts"; 
```

第一次运行脚本时，Deno 会获取、编译和缓存所有的导入，这样后续的启动会非常快。很明显，有时您可能想强制它重新获取导入，您可以使用`cache`子命令来完成:

```
deno cache --reload my_module.ts 
```

### 包托管

虽然 Deno 没有提供这样的包注册表，但有一个可用的第三方模块列表。该服务提供了一个标准化的、版本化的 URL，它映射到模块的 GitHub repo。您可以按名称搜索软件包并查看简短描述，还可以单击查看软件包自述文件。

## 标准图书馆

Deno 提供了一个[标准库](https://deno.land/std)——松散地基于 Golang 的——它提供了一组高质量的标准模块，没有外部依赖性。

标准库中的软件包不与 Deno 一起安装。相反，它们可以在网上找到，并链接到我们在上一节看到的内容。模块被版本化，允许您将您的代码固定到特定版本的使用:

```
import { copy } from "https://deno.land/std@0.50.0/fs/copy.ts"; 
```

这意味着您编写的任何依赖于标准库中的模块的代码都应该在未来的版本中继续工作。

该库包括构建命令行和基于 HTTP 的应用程序时可能需要的各种助手和实用程序:

*   **归档**:处理 tar 文件的模块
*   **异步**:异步实用程序
*   **字节**:处理二进制数组的助手
*   **datetime** :将日期字符串解析成`Date`对象的助手
*   **编码**:处理 base32、二进制、CSV、TOML 和 YAML 格式的编码器
*   **标志**:命令行参数解析器
*   **fmt** :打印格式化输出的工具
*   **fs** :使用文件系统的助手
*   **hash** :使用各种算法创建散列的模块
*   **http** :创建 http 和文件服务器，并操作 cookies
*   **io** :字符串输入/输出实用程序
*   **日志**:简单日志模块
*   **mime** :提供对多部分数据的支持
*   **node**:node . js 代码的(当前正在进行的)兼容层
*   **路径**:路径操作实用程序
*   **权限**:帮助者检查并提示安全权限
*   **信号**:处理 Deno 过程信号的助手
*   **测试**:测试用于 Deno 内置测试运行器的断言
*   **uuid** :用于生成和验证 uuid 的实用程序
*   **ws** :创建 WebSocket 客户端和服务器的助手

## 安装 Deno

Deno 作为一个单独的可执行文件发布，没有依赖性。您可以从[发布页面](https://github.com/denoland/deno/releases)下载二进制文件，或者使用下面的安装程序进行安装:

Shell (macOS、Linux):

```
curl -fsSL https://deno.land/x/install/install.sh |  sh 
```

PowerShell (Windows):

```
iwr https://deno.land/x/install/install.ps1 -useb | iex 
```

[家酿](https://formulae.brew.sh/formula/deno) (macOS):

```
brew install deno 
```

### 升级

安装后，Deno 还可以使用以下命令将自身升级到最新版本:

```
deno upgrade 
```

或者，您可以升级/降级到特定版本:

```
deno upgrade --version 1.0.1 
```

## 未来是光明的

Deno [手册](https://deno.land/manual)暗示它“是历史上用 Bash 或 Python 编写的实用程序脚本的绝佳替代品”。虽然这肯定是真的，但我希望看到它越来越多地用于 Node.js 目前流行的相同用例。随着越来越多的第三方模块的出现，已经出现了许多类似 Express/Koa 的框架，允许您构建类型安全的 REST APIs。

那么，是不是应该忘记 Node.js 和[开始学习](https://www.sitepoint.com/how-to-learn-quickly/) Deno 呢？业界目前的观点是 Node.js 不会很快消失，但 Deno 绝对是一项值得关注的技术。

## 德诺基金会

了解我们的德诺基金会收藏德诺。你可以在这里找到我们发布的所有 Deno 报道，以一种最有助于初学者的方式组织。

### Deno 基础知识

*   [Deno Guide:一个安全的 JavaScript & TypeScript 运行时](https://www.sitepoint.com/deno-guide/)
*   [Node.js vs Deno:你需要知道的](https://www.sitepoint.com/node-vs-deno/)
*   [如何使用 Deno 的内置工具](https://www.sitepoint.com/deno-built-in-tools)
*   [Deno 模块系统:初学者指南](https://www.sitepoint.com/deno-module-system-a-beginners-guide/)
*   [如何用 Deno 从第三方 API 获取数据](https://www.sitepoint.com/deno-fetch-data-third-party-api/)
*   [在 Deno 中使用文件系统](https://www.sitepoint.com/deno-file-system/)

### 演示项目

*   [使用 Deno、Reno 和 PostgreSQL 构建微服务](https://www.sitepoint.com/building-microservices-with-deno-reno-postgresql)
*   [在 Deno 中构建一个命令行天气应用](https://www.sitepoint.com/build-a-terminal-weather-app-in-deno)

## 分享这篇文章