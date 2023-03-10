# Android 库项目入门，第 2 部分

> 原文：<https://www.sitepoint.com/getting-started-with-android-library-projects-part-2/>

Google 提供 Android 库项目作为管理可重用代码的一种方式。在这个关于 Android 库项目的三部分系列的第一部分中，我向您介绍了库项目基础知识。

在本文中，我将通过展示一个可重用的 about 对话框库项目作为一个有用的例子，从理论转向实践。

我使用 Android SDK Release 20 以及 Android 2.3.3 (API 级别 10)和 Android 4.1 (API 级别 16)平台来开发和测试本文的[代码](http://tutortutor.ca/articles/gswalp/code2.zip)。

## 关于对话框 Android 库项目

about(也称为 info)对话框是可以存储在 Android 库项目中的可重用代码的一个很好的例子。“关于”对话框通常会标识应用程序(名称、版本号、图标)及其作者，提供获取支持和访问应用程序作者网站的电子邮件地址和其他链接，提供许可证/免责声明信息，等等。

在我开始开发这个库之前，我思考了各种各样的架构问题。这个库的最低 API 级别和包名是什么？什么类和其他引用类型是库的一部分？我是实例化这些类还是将它们视为单例？每种类型的公共接口是什么？我存储资源和代码吗？如果是，将存储何种资源？我总结了以下答案:

*   最低 API 级别为 10，以便 Android 2.3.3 和更高版本可以使用该库。包名将是`ca.tutortutor.about`。
*   这个库将由一个名为`About`的类组成。
*   `About`将作为单例(也称为实用程序类)。我不会实例化这个类，但会调用静态方法。毕竟，应用程序通常会显示一个关于对话框。
*   `About`将公开一个单独的`public static void show(Activity activity, String aboutText, String okButtonText)`方法。这个方法将使用 Android 的包管理器来获取应用程序的版本号、图标和名称。
*   `About`与布局资源相关联；没有其他资源。为了简化本地化，我决定不包含字符串资源。我不想为了支持更多的地区而不断更新库项目。相反，我认为本地化应该发生在应用程序级别；该应用程序应该将本地化的文本传递到“关于”对话框。

### 探索源代码和资源

“关于”对话框库项目由单个源文件(`About.java`)和单个资源文件(`ca_tutortutor_about.xml`)组成。清单 1 展示了`About.java`。

```
package ca.tutortutor.about;

import android.app.Activity;
import android.app.AlertDialog;

import android.content.pm.ApplicationInfo;
import android.content.pm.PackageManager;
import android.content.pm.PackageManager.NameNotFoundException;

import android.graphics.drawable.Drawable;

import android.text.Html;

import android.text.method.LinkMovementMethod;

import android.view.InflateException;
import android.view.LayoutInflater;
import android.view.View;

import android.widget.TextView;

public class About
{
   public static void show(Activity activity, String aboutText, 
                           String okButtonText)
   {
      String versionNumber = "unknown";
      Drawable icon = null;
      String appName = "unknown";
      try 
      {
         PackageManager pm = activity.getPackageManager();
         versionNumber = pm.getPackageInfo(activity.getPackageName(), 0)
                           .versionName;
         icon = pm.getApplicationIcon(activity.getPackageName());
         ApplicationInfo ai = pm.getApplicationInfo(activity.getPackageName(), 
                                                    0);
         appName = (String) pm.getApplicationLabel(ai);
         if (appName == null)
            appName = "unknown";
      } 
      catch (NameNotFoundException e)
      {
      }
      View about;
      TextView tvAbout;
      try 
      {
         LayoutInflater inflater = activity.getLayoutInflater();
         about = inflater.inflate(R.layout.ca_tutortutor_about, null);
         tvAbout = (TextView) about.findViewById(R.id.ca_tutortutor_aboutText);
      } 
      catch(InflateException e) 
      {
         about = tvAbout = new TextView(activity);
      }
      tvAbout.setText(Html.fromHtml(aboutText));
      tvAbout.setMovementMethod(LinkMovementMethod.getInstance());
      new AlertDialog.Builder(activity)
                     .setTitle(appName+" "+versionNumber)
                     .setIcon(icon)
                     .setPositiveButton(okButtonText, null)
                     .setView(about)
                     .show();
   }
}
```

**清单 1:** `About.java`

清单 1 展示了一个带有单独的`public static void show(Activity activity, String aboutText, String okButtonText)`类方法的`About`类:

*   `activity`包含对调用活动的引用，通常是主活动。
*   `aboutText`包含“关于”对话框的文本，其中可以包含 HTML 标记，如`<b>`(粗体)、`<i>`(斜体)、`<u>`(下划线)和`<p>`(段落)。在这里，您可以确定您的网站以获得支持、提供许可/免责声明信息等。
*   `okButtonText`包含出现在关闭对话框按钮上的文本。

这个方法首先获取包管理器来访问应用程序的版本号、图标和名称。包管理器由`PackageManager`类描述，该类通过调用`Activity`的继承`PackageManager getPackageManager()`方法来访问。

`PackageManager`声明了一个`PackageInfo getPackageInfo(String packageName, int flags)`方法，用于从应用程序的包中获取信息。这个包是通过调用`Activity`继承的`String getPackageName()`方法获得的。

返回的`PackageInfo`对象通过它的字段提供对包信息的访问。例如，`versionName`包含这个包的基于字符串的版本名(比如`1.0`)，由`androidmanifest.xml`的`<manifest>`标签的`versionName`属性指定。

`PackageManager`的`Drawable getApplicationIcon(String packageName)`方法让你获得应用程序的图标，作为一个`Drawable`实例。调用此方法时，将活动的包名作为参数。

`getApplicationIcon()`寻找由`<application>`标签的`icon`属性指定的图标，当没有找到该属性时返回默认图标。

现在获得了应用程序的名称。首先，`PackageManager`的`ApplicationInfo getApplicationInfo (String packageName, int flags)`方法被调用来返回一个对应于清单的`<application>`标签的`ApplicationInfo`对象。然后这个对象作为参数传递给`PackageManager`的`CharSequence getApplicationLabel(ApplicationInfo info)`方法，该方法返回`<application>`标签的`label`属性的值，或者当该属性丢失时返回 null。

`getPackageInfo()`和`getApplicationInfo()`方法能够投掷`PackageManager.NameNotFoundException`。当在系统上找不到具有给定名称的包时，前一种方法会引发此异常。当在系统上找不到具有给定包名的应用程序时，后一种方法会引发此异常。由于这种异常的可能性，在调用这些方法之前，`versionName`、`icon`和`appName`被初始化为默认值。

清单 1 的下一个主要任务是放大`ca_tutortutor_about.xml`(稍后描述)的内容，并访问其放大的 textview 小部件。它通过调用`Activity`的`LayoutInflater getLayoutInflater()`方法获得一个布局充气器，并使用这个方法将 XML 文件充气成一个`about`视图。然后它使用`about`来访问这个视图的 textview 小部件。注意`R.layout.ca_tutortutor_about`和`R.id.ca_tutortutor_aboutText`中的`ca_tutortutor_`前缀，以避免资源冲突。

现在，textview 小部件已经配置好了。首先，通过调用`Html`的`Spanned fromHtml(String source)`类方法，将`aboutText`的内容从 HTML 转换为文本，并将结果赋给 textview 进行显示。第二，为这个小部件分配了一个链接移动方法，这样点击一个链接就会将用户带到链接的目的地。(在模拟器中，点击电子邮件链接会导致一个*不支持的动作*对话框。)

最后的任务是实例化`AlertDialog`并配置和显示这个实例。为了方便，我使用`AlertDialog`的嵌套`Builder`类来完成这些任务。

清单 2 展示了`ca_tutortutor_about.xml`。

```
<?xml version="1.0" encoding="utf-8"?>
<scrollview xmlns:android="http://schemas.android.com/apk/res/android"
                  android:id="@+id/ca_tutortutor_aboutView"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent">
  <linearlayout android:orientation="horizontal"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:padding="5dp">
    <textview android:id="@+id/ca_tutortutor_aboutText"
              android:layout_width="wrap_content"
              android:layout_height="fill_parent"></textview>
  </linearlayout>
</scrollview>
```

**清单 2:** `ca_tutortutor_about.xml`

清单 2 描述了一个由嵌套了 linearlayout 的 scrollview 组成的布局，linear layout 嵌套了 textview。scrollview 的出现使您可以为 about 对话框指定所需的内容，并允许用户通过滚动来访问这些内容。

scrollview 和 textview 都被分配了一个 ID，这样就可以从`About.java`的`show()`方法中访问它，这在本文前面已经介绍过了。为了避免资源冲突，前缀`ca_tutortutor_`是必要的。

### 创建和初始化关于

让我们创建并初始化这个库项目。第一步是创建项目。我使用以下命令行在我的 Windows 平台上完成了这项任务:

```
android create lib-project -t 1 -p C:\prj\ap\About -k ca.tutortutor.about
```

目标 1 确定我的平台上的 Android 2.3.3。(执行`android list targets`以获得您平台上的等效目标数。)我将这个项目存储在我的`C:\prj\ap\About`目录中。最后，该项目的包名是`ca.tutortutor.about`。

接下来，我在`About\src`下创建了一个`ca\tutortutor\about`子目录，并将包含清单 1 内容的`About.java`文件复制到`about`中。我还将一个包含清单 2 内容的`ca_tutortutor_about.xml`文件复制到了`About\res\layout`中。

`About\res\layout`还包含一个`main.xml`文件，该文件在库项目创建期间被放在这个目录中。你可以删除这个文件或者保留它，因为你的应用程序项目很可能提供他们自己的`main.xml`文件，覆盖这个`main.xml`。

类似地，您会发现一个包含`strings.xml`的`About\res\values`目录。因为删除这个文件会阻止你构建(测试任何源代码修改的错误)，你最好不要管这个文件——它会被一个应用程序项目的`strings.xml`文件覆盖。

此时，您可能希望确保库项目正确构建。通过执行以下命令完成此任务:

```
ant debug
```

您应该观察到一条*构建成功的*消息。

## 结论

现在，您已经探索了构成“关于”对话框库项目的源代码和资源，并创建和初始化了该项目，您将希望将其集成到您自己的应用程序项目中。在本系列的最后一部分，我将展示一个通过选项菜单支持关于对话框的应用程序项目。

## 分享这篇文章