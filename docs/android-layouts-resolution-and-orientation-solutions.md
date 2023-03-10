# Android 布局:分辨率和方向解决方案

> 原文：<https://www.sitepoint.com/android-layouts-resolution-and-orientation-solutions/>

Android 应用面临着许多其他移动平台可能还没有处理的挑战。Android 可以在各种各样的硬件上使用，每种硬件都有不同的屏幕尺寸和像素密度(DPI)，有时有物理键盘，有时没有触摸屏(例如谷歌电视)。在不同版本的 Android 中加入不同的用户界面提示，对于应用程序设计者来说，这可能是需要考虑的许多变量。在这篇文章中，我们将看看 Android 提供的不同机制来简化这项任务。

### 基本布局

Android 用户界面可以通过编程的方式组合在一起，但更常见的是，它们是在 XML 文件中定义的，然后被使用它们的活动所膨胀。布局文件示例如下所示:

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" android:layout_width="fill_parent" android:layout_height="fill_parent" android:orientation="vertical">
    <ListView android:id="@android:id/list" android:layout_width="fill_parent"  android:layout_height="fill_parent" android:layout_weight="1.0"/>
    <LinearLayout android:gravity="center" android:paddingTop="5px" android:layout_width="fill_parent" android:layout_height="wrap_content" android:background="#777">
        <Button android:text="@string/addaccountbutton" android:id="@+id/addaccountbutton" android:layout_width="wrap_content" android:layout_height="wrap_content"/>
    </LinearLayout>
