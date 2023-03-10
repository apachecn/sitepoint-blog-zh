# ClojureScript 简介

> 原文：<https://www.sitepoint.com/introduction-to-clojurescript/>

*本文由 [Thomas Greco](https://www.sitepoint.com/author/tgreco/) 和 [Jérémy Heleine](https://www.sitepoint.com/author/jheleine/) 进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

几个月以来，越来越多的开发者秉承“永远赌 JavaScript”的理念。尽管如此，编译成 JavaScript 的语言数量正在增长。这种语言的一些例子是 Dart、TypeScript、CoffeeScript 和 ClojureScript。

在本文中，我们将讨论 ClojureScript，这是一个针对 JavaScript 的新编译器。我们将了解使用 ClojureScript 的好处，以及如何快速设置它以使用 npm 和您最喜欢的 Node.js 库。

## 为什么是 ClojureScript？

网上有很多文章解释 ClojureScript 的好处。一些汇总的高级点包括:

*   **简单性**:关于语法，ClojureScript 是一种基于 Lisp 的语言，给了它一个最小的语法。事实上，它是如此之小，以至于我们能够在本文中涵盖它。除了简单的语法之外，ClojureScript 还提供了帮助简化异步代码的工具。
*   安全:这意味着更少的 bug！ClojureScript 和其他函数式编程语言有许多有助于减少和缓解常见错误的属性。
*   **性能** : ClojureScript 使用[谷歌的闭包编译器](http://code.google.com/closure/compiler/docs/api-tutorial3.html)。这允许 ClojureScript 利用[死代码消除](https://en.wikipedia.org/wiki/Dead_code_elimination)和其他特性。
*   **现场编码**:ClojureScript 生态系统提供了很多工具来做“现场编码”。这意味着一旦代码被更改，它会立即反映到您的实际项目中。在这篇文章中，我们将看看 [Figwheel](https://github.com/bhauman/lein-figwheel) ，这样你可以更好地理解这个概念。
*   代码重用:ClojureScript 可以通用运行，或者像很多人说的那样，“同构运行”这意味着您可以在客户机和服务器上运行相同的代码。这已经成为 Node.js 生态系统中的一种流行模式。此外，ClojureScript 可以从 Node.js 和 Java 生态系统中导入库。

## 设置 Clojure(脚本)工具链

在本文中，我们将在 Mac OSX 环境中安装工具链。ClojureScript wiki 有[在其他环境下安装的指南](https://github.com/clojure/clojurescript/wiki/Windows-Setup)，以备您需要。我们需要一些系统依赖项来开始。其中之一是[家酿](https://github.com/Homebrew/homebrew)，受欢迎的 OSX 包装经理。

### 安装最新的 Java 版本

ClojureScript 需要最新的 Java 版本(撰写本文时是版本 8)。在这些练习中，如果您在运行`lein`时遇到类似如下的错误:

```
Exception in thread "main" java.util.regex.PatternSyntaxException: 
    Unknown inline modifier near index 2 (?U)^[\p{Alpha}_$]^, compiling:(cljs/util.clj:158:33)
```

那么你需要最新版本的 Java。

首先，在命令行界面上执行以下命令:

```
brew tap caskroom/cask
brew install brew-cask
```

如果您得到错误“已经安装”，按照将出现在您的脚本中的说明取消链接。完成后，再次安装。这可以通过以下命令完成:

```
brew unlink brew-cask
brew install brew-cask
```

此时，执行我们需要的最后一个命令:

```
brew cask install java
```

### 安装 Leiningen 雷宁根

Leiningen 是 Clojure 项目的构建工具。我们将使用它来运行 ClojureScript 代码并安装依赖项。这一步假设安装了[自制软件](https://github.com/Homebrew/homebrew)，给我们`brew`命令。

```
brew install leiningen
```

如果该步骤失败，可能需要[手动安装](https://github.com/technomancy/leiningen#installation)。

## 使用 Repl

现在我们已经安装了 Leningen，可以开始熟悉 ClojureScript 语法了。

执行命令`lein repl`，您应该得到类似的输出:

```
$ lein repl
nREPL server started on port 58371 on host 127.0.0.1 - nrepl://127.0.0.1:58371
REPL-y 0.3.7, nREPL 0.2.10
Clojure 1.7.0
Java HotSpot(TM) 64-Bit Server VM 1.6.0_65-b14-466.1-11M4716
    Docs: (doc function-name-here)
          (find-doc "part-of-name-here")
  Source: (source function-name-here)
 Javadoc: (javadoc java-object-or-class-here)
    Exit: Control+D or (exit) or (quit)
 Results: Stored in vars *1, *2, *3, an exception in *e

user=>
```

我们现在处于一个封闭的脚本回复中。这允许我们快速执行 ClojureScript 并查看结果。要退出 repl，您可以按`Control+D`。

完成这一步后，我们现在就可以开始研究 ClojureScript 的语法了，并且乐在其中！

## ClojureScript 语法

ClojureScript 是一种函数式语言，这意味着它具有函数和有限的附加语言结构。在接下来的部分中，我将介绍这种语言的特性。

### 基元

ClojureScript 具有以下基本类型:

*   编号

    ```
    user=> 1.23
        1.23
    ```

*   字符串

    ```
    user=> "foo"
        "foo"
    ```

*   向量(数组)

    ```
    user=> [:bar 3.14 "hello"]
        [:bar 3.14 "hello"]
    ```

*   映射(关联数组)

    ```
    user=> {:msg "hello" :pi 3.14 :primes [2 3 5 7 11 13]}
        {:msg "hello", :pi 3.14, :primes [2 3 5 7 11 13]}
    ```

*   关键字(用于访问地图)

    ```
    user=> :foo
        :foo
    ```

*   Set (distinct array)

    ```
    user=> #{:bar 3.14 "hello"}
        #{"hello" 3.14 :bar}
    ```

### 一路向下运行

函数是 ClojureScript 的构建块。您甚至可以使用内置的`defn`函数定义自己的函数。

下面你可以看到一个函数定义的例子。这里，我们将定义一个名为`myfunction`的函数。它接受一个参数`argument1`并返回它。这不是一个非常有用的函数，但它是一个很好的语法示例。

```
user=> (defn myfunction [argument1] argment1)
```

如果语法看起来有点奇怪，这是它在 Javascript 中的等价形式:

```
function myfunction(argument1){
    return argument1;
}
```

通过用括号将函数的名称和参数括起来来调用函数:

```
user=> (myfunction "hello world")
"hello world"
```

在非函数式编程语言中，有特殊的“操作符”或关键字。在 Javascript 中，一些常用的运算符是`+ - == if`。在 ClojureScript 和其他基于 Lisp 的语言中，没有特殊的运算符。这些只是常规函数。

如果语句是函数:

```
user=> (if true "do true stuff here" "do false stuff here")
"do true stuff here"
```

数学运算符也是函数，如下所示:

```
user=> (+ 2 3)
5
user=> (* 2 3)
6
```

要获得更多 Javascript to ClojureScript 同义词的示例，您可以访问这个网站。

## 创建 node . js–ClojureScript 项目

启动 ClojureScript 项目很简单。Leningen 提供了项目模板，可以帮助您启动并运行样板项目。

模板是一个很好的资源，可以用来研究 ClojureScript 项目的其他用途和配置。Clojars.org 收集了一些模板，其他的可以在网上搜索到。对于我们的项目，我们将使用一个 [Nodejs Figwheel 项目模板](https://github.com/malyn/figwheel-node-template)。

首先，在命令行界面上执行以下命令:

```
$ lein new figwheel-node hello-world
```

这将在目录`./hello-world`中创建新的 ClojureScript 项目。本文的剩余部分假设`hello-world`被用作项目名称。如果你愿意，你可以使用一个不同的名字，但是我建议你坚持使用这个名字，这样你就可以在不担心出错的情况下阅读这篇文章。

也就是说，移动到创建的目录并安装 npm 依赖项:

```
$ cd hello-world
$ npm install
```

### 兴趣点

项目文件夹包含几个文件。在这一部分，我想强调一些关于它们的重要概念:

*   `package.json`:这个应该从 Node.js 项目中熟悉。我们的`npm`依赖项将被添加到这里。
*   `project.clj`:该文件是 ClojureScript 项目配置文件。这是 ClojureScript 版本的`package.json`，我们在其中配置 Clojure 依赖项和编译目标。该文件还包含项目详细信息，如标题和描述。
*   `figwheel.js`:该文件是针对 Figweel 项目的。这是我们项目的引导文件。它将 Figwheel 指向我们的源代码，这样它就可以监控它的更新。我们将使用`node figwheel.js`运行它。
*   这是我们的入口点源文件。这是我们开始这个项目的地方。可以把它想象成 Node.js 项目中的一个`index.js`文件。

`core.cljs`文件包含以下内容。我在上面添加了注释，这样你就能明白发生了什么:

```
;; This defines a namespace and necesscary dependencies for the current file
(ns hello-world.core
  (:require [cljs.nodejs :as nodejs]))

;; This updates the default println method to write to Node.js stdout
(nodejs/enable-util-print!)

;; The main function of the module
;; it prints "Hello World!" to stdout
(defn -main []
  (println "Hello world!"))

;; *main-cli-fn* is a semi-magic var that's used to set the entry
;; *point for a node app
(set! *main-cli-fn* -main)
```

### 运行项目

要执行当前项目，打开一个终端窗口，并移动到我们的 hello-world 项目目录。然后，执行以下命令:

```
lein figwheel
```

这将启动 Figwheel 等待更新构建。保持此终端运行，并打开一个单独的终端。在这个新的终端中，再次移动到项目目录并执行命令:

```
node figwheel.js
```

您应该会看到如下所示的输出“Hello world ”:

```
$ node figwheel.js
Hello world!
Figwheel: trying to open cljs reload socket
Figwheel: socket connection established
```

## 将 Express.js 用于 web 服务器

现在我们已经有了 ClojureScript 项目设置的基础，让我们开始在新的终端中使用一些熟悉的库。在我们的`hello_world`目录中执行命令:

```
npm install --save express
```

那么我们需要更新`./src/hello-world/core.cljs`如下:

```
(ns hello-world.core
  (:require [cljs.nodejs :as nodejs]
            [clojure.string :as string]))

(nodejs/enable-util-print!)

(defonce express (nodejs/require "express"))
(defonce http (nodejs/require "http"))
(defonce server-port 3000)

(def app (express))

(. app (get "/hello"
      (fn [req res] (. res (send "Hello world")))))

(def -main
  (fn []
    (doto (.createServer http #(app %1 %2))
      (.listen server-port))))
      (.listen server))))
    (println (string/join " " ["Server running on" server-port]) )

(set! *main-cli-fn* -main)
```

现在，当您在项目上运行`node figwheel.js`时，您应该会看到输出为`running on 3000`。如果你访问网址[http://localhost:3000/Hello](http://localhost:3000/hello)，你应该会看到快速路由的结果写着“Hello world”

## 结论

在本文中，我们讨论了如何设置一个新的 ClojureScript 项目，并在其中安装一个流行的节点依赖项。这为我们更加熟悉 ClojureScript 这种语言打下了良好的基础。我还整理了这个项目的源代码，你可以在 Github 上找到。它超出了本文的范围，演示了如何集成 React 服务器端呈现。

## 分享这篇文章