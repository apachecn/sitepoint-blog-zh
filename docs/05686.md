# 在 Android 中调度后台任务

> 原文：<https://www.sitepoint.com/scheduling-background-tasks-android/>

您的 Android 应用程序可能需要定期执行一项任务，比如检查服务器的更新。`AlarmManager`类可用于调度和执行即使应用程序没有运行也需要发生的操作。

`AlarmManager`类支持对将来在设定点运行的重复报警进行调度。每当预定警报时，`AlarmManager`被给予一个`PendingIntent`来触发。当警报被触发时，Android 系统会广播注册的意图，如果目标应用程序尚未运行，则会启动它。

## 警报类型和精度

有两种主要的报警类型，经过实时和实时时钟。第一个对应于系统启动后的时间，第二个对应于 UTC 时间。不同的警报类型可以设置为在睡眠模式下唤醒设备的 CPU，或者在设备下次醒来时触发。以下是可用的不同报警类型变化列表。

*   ELAPSED _ real time–在设备启动后的指定时间长度后触发挂起的意向。如果设备处于睡眠状态，它会在设备下次醒来时触发。

*   ELAPSED _ real time _ wake up–在设备启动后的指定时间长度后触发挂起的意图。如果设备处于睡眠状态，它会唤醒设备。

*   RTC–在指定时间触发挂起的意图。如果设备处于休眠状态，则直到设备下次唤醒时才会发送。

*   RTC _ wake up–在指定的时间触发挂起的意图，唤醒睡眠中的设备。

决定使用何种报警取决于手头任务的要求。经过的时间适合于设置应该基于时间的流逝而触发的警报，例如定期检查服务器的变化。如果您想运行一个依赖于当前地区的任务，那么实时时钟会更合适。

请注意，经过的时间通常是更好的选择。如果用户改变他们的时间设置或移动到一个新的地区，这种变化可能会导致应用程序中一些意想不到的行为。如果您为应用程序与服务器同步设置了特定时间，当应用程序的所有实例同时达到时，服务器可能会不堪重负。

## 设置周期性任务

我们将创建一个简单的应用程序来显示任务的调度。我们将安排的任务是使用`Toast`显示一条消息(toast 在一个小弹出窗口中提供关于操作的简单反馈)。这个项目的代码可以在这个 [Git Repo](https://github.com/echessa/alarm_example "Git repo for example code") 找到。

创建新的 Android 项目。对于视图，创建两个按钮来启动和停止定期操作。

在`res/layout/activity_main.xml`

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/LinearLayout1"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context=".MainActivity" >

    <Button
        android:id="@+id/button1"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Start Alarm"
        android:onClick="startAlarm" />

    <Button
        android:id="@+id/button2"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Cancel Alarm"
        android:onClick="cancelAlarm" />

</LinearLayout> 
```

为了接收意图，我们将设置一个广播接收器，以便在发出警报时执行操作。创建一个继承自`BroadcastReceiver`的类。在`onReceive`方法中，当`BroadcastReceiver`正在接收`Intent`广播时调用该方法，我们将设置运行任务的代码。

在`AlarmReceiver.java`

```
package com.example.alarmexample;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.widget.Toast;

public class AlarmReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context arg0, Intent arg1) {
        // For our recurring task, we'll just display a message
        Toast.makeText(arg0, "I'm running", Toast.LENGTH_SHORT).show();

    }

} 
```

然后，我们需要在清单文件中注册 BroadcastReceiver。在清单文件中声明`AlarmReceiver`。

```
<application>
    .
    .
    <receiver android:name=".AlarmReceiver"></receiver>
    .
    .
