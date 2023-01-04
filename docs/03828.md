# 使用 Dart 和 Redstone 轻松定制 Web 服务器

> 原文：<https://www.sitepoint.com/custom-web-servers-dart-redstone/>

使用 [Node.js](https://nodejs.org/) 创建服务器端脚本现在非常流行，而且[有充分的理由](http://blog.modulus.io/top-10-reasons-to-use-node)。它是快速的、事件驱动的，也许对 web 开发人员来说最好的是，它由 JavaScript 驱动。如果你的前端代码都是 JavaScript，那么在后端使用相同语言的好处是显而易见的。Node 甚至有很棒的服务器端框架，如 [Express](http://expressjs.com/) ，可以快速轻松地创建定制的 Web 服务器。

但是有没有更好的办法呢？

## Dart 是什么？

Dart 是一种开源的、可扩展的、面向对象的编程语言，具有强大的库和运行时，用于构建 web、服务器和移动应用。它最初是由 Lars Bak 和 Kasper Lund 为谷歌开发的，但后来成为了 ECMA 的一个标准。

当您在服务器端使用 Dart 和 [Redstone](http://redstonedart.org/) 框架时，您可以获得 Node 的所有优势以及更多优势。作为额外的收获，您将抛弃 JavaScript 的怪癖。像 Node 一样，Dart 虚拟机是事件驱动的、异步的，并允许您用一种语言构建客户端和服务器应用程序，并在它们之间共享代码。这里没有足够的空间来详述 Dart 相对于 JavaScript 的所有[优势](https://www.youtube.com/watch?v=FqsU3TbUw_s)(可能是另一篇文章)，但是如果你对更多细节感兴趣，请点击下面的链接。

### Dart 的优势

*   不可变对象和更简单的语义，允许虚拟机中更好的代码优化(更快的速度)。
*   [可选类型](https://www.dartlang.org/docs/dart-up-and-running/ch02.html#variables)并支持终结符和常量。
*   支持带有默认值的可选位置或命名函数参数。
*   [变量、闭包和`this`的词法范围](https://www.dartlang.org/docs/dart-up-and-running/ch02.html#functions)。
*   无变量提升。
*   在赋值或比较中没有类型强制。
*   [期货(承诺)和现金流](https://www.dartlang.org/docs/dart-up-and-running/ch02.html#asynchrony)。
*   没有`undefined`；就`null`。
*   只有`true`是真实的。
*   综合[标准库](https://www.dartlang.org/docs/dart-up-and-running/ch03.html)。
*   [语法糖](https://www.dartlang.org/docs/dart-up-and-running/ch02.html#classes)减少类构造函数的冗长。
*   内置对[代码模块](https://www.dartlang.org/docs/dart-up-and-running/ch02.html#libraries-and-visibility)的支持，支持延迟加载。
*   Dart 有自己先进的代码剖析器，[天文台](https://www.dartlang.org/tools/observatory/)。
*   观看[从 Node.js 到 Dart](https://youtu.be/NHsmiY0rFS8?list=PLOU2XLYxmsIIQorIS8gagUiMau9S84vZV) 的迁移，了解一个开发人员的体验。

这份名单只是触及了表面。查看在线书籍 [Dart: Up and Running](https://www.dartlang.org/docs/dart-up-and-running/) 来学习这门语言的速成课程。如果您了解 JavaScript、Java、PHP、ActionScript、C/C++或另一种“花括号”语言，您会发现 Dart 很熟悉，并且您可以在一个小时左右的时间内高效地使用 Dart。

## 去拿飞镖

有许多编辑器支持 Dart 开发，Dart 团队已经宣布 [JetBrains WebStorm](https://www.jetbrains.com/webstorm/) 将成为未来的首选编辑器，但为了保持简单(和免费)，我们将在本教程中使用流行的 [Sublime Text 3](http://www.sublimetext.com/3) 和一个 [Dart 插件](https://github.com/guillermooo/dart-sublime-bundle)。尽管它在技术上仍处于测试阶段，但它是推荐使用的版本。

### 下载软件

你需要一些软件来完成这个教程。

#### 崇高文本 3

如果你还没有 Sublime Text 3，[下载并安装](http://www.sublimetext.com/3)适合你操作系统的版本。撰写本文时的最新版本是 3083。

#### Dart SDK

为您的系统下载正确的 [Dart SDK](https://www.dartlang.org/downloads/) 。请注意，对于本教程，您将不需要编辑器(现在已被否决)或 [Dartium](https://www.dartlang.org/tools/dartium/) (一个带有嵌入式 Dart VM 的 Chromium 特殊构建)。

解压缩 Dart SDK 并将`dart-sdk`文件夹放在系统上的任意位置。在 Windows 上，我更喜欢`C:/Program Files/dart/dart-sdk`。

### 配置崇高文本 3

运行崇高文本 3。您需要配置编辑器来支持 Dart。

#### 包装控制

如果你还没有安装[软件包控制](https://packagecontrol.io)，现在就按照[的说明](https://packagecontrol.io/installation)来安装。请注意，安装完成后，您需要重新启动 Sublime Text 3。

#### Dart 插件

1.  在 Sublime 的菜单中，选择*工具- >命令面板…* ，输入`install`。
2.  从下拉菜单中选择*包控制:安装包*。
3.  键入`dart`并选择 Dart 包。请注意，您可能需要重新启动 Sublime 才能使用该插件的所有功能。
4.  从 Sublime 菜单中选择*首选项- >套餐设置- >飞镖- >设置-用户*。这将为 Dart 插件打开一个设置文件。
5.  将以下代码输入设置文件并保存，其中`/path/to/dart-sdk`是系统上`dart-sdk`文件夹的路径。

```
{ 
  "dart_sdk_path": "/path/to/dart-sdk" 
}
```

## 创建 Dart 项目

1.  在 Sublime 的菜单中，选择*工具- >命令面板…* ，输入`Dart:`。
2.  选择 *Dart: Stagehand* ，然后选择 *console-full* 创建一个命令行应用程序。
3.  在 Sublime 窗口的底部，输入您希望 Dart 的 Stagehand 工具创建新 Dart 项目的路径。请注意，目标目录必须是新的或空的。我建议给它起个类似`redstone_intro`的名字。

**注意**:如果在上述过程中，您看到 Stagehand 未启用的错误，您需要从终端执行以下操作:

```
cd /path/to/dart-sdk/bin
pub global activate stagehand
```

### 获取依赖关系

创建好新项目后，打开文件`pubspec.yaml`。Dart 使用 pubspec 文件来管理项目的依赖关系。将`pubspec.yaml`中预先生成的依赖项部分替换为如下所示的部分(删除任何表示注释的`#`字符):

```
dependencies:
  redstone: '>=0.5.21 <0.6.0'
```

保存文件。Sublime 会自动指示 Dart 的包管理器，名为 [Pub](https://www.dartlang.org/tools/pub/) ，获取所有必要的依赖项，包括 Redstone 框架。Pub 将只获取指定范围内的 Redstone 版本。你也可以在编辑`pubspec.yaml`的时候用热键`F7`让 Sublime 获取你的依赖。

有关 Redstone 的更多信息和示例，请参见该项目的 Github [wiki](https://github.com/redstone-dart/redstone/wiki) 。

## 创建 Web 服务器

用 Redstone 设置一个简单的服务器很容易。打开`main.dart`文件，删除所有预先生成的代码。将下面的代码插入它的位置。

```
import 'package:redstone/server.dart' as Server;

void main() {
  Server.setupConsoleLog();
  Server.start();
}
```

因为这可能是您的第一个 Dart 程序，所以让我们一行一行地分析这段代码。熟悉 Java、JavaScript、C#或类似语言的开发人员会发现这些概念中的大部分都很熟悉。

```
import 'package:redstone/server.dart' as Server;
```

首先，您告诉 Dart 分析器您将使用来自 Redstone 的`server.dart`的代码。特殊的`package:`前缀表示该代码是由 Pub 获得的外部依赖项。(如果你愿意，你可以通过研究项目中`packages`文件夹的内容来检查这个包和所有其他下载的包。)这会将 Redstone 的类和顶级函数导入到 Dart 程序的名称空间中。因为它包含了像`start()`这样的普通名称的函数，所以您将导入的代码包含在一个名为`Server`的定制名称空间中，语法为`as Server`。

```
void main()
```

所有的 Dart 程序都从顶层的`main()`函数开始执行。Dart 允许您选择性地指定变量和函数返回值的类型，`void`表示`main()`将不返回任何内容。

```
Server.setupConsoleLog();
```

您在别名`Server`下导入了 Redstone 包，因此在调用其函数时必须使用该引用。这个调用不是绝对必要的，但是在开发过程中很有帮助。它为 Redstone 框架设置控制台日志记录，因此当 Redstone 的代码执行时，信息性消息将出现在控制台中。

```
Server.start();
```

这一行调用 Redstone 的`start()`函数，启动 web 服务器。默认情况下，它监听`0.0.0.0:8080`(端口 8080 上的当前 IP)上的请求，尽管这是可配置的。

就是这样！您的服务器还没有以任何有意义的方式响应请求，但是它正在监听。用热键`Shift+F7`运行`main.dart`中的代码。控制台输出将出现在 Sublime 的输出面板中，默认情况下显示在 Sublime 界面的下部。

```
INFO: <current date/time>: Running on 0.0.0.0:8080
```

您可以使用热键`Ctrl+Keypad0`(即键盘上的 Ctrl 和 zero 键)停止正在运行的应用程序。

**注意**:您也可以通过终端启动/停止服务器；

```
cd /path/to/dart-sdk/bin
./dart /path/to/redstone_intro/bin/main.dart
```

要通过 Sublime 的命令面板访问所有的 Dart 文件命令(如果你没有键盘，这是必要的)，从菜单中选择*工具- >命令面板…* ，并键入`Dart:`，然后选择你需要的命令。快捷键是`Ctrl+., Ctrl+.`(按住`Ctrl`并点击两次句点)。

更多方便的快捷键，请参考 Dart 插件的[快捷键](https://github.com/guillermooo/dart-sublime-bundle/wiki/Shortcuts)页面。

## 路径段参数

现在让服务器响应一些请求。您可以使用 Redstone 的`Route`注释来设置一个处理程序。

### 你好

将以下代码添加到`main.dart`的末尾(在`main()`函数之后)。

```
@Server.Route("/hello")
String hello() {
  print("User soliciting greeting...");
  return "Hello, Browser!";
}
```

注意，您仍然需要在注释中包含对`Server`的引用，因为这是您在导入 Redstone 时应用到它的别名。注释(以`@`开头)告诉 Redstone 的路由器，每当收到以下形式的请求时，就用`hello()`函数的返回值进行响应:

```
http://localhost:8080/hello
```

如果您的 Dart 服务器脚本仍在运行，请停止并重新启动它，然后打开浏览器并导航到该 URL 以查看运行中的服务器。您应该会看到字符串“您好，浏览器！”出现。同样，对`print()`的调用将向系统控制台输出一条有用的消息。

### 你好

在`main.dart`的末尾追加另一个`Route`块。

```
@Server.Route("/hi")
String hi() => "Hi, Browser!";
```

这段代码与前面的例子非常相似，但是它使用 Dart 的粗箭头语法来定义一个非常短的函数。这样写的话，`hi()`函数将返回箭头后面的一个表达式的结果，在本例中只是一个字符串。

要在您的浏览器中测试此示例，请使用

```
http://localhost:8080/hi
```

## 高级路径段参数

确认静态参数固然很好，但是在现实世界中，您经常需要将动态值传递给服务器，以便接收定制的响应。

### 模拟数据

在接下来的几个练习中，您需要添加一个数据模型作为模拟数据库，以及一些辅助函数。

在`main()`之上，但是在您的`import`语句之下，添加一个用户列表。

```
import 'package:redstone/server.dart' as Server;

List<Map> users = [
  {"id": "1", "username": "User1", "password": "123456", "type": "manager"},
  {"id": "2", "username": "User2", "password": "password", "type": "programmer"},
  {"id": "3", "username": "User3", "password": "12345", "type": "programmer"},
  {"id": "4", "username": "User4", "password": "qwerty", "type": "secretary"},
  {"id": "5", "username": "User5", "password": "123456789", "type": "secretary"}
];

void main() {
  Server.setupConsoleLog();
  Server.start();
}
```

在 Dart 中，[列表](https://api.dartlang.org/apidocs/channels/stable/dartdoc-viewer/dart:core.List)本质上是一个数组，而[映射](https://api.dartlang.org/apidocs/channels/stable/dartdoc-viewer/dart:core.Map)的工作方式类似于标准的 JavaScript 对象(或者字典，或者静态类型语言的 hashmap)。变量`users`被定义为具有 List < Map >语法的映射元素列表。JavaScript 程序员应该熟悉使用方括号和花括号的字面语法。在`main()`之上定义`users`使得它成为一个顶级变量，可以被文件中的所有函数访问。

### 助手功能

现在您已经有了要查询的用户列表，是时候定义几个助手函数来格式化服务器的响应了。把这些加到`main.dart`的末尾。

```
Map success(String messageType, payload) {
  return {
    "messageType": messageType,
    "payload": payload
  };
}

Map error(String errorMessage) {
  print(errorMessage);

  return {
    "messageType": "error",
    "error": errorMessage
  };
}
```

第一个函数`success()`返回一个 Map，它根据两个参数构造这个 Map。`messageType`是一个字符串，可以是“user”或“users”，这取决于服务器是用一个用户还是一组用户来响应。为了灵活起见，`payload`参数故意保持无类型。默认类型的`dynamic`由 Dart 语言应用。

`error()`函数做了本质上相同的事情，但是返回的映射填充了适合错误条件的值。

当其中一个处理程序返回一个 Map 而不是一个简单的字符串时，Redstone 框架会自动将其序列化为 JSON。

### 按 ID 获取用户

现在您已经准备好向`main.dart`添加另一个路由处理程序。

```
@Server.Route("/user/id/:id")
Map getUserByID(String id) {
  print("Searching for user with ID: $id");

  // convert the ID from String to int
  int index = int.parse(id, onError: (_) => null);

  // check for error
  if (index == null || index < 1 || index > users.length) {
    return error("Invalid ID");
  }

  // get user
  Map foundUser = users[index - 1];

  // return user
  return success("user", foundUser);
}
```

路由被配置为接受两个静态参数(`user`和`id`)和一个动态参数(`:id`)。冒号语法表示处理程序需要用户提供的值。为了清楚起见，这个函数的代码故意写得很长，并做了大量注释。

```
print("Searching for user with ID: $id");
```

首先，一条消息被打印到服务器的控制台。`$id`语法利用了 Dart 内置的字符串插值特性(稍后将详细介绍)。

```
int index = int.parse(id, onError: (_) => null);
```

接下来，将传入的`id`从字符串转换为整数，用作列表索引。`int.parse()`接受要转换的值，还可以选择一个回调函数来处理任何解析错误。`onError`是一个命名参数，回调是一个返回`null`的粗箭头函数。回调使用一个参数，但是由于没有使用，按照惯例它的别名是`_`，因此被忽略。在`id`不能被解析成有效整数的情况下，`index`将被赋予`onError`函数的返回值，在本例中为`null`。

```
if (index == null || index < 1 || index > users.length) {
  return error("Invalid ID");
}
```

如果`index`无效或超出范围，这段代码将使用`error()`辅助函数返回一个错误对象，并显示消息“无效 id”。

```
Map foundUser = users[index - 1];
return success("user", foundUser);
```

如果一切正常，您的处理程序会查找并向调用者返回所请求的用户。`success()` helper 函数为您构建类型为“user”的消息映射。有效负载是一个包含用户数据的地图对象。

作为一项测试，将您的浏览器定向到以下 URL:

```
http://localhost:8080/user/id/5
```

结果将是包含所请求的用户数据的 JSON 编码的字符串。

### 按类型获取用户

向您的`main.dart`文件添加另一个处理程序。

```
@Server.Route("/user/type/:type")
Map getUsersByType(String type) {
  print("Searching for users with type: $type");

  // find qualifying users
  List<Map> foundUsers = users.where((Map user) => user['type'] == type).toList();

  // check for error
  if (foundUsers.isEmpty) {
    return error("Invalid type");
  }

  // return list of users
  return success("users", foundUsers);
}
```

这个路由将允许用户被`type`而不是`id`查询。由于给定类型可能有多个用户，如果有必要，您需要准备好返回多个用户。

要构建匹配特定用户类型的用户地图对象列表，使用`where()`函数，它是任何列表对象的标准部分。您向它传递一个函数，该函数对每个元素进行保持测试，如果它检查的元素通过，则返回`true`。`where()`实际上返回了一个 [Iterable](https://api.dartlang.org/apidocs/channels/stable/dartdoc-viewer/dart:core.Iterable) ，它是 List 的祖先，所以您用`toList()`函数将其转换成所需的 List。如果没有找到`type`的用户，`foundUsers`将是一个空列表，在这种情况下服务器返回一个错误对象。

使用适当的 URL 测试新路由。响应对象将包含一个带有两个用户元素的 JSON 数组:

```
http://localhost:8080/user/type/programmer
```

## 查询参数

使用查询字符串和键/值对从 Redstone 获得您需要的东西也同样容易。

将此路线处理程序添加到`main.dart`。

```
@Server.Route("/user/param")
Map getUserByIDParam(@Server.QueryParam("id") String userID) {
  return getUserByID(userID);
}
```

这一次您需要注释处理程序的参数`userID`，使它被一个名为`id`的查询参数的值填充。

```
http://localhost:8080/user/param?id=2
```

## 提供静态页面

如果您希望您的 Dart 服务器发布静态页面，该怎么办？只需多几行代码，您也可以做到这一点。

首先，创建一个名为`web`的文件夹，作为项目的`bin`文件夹的兄弟。在新文件夹中，使用下面的代码创建一个名为`index.html`的 HTML 文件。

```
<!DOCTYPE html>

<html>
  <head>
    <meta charset="utf-8">
    <title>index</title>
  </head>

  <body>
    <p>Hello from index.html!</p>
  </body>
</html>
```

你需要从酒吧多拿几个包来，这样才能顺利。再次打开您的`pubspec.yaml`文件，使依赖部分看起来像这样:

```
dependencies:
  redstone: '>=0.5.21 <0.6.0'
  shelf_static: '>=0.2.2 <0.3.0'
  path: '>=1.3.5 <1.4.0'
```

Redstone 构建在 [Shelf](https://pub.dartlang.org/packages/shelf) 之上，后者是一个较低级别的服务器库，由谷歌的 Dart 团队构建和维护。这允许您使用任何现成的中间件向 Redstone 服务器添加功能。您还引入了[路径](https://pub.dartlang.org/packages/path)来帮助您解析和操作路径字符串。

Sublime 应该在保存`pubspec.yaml`时自动使用 Pub 获取新的依赖项。

一旦这些包被下载到您的项目中，将这些`import`语句添加到`main.dart`的顶部。

```
import 'dart:io' show Platform;
import "package:path/path.dart" as Path;
import 'package:shelf_static/shelf_static.dart';
```

您导入一个 Dart 核心库`io`，以访问`Platform`类。`show`关键字让您只导入`Platform`，将所有其他 I/O 函数和类从程序中删除。

因为路径库有一些顶层函数，它们有共同的名字，所以最好将导入的函数命名为`Path`。

在 main()的开头添加两行。

```
void main() {
  String pathToWeb = Path.normalize(
    "${Path.dirname(Path.fromUri(Platform.script))}/../web"
  );
  Server.setShelfHandler(
    createStaticHandler(pathToWeb, defaultDocument: "index.html")
  );
  Server.setupConsoleLog();
  Server.start();
}
```

您可以通过重新启动 Dart 服务器应用程序并导航到服务器的根目录来测试 index.html 是否得到了服务。

```
http://localhost:8080/
```

我将把它留给读者作为研究 Shelf 和 Path 的练习，但我们应该在这里简要讨论一下 Dart 的一个更有用的特性:字符串插值。您可以使用`${}`将表达式的值放入字符串中。如果表达式只是一个标识符，你只需要`$`。

```
int myNumber = 5;

// 5 is my favorite number
String str1 = "$myNumber is my favorite number.";

// 5 + 10 = 15
String str2 = "$myNumber + 10 = ${myNumber + 10}";
```

## 结论

在本教程中，我介绍了 JavaScript、Node 和 Express 在服务器端的一个奇妙替代方案。Dart 是一种更快的现代语言，可扩展到数百万行代码。Redstone 只是使开发人员的生活更轻松的许多服务器框架之一，但它是我最喜欢的框架之一，因为它充分利用了 Dart 的代码注释特性，减少了设置复杂的服务器交互所需的样板文件的数量。

如果您也使用 Dart 编写客户端代码，那么您可以在客户端和服务器之间共享代码，并且当您的代码库使用不同的语言构建时，您可以避免代价高昂的上下文切换。在开发过程中，您可以使用特殊的 Dartium 浏览器，实现 JavaScript 开发人员多年来一直享受的快速更改和刷新工作流。当您所有的客户端代码都准备好了，只需点击几下鼠标(或命令行输入)， [dart2js](https://www.dartlang.org/tools/dart2js/) 就会将您的 dart 代码编译成适用于所有现代浏览器的 JavaScript，经过缩小、连接、树摇动，并准备好进行部署。

加入黑暗面

## 分享这篇文章