# 冰淇淋三明治的开发者们将会得到什么？

> 原文：<https://www.sitepoint.com/whats-coming-for-developers-in-ice-cream-sandwich/>

现在人们普遍认为，谷歌将于下周 10 月 11 日发布其安卓操作系统的下一个版本——冰激凌三明治。它将与三星的一款新旗舰手机一起发布，但与此同时,[承诺将新的 tasty treat 操作系统安装到许多最近发布的手机上。如果他们支持这一点，这意味着很快就会有很多手机运行最新版本的谷歌操作系统。](http://www.techradar.com/news/phone-and-communications/mobile-phones/android-forges-alliance-for-consistent-os-updates-954283)

这对应用开发者意味着什么？有很多文章推测用户界面会有什么变化。特别是，几周前有一个*假定*的演练视频:

<iframe height="360" width="640">&lt; br / &gt;</iframe>

除了那段视频调侃，我今天不会重蹈那些博客作者的覆辙。而是想谈谈这会给 Android 开发者带来哪些编程上的改变。谷歌还没有发布太多关于新 API 的信息(守口如瓶的家伙！)，但他们已经向 [Android 开发者博客](http://android-developers.blogspot.com/2011/09/preparing-for-handsets.html)发出了一些信息。很大程度上，它涉及到使用蜂窝 API，但也进行了一些调整，使其适用于手机。我知道我在很大程度上忽略了 Honeycomb 的发布，因为它只面向平板电脑开发者。从表面上看，可能是时候熟悉这些 API 了，因为这是我们将在 IC 中大量使用的。让我们来看看这些变化会是什么:

### 一款适用于平板电脑和手机的应用

人们谈论最多的 ICS 功能是，它将把所有谷歌设备聚集在一起。手机、平板电脑，可能还有电视。这意味着，除了应用程序开发人员必须处理的各种屏幕尺寸之外，他们现在还有非常不同的屏幕和使用模式。到目前为止，为 Honeycomb 编写的应用程序通常只能在平板电脑上运行，普通的手机应用程序通常无法在全屏模式下正常运行。为了解决这个问题，Google 引入了一个基于片段的视图布局系统。

片段是用户界面的一部分，可以与其他片段一起组成整个屏幕。在手机上，屏幕可能只够显示一个片段。在平板电脑上，几个片段可以组合在一起，可以一起工作。

[![Ice Cream Sandwich Figure 1 Fragments](img/079579bc3aec35312ac85bffcc99367c.png "Ice Cream Sandwich Figure 1")](https://www.sitepoint.com/wp-content/uploads/2011/10/ics_fragments.png)

碎片

手机布局将如下所示:

```
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <fragment class="com.example.android.TitlesFragment"
              android:id="@+id/list_frag"
              android:layout_width="match_parent"
              android:layout_height="match_parent"/>
</FrameLayout> 
```

而平板电脑可能看起来像:

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:orientation="horizontal"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:id="@+id/frags">
  <fragment class="com.example.android.TitlesFragment"
            android:id="@+id/list_frag"
            android:layout_width="@dimen/titles_size"
            android:layout_height="match_parent"/>
  <fragment class="com.example.android.DetailsFragment"
            android:id="@+id/details_frag"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
</LinearLayout> 
```

开发人员仍然需要编写代码来处理不同的布局以及它们之间的通信，但是这是由`FragmentManager`类来完成的。开发人员可以测试其他片段的存在，并使用该信息来决定他是否只需要更新该片段，或者开始一个新的活动来显示信息。

### 屏幕大小

在关于 [Android 布局:分辨率和方向解决方案](https://www.sitepoint.com/mobile/android/)的文章中，我们了解了 Android 如何处理不同的屏幕尺寸。据传，新的 Nexus 设备的分辨率将为 1280×720 像素，与你在一些旧设备上看到的 480×320 像素有很大不同，因此这变得更加重要。

Honeycomb 增加的一个功能是指定布局的最小屏幕尺寸，如[Android Docs:Supporting multi Screens](http://developer.android.com/guide/practices/screens_support.html#DeclaringTabletLayouts)所示。为了使用它，我们的布局资源可以有`smmallestWidth`、`Available Screen Width`或`Available Screen Hight`选择器。例如，如果您的布局始终需要 600dp 的最小布局大小，您可以将它放在`res/layout-sw600dp`目录中。

您的应用程序清单还可以指定它将适用于哪些屏幕尺寸。正如在[Android Docs:Android manifest . XML 文件](http://developer.android.com/guide/topics/manifest/supports-screens-element.html)中看到的，通过在应用程序清单中使用`<supports-screens>`，您可以指定以下限定符:

*   可调整大小的
*   小屏幕
*   正常屏幕
*   大屏幕
*   xlargeScreens
*   任意密度
*   requiresSmallestWidthDp(整数)
*   compatible lewidth limit(integer)
*   largestwidthlimitdp(整数)

例如，您可以通过将`smallScreens`、`normalScreens`和`largeScreens`设置为`false`来裁剪手机。那么你的应用程序将不会在市场上向不具备合格屏幕尺寸的手机显示。不过就我个人而言，我建议让你的应用足够灵活，可以在所有屏幕尺寸上工作，以最大化你的曝光率。

### 软主页、后退和菜单按钮

在 Honeycomb 版本中，谷歌摆脱了自 Android 开始以来一直存在的熟悉的硬件按钮。相反，这些按钮已经合并到屏幕布局中，出现在所有活动中。在集成电路中，这种方法也将用于手机。

在我看来，这对开发者来说没什么区别。仍然会有 home、back 和 menu 按钮。他们只会出现在屏幕上。它根本不会改变应用程序的工作方式。它不会改变您将编写的支持按钮的代码。

### 动作菜单

Honeycomb 引入的另一个特性是动作栏。在 Android 的早期版本中，应用程序的标题栏除了显示视图的标题之外什么也不做，除非程序员选择用他们自己的自定义标题来替换它。

Honeycomb 引入了 ActionBar 来提供一种在标题栏中显示标签、按钮和菜单的标准化方法，从而节省空间并为用户提供一致的用户界面。在冰激凌三明治中，这也延伸到了手机设备。

[![Ice Cream Sandwich Figure 2 ActionBar](img/665f442770b88bf02f9168c70b56baf2.png "Ice Cream Sandwich Figure 2")](https://www.sitepoint.com/wp-content/uploads/2011/10/ics_actionbar.png)

ActionBar

### 做好准备并支持旧 Android

冰淇淋三明治 SDK 尚未发布，但很明显它将从蜂巢中获得许多线索。那些想要抢先一步的开发人员应该首先确保他们的应用程序使用蜂巢中使用的片段和动作栏系统。[兼容性包](http://developer.android.com/sdk/compatibility-library.html)可用于确保您为 Honeycomb/ICS 编写的应用仍能在旧设备上运行。特别是，这将允许您在旧设备上使用片段。

会有更多的 API 可以玩吗？可能吧，但是我们要等到下周二才能知道！

## 分享这篇文章