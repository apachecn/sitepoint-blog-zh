# 介绍 Nashorn JavaScript 引擎

> 原文：<https://www.sitepoint.com/introducing-nashorn-javascript-engine/>

[Nashorn](http://openjdk.java.net/projects/nashorn/) 是 Oracle 用 Java 编程语言开发的新 JavaScript 引擎，随 Java 8 发布。Nashorn 的目标是用原生 JVM 用 Java 实现一个轻量级的高性能 JavaScript 运行时。通过使用 Nashorn，开发人员可以在 Java 应用程序中嵌入 JavaScript，还可以从 JavaScript 代码中调用 Java 方法和类。

## 为什么离开犀牛？

[Rhino](http://www.mozilla.org/rhino) 是 Nashorn 的前身。它于 1997 年在网景公司作为一个项目开始，并于 1998 年发布。

Rhino 发布至今已经有 16 年了，这个 JavaScript 引擎已经寿终正寝了。因此，Java 人员决定从头开始开发一个新的 JavaScript 引擎，而不是重写现有的引擎。这就诞生了 nashorn(有趣的事实:Nashorn 在德语中是犀牛的意思)。

这里几乎所有人都一直在浏览器中使用 JavaScript，也有人一直在服务器上使用(像 Node.js)，但 Nashorn 是为了另一个目的而开发的。通过使用 Nashorn，开发人员可以实现以下神奇功能:

*   将 JavaScript 作为本机桌面代码运行。
*   使用 JavaScript 编写 shell 脚本。
*   从 JavaScript 代码中调用 Java 类和方法。

## 纳松的目标

当 Nashorn 被设计出来时，开发人员为它确定了一系列目标:

*   它应该基于 ECMAScript-262 版本 5.1 语言规范，并且必须通过 ECMAScript-262 兼容性测试。
*   它应该支持 [javax.script](http://docs.oracle.com/javase/7/docs/api/javax/script/package-frame.html) (JSR 223) API。
*   它应该允许从 JavaScript 调用 Java，反之亦然。
*   它应该定义一个命令行工具`jjs`，用于评估“shebang”脚本(通常以`#!/bin/sh`开头)中的 JavaScript 代码，这里是文档和编辑字符串。
*   它的性能应该明显优于 Rhino。
*   它应该没有安全风险。

此外，没有人决定 Nashorn 将不包括调试和不支持 CSS 和 JavaScript 库/框架。这意味着 Nashorn 可以在浏览器中实现，而不会成为一场噩梦。

## 外壳中的 JavaScript

为了通过使用 Nashorn 的`jjs`工具在 shell 中使用 JavaScript，您应该首先安装 [JDK8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ，它可以免费下载。要测试其安装，请执行:

```
>_ javac -version
# it should echo
# java version "1.8.x"
jjs -version
# it should echo
# nashorn 1.8.x
jjs>
```

> 如果您在使用第一个或第二个命令时遇到任何问题，请尝试在路径中添加 JDK

现在，您可以将 JavaScript 用作 shell 脚本。看看这个简单的例子:

```
jjs> var a = 1
jjs> var b = 4
jjs> print (a+b)
5
jjs>
```

您可能已经想通了，您不必将代码写入`jjs` shell。您可以将代码写入 JavaScript 源文件，然后从 shell 中调用它。考虑以下 JavaScript 代码:

```
var isPrime = function(num) {
    if (isNaN(num) || !isFinite(num) || num < 2) 
        return false;

    var m = Math.sqrt(num);

    for (var i = 2;i <= m; i++) 
        if (num % i === 0) 
            return false;

    return true;
}

var numbers = [ 2, 3, 4, 5, 6, 7, 8, 9, 10 ];

for (var i = 0; i < numbers.length; i++) {
    if (isPrime(numbers[i]))
        print(numbers[i] + " is prime");
    else
        print(numbers[i] + " is not prime");
}
```

假设代码在一个名为`prime.js`的文件中，我们可以通过执行以下命令在 shell 中运行它:

```
>_ jjs prime.js
2 is prime
3 is prime
4 is not prime
5 is prime
6 is not prime
7 is prime
8 is not prime
9 is not prime
10 is not prime
```

这可能会让您想起 Python 代码或 bash 脚本，但它是 JavaScript。为了使它更像 bash，Nashorn 给出了`arguments`变量来提取命令行参数。考虑这个例子:

```
if (arguments.length === 0)
    print("No command-line arguments.");
else {
    print("Called with these command-line arguments:");

    for each (cli_arg in arguments) {
        print(cli_arg);
    }
}
```

运行它将得到以下输出(参数在`--`之后):

```
>_ jjs cliargs.js
No command-line arguments.

>_ jjs cliargs.js -- a b "c d e"
Called with these command-line arguments:
a
b
c d e
```

此外，JavaScript 可以使用 Java 类和方法。请看这个多线程 JavaScript 代码的例子:

```
var Thread = Java.type("java.lang.Thread"); 
var Runnable = Java.type('java.lang.Runnable');

var Run1 = Java.extend(Runnable, { 
    run: function() { 
        print("One thread");
        print("One thread");
    } 
}); 

new Thread(function() {
    print("Another thread");
    print("Another thread");
    print("Another thread");
}).start()

new Thread(new Run1()).start();
```

输出将是:

```
Another thread
Another thread
One thread
One thread
Another thread
```

从输出可以看出代码是多线程的。通过使用`Java.type("java.lang.Thread");`,我们可以在 JavaScript 代码中调用 Java 类。Nashorn 甚至允许反其道而行之，在 Java 代码中调用 JavaScript 代码。

```
package j2js.example;

import javax.script.ScriptEngine;
import javax.script.ScriptEngineManager;
import javax.script.ScriptException;

public class Main {

    public static void main(String[] args) {

        ScriptEngine nashorn = new ScriptEngineManager().getEngineByName("nashorn");

        try {
            nashorn.eval("print('Am I Java or JavaScript?')");
        } catch (ScriptException e) {
            e.printStackTrace();
        }
    }

}
```

这个例子只是在第 14 行打印了`Am I Java or JavaScript?`问题，但是这是将 JavaScript 代码转换成 Java 的最简单的例子。可以使用 Java 方法从 JavaScript 文件中读取整个源代码，然后将代码作为字符串参数传递给`eval()`方法。这将使 JavaScript 代码在 Java 内部执行。

## 结论

现在 JavaScript 无处不在！您可以将它用于客户端应用程序、服务器端应用程序，甚至更好，有时用于客户端和服务器端。你可以将它用于[移动应用](https://www.sitepoint.com/native-vs-hybrid-app-development/)或者设置[小型物联网](https://www.sitepoint.com/controlling-arduino-nodejs-johnny-five/)。现在，有了 Nashorn，通过利用 JavaScript 的简单性和 Java 的丰富 API，您可以将它用作一种强大的类似 shell 的脚本语言。

## 分享这篇文章