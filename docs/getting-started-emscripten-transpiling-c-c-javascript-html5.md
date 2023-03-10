# Emscripten 入门:将 C/C++转换为 JavaScript/HTML5

> 原文：<https://www.sitepoint.com/getting-started-emscripten-transpiling-c-c-javascript-html5/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

![Transpiling multiple platforms](img/0cc2e5f07e6f605421f9de35bd523ed2.png)

为多个平台编写代码可能需要大量的工作。每一个都要完全重写，这可能会更麻烦。如果您用 C++编写了一个应用程序，但希望它以某种方式显示在浏览器中，该怎么办？现在，有了一个叫做 Emscripten 的工具，这是可能的。

Emscripten 是一个基于 LLVM 的项目，它将 C 和 C++编译成 asm.js 格式的高性能 JavaScript。简而言之:在浏览器内部，使用 C 和 C++，接近本地速度。更好的是，Emscripten 将 OpenGL(一种桌面图形 API)转换为 WebGL，这是该 API 的 web 变体。

我之前写过一篇博文，阐述了什么是 Emscripten，以及它如何与我最喜欢的游戏开发工具相关联:虚幻引擎 4 和 Unity。因此，我在这里不会详细介绍它是如何工作的，而是想专注于创建您自己的 web 项目，该项目利用了 Emscripten，这样您就可以获取 C/C++代码并让它在浏览器中运行。

## 平台

