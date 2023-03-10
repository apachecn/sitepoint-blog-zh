# 让我们来谈谈:PHP 和 Android 的高效通信，第 1 部分

> 原文：<https://www.sitepoint.com/lets-talk-1/>

移动领域在过去几年中出现了爆炸式增长，促使人们意识到面向服务的体系结构是实现 web 和移动应用程序消费的业务逻辑的一种很好的方式。虽然移动设备已经发生了巨大的变化，但它们对消费者越来越多的可用性增加了网络和服务提供商的压力，他们仍然在努力使容量满足需求。为了降低硬件成本，开发人员现在必须节省带宽，就像他们在计算早期对内存所做的那样。

这篇由两部分组成的文章将指导您构建一个高效的基于 PHP 的 REST web 服务，供基于 Android 的应用程序使用。这里介绍的一些概念也适用于其他移动平台，比如 iOS。我假设您已经了解 PHP 和 Android 开发的基础，并且已经为这两者设置了合适的开发环境。我将主要向您展示如何在这两种环境中处理数据序列化和压缩。

## 共同的要求

下面是我们感兴趣的典型 HTTP 操作的特定部分:

1.  客户端(例如 Android 应用程序)向 REST 服务(例如服务器)发送 HTTP 请求，并使用请求头来指示它支持哪些数据序列化和压缩格式。
2.  根据请求头，服务器确定它与客户机共有的数据序列化和压缩格式，选择其中一种格式，将它们应用于请求的数据，并将包含指定其选择和数据的头的响应发送回客户机。
3.  基于响应头，客户端对数据应用相应的解压缩和反序列化例程，以将其恢复到原始状态，然后可以将它用于预期目的。

让我们从头开始，经历每一步。

## 请求数据