</LinearLayout> 
```

生成的小部件如下所示(列出程序提供的内容):

[![Android Layouts Device Independence Figure 1](img/7c143fdbb00453237e2b851ac2621220.png)](https://www.sitepoint.com/wp-content/uploads/2011/09/example_layout.png)

布局示例

布局管理器决定如何分配空间，而不是指定像素位置。布局提示使用 XML 属性传递给布局管理器。如果你看上面的例子，你会看到使用的顶部布局是一个`LinearLayout`，有两个子视图，一个`ListView`和另一个`LinearLayout`，它又有一个`Button`子视图。每个组件都有布局属性`layout_width`和`layout_height`，它们指定了应该如何计算它们的首选大小。在这种情况下，我们使用`fill_parent`来用尽尽可能多的空间，或者使用`wrap_content`来容纳它的内容。`ListView`组件还有一个`layout_weight`属性，指示布局应该将任何剩余空间分配给该组件。

有许多默认布局可用，或者您可以定义自己的布局。默认值为:

*   linear layout–子元素水平或垂直堆叠，每个子元素都有一个布局权重来分配额外的空间。
*   框架布局——所有的孩子堆叠在一起，用尽所有的空间
*   relative layout–允许组件向左、向右等方向布局..或者框架本身。
*   表格布局–在表格中布置组件

布局可以嵌套，如果需要，允许复杂的布局。一个常见的例子是外部带有边框的标准中心组件。这只是一个垂直的`LinearLayout`和一个水平的`LinearLayout`作为它的中间子节点。

[![Android Layouts Device Independence Figure 2](img/ec71420c50c5d9ae31386598a7f55f0d.png)](https://www.sitepoint.com/wp-content/uploads/2011/09/borders.png)

带边框的中心组件

### 处理不同的屏幕尺寸和方向

到目前为止，定义我们的布局非常简单。然而，有时候仅仅指示布局如何调整大小是不够的。为纵向屏幕设计的布局在横向屏幕上可能看起来不正确，需要不同的布局。Android 使用资源选择器解决了这个问题。

Android 应用程序的资源位于项目的`res`目录中。示例应用程序的目录结构可能如下所示。请注意以`-en`和`-it`结尾的最后两行，我们稍后会谈到它们。

/RES
/RES/layout/
/RES/layout-port/main screen . XML
/RES/layout-land/main screen . XML
/RES/drawable-hdpi/icon . png
/RES/drawable-ldpi/icon . png
/RES/values-en/strings . XML
/RES/values-it/strings . XML

当您的应用程序请求资源时，它会尝试根据手机的属性来查找文件。例如，如果我们的手机在请求`mainscreen`布局时是横向的，它将首先在`layout-land`目录中查找，如果没有找到，将退回到主`layout`目录。

因此，为了让纵向和横向有不同的布局，我们简单地分别在`layout-port`和`layout-land`目录中创建两个同名的文件。这同样适用于系统中的几乎所有资源。其他常用的选择器是屏幕 DPI 密度或屏幕尺寸。

[![Android Layouts Device Independence Figure 3](img/b4e18152857390a576ef5d5599c5aaf3.png)](https://www.sitepoint.com/wp-content/uploads/2011/09/screens-ranges.png)

屏幕密度

注意:Android 3.2(蜂巢)引入了新的资源选择方案。有关更多信息，请参见 Android 开发人员文档的支持多屏幕文章中名为[声明 Android 3.2 平板电脑布局](http://developer.android.com/guide/practices/screens_support.html#DeclaringTabletLayouts)的部分。

另一个有用的选择器是设备的语言。字符串可以在 Android 中抽象成 XML 文件，然后可以国际化。在本文开头的布局示例中，按钮的文本由字符串引用`@string/addaccountbutton`定义。在运行时，将在 values/strings.xml 中查找这个值，但是这个资源像其他任何东西一样受选择器的支配。这样，我们可以使用`values-it/strings.xml`为应用程序提供意大利语翻译。

### 主题和样式

设计难题的最后一部分归结为主题化。我们不想在整个应用程序中重复颜色和数值。就像 HTML 有 CSS 一样，Android 提供了主题化功能。通常，主题是在一个名为`res/values/styles.xml`的 XML 文件中定义的。下面显示了一个示例样式文件:

```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="TitleTheme" parent="android:Theme">
        <item name="android:windowTitleStyle">@style/WindowTitle</item>
        <item name="android:windowTitleSize">49dip</item>
        <item name="android:windowTitleBackgroundStyle">@style/CustomWindowTitleBackground</item>
    </style>

    <style name="CustomWindowTitleBackground">
        <item name="android:background">@drawable/bluegradient</item>
    </style>

    <style name="largeText">
        <item name="android:textSize">27sp</item>
    </style>

    <style name="ttButton">
        <item name="android:layout_width">wrap_content</item>
        <item name="android:layout_height">wrap_content</item>
        <item name="android:textColor">#ffffff</item>
        <item name="android:paddingTop">10dp</item>
        <item name="android:paddingLeft">10dp</item>
        <item name="android:paddingBottom">10dp</item>
        <item name="android:paddingRight">10dp</item>
        <item name="android:layout_marginTop">10dp</item>
        <item name="android:layout_marginLeft">10dp</item>
        <item name="android:layout_marginBottom">10dp</item>
        <item name="android:layout_marginRight">10dp</item>
        <item name="android:textSize">25dp</item>
        <item name="android:width">120dp</item>
        <item name="android:shadowColor">#000</item>
        <item name="android:shadowDx">2.0</item>
        <item name="android:shadowDy">2.0</item>
        <item name="android:shadowRadius">2.0</item>
        <item name="android:textStyle">bold</item>
    </style>

    <style name="greenButton" parent="ttButton">
        <item name="android:background">@drawable/greenbutton</item>
    </style>
    <style name="redButton" parent="ttButton">
        <item name="android:background">@drawable/redbutton</item>
    </style>
    <style name="blueButton" parent="ttButton">
        <item name="android:background">@drawable/bluebutton</item>
    </style>
</resources> 
```

样式有一个名称，可以从其他样式继承，并被指定为一组键/值对。要使用一个样式，只需向您希望应用该样式的视图添加一个`style=@styles/stylename`属性。例如，红色大按钮可以定义为:

```
<Button android:text="@string/addaccountbutton" style="@style/redButton" android:id="@+id/addaccountbutton"/> 
```

所有其他值都由样式指定。它不像 CSS 那样具有表现力，但是它提供了所需内容的基础。

### 总结

所以你有它。Android 没有一个很好的图形用户界面构建器，但确实提供了强大的机制来提供跨各种设备工作的界面。敏锐的读者会注意到，布局组件的方式与使用 CSS 布局 HTML 文档的方式非常相似。任何熟悉网页设计的人都应该能够很容易地整合 Android 视图。就个人而言，我更喜欢能够灵活地查看代码/XML 中的完整定义。

## 分享这篇文章