我在 [Windows 10](http://insider.windows.com/?WT.mc_id=16526-DEV-sitepoint-article62) 上做这个，但是对于基于 Unix 的平台，OS X 和 Linux，这个过程是一样的。在这一点上，您需要做的唯一真正的改变是在命令行终端中输入代码的方式。对于 Windows，我简单的写 *emcc* 。对于基于 Unix 的环境，您必须在您的调用前面加上 *`./`* ，因此您的命令应该是: *`./emcc`*

## 安装过程

我最初考虑过不写这篇教程，但是当我在安装程序上遇到这么多问题时，我想它会对其他人有所帮助。我看了 Epic 如何推荐它从 UE4 导出项目，他们[建议使用 SDK Web 安装程序](https://docs.unrealengine.com/latest/INT/Platforms/HTML5/GettingStarted/)。这让我掉进了一个兔子洞。我希望安装过程会很顺利，但是在成功之前，我花了将近两天的时间试图让 Emscripten 在我的机器上正常工作。该团队在网站上有很棒的[入门指南](https://kripken.github.io/emscripten-site/docs/getting_started/index.html)，但是如果你选择了错误的安装程序，你可能会有麻烦。

![SDK Web installer](img/ff558a9210bc2f4d12baefc7c8fd5087.png)

我的第一个问题是我选择了 Web 安装程序。这不是你要找的安装程序。相反，使用 **SDK 离线安装程序**，你会一帆风顺，它应该只需要你几分钟。

![SDK Web installer](img/c2d29ac662fd19926a783df2fa60a272.png)

Web 安装程序有许多错误，尤其是在您已经安装的各种版本的 Visual Studio 周围。这是一个已知的问题，[，但它正在被纠正](https://github.com/kripken/emscripten/issues/3814#issuecomment-144595500)。我相信这与 Emscripten 在幕后进行的并行优化有关，我发现 Cmake 指向我安装的 Visual Studio (2015 community)的最新版本，而不是 Emscripten 要求的 VS 2010。

您可以从 Visual Studio 中使用 Emscripten，但是我发现从 Emscripten CLI 中执行我的构建更容易。如果你确实想使用 Visual Studio，[指令可以在这里找到](https://kripken.github.io/emscripten-site/docs/getting_started/getting_started_with_emscripten_and_vs2010.html)，尽管**目前只支持 VS 2010 和 2013】。**

> Emscripten SDK 在一个易于安装的软件包中提供了完整的 Emscripten 工具链(Clang、Python、Node.js 和 Visual Studio 集成),并集成了对发布时更新到较新 SDK 的 *[支持。](https://kripken.github.io/emscripten-site/docs/getting_started/downloads.html#updating-the-emscripten-sdk)*

–[EMC scriten 入门指南](https://kripken.github.io/emscripten-site/docs/getting_started/downloads.html)

## 入门指南

运行 **Emscripten 命令提示符**开始。我一般点击 *`win + ems`* 就会出现。

![Emscripten Command Prompt](img/9c23da46e2d728255825a6ff5897d7ee.png)
您将看到 CLI，并从您安装 Emscripten 的位置开始。

导航到您的 **emsdk 根目录**。我通过将目录更改为 *`emscripten\tag-1.34.1`* 来做到这一点，因为您可以看到，在上图中，emscripten 的路径设置为:

```
EMSCRIPTEN = C:\Program Files\Emscripten\emscripten\tag-1.341
```

![Emscripten Command Prompt](img/035161bc6613ecd47841747d6fc99eab.png)
**提示:**在任何一点你都可以输入' *`explorer` 【T7 . '进入 CLI，打开资源管理器文件夹，更好地可视化您正在查看的内容。对于有几十个项目的文件夹，这通常比键入' *`ls`* '来列出目录中的所有内容更容易。*

![Explorer folder directory](img/471d6446d9dee99798dface2be7090fa.png)

运行`'*emcc -v*'`将是您的健全性检查，以验证在您运行的版本中一切都正常工作。您应该会看到类似这样的内容:

![Emscripten Command Prompt](img/a7f5fa47b2f5c9c3d828e027447fbc2d.png)

现在您可以将 C / C++工作编译成 JavaScript 了，让我们直接从 Emscripten 文档开始运行第一个示例。让我们把 **`hello_world.c`** 转换成 JavaScript。C 代码看起来像这样:

```
#include<stdio.h>
int main() {
  printf("hello, world!\n");
  return 0;
}
```

我们将在测试文件夹中运行它，这是链的下一级。要构建代码，请输入:

```
emcc tests/hello_world.c
```

如果一切顺利，您的 CLI 应该暂停片刻，然后返回到同一位置。这是怎么回事？好吧，编译器并没有很详细地给我们任何反馈，所以让我们现在添加它，以便更好地理解幕后发生的事情。

我们将在命令中添加' *`-v`* '参数，让编译器吐出更多的*冗长的*信息。

```
emcc tests/hello_world.c -v
```

这还差不多:

![C compiling](img/937f82cd88e3a933999c2093207e169e.png)

所有这些只是将我们的应用程序编译成 C，并在当前目录下创建一个 **`a.out.js`** 文件。如果你喜欢用 C++，你可以用' *`em++`* '代替' *`emcc`* '。

![Explorer folder directory](img/19a8b52a696ce501469c3e0ac595ba40.png)

这个 **`a.out.js`** 是这里的关键，因为它包含了基于 [asm.js 规范](http://asmjs.org/spec/latest/)的 JavaScript 代码。如果我们用 node 执行此操作，您将会看到'*`hello, world`【T7]'出现在 CLI 中。输入以下命令:*

```
node a.out.js
```

![Node Command](img/5c3f3b808683eca5aaea8b416357b88b.png)

## 太好了，但是我想在我的浏览器中看到一些东西

### 生成 HTML

首先检查你的网络平台是否有支持 asm.js 的[浏览器。实际上，我们可以使用 Emscripten 通过`–o`参数生成几种不同类型的扩展。从文档中:](https://dev.modern.ie/platform/status/?utm_source=SitePoint&utm_medium=article62&utm_campaign=SitePoint/?filter=f3f0000bf&search=asm.js)

```
-o <target>
```

目标文件扩展名定义要生成的输出类型:

```
<name>.js : JavaScript.
```

```
<name>.html : HTML + separate JavaScript file (<name>.js).
Having the separate JavaScript file improves page load time.
```

```
<name>.bc : LLVM bitcode (default).
```

```
<name>.o : LLVM bitcode (same as .bc).
```

在这种情况下，我们需要 HTML，以便可以在浏览器中查看它。输入此命令以指定 HTML 文档:

```
Emcc tests/hello_world./c -o hello.html
```

我们告诉`Emscripten`使用 C 编译器在 *`tests`* 文件夹中查找文件 *`hello_world.c`* ，然后将其输出为 *`hello.html`* 。在你的 **`C:\Program Files \Emscripten\emscripten\tag-1.34.1`** 文件夹里面你应该会看到 **`hello.html`** 。

![Hello.html generated](img/ae4ebfe4f54182236c2fa0600b82e907.png)

用你的浏览器打开它，你可以看到一个网页:

![Emscripten demo webpage](img/a9e48cdd31471eb240357e8a705e81b8.png)

但是让我们更进一步。我想从一个文件中读取并提供一些内容。

## 从文件中读取

因为浏览器在沙盒环境中运行 JavaScript，所以我们不能直接访问本地文件系统。为了解决这个问题，Emscripten 创建了一个模拟文件系统，这样我们就可以使用 **`libc stdio`** API 来访问编译后的`C/C++ code`。问题是虚拟文件系统要求这些文件被[嵌入](https://kripken.github.io/emscripten-site/docs/tools_reference/emcc.html#emcc-preload-file)或[预加载](https://kripken.github.io/emscripten-site/docs/tools_reference/emcc.html#emcc-preload-file)。

让我们转到 *`hello_world_file.cpp`* 作为我们教程的最后一部分，并阅读一个文件，因为这将涵盖我想讨论的几个部分，包括如何提供内容。

该文件的代码如下所示:

```
#include <stdio.h>
int main() {
  FILE *file = fopen("tests/hello_world_file.txt", "rb");
  if (!file) {
    printf("cannot open file\n");
    return 1;
  }
  while (!feof(file)) {
    char c = fgetc(file);
    if (c != EOF) {
      putchar(c); 
    }
  }
  fclose (file);
    return 0;
  } 
```

如果你有网络背景，并且不习惯使用强类型语言，你可能会想“ *`feof`* 是什么意思？”这也让我陷入了一个循环，但是这个[堆栈溢出问题](https://stackoverflow.com/questions/5431941/why-is-while-feof-file-always-wrong)为我澄清了相当多的困惑:

> 现在我们到了 EOF。EOF 是从一个*尝试的* I/O 操作中得到的*响应*。这意味着你试图读或写一些东西，但当你这样做时，你不能读或写任何数据，而是遇到了输入或输出的结束。基本上所有的 I/O API 都是如此，无论是 C 标准库、c++ iostream 还是其他库。只要 I/O 操作成功，你根本*无法知道*进一步，未来的操作是否会成功。你*必须*永远先尝试操作，再应对成败。

这将尝试读取位于 **`tests/hello_world_file.txt`** 的文件。为此，我们需要运行以下命令:

```
Emcc tests/hello_world_file.cpp -o hello.html –preload-file tests/hello_world_file.txt
```

现在尝试再次打开同一个 **`hello.html`** 文件，看看会发生什么。

在 FireFox 中，一切正常:

![Emscripten demo webpage (Firefox)](img/32b469230f323a25b1d6f0f53d7535fc.png)

但在 Chrome 和 Edge 上，我们就没那么幸运了。

![Emscripten demo webpage (Chrome)](img/063a68c27ca31fae03b4223961cfa26c.png)

![Emscripten demo webpage (Edge)](img/a8f432c488f6188a02e40734be3d9490.png)

这是因为那些浏览器不支持 *`file:// XHR requets`* ，所以无法加载预装数据的本地文件。我们需要启动一个网络服务器。我们已经安装了 python，因为它是`emscriten`包的一部分，所以让我们从当前目录中旋转出一个[简单服务器](http://www.linuxjournal.com/content/tech-tip-really-simple-http-server-python)并再次尝试。

```
python -m SimpleHTTPServer 8080
```

![Emscripten command prompt](img/5634a4a9b3f6637c492a1d1ee057b202.png)

现在，在 Edge 或 Chrome 中，让我们再次导航到该文件:

[http://localhost:8080/hello . html](http://localhost:8080/hello.html)

![Emscripten demo webpage (Edge)](img/aaf714ff868e10c3297bacf6bf9f8a01.png)

我们走吧！在浏览器中提供 HTML，以前只是 C++。

## 连接 C++和 JavaScript

我知道你在想什么。“这很好，但如果我的 C++和 JavaScript 之间没有任何互操作性，这就没什么用了。”还好有！

有几种方法:

*   从普通 JavaScript 调用编译的 C 函数:
    *   *[使用 ccall 或 cwrap](https://kripken.github.io/emscripten-site/docs/porting/connecting_cpp_and_javascript/Interacting-with-code.html#interacting-with-code-ccall-cwrap)*
    *   *[使用直接函数调用](https://kripken.github.io/emscripten-site/docs/porting/connecting_cpp_and_javascript/Interacting-with-code.html#interacting-with-code-direct-function-calls)* (更快但更复杂)。
*   使用通过以下方式创建的绑定从 JavaScript 调用编译的 C++类:
    *   *[Embind 或 WebIDL-Binder](https://kripken.github.io/emscripten-site/docs/porting/connecting_cpp_and_javascript/Interacting-with-code.html#interacting-with-code-binding-cpp)*
*   从 C/C++调用 JavaScript 函数:
    *   *[使用 emscripten_run_script()](https://kripken.github.io/emscripten-site/docs/porting/connecting_cpp_and_javascript/Interacting-with-code.html#interacting-with-code-call-javascript-from-native)*
    *   *[【使用 EM _ ASM()】](https://kripken.github.io/emscripten-site/docs/porting/connecting_cpp_and_javascript/Interacting-with-code.html#interacting-with-code-call-javascript-from-native)*(更快)
    *   *[使用 JavaScript 实现的 C API](https://kripken.github.io/emscripten-site/docs/porting/connecting_cpp_and_javascript/Interacting-with-code.html#implement-c-in-javascript)*
    *   *[作为来自 C 的函数指针](https://kripken.github.io/emscripten-site/docs/porting/connecting_cpp_and_javascript/Interacting-with-code.html#interacting-with-code-call-function-pointers-from-c)*
    *   *[使用 Embind val 类](https://kripken.github.io/emscripten-site/docs/porting/connecting_cpp_and_javascript/embind.html#embind-val-guide)*

这超出了本教程的范围，但是我将在以后的教程中介绍它。同时，查看关于该主题的 [Emscripten 文档](https://kripken.github.io/emscripten-site/docs/porting/connecting_cpp_and_javascript/index.html)。

## 并非没有它的局限性

Emscripten 在几个领域仍然受到限制。例如，不能使用多线程的代码，因为 JavaScript 是单线程的。我能想到的最好的解决方法是，你可以将这些代码分割成[个 web workers](http://www.html5rocks.com/en/tutorials/workers/basics/) ，这是 JavaScript 模拟多线程的方式。

还有一些已知可以编译的代码，但是运行时会出现性能问题。值得注意的是，64 位整型变量。数学运算很慢，因为它们是仿真的，但随着 [浏览器采用](https://dev.modern.ie/platform/status/?utm_source=SitePoint&utm_medium=article62&utm_campaign=SitePoint/?filter=f3f0000bf&search=simd)，包含 [SIMD(单指令多数据)应该会在不久的将来有所帮助。](https://hacks.mozilla.org/2014/10/introducing-simd-js/)

关于便携性指南的这一页可以提供更多信息。

## 后续行动

今天我不想深入讨论这个问题，但在本系列的下一篇教程中，我想介绍一些更复杂的例子，它们利用了 SDLib 以及一个中间件工具，如 Unreal Engine 4 或 Unity，以便我们可以看到 asm.js 在浏览器中的实际表现。

## 更多的 Web 开发实践

这篇文章是微软技术倡导者的 web 开发系列的一部分，内容涉及实用的 JavaScript 学习、开源项目和互操作性最佳实践，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=17917-DEV-sitepoint-footer) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=17917-DEV-sitepoint-footer)。

我们鼓励您使用 [dev.modern.IE](http://dev.modern.ie/?utm_source=SitePoint&utm_medium=footer&utm_campaign=SitePoint) 上的免费工具跨浏览器和设备进行测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=footer&utm_campaign=SitePoint)
*   [下载适用于 Mac、Linux 和 Windows 的免费虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=footer&utm_campaign=SitePoint)
*   [跨浏览器检查网络平台状态，包括微软 Edge 路线图](https://dev.modern.ie/platform/status/?utm_source=SitePoint&utm_medium=footer&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=footer&utm_campaign=SitePoint)

从我们的工程师和传道者那里获得更深入的学习:

*   [GitHub 编码实验室:跨浏览器测试和最佳实践](https://github.com/deltakosh/interoperable-web-development)
*   【2015 年微软 Edge 网络峰会(来自我们的工程团队和 JS 社区)
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web/?WT.mc_id=17917-DEV-sitepoint-footer)
*   使网络正常工作的边缘渲染引擎(Jacob Rossi)
*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge/?WT.mc_id=17917-DEV-sitepoint-footer)(来自 David Catuhe)
*   托管网络应用和网络平台创新(来自律师奶爸和基里尔·赛克谢诺夫)

我们的社区开源项目:

*   伏龙。JS (跨设备远程 JavaScript 测试)
*   [manifoldJS](http://manifoldjs.com/?WT.mc_id=17917-DEV-sitepoint-footer) (部署跨平台托管的 web 应用)
*   [babylonJS](http://babylonjs.com/?WT.mc_id=17917-DEV-sitepoint-footer) (轻松制作 3D 图形)

更多免费工具和后端 web 开发工具:

*   [适用于 Linux、MacOS 和 Windows 的 Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=17917-DEV-sitepoint-footer)
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422/?WT.mc_id=17917-DEV-sitepoint-footer) 和[在 Azure 上免费试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=17917-DEV-sitepoint-footer)

## 分享这篇文章