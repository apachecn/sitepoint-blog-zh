# 开发者在 Android N 中能期待什么？

> 原文：<https://www.sitepoint.com/what-can-developers-expect-in-android-n/>

*本文于 2016 年 5 月更新，以反映谷歌 IO* 上宣布的变化

Android N 是谷歌 Android 平台的下一个版本，将于今年某个时候推出。

![Android N](img/437fcca8c4d87bf962dd99bd8685d4bd.png)

## Android N 有什么好了解的？

通常谷歌会在 5 月的 [I/O 大会期间宣布下一个版本的 Android，但 Android N 的时间表却不同。从三月开始，谷歌将](https://events.google.com/io2016/)[发布连续的预览版](http://developer.android.com/preview/overview.html)，让开发者有时间准备他们的应用程序以兼容 N 版本。Android N 的最终确定(以及它的正式命名)要到今年第三季度才能完成。

![Android N Development Timeline](img/64a0a0c1b5c21a0fbcccbc8cf32ca73a.png)

像所有的 Android 更新一样，N 也有一系列的新特性和改进，我今天将会谈到。

## 下载并安装预览版

获得 Android N 包括谷歌的[入门](http://developer.android.com/preview/setup-sdk.html)指南中概述的几个步骤，但这里有一个基于我的经验的快速总结。

### 下载 Android Studio 2.1

[下载 Android Studio 2.1](http://tools.android.com/download/studio/canary/latest) ，目前正在预览中。不要下载 2.0，它与 Java 8 有问题。如果您想保留当前的 Android Studio 版本(例如 1.5)并测试 Android N，您应该编辑现有的 Android Studio 应用程序名称，这样您就可以运行这两个版本。

一旦你安装了 Android Studio 预览版，从“金丝雀频道”设置自动更新，这样你就可以更新你的 Android N 映像(它会定期更新)。

### 下载 Android N 镜像

打开 *SDK 管理器*，搜索 *Android N* 镜像，选择*英特尔 x86 Atom 系统镜像*版本。在我的整个测试过程中，我从未让*英特尔 x86 Atom_64 系统映像*工作，但您的体验可能会有所不同。

![Pick SDK](img/9ab71e325872cb0747dbefc6182922c2.png)

### 获取 Java 8 JDK / JRE

要为 Android N 编译应用程序，你需要 Java [8 开发工具包](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)和 [Java 8 运行时环境](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html)，因为 Android Studio 2.1 的某些部分需要它。

一旦安装了这两个工具，打开任何项目的文件结构，并将其指向 Java 8 运行时。

![Change Java Runtime](img/519406ab1b299de40928707065b310ce.png)

### 更新梯度

打开 Android Studio 2.1 中的任何新项目，并打开 *build.gradle* 以检查您是否针对 Android N。在我的所有测试中，我发现最简单的方法是指定该应用程序只能在 Android N 上运行:

```
android {
  compileSdkVersion 'android-N'
  buildToolsVersion 24.0.0 rc1
  ...

  defaultConfig {
     minSdkVersion 'N'
     targetSdkVersion 'N'
     ...
  }
  ...
} 
```

完成这些后，你应该能够构建你的应用程序，并看到 Android N 的荣耀！

## 新功能

### 安全改进

谷歌正在努力改善 Android 在安全方面的不良声誉，包括:

*   **直接引导**:设备开启但锁定时的新模式。这允许某些应用程序访问，但不允许其他应用程序访问。
*   **基于文件的加密**:对单个文件夹和文件进行细粒度加密，而不是对整个磁盘块进行加密。
*   **媒体框架强化**:在 stagefright 恐慌之后，媒体处理被分解成单独的组件，而不是一个大型组件为一个设备提供太多权限。

### 多窗口和自由形式模式

一段时间以来，设备制造商一直希望 Android 支持多窗口。多窗口(也称为多任务)允许用户通过同时显示两个或更多应用程序来共享屏幕空间。

这些应用程序可以并排显示，或者一个在另一个上面，让用户在多个任务之间来回切换。例如，在导航应用程序旁边打开网页浏览器。

随着 Android N 的推出，谷歌正在让这成为一项原生功能。请看下面的截图，它显示了 Chrome 在分屏模式下与谷歌地图并排运行。

![Multi-Window Example](img/1887415a51df9afaedc82a5957cc5951.png)

通过这种并排分割筛选应用程序的方法，应用程序会吸附到中间或稍微偏离中心的位置。更大设备的制造商也可以启用“自由模式”，让用户手动控制每个应用程序的大小。

### 多窗口应用程序生命周期的变化

所有应用程序都会经历它们的[生命周期事件](http://developer.android.com/training/basics/activity-lifecycle/index.html)，这取决于应用程序要转换到的状态。例如，当用户按下 home 键时，活动的应用程序进入非活动状态。开发人员可以利用这些变化来触发应用程序功能。

因为现在可能一次显示不止一个应用程序，所以你需要考虑当事件改变时应用程序将如何反应。尽管任何时候都只有一个应用程序处于活动状态，但仍然可以看到多个应用程序。当一个应用程序进入它的[‘暂停状态’](http://developer.android.com/training/basics/activity-lifecycle/pausing.html)时，它不应该取消用户界面或者改变视觉显示。一个应用程序应该只在[【停止状态】](http://developer.android.com/training/basics/activity-lifecycle/stopping.html)下这样做。

### 向您的应用程序添加多窗口支持

要在你的应用中启用多窗口模式，你需要在清单文件中为整个`<application>`或每个支持的`<activity>`设置新的属性`android:resizeableActivity`到`true`。

默认情况下，如果你的应用程序的`targetSdkVersion`小于`android-N`，则为`false`，否则为`true`。

### 多窗口应用程序的布局属性

一旦你的应用程序被设置为使用多窗口，你可以使用新的[布局属性](http://developer.android.com/preview/features/multi-window.html#layout)来决定应用程序如何调整大小。

*   `android:defaultWidth`:指定应用程序在自由模式下启动时的默认宽度(在 DPs **或**中为分数)
*   `android:defaultHeight`:指定应用程序在自由模式下启动时的默认高度(以 DPs **或**的分数表示)
*   `android:gravity`:应用程序以“自由模式”启动时的初始重力。这将告诉系统这个应用程序相对于屏幕容器的位置(例如用`top|start`捕捉到左下方)。阅读更多关于[布局重力在这里。](http://developer.android.com/reference/android/view/Gravity.html)
*   `android:minimumSize`:设置应用程序在“自由模式”和标准“并排”多任务模式下启动时的最小尺寸(高度和宽度**仅在 DPs 中为**)。

### 多窗口查询方法

作为该特性的一部分，`activity`类已经被扩展，因此您可以查询 activity 来确定您是处于多窗口模式还是画中画模式。如果您需要自定义布局或基于多窗口状态执行其他计算，这将非常有用。

*   `Activity.inMultiWindow()`:告诉你当前活动是否在多窗口中
*   `Activity.inPictureInPicture()`:告诉你当前活动是否在画中画模式内(一般在为 Android TV 开发时)
*   `Activity.onMultiWindowChanged()`:当应用程序切换进或切换出多窗口模式时调用。如果当前在模式内，则为`true`，否则为`false`。
*   `Activity.onPictureInPictureChanged()`:画中画状态变化时调用。如果在此模式内，则为`true`，否则为`false`。

#### 对多窗口状态做出反应

下面是一个如何使用这些事件来改变用户界面的例子。

当一个应用程序切换到多窗口模式时，它的垂直/水平尺寸将会缩小 30%到 70%(因为用户可以重新定位多窗口以使应用程序变得更小或更大)。如果是这种情况，你可能想放大文本尺寸，使其更容易阅读。

```
//On Multi-Window, adjust text sizing
public void onMultiWindowChanged(boolean inMultiWindow) {
    super.onMultiWindowChanged(inMultiWindow);

    //find our UI items
    TextView title = (TextView) findViewById(R.id.appTitle);
    TextView subtitle = (TextView) findViewById(R.id.appSubtitle);
    TextView content = (TextView) findViewById(R.id.appContent);

    //read default text sizes (In Sp's from dimension resources) and divide by density to find its size
    Float titleSize = getResources().getDimension(R.dimen.titleTextSize) / getResources().getDisplayMetrics().density; //22 SP
    Float subtitleSize = getResources().getDimension(R.dimen.SubtitleSize) / getResources().getDisplayMetrics().density; //18 SP
    Float contentSize = getResources().getDimension(R.dimen.contentSize) / getResources().getDisplayMetrics().density; //15 SP

    //Entering Multi-window, adjust sizing up
    if(inMultiWindow == true){
        //adjust text size by 25%
        title.setTextSize(TypedValue.COMPLEX_UNIT_SP, (float) (titleSize + (titleSize * 0.25)));
        subtitle.setTextSize(TypedValue.COMPLEX_UNIT_SP, (float) (subtitleSize + (subtitleSize * 0.25)));
        content.setTextSize(TypedValue.COMPLEX_UNIT_SP, (float) (contentSize + (contentSize * 0.25)));
    }
    //Exiting multi-window, adjust size back to normal
    else{
        title.setTextSize(TypedValue.COMPLEX_UNIT_SP, titleSize);
        subtitle.setTextSize(TypedValue.COMPLEX_UNIT_SP, subtitleSize);
        content.setTextSize(TypedValue.COMPLEX_UNIT_SP, contentSize);
    }
} 
```

以上代码的作用如下:

1.  活动内部是新的`onMultiWindowChanged`事件监听器。
2.  查找所有 UI 项目(在本例中是一系列的`TextView`)。
3.  从维度资源文件中获取 SP 大小，除以屏幕密度以获得正确的大小。
4.  检查以查看`inMultiWindow`标志的值。
5.  如果`true`表示应用程序正在进入多窗口，那么将 UI 元素的文本设置为 25%大。
6.  如果`false`表示应用程序退出多窗口，则从尺寸文件中重置回默认值。

这里有一个比较来看看这一点。左边是切换到多窗口模式的应用程序，带有缩放的文本。右边是正常全屏模式的应用。

![Screen Mode Comparison](img/ca91c50998278a8bae1fd1b476f9bbac.png)

您可以在这里使用任何您想要的逻辑，这是一个检查您的 UI 在这些模式之间转换时是否仍然看起来很棒的完美地方。

## 更新的通知

自 Android 4.1 (Jellybean)和 Android N 增加了直接通知回复和捆绑通知后，通知得到了持续改进。

### 捆绑通知

这是一个伟大的功能，它已经从 Android Wear 发展到手机和平板设备。

通过 Wear，当一个应用程序发送多个通知(例如收到的电子邮件)时，应用程序可以选择将它们“捆绑”在一起。用户在顶部有一个通知和一个查看相关通知的开关，而不是一个接一个的通知。这让他们尽量减少混乱。

![Stacked Notifications](img/eae5496920c24284b4608292f9ef730f.png)

Android N 允许开发者在堆栈中将通知分组在一起。这个例子创建了一个带有按钮的场景，当按钮被按下时，发送多个通知。

```
//stacked notification example
public void startStackedNotifications(View view){

    final String NotificationKey = "notification_key"; //used to group notifications together

    //create 3 notifications
    for(int i = 1; i <= 3; i++){

        //build notification
        NotificationCompat.Builder builder = new NotificationCompat.Builder(this);
        builder.setContentTitle("Message " + i + " title");
        builder.setContentText("Content for message number " + i + ". This is some additional long content that will get trimmed off when it's too long");
        builder.setSmallIcon(R.drawable.ic_announcement);
        builder.setGroup(NotificationKey);

        //If on the last notification, set the notification group
        if(i == 3){
            Bitmap largeIcon = BitmapFactory.decodeResource(getResources(), R.drawable.ic_person);

            NotificationCompat.Builder summaryNotification = new NotificationCompat.Builder(this);
            summaryNotification.setContentTitle("You have messages");
            summaryNotification.setSmallIcon(R.drawable.ic_announcement);
            summaryNotification.setLargeIcon(largeIcon);
            summaryNotification.setGroup(NotificationKey);
            summaryNotification.setGroupSummary(true);

            int summaryNotificationID = 400;
            NotificationManagerCompat summaryManager = NotificationManagerCompat.from(this);
            summaryManager.notify(summaryNotificationID, summaryNotification.build());
        }

         //issue the notification
        int notificationID = (300 + i);
        NotificationManagerCompat manager = NotificationManagerCompat.from(this);
        manager.notify(notificationID, builder.build());
    }
} 
```

这段代码在活动中创建一个`Button`小部件，并将其点击处理程序设置为`startStackedNotifications`函数。该功能的目的是创建通知并显示它们。然后，它使用一个循环来创建 3 个通知。在其中的每一个中，会发生以下情况:

1.  使用`NotificationCompat.Builder`类创建一个新的通知生成器。正是这个类设置了消息的标题和内容。
2.  调用`setGroup`方法并传递一个通知键来将通知分组在一起(这很重要)。
3.  如果是最后一个循环，请创建一个通知摘要，用于分组通知的顶部。这与上面的过程相同，但是将`setGroupSummary`方法设置为`true`来告诉 Android 这是摘要。
4.  使用`NotificationManagerCompat`类及其`notify`方法发送通知。
5.  像以前一样，使用`NotificationManagerCompat`类及其`notify`方法发出三个主要通知。

这是通知堆叠在左边、分隔在右边时的样子。

![Grouped Messages](img/b65d87de3bffe34546c578e43a111fcc.png)

### 直接通知回复

直接通知回复允许用户在通知到达时立即回复通知。

Android N notifications 的[文档为如何启动和运行这些通知提供了一个很好的分步指南。了解通知是如何工作的，以及它们在不同设备/平台上是如何处理的，这是一个好主意，](http://developer.android.com/preview/features/notification-updates.html#direct) [Android 对通知的介绍应该有所帮助](http://developer.android.com/guide/topics/ui/notifiers/notifications.html)。

![Direct Notifications](img/cb58898b52d7a6df72c1d5279063c9e7.png)

## 数据保护程序

从 Android N 开始，谷歌包括更多的工具和选项，让用户尽量减少数据使用。n 让用户能够在他们的设备上保存数据，向应用程序发送信号以使用更少的数据。当激活时，数据保存模式将阻止所有后台数据的使用。

这一变化是对 [ConnectivityManager](http://developer.android.com/reference/android/net/ConnectivityManager.html) API 更新的一部分。这个 API 提供了一个接口来访问关于当前网络状态的信息，并在网络状态改变时提供通知。

开发人员可以使用三个新的常量来检测数据保存首选项。

*   `RESRICT_BACKGROUND_STATUS_DISABLED`:数据保护被禁用。
*   `RESRICT_BACKGROUND_STATUS_ENABLED`:数据保护开启，后台数据。使用被阻止。开发人员还应该尽量减少前台数据。
*   `RESRICT_BACKGROUND_STATUS_WHITELISTED` : Data saver 已启用，但您的应用程序被列入白名单，后台数据将照常处理。你应该尽量减少前台数据。

### 检测对数据保护程序首选项的更改

当用户改变数据保护程序偏好设置时，你的应用程序应该相应地调整。`ConnectivityManager`公开了一个`ACTION_RESTRICT_BACKGROUND_CHANGED`事件，您可以监听该事件以根据需要做出反应。

在 activity 类中添加以下私有字段。这些主要是为了方便访问`TextView`和`ConnectivityManager`项。

```
private TextView tv_dataSaverStatus;
private ConnectivityManager conManager;

 protected void onCreate(Bundle savedInstanceState) {

    //find the TextView and set up our connectivity manager
    tv_dataSaverStatus = (TextView) findViewById(R.id.dataSaverStatus);
    conManager = (ConnectivityManager) getSystemService(CONNECTIVITY_SERVICE);
    displayDataSaverStatus(conManager, tv_dataSaverStatus);

    //new intent filter to look for (dataSaver)
    IntentFilter filter = new IntentFilter();
    filter.addAction(conManager.ACTION_RESTRICT_BACKGROUND_CHANGED);

    //new receiver, extending broadcast receiver
    DataSaverReceiver receiver = new DataSaverReceiver();

    //register receiver for us to use
    registerReceiver(receiver, filter);
} 
```

在`onCreate`函数中，找到将显示当前数据保护程序设置的`TextView`，并为显示函数创建`ConnectivityManager`对象。

创建一个意图过滤器，并对其应用`ACTION_RESTRICT_BACKGROUND_CHANGE`。它告诉 Android 当这个事件被触发时，你想要观察它。从`DataSaverReceiver`类创建一个新的对象，当这些设置改变时将执行这个对象(下面将进一步解释)。最后，通过注册接收者将所有这些联系在一起。

在`onCreate`方法下面，创建一个扩展`BroadcastReceiver`类的新类。这是一个扩展广播，将在应用程序打开时以及每次 Data Saver 设置更改时执行。在本例中，通过调用函数来改变显示。

```
//custom class for capturing the dataSaver broadcast
public class DataSaverReceiver extends BroadcastReceiver{

    //trigger as we receive the event
    public void onReceive(Context context, Intent intent) {
        //update the dataSaver display
        displayDataSaverStatus(conManager, tv_dataSaverStatus);
    }
} 
```

该功能检查用户是否连接到移动网络(通过`isActiveNetworkMetered`)，如果是，检查是否是由于数据保护功能。

根据检测到的设置，更改`TextView`的文本和背景颜色。

```
//writes the current status of the dataSaver setting to the UI
public void displayDataSaverStatus(ConnectivityManager conManager, TextView tv_dataSaverStatus){

     //cellular network
     String message = "";
     int color = R.color.dataDefault;
     if(conManager.isActiveNetworkMetered()){

         int restrictBackgroundStatus = conManager.getRestrictBackgroundStatus();
         if(restrictBackgroundStatus == conManager.RESTRICT_BACKGROUND_STATUS_ENABLED) {
             message = "Datasaver Status: Enabled \nNo background processing allowed while data saver enabled!";
             color = R.color.dataSaverEnable;
         }else if(restrictBackgroundStatus == conManager.RESTRICT_BACKGROUND_STATUS_DISABLED) {
             message = "Datasaver Status: Disabled \nBackground processing allowed. Continue as normal";
             color = R.color.dataSaverDisabled;
         }else if(restrictBackgroundStatus == conManager.RESTRICT_BACKGROUND_STATUS_WHITELISTED) {
             message = "Datasaver Status: Enabled \nHowever, app is whitelisted so background data will work still.";
             color = R.color.dataSaverEnabledWhitelist;
         }
     }
     //normal network
     else{
         message = "Datasaver Status: Irrelevant \n Currently connected to a non-meters network so all good";
         color = R.color.dataDefault;
     }

     tv_dataSaverStatus.setText(message);
     tv_dataSaverStatus.setBackgroundColor(getResources().getColor(color));
 } 
```

这是一个简单的示例，但是现在您可以检索数据保存器的状态并查找其状态的变化，您可以调整您的应用程序以匹配新的数据保存策略。

![Data Saver Example](img/10a8a62732b1c53832d1d361348e3e54.png)

## Android“Doze”的更新

Android Marshmallow 推出了 [Doze](http://developer.android.com/training/monitoring-device-state/doze-standby.html) 来提高电池的使用率和寿命，方法是在设备断电、静止或屏幕关闭时推迟后台同步和任务等活动。 [Doze 现在根据你设备的状态分两个阶段工作](http://developer.android.com/preview/behavior-changes.html#doze)。

当您的设备屏幕关闭、使用电池电源且不稳定时，第一级节能将启用，即推迟作业和后台进程以最大限度地降低功耗。

![Doze Details](img/cfed2d469759f101239c4af1c30b8230.png)

在 N 中，谷歌引入了一个更积极的瞌睡状态，一旦设备**静止**并处于第一瞌睡状态一段时间就会触发。当这种次要状态应用时，它会禁用更多功能，如 GPS / WiFi 扫描和唤醒锁。

![Doze Updates](img/00702357632486894caca00027c3a8c4.png)

这背后的想法是，如果一个设备是完全静止的，它很可能没有在使用，例如放在桌子上的电话。Android 可以利用这一时间节省更多的电池电量，同时仍然提供性能。

**注意**:即使 doze 状态处于活动状态，设备也会定期唤醒，让应用程序执行它们的任务，确保更新和通知仍然有效。

### Java 8 特性

[Android N 推出支持 Java 8 及其语言特性](http://developer.android.com/preview/j8-jack.html)。如果你的应用针对 Android N 平台，那么你可以利用这些 Java 8 的特性。但是要确保你能为旧版本的 Android 提供支持。

这些功能包括:

*   [默认和静态接口方法](https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html)
*   [λ表达式](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)
*   [可重复注释](https://docs.oracle.com/javase/tutorial/java/annotations/repeating.html)

### 实时(JIT)编译

Android 5.0 版本向 Android 运行时(ART)的转变完全转变为超前(AOT)代码编译。Android N 重新引入了 JIT 编译，这意味着在初始安装或操作系统更新后运行应用程序可能会快得多。有时候一种编译方法比另一种更合适，Android 系统会在每种情况下智能地选择最有用的。

### 用于工作改进的 Android

Android for work 是谷歌在工作场所使用 Android 的企业/商业模式。

Android for work 从 Android 4.0 开始通过' [Android for Work](https://support.google.com/work/android/answer/6207694) '应用程序提供。自从 Android 5.0 和“[工作简介](https://support.google.com/work/android/answer/6191949?hl=en)”以来，信息技术经理们已经获得了更好的工具和控制来帮助他们管理使用 Android 的企业解决方案。

Android N 的改进包括:

*   **切换工作模式**:让用户关闭他们的工作模式。这将暂时删除后台同步、通知和工作档案应用，直到重新启用
*   **永远在线 VPN** :强制工作用户通过 VPN 连接访问应用和信息，提高安全性。
*   **禁止访问应用**:信息技术管理员可以暂停访问工作应用，确保公司数据安全。稍后可以重新启用它。
*   **远程启动**:授权方可以远程启动设备，这对于安装的 POS 终端等始终在线的设备来说非常棒。

如果你感兴趣，你可以在预览网站上看到[完整的功能说明。](http://developer.android.com/preview/features/afw.html)

### 屏幕缩放

Android 支持缩放以及其他可访问性问题已经有一段时间了，用户可以选择缩放文本和其他屏幕元素。Android N 提供了缩放整个 UI 的选项，这将按比例缩放所有内容。我建议您在使用这个特性时测试一下界面的外观。

## 结论

Android N 在 Android 已经提供的基础上做了很多改进，增加了它的灵活性和健壮性。对于开发人员来说，此版本的主要特性是:

*   **多窗口**:真正实现 2 个或更多应用程序的并行多任务处理。
*   **数据节省器**:帮助您的用户减少移动数据并延长电池寿命。
*   **分组和直接通知**:通过更加直观和灵活的通知来增强 UI。

你应该计划并测试你的应用程序中的每一个功能，以确保它们能够工作并遵守 Android 的开发准则。

在接下来的几个月中，谷歌将发布 Android N 的进一步更新，希望在 2016 年第三季度，我们将看到 Android N 平台的最终命名和推出。

我期待着用这些新功能来增强我的应用，*你呢*？

## 分享这篇文章