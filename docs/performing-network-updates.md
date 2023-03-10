# 执行网络更新

> 原文：<https://www.sitepoint.com/performing-network-updates/>

这是关于 Android 小部件的系列文章的最后一篇，我们将在之前文章的基础上，定期更新小部件，并添加一个后台服务来从网络获取数据。为了方便起见，我们将显示来自 Twitter 的公共时间线的最新 Tweet 作为我们的数据源，但这将很容易适用于其他来源。我们还将添加一个配置活动，在显示小部件之前，提示小部件的用户输入他们想要显示的 Twitter 用户。

如果您需要赶上进度，之前的文章是:

*   [如何编写 Android 小工具的代码](https://www.sitepoint.com/how-to-code-an-android-widget/)
*   [AlarmManager 和 Sleepy Android 应用](https://www.sitepoint.com/alarmmanager-and-sleepy-android-apps/)

### 准备演示

在我们之前使用 [AlarmManager](https://www.sitepoint.com/alarmmanager-and-sleepy-android-apps/) 的例子中，我们有一个显示时钟的工作小部件，它通过使用 AlarmManager 每秒更新一次。这对我们来说是一个完美的起点，因为我们希望定期更新数据。不过，我们需要将频率调低一点，大约每 5 分钟一次。

如果你还没有源代码，可以从我的 [Android Examples GitHub 资源库](https://github.com/brucejcooper/Android-Examples)中使用`frequentUpdates`标签获取。这可以通过运行以下命令来完成。我们假设您已经设置了命令行 Git，但是您当然已经设置了。

```
git clone https://github.com/brucejcooper/Android-Examples.git
cd Android-Examples/WidgetExample
git tag frequentUpdates
```

现在我们准备添加我们的网络变化

### 后台任务–服务

在 Android 上，调用网络服务，尤其是像 Twitter 这样用户友好的服务，是相对简单的。然而，我们不能只在我们的小部件提供者中执行调用，因为网络调用将花费相对较长的时间，并且我们不允许阻塞应用程序主线程。它需要在单独的线程中运行。这也没什么大不了的，但是还有一个更复杂的问题。Android 进程有一个特定的生命周期，可以在任何时候被操作系统破坏以回收内存。我们需要一种方式向操作系统表明，我们正在后台做一些事情，不应该被杀死，即使我们没有直接在屏幕上显示一些东西。这是通过[服务](http://developer.android.com/guide/topics/fundamentals/services.html)类完成的。

服务类似于我们在[活动、任务&意图](https://www.sitepoint.com/activities-tasks-and-intents-oh-my/)中讨论的活动，除了服务是为了在后台运行代码。它有一个生命周期，类似于一个活动的生命周期，可以明确开始，也可以根据一个意图开始。服务可以是其所在应用程序的私有服务，也可以通过进程间通信(IPC)为整个系统提供服务。在这种情况下，我们将创建一个非常简单的服务，该服务显式启动、自行停止，并且没有任何绑定。

与所有向操作系统公开的组件一样，您的服务需要在`ApplicationManifest.xml`文件中声明。因为我们的服务非常简单，所以我们只需指定托管服务的类的名称。

```
...
<application android:icon="@drawable/icon" android:label="@string/app_name">
    <service android:name=".TwitterFetcherService"/>
...
```

实现服务应该实现生命周期方法`onCreate()`、`onStartCommand()`、`onDestroy()`和`onBind()`。我们没有使用绑定，所以`onBind()`可以简单地返回 null。相反，我们使用`onStartCommand()`来产生实际执行网络获取的线程。

```
@Override
public void onCreate() {
    mNM = (NotificationManager)getSystemService(NOTIFICATION_SERVICE);

    // Display a notification about us starting.  We put an icon in the status bar.
    showNotification();
}

@Override
public int onStartCommand(Intent intent, int flags, int startId) {
    Log.i(LOG_TAG, "Received start id " + startId + ": " + intent);

    SharedPreferences settings = getSharedPreferences(PREFS_FILE, MODE_PRIVATE);
    String username = settings.getString("twitterUser", null);

    if (fetcherThread != null &amp;&amp; fetcherThread.isAlive()) {
        Log.d(LOG_TAG, "Killing existing fetch thread, so we can start a new one");
        fetcherThread.interrupt();
    }
    fetcherThread = new Fetcher(username);
    fetcherThread.start();
    // We want this service to continue running until it is explicitly
    // stopped, so return sticky.
    // WE have no reason to exist!
    return START_STICKY;
}

@Override
public void onDestroy() {
    // Cancel the persistent notification.
    mNM.cancel(NOTIFICATION);

    if (fetcherThread.isAlive()) {
        fetcherThread.interrupt();
    }

    Log.i(LOG_TAG, "Stopped Service");
}
```

该代码还创建了一个通知消息，它将显示在手机的通知栏中。我这样做是为了当我们知道服务正在运行时，它对我们是显而易见的。不过，我不会在真正的小部件中这样做，因为这会分散用户的注意力。他们不关心什么时候打电话，只关心小部件完成它的工作。

### 打电话

Android 内置了 [Apache HttpClient](http://hc.apache.org/) ，这使得呼叫网络变得非常容易。为了让这篇文章简单，我选择了相对简单的[用户时间线 Twitter API](https://dev.twitter.com/docs/api/1/get/statuses/user_timeline) ，通过简单的`GET`操作获取用户的时间线。我们获取的 URL 是:

```
https://api.twitter.com/1/statuses/user_timeline.json?include_entities=false&amp;include_rts=true&amp;screen_name=username&amp;count=1
```

这将返回所提供的用户的最后一条 Tweet 作为 [JSON](http://json.org/) ，以及一堆我们不关心的附加信息。执行提取的线程的完整代码如下所示:

```
public class Fetcher extends Thread {
    private String username;

    public Fetcher(String username) {
        this.username= username;
    }

    public void run() {
        // Set a timeout on connections
        HttpParams httpParams = new BasicHttpParams();
        HttpConnectionParams.setConnectionTimeout(httpParams, 10 * 1000);
        HttpConnectionParams.setSoTimeout(httpParams, 10 * 1000);

        HttpClient client = new DefaultHttpClient(httpParams);

        try {
            if (username == null) {
                Log.d(LOG_TAG, "Stopping before I've started. How sad");

            } else {
                Log.d(LOG_TAG, "Fetching tweet");
                HttpGet fetch = new HttpGet("https://api.twitter.com/1/statuses/user_timeline.json?include_entities=false&amp;include_rts=true&amp;screen_name="+username+"&amp;count=1");
                if (!isInterrupted()) {
                    HttpResponse response = client.execute(fetch);
                    if (response.getStatusLine().getStatusCode() == HttpStatus.SC_OK) {
                        JSONArray tweets = new JSONArray(EntityUtils.toString(response.getEntity()));

                        String tweetTxt = Html.fromHtml(tweets.getJSONObject(0).getString("text")).toString();

                        updateWidgets(tweetTxt);
                    } else {
                        showError(null);
                        // An Error happened.  Deal with it somehow
                    }
                }
            }
        } catch (InterruptedIOException e) {
            Log.d(LOG_TAG, "I was interrupted!");
        } catch (Exception e) {
            showError(e);
        } finally {
            Log.d(LOG_TAG, "Shutting myself down");
            // We're done.  Shut ourself down.
            stopSelf();
        }
    }

    private void showError(Exception ex) {
        Log.e(LOG_TAG, "Error fetching tweets", ex);
        updateWidgets( "Fetching Tweets failed");
    }
}
```

特别注意 finally 块中对`stopSelf()`的调用。一旦我们完成处理，服务就不需要继续运行了。我们正在以一种一次性使用的方式使用这项服务。因此，完成后自行关闭是服务的责任。这就是这个调用的作用。

最后，一旦有了数据，我们需要一种方法来更新小部件本身。这与我们之前的代码完全相同，只是我们将它从 WidgetProvider 移到了服务中。

```
public void updateWidgets(String tweetTxt) {
    // Get the widget manager and ids for this widget provider, then call the shared
    // clock update method.
    ComponentName thisAppWidget = new ComponentName(getPackageName(), ExampleAppWidgetProvider.class.getName());
    AppWidgetManager appWidgetManager = AppWidgetManager.getInstance(this);
    int ids[] = appWidgetManager.getAppWidgetIds(thisAppWidget);
    for (int appWidgetID: ids) {

        if (pendingIntent == null) {
            // Create an Intent to launch ExampleActivity
            Intent intent = new Intent(this, WidgetExampleActivity.class);
            pendingIntent = PendingIntent.getActivity(this, 0,  intent, 0);
        }

        RemoteViews updateViews = new RemoteViews(getPackageName(), R.layout.widget1);

        // Set the Button Action
        updateViews.setOnClickPendingIntent(R.id.button, pendingIntent);

        // Update the text.
        updateViews.setTextViewText(R.id.widget1label, tweetTxt);
        appWidgetManager.updateAppWidget(appWidgetID, updateViews);

    }
}
```

现在我们已经设置了服务代码，只需更改我们的应用程序提供者类`ExampleAppWidgetProvider`来调用我们的服务，而不是像现在一样更新小部件文本。为了直接启动服务，调用`startService()`。打开 ExampleAppWidgetProvider，删除当前更新文本的代码，并替换为对此方法的调用:

```
public static void triggerUpdate(Context context) {
    // Start the service which will load the twitter data from the server.
    context.startService(new Intent(context, TwitterFetcherService.class));

}
```

### 配置小部件

我们现在已经有了从互联网加载数据的小部件，但是我们如何知道我们想要为哪个用户获取数据呢？许多小部件都需要配置，Android 提供了一种简单的方法来添加一个在小部件创建之前显示的配置活动。配置活动在小部件元数据文件中指定。在这种情况下，元数据文件位于`res/xml/widget1_info.xml`中。将其修改为包含一个`android:configure`属性。在我们进行更改后，文件应该如下所示:

```
<?xml version="1.0" encoding="utf-8"?>
    <appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android" 
    android:minWidth="294dp"
    android:minHeight="72dp"
    android:updatePeriodMillis="1800000"
    android:configure="com.eightbitcloud.example.widget.ConfigureActivity" 
    android:initialLayout="@layout/widget1"
    >
</appwidget-provider>
```

该活动是一个标准活动，可以像任何其他活动一样编写，但有两个例外。首先，`AndroidManifest.xml`文件中的活动规范应该包含一个针对`android.appwidget.action.APPWIDGET_CONFIGURE`活动的过滤器。

```
<activity android:name="com.eightbitcloud.example.widget.ConfigureActivity" android:label="@string/configure_title">
    <intent-filter>
        <action android:name="android.appwidget.action.APPWIDGET_CONFIGURE" />
    </intent-filter>
</activity>
```

确保这里的类名是完全限定的，因为小部件容器(home 应用程序)将会查找这个类。其次，当配置完成时，活动应该返回一个结果，以便小部件容器知道小部件配置已经完成。

```
public class ConfigureActivity extends Activity {
    /** Called when the activity is first created. */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.configure);
    }

    public void createClicked(View view) {
        String username = ((TextView)findViewById(R.id.configure_username)).getText().toString().trim();

        if (username == null || username.length() == 0) {
            Toast.makeText(this,  "Username is required", Toast.LENGTH_SHORT);
            return;
        }

        Intent intent = getIntent();
        Bundle extras = intent.getExtras();
        if (extras != null) {
            int mAppWidgetId = extras.getInt(AppWidgetManager.EXTRA_APPWIDGET_ID, AppWidgetManager.INVALID_APPWIDGET_ID);

            Log.d("ConfigureActivity", "App Widget ID is " + mAppWidgetId);

            // Save the username to application preferences
            Editor settings = getSharedPreferences(TwitterFetcherService.PREFS_FILE, MODE_PRIVATE).edit();
            settings.putString("twitterUser", username);
            settings.apply();

            // Kick off a refresh, as when you have a configure activity android does not do one automatically the first time.
            startService(new Intent(this, TwitterFetcherService.class));

            // Tell android that we're done
            Intent resultValue = new Intent();
            resultValue.putExtra(AppWidgetManager.EXTRA_APPWIDGET_ID, mAppWidgetId);
            setResult(RESULT_OK, resultValue);
            finish();
        }
    }
}
```

### 包裹

现在我们有了。在三篇文章之后，我们有了一个完全支持网络的、可配置的、经常更新的 Android 小部件。像这样设置的项目将会是几乎所有小部件的基础。我希望你从中有所收获。

本文涵盖了很多内容，所以我并没有完全展示让它运行所需的每一个变化。看看我的 [Android Examples GitHub 资源库](https://github.com/brucejcooper/Android-Examples/tree/networkFetch/WidgetExample)的`networkFetch`标签，获取完整的源代码。

## 分享这篇文章