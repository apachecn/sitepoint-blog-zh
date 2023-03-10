# 了解 Android 中活动的生命周期

> 原文：<https://www.sitepoint.com/understanding-the-life-cycle-events-of-activity-in-android/>

Android 中的“活动”是用户将与之交互的单个屏幕。如果您想要创建一个表单来接受用户的输入，或者如果您需要向用户显示信息或反馈，您通常会为此目的构建一个活动。

在某些情况下，一个活动将在用户的手机(或任何其他 Android 设备)上运行，他们可能希望切换到其他任务。在这种情况下，Android 运行时会将你的初始活动放在后台。当有很多活动并且 android 运行时资源不足时，它可能会根据活动的优先级来破坏您的活动(用户当前正在交互的活动总是具有最高的优先级)。

因此，根据具体情况，一旦你的活动启动，Android 运行时可能会以多种不同的方式处理你的活动。当你的活动状态改变时，Android 运行时调用特定的事件(函数),允许你的活动在其状态改变之前执行某些任务。在本文中，我们将研究活动可能处于的不同状态，在活动的生命周期中执行的不同事件，以及一个设计良好的活动在这些事件中能够并且应该执行什么。

### 活动的状态

在其生命周期内，Android 活动将处于以下状态之一:

*   **活动**–当活动在 Android 设备的前台与用户直接交互时，活动处于活动状态。
*   **非活动—**活动在启动前或被 android 运行时“杀死”后处于非活动状态。
*   **暂停**–当活动可见但不在焦点上时，活动处于暂停状态。
*   **停止**–当活动不再可见时，它处于停止状态。它仍然在内存中，但可能会被 android 运行时破坏，因为“停止”的活动通常被认为是低优先级的。

### 活动生命周期

当活动改变状态时，Android 运行时在活动上调用不同的事件处理程序。这些事件处理程序的默认实现已经存在于 Activity 类中。根据活动的需要，您可以重写以下任何事件处理程序。

为了理解事件处理程序，让我们创建一个名为 ActivityLifeCycleDemo 的简单活动。默认活动如下所示:

[sourcecode language="java"]

公共类 ActivityLifeCycleDemo 扩展 Activity {

/**首次创建活动时调用。*/

@覆盖

public void on create(Bundle saved instancestate){

超级。oncreate(savedinstancestat)：

setContentView(r . layout . main)；

}

}
[/sourcecode]

现在，我将添加两个我们将在活动中使用的支持函数。在该类中，首先定义一个 final "int ",然后使用函数:

[sourcecode language="java"]

静态最终 int NOTIFICATION _ ID = 1776

受保护的 void 显示通知(字符串标题，字符串消息)

{

NOTIFICATION manager notifManager =(NOTIFICATION manager)getsystem SERVICE(NOTIFICATION _ SERVICE)；

Notification note = new Notification(r . drawable . icon，title，system . current time millis())；

pending Intent Intent = pending Intent . get activity(this，0，new Intent(this，ActivityLifeCycleDemo.class)，0)；

note.setLatestEventInfo(this，title，message，intent)；

notifmanager . notify(NOTIFICATION _ ID，注意)；

}

受保护的无孔睡眠有孔虫()

{

尝试

{

线程.睡眠(60000)；

} catch(中断异常 e)

{

// TODO 自动生成的 catch 块

e . printstacktrace()；

}

}

[/sourcecode]

在上面的例子中，函数“DisplayNotification”将使用 Android 的标准通知类简单地显示一个通知。“sleepForaMinute”功能只是休眠一分钟，以便我们可以看到事件，以防它们发生得非常快。

#### onCreate

让我们首先覆盖 onCreate 处理程序，如下所示:

[sourcecode language="java"]

public void on create(Bundle saved instancestate){

超级。oncreate(savedinstancestat)：

setContentView(r . layout . main)；

display notification(" ActivityLifeCycleDemo "，" onCreate ")；

sleep foraminute()；

}

[/sourcecode]

现在，当您开始活动时，您应该会在您的 Android 设备上看到如下所示的通知。

![](img/240ab8dceafa3b1d7d04801861ffec33.png "android1")

