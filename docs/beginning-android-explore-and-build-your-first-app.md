# Android 入门:探索并构建您的第一个应用程序

> 原文：<https://www.sitepoint.com/beginning-android-explore-and-build-your-first-app/>

第 3 课:探索并构建您的第一个应用程序

在[安装了 Android SDK 和 Android 平台](https://www.sitepoint.com/beginning-android-install-an-android-platform/)之后，您就可以为这个平台创建应用程序了。第 3 课向您介绍了第一个应用程序，并向您展示了如何在命令行构建这个应用程序。

## 探索 W2A

通过展示一个输出某种欢迎信息的短程序来介绍一项技术是很有帮助的。清单 1 展示了执行这项任务的小型 Android 应用程序的 Java 源代码。

*清单 1。来自 Android 的问候*

`package ca.tutortutor.w2a;`

`import android.app.Activity;
import android.os.Bundle;
import android.widget.TextView;`

`public class W2A extends Activity
{`

//第一次创建活动时调用下面的方法。
@覆盖

public void onCreate(Bundle savedInstanceState)
{
super . onCreate(savedInstanceState)；
TextView tv = new TextView(本)；
tv.setText("欢迎使用 Android。");
setContentView(电视)；

}
}

清单 1 显示了一个名为`W2A.java`的源文件的内容——Android 应用程序是用 Java 语言编写的，这可能并不奇怪，因为 Android SDK 依赖于安装的 JDK。清单以强制性的 package 语句开始，因为每个 Android 应用程序都与一个包相关联。随后的导入语句标识了从`W2A.java’s` W2A(欢迎使用 Android)类引用的三种特定于 Android 的类型，这有助于编译器定位这些类型。

* * *

**提示:**熟悉 Google 的 Java API 文档，这样可以快速定位 Android 特有的类型。你可以在 http://developer.android.com/reference/packages.html 的[找到这份文件。](http://developer.android.com/reference/packages.html)

* * *

Android 应用程序基于一个或多个相互交互的组件。组件包括活动、广播接收器、内容提供者和服务。为了简洁起见，我忽略了广播接收器、内容提供者和服务。

一个*活动*呈现了应用程序用户界面的一部分，由`android.app.Activity`类描述。清单 1 的`W2A`类继承了 Activity，使得`W2A`成为一个专门化的活动。

当用户启动一个应用程序时，Android 实例化该应用程序的主活动类(该应用程序可能由多个活动类组成)，并调用该类的覆盖版本的`Activity`的`void onCreate(Bundle savedInstanceState)`方法来初始化活动。

如果活动在终止前保存了它的状态，Android 通过向`savedInstanceState`传递一个`android.os.Bundle`实例参数来使那个状态对`onCreate()`可用。否则，它传递一个空参数(没有保存的状态)。

首先用传递的参数调用这个方法的超类版本。然后实例化`android.widget.TextView`类来创建 textview 小部件(用户界面控件)，这是活动 UI 的范围。

* * *

**注意:** `onCreate()`是一个生命周期回调方法的例子。Android 在应用的生命周期中调用`onCreate()`和其他方法。每个生命周期回调方法必须首先调用`Activity`类中同名的方法。

* * *

参数`this`被传递给`TextView`的构造函数，以将这个小部件连接到当前活动。传递给`TextView`的 void `setText(CharSequence text)`方法的文本将显示给用户。

在创建并初始化小部件实例后，`onCreate()`调用`Activity`的`void setContentView(View view)`方法将小部件实例(也称为视图)直接放入活动的视图层次结构中，以便显示小部件。

## 建设 W2A

让我们建设 W2A。首先，使用 android 工具创建一个 W2A 项目，该工具提供了以下用于创建项目的语法(为了便于阅读，分为多行):
`android create project
--target target_ID --name project_name
--path /path/to/project/project_name
--activity default_activity_name
--package package_namespace`

除了`--name`(或-n)，必须指定以下所有选项:

