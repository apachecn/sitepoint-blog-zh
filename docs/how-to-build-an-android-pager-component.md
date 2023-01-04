# 构建一个 Android 页面组件

> 原文：<https://www.sitepoint.com/how-to-build-an-android-pager-component/>

现代手机最重要的用户界面范例之一是可旋转多点触控界面。最明显的例子是分页视图，它允许用户直观地浏览信息页面。这方面最著名的例子是 iOS 的主屏幕，它将应用程序排列在多个页面的网格中。这被证明是如此成功，以至于它已经在许多其他地方被使用，并被其他手机制造商直接复制。

当我开始为 Android 编程时，我认为会有一个现成的组件允许我这样做，或者至少有一个可以轻松配置来执行分页视图的组件。事实证明并非如此，所以我不得不钻研 Android 源代码来找出实现它的方法(顺便说一句，这是 iOS 的开箱即用功能)。我想我应该和你分享我的劳动成果。

### 调查

起初，我开始玩 [ViewFlipper](http://developer.android.com/reference/android/widget/ViewFlipper.html) ，用[手势](http://developer.android.com/resources/articles/gestures.html)提供输入来翻页。这是可行的，但是在滚动时没有给用户正确的反馈。当页面滚动时，用户希望页面随着滑动而移动。使用这种方法，页面在完成滑动手势之前不会显示动画。

接下来，我想从头开始编写自己的滚动组件。这将给我最大的灵活性，但会涉及很多努力。一般来说，在现有组件的基础上构建要比自己编写好得多，所以我对 API 进行了最后一次搜索，看看我可以使用什么。就在那时，我发现了[水平滚动视图](http://developer.android.com/reference/android/widget/HorizontalScrollView.html)。

这作为一个传统的滚动视图，除了它的工作水平，这正是我想要的。它还免费提供按键绑定，这是一个额外的好处。唯一缺少的部分是当用户松开手指时让它与页面边界对齐的方法，一个易于使用的添加和删除页面的 API，以及一个显示当前页面的页面指示器。我创建了一个子类，并开始定制它以产生我想要的效果。

### 简单的页面管理

默认情况下只接受一个子视图，由程序员来确保它的大小正确。我希望有多个子视图或页面，并且每个页面必须占据整个视口。为此，我添加了一个`addPage()`方法，该方法获取一个视图，将其大小调整为视口的大小，并将其添加到作为`ScrollView`的子视图的`LinearLayout`中。

```
public class Pager extends HorizontalScrollView {
    private LinearLayout contents;

    ...

    public void addPage(View child) {
        int width = getWidth();
        child.setLayoutParams(new LayoutParams(width, LayoutParams.FILL_PARENT));
        contents.addView(child);
        contents.requestLayout();

        firePageCountChanged();
    }

    ...

} 
```

### 对齐页面边界

为此，我们简单地监听`touch-up`事件，然后发出最后一个`smoothScrollTo()`调用来滚动回最近的页面边界

```
public boolean onTouchEvent(MotionEvent evt) {
    boolean result = super.onTouchEvent(evt);

    int width = getWidth();

    if (evt.getAction() == MotionEvent.ACTION_UP) {
        int pg = (getScrollX() + width / 2) / width;
        smoothScrollTo(pg * width, 0);
    }
    return result;
} 
```

需要额外的代码来以类似的方式处理基于键盘的事件，这一点我留给读者作为练习。

[GitHub](https://github.com/brucejcooper/Android-Examples/blob/master/PagingScrollerExample/src/com/eightbitcloud/pagingscroller/Pager.java)中的 Pager 包含了 [PagingScrollerExample](https://github.com/brucejcooper/Android-Examples/tree/master/PagingScrollerExample) 中的全部源代码。

### 页面指示器

默认情况下，当滚动窗格滚动时，水平滚动窗格将显示标准样式的滚动条，几秒钟后滚动条会消失。这在大多数情况下都没问题，这正是我在我的使用检查应用程序 [NodeDroid](https://www.sitepoint.com/featured-app-nodedroid/) 上使用的。但我认为在屏幕底部有一个 iOS 风格的页面指示器可能会很好。

这是通过创建一个单独的自定义视图组件来显示页面实现的，我称之为 *PageIndicator* 。该组件独立于页面调度器，因此您可以将视图放在您希望的位置。在您的活动启动时，您只需告诉寻呼指示器要收听哪个寻呼机，它就会从那时起自动更新自己。

为了让分页器正确地更新自己，它需要知道页面何时改变，以及页面何时被添加到分页器或从分页器中删除。令人费解的是，Android 中没有 ScrollView 的默认监听器，所以您接收通知的唯一方式是使用`onScrollChanged`方法将其捕获到一个子类中。在我的代码中，我创建了第二组名为`OnPageChangeListener`的监听器/事件类，其中两个事件名为`onPageChange()`和`onPageCountChange()`。

GitHub 中的 [PageIndicator 包含了](https://github.com/brucejcooper/Android-Examples/blob/master/PagingScrollerExample/src/com/eightbitcloud/pagingscroller/PageIndicator.java) [PagingScrollerExample](https://github.com/brucejcooper/Android-Examples/tree/master/PagingScrollerExample) 中的完整源代码。

### 你自己试试

我制作了一个示例应用程序，演示了视图的使用，代码可以在 GitHub 上找到。查看[pagingscroller 示例](https://github.com/brucejcooper/Android-Examples/tree/master/PagingScrollerExample)。如果您想尝试一下，克隆 repo，在 Eclipse 中加载项目(安装了 Eclipse 的 [ADT 插件)，然后您就可以离开了。](http://developer.Android.com/sdk/eclipse-adt.html)

*   [pagingscroller 示例](https://github.com/brucejcooper/Android-Examples/tree/master/PagingScrollerExample)。
*   [GitHub 上的寻呼机](https://github.com/brucejcooper/Android-Examples/blob/master/PagingScrollerExample/src/com/eightbitcloud/pagingscroller/Pager.java)
*   [GitHub 页面指示器](https://github.com/brucejcooper/Android-Examples/blob/master/PagingScrollerExample/src/com/eightbitcloud/pagingscroller/PageIndicator.java)
*   【Eclipse 的 ADT 插件

## 分享这篇文章