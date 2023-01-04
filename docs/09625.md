# AlarmManager 和 Sleepy Android 应用

> 原文：<https://www.sitepoint.com/alarmmanager-and-sleepy-android-apps/>

在我的第一篇解释如何编写 Android 小部件的文章中，我们介绍了创建一个基本小部件所需的步骤，这个小部件不经常更新，并且使用标准组件。然而，我们无法让它频繁地更新小部件。

在本文中，我将向您展示如何使用“AlarmManager”向您的小部件添加定期触发更新。我假设你已经阅读了介绍 [Android Widget 编码](https://www.sitepoint.com/how-to-code-an-android-widget/)的文章，或者你知道[应用 Widget](http://developer.android.com/guide/topics/appwidgets/index.html) 编程的基础知识。

### 频繁更新小部件

Android widgets 会定时更新内容，即使屏幕关闭，也会唤醒手机来更新内容。如果更新频率设置得太高，可能会导致电池过度使用。因此， [Android 系统将微件更新](http://developer.android.com/guide/topics/appwidgets/index.html#MetaData)的频率限制为每半小时一次或更长。

如果你想要一个经常更新的小部件，比如一个时钟，那就不好了。这里我们需要的是一种在屏幕打开时频繁更新小部件的方法，并且只在屏幕打开时更新。幸运的是，有一种方法可以做到这一点，使用一个“ [AlarmManager](http://developer.android.com/reference/android/app/AlarmManager.html) ”，这是一个系统服务，其中您的代码注册在特定时间或特定时段触发的事件。这允许某些事情在特定的时间发生，即使应用程序当时没有运行。更好的是，当你指定正确的定时器类型时，你可以告诉它如果手机在关机时处于睡眠状态，就不要发送事件。这使得它非常适合我们的事业。

就像小工具更新一样，警报是作为[意图](http://developer.android.com/reference/android/content/Intent.html)发送的，就像 Android 中的一切一样。因此，我们要做的第一件事是配置我们的应用程序来监听我们将专门为时钟更新创建的自定义意图类型。

打开`AndroidManifest.xml`,在 APPWIDGET_UPDATE 的现有过滤器下添加一个新的`intent-filter`。因为我们使用的是自定义意图，所以我们可以自己命名它，但它应该是完全限定的(即前面有一个反向域名)，这样我们就可以区分我们的意图和系统中其他人的意图。在这种情况下，我选择了`com.eightbitcloud.example.widget.8BITCLOCK_WIDGET_UPDATE`作为名字。

```
<intent-filter>
    <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
    <intent-filter>
    <action android:name="com.eightbitcloud.example.widget.8BITCLOCK_WIDGET_UPDATE" />
</intent-filter>
```

接下来，我们需要在“ProviderHandler”中编写一些代码，以配置“AlarmManager”定期发送我们的意图。加载我们在第一个教程中创建的“WidgetProvider ”,并添加以下方法:

```
private PendingIntent createClockTickIntent(Context context) {
    Intent intent = new Intent(CLOCK_WIDGET_UPDATE);
    PendingIntent pendingIntent = PendingIntent.getBroadcast(context, 0, intent, PendingIntent.FLAG_UPDATE_CURRENT);
    return pendingIntent;
}

@Override
public void onEnabled(Context context) {
        super.onEnabled(context);
        Log.d(LOG_TAG, "Widget Provider enabled.  Starting timer to update widget every second");
        AlarmManager alarmManager = (AlarmManager)context.getSystemService(Context.A
LARM_SERVICE);

        Calendar calendar = Calendar.getInstance();
        calendar.setTimeInMillis(System.currentTimeMillis());
        calendar.add(Calendar.SECOND, 1);
        alarmManager.setRepeating(AlarmManager.RTC, calendar.getTimeInMillis(), 1000
, createClockTickIntent(context));
}

@Override
public void onDisabled(Context context) {
        super.onDisabled(context);
        Log.d(LOG_TAG, "Widget Provider disabled. Turning off timer");
        AlarmManager alarmManager = (AlarmManager)context.getSystemService(Context.ALARM_SERVICE);
        alarmManager.cancel(createClockTickIntent(context));
}
```

当创建您的类型的第一个小部件时，分别调用`onEnabled()`和`onDisabled()`，删除最后一个小部件。我们希望在创建第一个小部件时开始发送常规刻度，并希望在删除最后一个小部件时停止“AlarmManager”。我们简单地调用`AlarmManager.setRepeating()`来创建重复的闹铃，然后在完成后调用`cancel()`。

注意，当我们创建警报时，我们指定了一个警报类型`AlarmManager.RTC`。这种类型的警报只有在手机处于唤醒状态时才会产生。只有在处于唤醒状态时，我们才依靠这一功能来提供更新，而不是在设备处于睡眠状态时。

现在我们已经告诉 Android，我们的应用程序对接收这些意图感兴趣，并且我们已经配置了“AlarmManager”来定期发送这些意图。剩下的工作就是编写代码，在意图到达时处理它们。更改您的' [WidgetProvider](http://developer.android.com/reference/android/appwidget/AppWidgetProvider.html) '类以包含以下代码:

```
/**
 * Custom Intent name that is used by the 'AlarmManager' to tell us to update the 
clock once per second.
 */
public static String CLOCK_WIDGET_UPDATE = "com.eightbitcloud.example.widget.8BITCLOCK_WIDGET_UPDATE";

@Override    public void onReceive(Context context, Intent intent) {
    super.onReceive(context, intent);
    Log.d(LOG_TAG, "Received intent " + intent);
    if (CLOCK_WIDGET_UPDATE.equals(intent.getAction())) {
        Log.d(LOG_TAG, "Clock update");
        // Get the widget manager and ids for this widget provider, then call the shared
        // clock update method.
        ComponentName thisAppWidget = new ComponentName(context.getPackageName(), getClass().getName());
        AppWidgetManager appWidgetManager = AppWidgetManager.getInstance(context);
        int ids[] = appWidgetManager.getAppWidgetIds(thisAppWidget);
        for (int appWidgetID: ids) {
            updateAppWidget(context, appWidgetManager, appWidgetID);
        }
    }
}
```

这段代码监听意图，并在收到时更新组件内容。更新小部件的实际代码与前一篇文章中的代码相同，但是被重构为一个名为`updateAppWidget`的方法，以避免重复。请查看该类的完整代码以获取更多信息。

所以你有它。使用“AlarmManager ”,我们可以让 Android 操作系统定期向我们的应用程序发送一个意图。在本例中，我们使用该技术非常频繁地更新一个小部件，但是它也可以用于对应用程序中的其他组件进行定期更新。例如，我经常看到有人运行后台服务来定期更新他们的网络订阅源。这是可行的，但是服务一直在运行，占用了设备的内存。如果他们使用“AlarmManager ”,他们可以在每次需要更新时简单地唤醒他们的服务。

我希望这篇文章已经展示了“AlarmManager”的易用性。与往常一样，代码可以在 GitHub 的[小部件示例](https://github.com/brucejcooper/Android-Examples/tree/frequentUpdates/WidgetExample)中获得。在以后的文章中，我们将研究如何向小部件引入复杂的用户界面组件。

## 分享这篇文章