*   `--target`(或-t)标识应用程序的构建目标。`target_ID`是标识一个 Android 平台的整数。通过调用`android list targets`获得该值。因为您只安装了版本 2.3.3，这个命令应该输出一个标识为整数 ID 1 的 Android 2.3.3 平台目标。
*   `--name`(或-n)标识项目的名称。该名称用于生成的 Android 包(APK)的文件名。APK 是一种基于 ZIP 的归档文件，用于存储应用程序的文件，它安装在设备上。它的文件扩展名是`.apk`。
*   `--path`(或-p)标识项目目录。如果目录不存在，则创建该目录。
*   `--activity`(或-a)标识默认的活动类。产生的 classfile 在`/path/to/project/project_name/src/package_namespace/`中生成，如果没有指定`--name`(或-n)，它将被用作 APK 的文件名。
*   `--package`(或-k)标识项目的包名称空间，它必须遵循 Java 语言规范规定的包规则。

假设有一个 Windows 平台和一个将存储 W2A 项目的`C:prjdevW2A`层次结构，下面的命令行(为了可读性分成两行)创建了这个项目:

`android create project -t 1 -p C:prjdevW2A -a W2A
-k ca.tutortutor.w2a`

该命令行创建几个目录，并向其中一些目录添加各种文件。具体来说，它在`C:prjdevW2A`中生成以下文件和目录结构:

*   `AndroidManifest.xml`是正在构建的应用程序的基于 XML 的清单文件。Android manifest 标识了 APK 的应用程序组件，例如之前通过`--activity`(或-a)指定的`Activity`子类，并向 Android 提供其他有用的信息。
*   `ant.properties`是 Ant 构建系统的可定制属性文件。您可以编辑此文件以覆盖 Ant 的默认构建设置，并提供一个指向您的密钥库和密钥别名的指针，以便构建工具可以在以发布模式构建应用程序时对其进行签名(将在本课稍后讨论)。
*   `bin`是 Apache Ant 构建脚本使用的输出目录。生成的 apk 存储在这里。
*   `build.xml`是这个项目的 Apache Ant 构建脚本。
*   包含私有库，这不是这个项目所必需的，但是可能会出现在更复杂的项目中。
*   `local.properties`是一个包含 Android SDK 主目录位置的生成文件。
*   `proguard.cfg`包含 SDK 的 ProGuard 工具的配置数据，该工具允许开发人员*混淆*(隐藏)他们的代码(使其很难进行逆向工程)，作为发布版本的一个组成部分。
*   `project.properties`是一个生成的文件，标识项目的目标 Android 平台。
*   `res`包含项目资源(图像、字符串、布局等等)。此项目不使用任何资源。(为了简洁起见，我在本课程中不讨论资源。)
*   `src`在一系列子目录中包含项目的源代码，这些子目录反映了项目的包结构。

`src`包含一个`catutortutorw2a`目录层次，`w2a`包含一个骨架`W2A.java`源文件。用清单 1 替换这个文件的内容。

假设`C:prjdevW2A`是当前的，通过指定`ant debug`或`ant release`来构建这个应用程序——默认情况下`ant`处理这个目录的`build.xml`文件:

*   `ant debug`构建用于测试和调试的应用。构建工具用调试密钥对生成的 APK 文件进行签名，然后用 Android 的`zipalign`工具优化 APK。
*   `ant release`构建应用程序并发布给用户。您必须用您的私钥签署生成的 APK 文件，然后用 SDK 的`zipalign`基本工具优化 APK。(这些任务超出了本课的范围。)

* * *

**注意:** Android 要求开发者用他们证书的私钥签署他们的 apk。它使用证书来识别应用程序的作者，并在应用程序之间建立信任关系；Android 不使用证书来确定可以安装哪些应用程序。自签名证书是可接受的–证书不必由证书颁发机构签名。因为使用特殊的调试证书来签署调试模式下构建的 APK，所以不能向公众发布 APK。维基百科的“公钥证书”条目(【http://en.wikipedia.org/wiki/Public_key_certificate】T2)向你介绍证书。

* * *

假设`C:prjdevW2A`是当前目录，通过调用`ant debug`在调试模式下构建 W2A。该命令创建一个 gen 子目录，存储生成的`R.java`面向资源的文件(可以忽略)，并在 bin 子目录中创建`W2A-debug.apk`。我们将在后面的课程中安装此 APK 并运行其应用程序。

## 回顾

以下复习问题有助于测试您对第 3 课材料的掌握程度:

*   什么是活动？
*   识别存储安卓`Activity`类的包。
*   Android 调用什么`Activity`方法来初始化一个活动？
*   指定创建 Android 项目时`android`工具需要的语法。这个语法的哪个选项是可选的？
*   确定在项目目录中创建的文件和目录。你如何构建 Android 项目的应用程序？

## 分享这篇文章