在我们的 onCreate 处理程序中，我们设置主内容视图并显示通知，以表示该事件处理程序被调用。每当创建新活动时，onCreate 处理程序都会被调用。在 onCreate 处理程序中，应该包含所有典型的初始化任务，比如创建 UI、获取一些资源、创建服务等。

onCreate 方法甚至传递 savedInstanceState 它可以读取值，并协助初始化用户界面和其他基本任务。几乎所有的活动都需要您覆盖这个方法。

#### onDestroy

现在让我们覆盖 onDestroy 事件，如下所示:

[sourcecode language="java"]

@覆盖

**受保护的** **作废的** onDestroy() {

super . on destroy()；

display notification(" ActivityLifeCycleDemo "，" on destroy ")；

}

[/sourcecode]

每当活动被销毁时，就调用 onDestroy 事件。如果您开始一个活动，然后按 back 按钮，该活动将被销毁，您应该会看到下面的通知。

![](img/c786aea9123a84ac36a92716b3d1bf20.png "android2")

onDestroy 方法应该清理所有由 onCreate 方法获取的资源，这些资源以前被现已销毁的活动使用过。

#### onStart

现在让我们重写 onStart 方法，如下所示:

[sourcecode language="java"]

@覆盖

受保护的 void onStart() {

super . onstart()；

display notification(" activitylifycledemo "，" onStart ")；

sleep foraminute()；

}

[/sourcecode]

只要活动变得可见，就会调用 onStart 事件。当您开始一个新的活动时，onStart 事件将在 onCreate 事件之后被调用。如果活动变得不可见(当用户启动另一个活动时)，然后又变得可见，将调用 onStart 事件(在这种情况下不会调用 onCreate 它是为全新的活动保留的)。每当活动从不可见状态变为可见状态时，将会看到以下通知。

![](img/55c04499d47346a2fb2bcbb2224cb37f.png "android3")

在 onStart 方法中，您应该启动活动所需的所有可见动作，比如显示动画、显示用户提示等。

#### onStop

现在，让我们重写 onStop 方法，如下所示:

[sourcecode language="java"]

@覆盖

受保护的 void onStop() {

super . onstop()；

display notification(" ActivityLifeCycleDemo "，" on stop ")；

}

[/sourcecode]

每当活动变得不可见时，就会执行 onStop 事件。因此，如果您启动了活动，并单击 home 按钮(使您当前的活动不可见)，您应该会看到下面的通知。

![](img/b346bde1d7237a551852bf24b66860fb.png "android4")

onStop 方法应该停止 onStart 方法中启动的所有操作。因为活动是不可见的，所以您的活动不应该执行(并消耗 CPU 周期)Android 界面所需的任何任务。

#### onResume

现在让我们重写 onResume 方法，如下所示:

[sourcecode language="java"]

受保护的 void onResume() {

super . on resume()；

display notification(" ActivityLifeCycleDemo "，" on resume ")；

}

[/sourcecode]

onResume 事件发生在活动可见之前，并准备好再次接受用户输入。当活动开始或重新成为焦点时，您将看到下面的通知。

![](img/b65d6e3fbe555bc7d35ccaefafe1b652.png "android5")

您应该快速地将代码保存在 onResume 中，因为它会被频繁地调用。如果你低效地构建 Resume，你可能最终会得到一个缓慢的、反应迟钝的 Android 应用。

#### 暂停

现在让我们重写 onPause 方法，如下所示:

[sourcecode language="java"]

@覆盖

受保护的 void onPause() {

super . on pause()；

display notification(" ActivityLifeCycleDemo "，" on pause ")；

}

[/sourcecode]

每当活动失去焦点时，就会调用 onPause。onPause 中的代码(比如 onResume)应该非常快，因为它可能会被频繁调用。

#### onSaveInstanceState

onSaveInstanceState 是一个事件，可用于保存包中活动的状态。该包将被传递给 onRestoreInstanceState 或 onCreate 事件，这两个事件都可以从包中读取并恢复活动的状态。

### 结论

一个 Android 活动在其生命周期中会经历几个不同的状态。了解状态和事件将有助于您以更高效、响应更快的方式为用户编写应用程序。Android 操作系统通过调用活动上的事件，让活动非常有效地管理自己，只要你仔细开发不同的事件。所以，在管理你的下一个 Android 应用程序的生命周期的同时享受乐趣吧！

## 分享这篇文章