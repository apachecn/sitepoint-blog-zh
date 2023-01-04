# Android 库项目入门，第 1 部分

> 原文：<https://www.sitepoint.com/getting-started-with-android-library-projects-part-1/>

Android 库项目提供了可重用的代码和资源，可以在开发应用程序时节省您的时间和精力——您不必继续“重新发明轮子”本文是向您介绍 Android 库项目的三部分系列的第一部分。在第 1 部分中，您将学习图书馆项目的基础知识。

## Android 库项目基础

一个 *Android 库项目*是一个可共享 Android 源代码和资源的容器，可以从 Android 应用程序项目中引用。在构建应用程序项目时，一个库项目被合并到一个 Android 包(APK)文件(文件扩展名为`.apk`)中。多个应用程序项目可以引用同一个库项目。任何单个应用程序项目都可以引用多个库项目。

在 Android 4.0 SDK (r14)之前，在编译一个 app 的源代码和资源时，Android 库项目被视为额外的源代码和资源文件夹。因为这种安排在 Eclipse 中被证明是极其脆弱的，并且因为开发人员更喜欢将编译代码和资源的库作为 JAR 文件分发，r14 为库项目引入了编译代码库机制。查看 Android SDK Tools 中对库项目的[更改，r14](http://android-developers.blogspot.ca/2011/10/changes-to-library-projects-in-android.html) 了解更多信息。编译代码库机制是将库作为 JAR 文件分发的第一步。在这个 JAR 分发功能完全实现之前(在 Android 工具的未来版本中)，需要解决 Android 资源及其编译 id 的本质问题。

Android 库项目类似于 Android 应用程序项目，因为它也包括项目根目录中的项目清单文件。此外，该目录包含`src`、`res`和其他目录，您也可以在构建 app 项目时找到这些目录。此外，项目可以包含与 app 项目中相同种类的源代码和资源。

然而，有一个显著的区别。您不能将库项目编译到 APK 文件中，因为库项目不描述应用程序。相反，当应用程序的项目和源代码引用库项目时，它提供有助于应用程序的可重用代码和资源。在构建时，这些代码和资源被合并到应用程序的 APK 文件中。

当构建依赖于库项目的应用程序时，库首先被编译成一个临时 JAR 文件，该文件随后与应用程序项目的代码和资源合并。如果在应用程序和库中定义了相同的资源 ID，SDK 会确保应用程序的资源获得优先级；库项目的资源没有编译到 APK 文件中。

一个应用程序项目可以添加对多个库项目的引用，并指定它们的相对优先级。当从应用引用的多个库定义相同的资源 ID 时，SDK 从具有较高优先级的库中选择资源，并丢弃其他资源。这些库与应用程序一次合并一个库，从最低优先级开始，到最高优先级。

库项目可以引用其他库项目。此外，他们可以导入不引用 Android APIs 的外部基于 JAR 的编译 Java 代码库。

开发 Android 库项目及其相关应用程序时，需要记住几个事项:

*   *使用前缀避免资源冲突。*例如，为了保证唯一性，我将反转我的域名，并在库项目资源文件中定义`@+id/ca_tutortutor_gameboard`而不是`@+id/gameboard`。

*   *您不能将库项目导出到 JAR 文件。*该功能将在 SDK 工具的未来版本中引入。

*   一个库项目可以包含一个 JAR 库。您必须手动编辑依赖应用程序项目的构建路径，并向 JAR 文件添加一个路径。

*   *一个库项目可以依赖于一个外部 JAR 库。*依赖 app 项目必须针对包含外部库的目标进行构建。此外，库和依赖的应用程序项目必须在它们的清单文件中的`<uses-library>`元素中声明外部库。

*   *库项目不能包含原始资产。*原始资产文件(保存在库项目的`assets`目录中)被忽略。应用程序使用的任何资产资源都必须存储在其项目的`assets`目录中。

*   *库项目平台版本号必须小于等于 app 项目平台版本号。*一个库项目使用的 API 级别(比如 10，匹配 Android 2.3.3)应该和依赖 app 使用的 API 级别相同或者更低。

*   对库包名称没有限制。库的包名不需要与其依赖的应用程序的包名匹配。

*   *每个库项目创建自己的`R`类。*每个库都有自己的`R`类，根据库的包名命名。从应用程序项目及其库项目生成的`R`类在所有需要的包中创建，包括应用程序项目包和库包。

*   *一个库项目的存储位置是灵活的。*库项目可以存储在硬盘上的任何位置，只要相关的应用程序项目可以引用它们(通过相对链接)。

Android 库项目是通过 Android SDK 以类似于 Android 应用程序项目的方式创建的。以下命令行语法(为便于阅读，分为多行)概括了创建库项目所需指定的内容:

```
android create lib-project --target target_ID
                           --path /path/to/project/project_name 
                           --package library_package_namespace
```

`create lib-project`命令类似于`create project`(用于创建一个应用程序项目)，但也在项目的`project.properties`文件中添加了下面一行，表示该项目是一个库:

```
android.library=true
```

您也可以在创建库项目时指定`--name *project_name*`。然而，您通常可以省略`--name`,因为分配给这个可选参数的值除了分配给项目的`build.xml`文件中的`<project>`元素的`name`属性之外不会被使用。

要从依赖的应用程序项目引用库，首先创建应用程序项目(例如，通过`android create project`)。然后，根据以下抽象语法(为了便于阅读，分为多行)执行更新命令，以引用库项目:

```
android update project --target target_ID 
                       --path /path/to/project/project_name 
                       --library relative/path/to/library/project
```

`update project`命令通过向应用程序项目的`project.properties`文件添加以下行来更新应用程序项目以引用库项目:

```
android.library.reference.n=library_project_relative_path
```

库项目引用的形式为`android.library.reference.*n*`，其中 *`n`* 是从 1 开始的整数值。重复调用`android update project`命令，其中每个命令标识一个不同的库项目，引入了对`project.properties`的额外库引用。对于每个连续的引用，整数值递增 1 (2、3、4 等)。不允许数字之间有空洞(例如，指定了 1 和 3，但没有指定 2)；出现在孔后面的引用被忽略(例如，当没有指定 2 时，3 将被忽略)。

## 结论

既然您已经了解了 Android 库项目的基础，那么您就可以开始研究一个例子了。在本系列的第二部分中，我将展示一个关于对话框库项目。在库中存储可重用的关于对话框和其他用户界面项目(如自定义小部件)有助于减少应用程序开发时间。

## 分享这篇文章