</application> 
```

为了启动闹铃，我们将把上面`res/layout/activity_main.xml`中声明的方法`startAlarm`设置为单击启动闹铃按钮时运行的方法。

在`MainActivity.java`中声明一个`PendingIntent`和一个`AlarmManager`变量。`PendingIntent`将用于设置和取消警报。

在`MainActivity.java`中包含以下实例变量。

```
private PendingIntent pendingIntent;
private AlarmManager manager; 
```

在`onCreate()`中，我们创建了一个引用我们的广播接收器类的`Intent`，并在我们的`PendingIntent`中使用它。

```
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    // Retrieve a PendingIntent that will perform a broadcast
    Intent alarmIntent = new Intent(this, AlarmReceiver.class);
    pendingIntent = PendingIntent.getBroadcast(this, 0, alarmIntent, 0);
} 
```

然后，我们将包括设置重复报警的方法。一旦设置好，闹钟会每 10 秒响一次。

```
public void startAlarm(View view) {
    manager = (AlarmManager)getSystemService(Context.ALARM_SERVICE);
    int interval = 10000;

    manager.setRepeating(AlarmManager.RTC_WAKEUP, System.currentTimeMillis(), interval, pendingIntent);
    Toast.makeText(this, "Alarm Set", Toast.LENGTH_SHORT).show();
} 
```

运行该应用程序，点击“开始闹铃”按钮，将出现“闹铃设置”消息，然后每 10 秒钟出现一次“我正在跑步”消息。

我们使用了`setRepeating()`方法来设置重复报警，但是也可以使用`setInexactRepeating()`。通过`setInexactRepeating()`，来自多个应用程序的重复警报将被同步并同时触发。这将减少设备被唤醒的次数，从而节省电池电量。截至 Android 4.4，所有重复报警都不准确。

`setRepeating()`方法有四个参数:

1.  `type`–警报类型，由使用的时间单位和设备处于睡眠模式时是否发生来指定。可以是 [ELAPSED_REALTIME](http://developer.android.com/reference/android/app/AlarmManager.html#ELAPSED_REALTIME) 、 [ELAPSED_REALTIME_WAKEUP](http://developer.android.com/reference/android/app/AlarmManager.html#ELAPSED_REALTIME_WAKEUP) 、 [RTC](http://developer.android.com/reference/android/app/AlarmManager.html#RTC) 或 [RTC_WAKEUP](http://developer.android.com/reference/android/app/AlarmManager.html#RTC_WAKEUP) 。

2.  `triggerAtMillis`–警报首次触发的时间(毫秒)。

3.  `intervalMillis`–连续重复报警之间的时间间隔，单位为毫秒。

4.  `operation`–警报触发时要执行的动作

接下来，我们将设置`cancelAlarm()`方法来停止警报。在`MainActivity.java`中包含以下方法

```
public void cancelAlarm(View view) {
    if (manager != null) {
        manager.cancel(pendingIntent);
        Toast.makeText(this, "Alarm Canceled", Toast.LENGTH_SHORT).show();
    }
} 
```

再次运行应用程序之前，请强制停止或卸载设备或模拟器中的应用程序。如果不这样做，以前的闹钟时间表仍将运行。运行应用程序，可以停止预定的警报。

以上是一个简单的例子，展示了如何使用`AlarmManager`来设置和取消警报。我使用同一个`AlarmManager`对象来启动和停止闹钟。当应用程序退出时，这当然不会持续，所以对于一个真正的应用程序，而不是只在`manager`不为空时取消警报(如果应用程序退出，无论警报是否运行，它都会为空)，你应该用一个等同于设置它的`PendingIntent`来取消警报。例如，你可以这样做。

```
 Intent alarmIntent = new Intent(this, AlarmReceiver.class);
pendingIntent = PendingIntent.getBroadcast(this, 0, alarmIntent, 0);
manager = (AlarmManager)getSystemService(Context.ALARM_SERVICE);
manager.cancel(pendingIntent);
Toast.makeText(this, "Alarm Canceled", Toast.LENGTH_SHORT).show(); 
```

## 结论

我们已经了解了如何使用`AlarmManager`来调度周期性任务。`AlarmManager`最适用于即使在应用程序没有打开的情况下也需要运行的任务。对于需要在应用程序使用期间运行的正常计时操作(滴答、超时等)，使用[处理程序](http://developer.android.com/reference/android/os/Handler.html)的`postDelayed()`和`postAtTime()`方法会更有效。`AlarmManager`需要太多的开销来证明这样使用它是正确的。

## 分享这篇文章