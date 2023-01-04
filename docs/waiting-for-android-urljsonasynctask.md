# 等待 Android: UrlJsonAsyncTask

> 原文：<https://www.sitepoint.com/waiting-for-android-urljsonasynctask/>

## 等待 Android: UrlJsonAsyncTask

*   本文源代码:GitHub 上的 UrlJsonAsyncTaskTest
*   下载安装安卓应用: [UrlJsonAsyncTaskTest.apk](http://savagelook.com/apk/UrlJsonAsyncTaskTest.apk) (安卓 2.2 以上)
*   您也可以扫描此二维码，将其下载到您的 Android 手机:

[![UrlJsonAsyncTaskTest QR Code](img/fe3cec566220c4ebd0c1dea15b228e55.png)](https://www.sitepoint.com/wp-content/uploads/2011/07/urljsonasynctasktest.png)

### 概观

我甚至数不清有多少次不得不在 Android 中加载一个 [ProgressDialog](http://developer.android.com/guide/topics/ui/dialogs.html#ProgressDialog) ，从远程 URL 查询 JSON，然后在查询完成后将控制权交还给应用程序。这是一个非常常见的控制流程，我讨厌一遍又一遍地重复。我相信我不是一个人。

为了保持这种[干燥](http://en.wikipedia.org/wiki/Don't_repeat_yourself)的情况，我的 Android 助手集合[com . nature look . Android](https://GitHub.com/tonylukasavage/com.savagelook.android)中的类 [UrlJsonAsyncTask](https://GitHub.com/tonylukasavage/com.savagelook.android/blob/master/com/savagelook/android/UrlJsonAsyncTask.java) 致力于上述任务。它将每个任务 80 多行代码减少到大约 15 行或更少。

在深入研究之前，有几件事需要考虑。

*   这不是一个基础教程，所以一些 Android 知识是假定的。特别是，你应该理解 Android 是如何使用意图、活动和基本事件处理的。
*   虽然没有必要，但如果你至少熟悉 Android 的 [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) 类，你会发现这一切都更容易掌握。
*   我在`UrlJsonAsyncTask`中使用了其他几个你可能不认识的类，比如 [JsonHelper](https://GitHub.com/tonylukasavage/com.savagelook.android/blob/master/com/savagelook/android/JsonHelper.java) 和[懒惰。Ex](https://GitHub.com/tonylukasavage/com.savagelook.android/blob/master/com/savagelook/android/Lazy.java) 。所有这些都是开源的，可以在 GitHub 上免费获得。
*   我不想深入了解`UrlJsonAsyncTask`是如何工作的，只想知道如何使用它。如果你想让我跟进，请在评论中发表你的意见。

### UrlJsonAsyncTaskTest

了解`UrlJsonAsyncTask`如何加速你的发展的最好方法是看它的行动。您可以跟随下面的代码片段，或者您可以下载我所基于的完整项目源代码。该项目名为 [UrlJsonAsyncTaskTest](https://GitHub.com/tonylukasavage/com.buildmobile.UrlJsonAyncTaskTest) ，可以从 GitHub 免费下载或克隆。

该应用程序是故意尽可能裸机。我们只需在主活动上创建一个按钮，该按钮将启动一个`UrlJsonAsyncTask`，然后在一个新活动中返回其查询结果。

### 在我们进入代码之前…

UrlJsonAsyncTask 只做了一个“约定优于配置”的假设。为了在幕后处理所有的错误处理和超时特性，您的 JSON 需要采用以下格式:

```
{
    "success": true|false,
    "info": "an error message to present to the user when success is false",
    "data": YOUR_DATA
}
```

*   `success`是一个布尔值，表示在服务器端查询是否成功。
*   向用户提供一个描述性的，但经过净化的错误描述。
*   `data`是您希望从查询中返回的实际数据。`YOUR_DATA`可以是任何值，甚至是另一个 JSON 对象或数组。

### (无聊的)代码

好了，让我们先把无聊的东西拿出来。如果你懒得自己下载/克隆它，下面是你需要对一个基本的 Android 项目进行的修改，使它看起来像 [UrlJsonAsyncTaskTest](https://GitHub.com/tonylukasavage/com.buildmobile.UrlJsonAyncTaskTest) 。

**AndroidManifest.xml**

你需要添加 2 样东西。首先，通过在 manifest 元素下添加一个`uses-permission`子句，允许 Android 与互联网对话。

```
<uses-permission android:name="android.permission.INTERNET" />
```

其次，您需要在`application`元素下添加我们将在成功调用`UrlJsonAsyncTask`后打开的第二个活动。它将被恰当地命名为`SecondActivity`。

```
<activity android:name=".SecondActivity"/>
```

**res/layout/main.xml**

这是我们将应用于`MainActivity`的布局 XML。这里的按钮是我们将用来启动`UrlJsonAsyncTask`的按钮。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/mainButton"
        android:layout_height="wrap_content"
        android:layout_width="wrap_content"
        android:text="Get Remote JSON"/>
</LinearLayout>
```

**RES/layout/sec . XML**

这是`SecondActivity`的布局 XML。这里的 TextView 是我们放置从我们的`UrlJsonAsyncTask`调用返回的 JSON 字符串的地方。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <TextView
        android:id="@+id/jsonText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
</LinearLayout>
```

### (有趣的)代码

现在是时候实际扩展、创建实例并调用我们的`UrlJsonAsyncTask`。我们将用几行代码在`MainActivity`中完成所有这些。如果您对整个 UrlJsonAsyncTaskTest 项目示例不感兴趣，只想知道如何在您自己的项目中使用`UrlJsonAsyncTask`，*这是您需要关注*的部分。

**MainActivity.java**

我们需要做的第一件事是创建我们自己的扩展`UrlJsonAsyncTask`的类。这将允许我们利用内置功能，同时让我们能够自由决定如何处理生成的 JSON。我们作为`MainActivity`的`private class`成员这样做。阅读每个部分的详细信息的评论。

```
private class MyTask extends UrlJsonAsyncTask {
    // Create a default constructor
    public MyTask(Context context) {
        super(context);
    }

    // onPostExecute(JSONObject) is what is called when the UrlJsonAsyncTask completes.
    // The JSONObject represents the JSON returned by the remote URL we queried.
    @Override
    protected void onPostExecute(JSONObject json) {
        try {
            // call validateJson() to ensure well formatted JSON
            this.validateJson(json);

            // If the JSON is returned successfully and well formatted, send the
            // JSON "data" as String Extra "json" to SecondActivity
            Intent intent = new Intent(context, SecondActivity.class);
            intent.putExtra("json", json.getString("data").toString());
            startActivity(intent);
        } catch (JSONException e) {
            // If there were exceptions handling the JSON...
            Toast.makeText(context, this.getMessageError(), Toast.LENGTH_SHORT).show();
        } catch (Exception e) {
            // All other uncaught exceptions, like HTTP or IO problems...
            Toast.makeText(context, e.getMessage(), Toast.LENGTH_SHORT).show();
        } finally {
            // You MUST call this or your UrlJsonAsyncTask's ProgressDialog will
            // not close.
            super.onPostExecute(json);
        }
    }
}
```

既然我们已经创建了`MyTask`，我们所要做的就是实例化它，给它一个(远程)URL，让底层的`UrlJsonAsyncTask`完成剩下的工作。为了使它具有交互性，我们将在`MainActivity`的 onCreate()函数中的`mainButton`按钮的点击处理程序中完成所有这些。

```
@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.main);

    Button button = (Button)findViewById(R.id.mainButton);
    button.setOnClickListener(new OnClickListener() {
        @Override
        public void onClick(View view) {
            // The URL that'll we'll test against
            String remoteJsonUrl = "http://savagelook.com/misc/UrlJsonAsyncTaskTest.php";

            // assign a context and loading message
            MyTask task = new MyTask(MainActivity.this);
            task.setMessageLoading("Getting remote JSON...");

            // Launch the UrlJsonAsyncTask with the test URL
            task.execute(remoteJsonUrl);
        }
    });
}
```

如果一切顺利，这个任务一从`http://savagelook.com/misc/UrlJsonAsyncTaskTest.php`返回请求的 JSON，`MyTask.onPostExecute()`就会被调用。

**SecondActivity.java**

为了确保一切按计划进行，我们将在`SecondActivity`中显示 JSON“data”字符串。为此，我们将下面几行代码添加到`SecondActivity.onCreate()`中。

```
@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.second);

    // Get the JSON string we assigned to the intent in MyTask.onPostExecute()
    String jsonString = this.getIntent().getStringExtra("json");

    // assign the JSON string to the TextView
    TextView textView = (TextView)findViewById(R.id.jsonText);
    textView.setText(jsonString);
}
```

仅此而已。现在应该有一个 UrlJsonAsyncTask 成功地连接到一个远程 URL 并请求 JSON，同时有一个 ProgressDialog 让用户知道正在发生什么。正如我前面所说的，这是一个非常普通的任务，现在它是一个非常简单的任务。

### 摘要

最终，我们在这里取得的成就并不是火箭科学。我们所做的只是为依赖远程数据的应用程序创建一个典型的控制流。我的目标是在不牺牲太多灵活性的情况下，使这个流程尽可能简单。

我强烈建议你查看一下 GitHub 仓库中我所有的[com . nature look . Android](https://GitHub.com/tonylukasavage/com.savagelook.android)代码。打开引擎盖，看看`UrlJsonAsyncTask`到底是怎么做的。尝试该类的一些其他属性，如超时和重试。看看其他的助手类。此外，请随意克隆和反馈。

希望这将帮助你在控制流程上花费更少的时间，而在你的 Android 应用提供的独特功能上花费更多的时间。

## 分享这篇文章