为了发出 HTTP 请求，你的 Android 应用程序[需要许可](http://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)来访问互联网。您需要在项目的`AndroidManifest.xml`文件中声明如下:

```
<?xml version="1.0" encoding="utf-8"?>
<manifest ...>
 <uses-permission android:name="android.permission.INTERNET" />
</manifest>
```

之后，您需要一个实际处理 HTTP 请求和处理响应的类。为此，我将使用原生的`AndroidHttpClient`类，它带有一个默认配置，适用于大多数目的，包括使用线程安全的连接管理器。

`AndroidHttpClient`类只有 Android 2.2(API 8 级)及以上版本才有。为了支持[旧版本](http://developer.android.com/resources/dashboard/platform-versions.html)，看看来自 [Apache Harmony 库](http://harmony.apache.org/)的`[DefaultHttpClient](http://developer.android.com/reference/org/apache/http/impl/client/DefaultHttpClient.html)`类，Android 从第一个版本开始就包含了这个类。

```
import android.net.http.AndroidHttpClient;
import java.io.IOException;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpGet;

public class DataModel {
    protected AndroidHttpClient httpClient;
    protected HttpGet httpRequest;

    public DataModel() {
        this.httpClient = AndroidHttpClient.newInstance(
            "Android " + android.os.Build.VERSION.RELEASE
            // Your app name would also be an acceptable value here
        );
    }

    public DataValueObject getData() throws IOException {
        HttpGet httpRequest = new HttpGet(
            "http://10.0.2.2/php-android/");
        httpRequest.addHeader("Accept", 
            "application/json;q=1,application/x-msgpack;q=0.9");
        httpRequest.addHeader("Accept-Encoding", 
            "bzip2,gzip,deflate");
        HttpResponse httpResponse = this.httpClient.execute(httpRequest);
        // ...
    }
}
```

当这个类被实例化时，它在内部创建一个`AndroidHttpClient`的实例。当调用`getData()`方法请求数据时，它创建一个`HttpGet` 的[实例，用请求数据填充它，并使用`AndroidHttpClient`实例发送它。请求数据中包括以下内容:](http://developer.android.com/reference/org/apache/http/client/methods/HttpGet.html)

1.  被请求的资源的 URL(即您的 REST 端点)。如果您在运行 Android 开发环境的同一台机器上运行它，您可以使用 IP 地址 10.0.2.2 从仿真器访问它(如上例所示),也可以使用 IP 地址 10.0.1.2 从通过 USB 连接的物理设备访问它。
2.  客户端支持的数据序列化格式，在这种情况下是 [JSON](http://json.org/) 和 [MessagePack](http://msgpack.org/) ，以及它们各自的质量因子，0 和 1 之间的浮点数，数字越大表示对该特定格式的偏好越高。关于这些的更多信息可以在 [RFC 2616 第 12 节中找到。](https://www.w3.org/Protocols/rfc2616/rfc2616-sec12.html)
3.  客户端支持的数据编码格式，本例中为 [bzip2](http://en.wikipedia.org/wiki/Bzip2) 、 [gzip](http://en.wikipedia.org/wiki/Gzip) 和 [deflate](http://en.wikipedia.org/wiki/Deflate) 。关于这些的更多信息可以在 [RFC 2616 第 3.5 节](https://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.5)中找到。

`AndroidHttpClient`实例可能会遇到问题，比如服务器不可用。它的`execute()`方法可以抛出一个`IOException`来表明这一点，调用`getData()`的代码可以捕捉并适当地处理它。

当我们检查处理响应时，我们将在后面的部分中回到其余的`getData()`方法，但是现在我们需要在一个 [Android 活动](http://developer.android.com/guide/topics/fundamentals/activities.html)中实现这个类的使用。

## 实现后台任务

在移动应用程序的上下文中请求和处理数据是一种异步操作。也就是说，我们希望应用程序发送对数据的请求，然后在收到响应后采取一些行动，比如用数据填充用户界面。

默认情况下，Android [在其自己的](http://developer.android.com/guide/topics/fundamentals/processes-and-threads.html#Threads)[进程](http://en.wikipedia.org/wiki/Process_(computer_science))和[线程](http://en.wikipedia.org/wiki/Thread_(computer_science))中包含每个单独的应用，后者通常被称为 UI 线程，因为用户界面操作在其上运行。因此，像从 web 服务获取和处理数据这样的密集型操作应该在单独的线程上运行，以避免阻塞 UI 操作，这通常会导致用户恼怒。为此，我们需要编写一个`[AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html)`的子类来封装流程。

```
import java.io.IOException;
import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.DialogInterface;
import android.os.AsyncTask;

public class GetDataTask extends AsyncTask<Void, Void, DataValueObject> {
    ProgressDialog progressDialog;
    Activity activity;
    String error;

    public GetDataTask(Activity activity) {
        super();
        this.activity = activity;
    }

    protected String getString(int id) {
        return this.activity.getResources().getString(id);
    }

    @Override
    protected void onPreExecute() {
        progressDialog = ProgressDialog.show(
            this.activity,
            "", 
            getString(R.string.loading),
            true,
            false
        );
    }

    @Override
    protected DataValueObject doInBackground(Void... params) {
        DataModel dataModel = new DataModel();
        DataValueObject dvo = null;
        try {
            dvo = dataModel.getData();
        } catch (IOException e) {
            this.error = getString(R.string.error); // or e.getMessage() when debugging
        }
        return dvo;
    }

    @Override
    protected void onPostExecute(DataValueObject dvo) {
        if (dvo != null) {
            // Do something useful with dvo here

            // Dismiss the progress dialog when done
            progressDialog.dismiss();
        } else {
            // Dismiss the progress dialog
            progressDialog.dismiss();

            // Display a simple error dialog to the user
            new AlertDialog.Builder(this.activity)
                .setMessage(this.error)
                .setNeutralButton(
                    getString(R.string.ok),
                    new DialogInterface.OnClickListener() {
                        public void onClick(DialogInterface dialog, int id) {
                            dialog.cancel();
                        }
                    }
                )
                .create()
                .show();
        }
    }
}
```

作为扩展`AsyncTask`的一部分，这个类指定操作的结果将是`DataModel`类的`getData()`方法返回的`DataValueObject`类的一个实例。类构造函数将调用它的活动实例作为唯一的参数。稍后将使用它来访问本地化字符串，并在其他特定于上下文的任务中使用该活动。

`onPreExecute()`和`onPostExecute()`方法将根据您在后台操作开始之前和之后想要做的事情而有所不同。我上面的例子展示了一个简单的用例，显示了一个进度对话框，当错误发生时，还可能显示一个警告对话框。

`doInBackground()`包含需要在后台线程中运行的逻辑，在本例中称为`getData()`。如果出现错误，它将返回一个`DataValueObject`实例或 null。该值在被调用时被传递给`onPostExecute()`，这样您就可以用它做一些有用的事情。

## 执行后台任务

既然我们已经正确地封装了要运行以获取数据的后台任务，我们需要在活动中实际调用它。其中一部分需要检查网络连接是否可用，以便数据请求可以实际到达服务器。这样做需要向您的`AndroidManifest.xml`文件添加一个额外的权限:

```
<?xml version="1.0" encoding="utf-8"?>
<manifest ...>
  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
</manifest>
```

创建活动时启动后台任务的活动类可能如下所示:

```
import android.app.Activity;
import android.content.Context;
import android.net.ConnectivityManager;
import android.net.NetworkInfo;
import android.os.Bundle;

public class MyActivity extends Activity {
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        if (networkIsAvailable()) {
            new GetDataTask(this).execute();
        } else {
            // Display an error to the user about network unavailability
        }
    }

    public boolean networkIsAvailable() {
        ConnectivityManager cm = (ConnectivityManager) 
            getSystemService(Context.CONNECTIVITY_SERVICE);
        NetworkInfo networkInfo = cm.getActiveNetworkInfo();
        return networkInfo != null && networkInfo.isConnected();
    }
}
```

`networkIsAvailable()`方法简单地查询适当的服务来确定网络连接是否可用。如果您的应用程序有一个基本的 activity 类，这可能是一个有用的方法。`onCreate()`活动挂钩使用`networkIsAvailable()`来执行后台任务或者向用户显示适当的错误消息。执行任务只是用对活动的引用实例化它并调用它的`execute()`方法。

## 下次

本文的第 1 部分主要关注设置 Android 应用程序来发出请求。在本文接下来的第 2 部分中，我们将深入探讨在这两种环境中处理数据序列化和压缩的问题。敬请期待！

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章