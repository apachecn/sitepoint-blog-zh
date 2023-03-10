# 设计始终在线的智能手表应用

> 原文：<https://www.sitepoint.com/designing-always-on-smart-watch-apps/>

这是智能手表腾飞的一年。有人估计 Apple Watch 在发布的第一天就卖出了几百万块。Android Wear 手表在不到一年的时间里销售了超过 100 万只，Pebble 手表为其新的 Pebble 彩色产品在 Kickstarter 上发起了一场成功的活动。

随着智能手表平台越来越受欢迎，是时候考虑为智能手表开发应用程序了。智能手表应用程序不同于智能手机应用程序，因为大多数应用程序都是为轻度交互而设计的。用户可能会在手机上花费数小时，但在手表上花费数秒。智能手表平台限制了应用程序减少交互时间的能力。例如，当用户降低手腕时，iOS [WatchKit](https://developer.apple.com/watchkit/) 会暂停正在运行的应用程序，当用户调暗屏幕时，Android Wear 会返回到钟面。

如果手表应用程序需要更长的交互时间，开发人员必须特别注意规避平台限制。一个例子是我帮助开发的[高尔夫挥杆分析器](http://play.google.com/store/apps/details?id=co.vimo.golf)应用程序，它持续运行以跟踪用户的高尔夫挥杆。在这篇文章中，我将向你展示一些让 Android Wear 手表应用程序“永远在线”的方法。

## “永远在线”模式

认识到对“永远在线”应用的需求，谷歌为 Android Wear 手表推出了*环境模式*。如果您使用的是最新的可穿戴支持库 1.2.0 及更高版本，它可以轻松启用。首先，确保*活动*继承了*可穿戴活动*，并在活动开始时启用*环境模式*:

```
public class MainActivity extends WearableActivity {

@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    setAmbientEnabled();
    ...
}
```

接下来，听环境切换回调函数。在环境模式下，尽量使用最小的用户界面以节省电池，例如，黑色背景，最少的白色图形和文本。

```
@Override
public void onEnterAmbient(Bundle ambientDetails) {
    super.onEnterAmbient(ambientDetails);

    mStateTextView.setTextColor(Color.WHITE);  // use minimal white graphics and text
    mStateTextView.getPaint().setAntiAlias(false);
}

@Override
public void onExitAmbient() {
    super.onExitAmbient();

    mStateTextView.setTextColor(Color.GREEN);
    mStateTextView.getPaint().setAntiAlias(true);
}
```

除了要在应用程序清单文件中设置的几行代码(更多细节请参考 Android Wear 的文档)，这几行代码是打开环境模式所需要的。当用户点击电源按钮或手掌屏幕时，应用程序不会显示时钟，而是保持在顶部，并进入环境模式。

## 使用通知将应用程序置于前台

Android Wear 环境模式有一些限制。开发者应该不经常更新屏幕，例如每分钟一次。否则，必须设置警报来定期唤醒 CPU。但是，环境模式下的 CPU 周期是有限的。

如果一个应用程序需要恒定的 CPU 周期，环境模式是不适合的。我开发了一款 [Android Wear 健身](http://play.google.com/store/apps/details?id=vimo.co.seven)应用，这是一个很好的例子。它不断监控用户的运动，并寻找锻炼模式，因此它不断需要 CPU 周期进行计算。

为了不丢失 CPU 周期，健身应用程序必须禁用环境模式。如果用户不小心触摸了电源按钮或手掌屏幕，应用程序将进入后台。由于环境模式被禁用，当用户再次看手表时，它将显示时钟。这是一个糟糕的用户体验，因为很容易不小心触摸到电源按钮。如果用户想要继续，他们必须转到“开始”菜单并再次找到该应用程序。

幸运的是，有一个简单的技巧。当用户关闭应用程序时，应用程序可以发送通知，然后用户可以点击通知卡继续。app 可以运行`onStop()`中的以下代码。

```
public void onStop() {
        super.onStop();
        if (!isFinishing()) {  // if not terminating, send a notification so that we can wake up if needed
            // create intent that re-launches the activity
            Intent intent = new Intent(getApplicationContext(), MyActivity.class);
            PendingIntent pendingIntent = PendingIntent.getActivity(getApplicationContext(), 0, intent, PendingIntent.FLAG_UPDATE_CURRENT);
        // when user dismiss the notification, terminate the app as well
            Intent deleteIntent = new Intent(getApplicationContext(), MyActivity.class);
            deleteIntent.setAction("delete");
            PendingIntent pendingDeleteIntent = PendingIntent.getActivity(getApplicationContext(), 0, deleteIntent, PendingIntent.FLAG_UPDATE_CURRENT);

            NotificationCompat.Action action = new NotificationCompat.Action.Builder(android.R.drawable.ic_media_play, null, pendingIntent).build();

            // set notification to let user know
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle("MyApplication")
                    .setContentText("Tap to resume.")
                    .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.drawable.launchbg))
                    .setLocalOnly(true)
                    .setDefaults(Notification.DEFAULT_VIBRATE)   // must vibrate to wake up the watch
                    .setDeleteIntent(pendingDeleteIntent)
                    .addAction(action)
                    .extend(new NotificationCompat.WearableExtender().setContentAction(0))
                    ;

            Notification notification = builder.build();

            NotificationManagerCompat notificationManagerCompat = NotificationManagerCompat.from(this);
            notificationManagerCompat.notify(Constant.NOTIFICATION_RESUME, notification);
        }
  }
```

当用户按下电源按钮时，应用程序只是进入后台，仍然保持所有状态。当用户按下通知时，活动将以其原始状态从后台返回。

上面的代码在通知中添加了一个删除意图。当用户取消通知时，会触发此意图。由于用户可能故意按下了电源按钮，这给了用户完全关闭应用程序的机会。在应用程序内部，删除意图被捕获并正确终止应用程序:

```
@Override
protected void onNewIntent(Intent intent) {
    super.onNewIntent(intent);

   String command = intent.getAction();
   if (command != null && command.equals("delete")) {
        finish();
    }
}
```

Apple Watch 缺乏“永远在线”的概念。但是苹果采取了不同的方式来满足这种需求。当用户降低手腕时，一个应用程序可能会被暂停，当用户抬起手腕时，该应用程序将再次活跃。因为框架交互与 Android Wear 完全不同，所以在 Apple Watch 上设计一个永远在线的应用程序需要一种新的方法，我将在未来的帖子中介绍这一点。

## 分享这篇文章