# 一个 Android 网络库

> 原文：<https://www.sitepoint.com/volley-a-networking-library-for-android/>

*本文更新于 2016 年 12 月 21 日。具体来说:新条目，更新了不推荐使用的代码，并添加了新的图像加载部分。*

# 在 Android 中使用凌空联网

从移动技术诞生的第一天起，网络就已经存在，Android 也不例外。改变的是网络操作的处理方式。网络库(如凌空)的焦点是网络操作的自动化。

在 HoneyComb 之前，网络调用从主线程运行，而在后续的 Android 版本中，网络请求从主线程异步执行。为了进行网络调用，开发人员需要在与主应用程序线程不同的线程中实现一个`Asynctask`,否则就会抛出一个`NetworkOnMainThreadException`。

## 截击概述

凌空是一个 Android 的网络库，管理网络请求。它将您需要的最重要的特性捆绑在一起，比如访问 JSON APIs、在一个更易于使用的包中加载图像和字符串请求。

通过使用网络操作的凌空，你避免了处理网络的标准方式。另一个原因是不同步。凌空处理异步本身，没有必要手动创建`Asynctask`。

根据[文档](https://developer.android.com/training/volley/simple.html)，凌空执行的每个网络操作的核心都是`RequestQueue`。通过创建一个`RequestQueue`并传递给它`Request`对象来处理凌空。`RequestQueue`管理运行网络操作的工作线程，读取和写入缓存，并解析响应。

正如在文档中提到的，凌空提供了多种特性:

1.  网络请求的自动调度。
2.  多个并发网络连接。
3.  缓存。
4.  请求优先排序。
5.  取消正在进行的 API 请求。

这些是这个库提供的核心好处，也是本文的主要焦点。

## 导入凌空，添加权限

在开始使用凌空之前，您需要配置您的 Android 项目。
创建一个新的 Android 项目。打开`build.gradle(Module: app)`并添加以下依赖项。

```
dependencies {
    //...

    compile 'com.android.volley:volley:1.0.0'
}
```

在`AndroidManifest.xml`中添加互联网权限。

```
<uses-permission android:name="android.permission.INTERNET" />
```

## 标准网络请求

正如我之前提到的，由凌空执行的每个网络操作的核心是`RequestQueue`。对网络的每个请求都通过`Request`对象传递给`RequestQueue`。
有三种类型的请求，JSON 请求、图像请求和字符串请求。我将带着解释和代码示例逐一介绍这些类型。

现在，在`MainActivity`的`onCreate()`方法中，启动`RequestQueue`。

```
 RequestQueue requestQueue;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        //...
        requestQueue = Volley.newRequestQueue(this); // 'this' is the Context

    }
```

## JSON 请求

凌空有对`JSONObject`和`JSONArray`的请求。JSONRequest 的结构和包含在凌空中的大多数请求类使用如下的构造函数。

```
JsonObjectRequest request JsonObjectRequest(RequestMethod, URL, null,  new ResponseListener(), new ErrorListener());
```

传递给构造函数的参数:

*   `RequestMethod`(获取、发布、放置、删除等。)
*   `URL`:所需对象的 URL 的字符串
*   `JSONObject`:随请求发布的可选对象，如果没有发布对象，则为空
*   `ResponseListener`:响应监听器，其回调方法将包含响应
*   `ErrorListener`:一个`Response.ErrorListener`，其回调方法将包含请求的任何问题。

下面的代码片段分别是`JsonObjectRequest`和`JsonArrayRequest`的完整实现。

```
 /*Json Request*/
   String url = "https://json_url/";
   JsonObjectRequest jsonObjectRequest = new JsonObjectRequest(Request.Method.GET, url, null,
           new Response.Listener<JSONObject>() {
               @Override
               public void onResponse(JSONObject response) {

               }
           },
           new Response.ErrorListener() {
               @Override
               public void onErrorResponse(VolleyError error) {

               }
           });
   //add request to queue
   requestQueue.add(jsonObjectRequest);

   JsonArrayRequest jsonArrayRequest = new JsonArrayRequest(Request.Method.GET, url, null,
           new Response.Listener<JSONArray>() {
               @Override
               public void onResponse(JSONArray response) {

               }
           },
           new Response.ErrorListener() {
               @Override
               public void onErrorResponse(VolleyError error) {

               }
           });
   //add request to queue
   requestQueue.add(jsonArrayRequest);
```

现在我已经展示了如何在 Android 中接收 JSON 数据，接下来让我们看看如何用 Volley 发布数据。

请求的格式与接收 JSON 数据的格式相同，只是您必须指定一个与请求和请求头一起发布的`JSONObject`,以便将数据从客户端发送到服务器端。

```
 /*Post data*/
    Map<String, String> jsonParams = new HashMap<String, String>();

jsonParams.put("email", "user@gmail.com");
jsonParams.put("username", "user");
jsonParams.put("password", "pass");

    JsonObjectRequest postRequest = new JsonObjectRequest( Request.Method.POST, URL,

          new JSONObject(jsonParams),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                  //   Handle Error
                }
            }) {
        @Override
        public Map<String, String> getHeaders() throws AuthFailureError {
            HashMap<String, String> headers = new HashMap<String, String>();
            headers.put("Content-Type", "application/json; charset=utf-8");
            headers.put("User-agent", System.getProperty("http.agent"));
            return headers;
        }
    };
requestQueue.add(postRequest);
```

请注意，请求头可能必须根据开发用例进行更改。

## 字符串请求

这种类型的请求用于以字符串形式从服务器检索数据。

```
 /*String Request*/
   String url = "https://string_url/";
   StringRequest stringRequest = new StringRequest(Request.Method.GET, url,
           new Response.Listener<String>() {
               @Override
               public void onResponse(String response) {

               }
           },
           new Response.ErrorListener() {
               @Override
               public void onErrorResponse(VolleyError error) {

               }
           });
   //add request to queue
   requestQueue.add(stringRequest);
```

`StringRequest`的结构包含一个请求方法(POST 或 GET)、一个指向字符串源的 URL、`Response.Listener`和一个`Response.ErrorListener`。和`JsonRequest`差不多。

## 图像请求

凌空提供了几种在 Android 中加载图像的方法。我将逐一解释它们的不同之处，并提供一些代码示例。

### `ImageRequest`

`ImageRequest`是一种加载图像的方法，用于在给定的 URL 获取图像。这个网络调用的结构类似于前面提到的其他请求。
下面的代码是一个`ImageRequest`将图像加载到`ImageView`的例子。

```
 int maxWidth = ...;
   int maxHeight = ...;
   String URL = "http://image_url.png";
   ImageRequest imageRequest = new ImageRequest(URL, new Response.Listener<Bitmap>() {
       @Override
       public void onResponse(Bitmap response) {
           // Assign the response to an ImageView
           ImageView imageView = (ImageView) findViewById(R.id.imageView);
           imageView.setImageBitmap(response);
       }
   }, maxWidth, maxHeight, null);
   //add request to queue
   requestQueue.add(imageRequest);
```

`ImageRequest`的结构很简单。它需要图像的 URL，一个响应监听器，其回调方法将包含图像位图作为响应，一个`maxWidth`和`maxHeight`，一个将位图解码成的配置格式，以及一个用于任何响应错误的`Response.ErrorListener`。将请求添加到`RequestQueue`中，Android 运行时将处理剩下的部分。

### `ImageLoader`

是一个助手类，处理从远程 URL 加载和缓存图像。这个请求对大量的`ImageRequest`有用。根据[文档](https://developer.android.com/training/volley/request.html#request-image) `ImageLoader`提供了一个内存缓存坐在正常的凌空缓存前面，这对防止闪烁很重要。

### `NetworkImageView`

从网络加载图像时，`NetworkImageView`是替代`ImageView`的有效方式。

下面的代码片段提供了用凌空摄影加载图像的最后两个部分的实现。下面的`VolleySingleton`类是一个`RequestQueue`的实现。这个单例类具有内存 LRU 缓存的缓存功能。`LruCache`设置为存储多达 30 个元素。

```
import android.content.Context;
import android.graphics.Bitmap;
import android.support.v4.util.LruCache;
import android.util.Log;

import com.android.volley.RequestQueue;
import com.android.volley.toolbox.ImageLoader;
import com.android.volley.toolbox.Volley;

public class VolleySingleton {
    private static VolleySingleton singletonInstance = null;
    private RequestQueue requestQueue;
    private ImageLoader imageLoader;

    private VolleySingleton(Context context) {
        requestQueue = Volley.newRequestQueue(context);
        imageLoader = new ImageLoader(this.requestQueue, new ImageLoader.ImageCache() {
            private final LruCache<String, Bitmap> lruCache = new LruCache<String, Bitmap>(30);
            //30 -> the maximum number of entries in the cache.

            public void putBitmap(String url, Bitmap bitmap) {
                lruCache.put(url, bitmap);
                Log.d("CachedItems", String.valueOf(lruCache.size()));
            }

            public Bitmap getBitmap(String url) {
                return lruCache.get(url);
            }
        });
    }

    public static VolleySingleton getInstance(Context context) {
        if (singletonInstance == null) {
            singletonInstance = new VolleySingleton(context);
        }
        return singletonInstance;
    }

    public RequestQueue getRequestQueue() {
        return this.requestQueue;
    }

    public ImageLoader getImageLoader() {
        return this.imageLoader;
    }
}
```

既然已经设置了`VolleySingleton`类，让我们将一个图像加载到`NetworkImageView`中。但是首先，在活动的布局文件中添加一个实例。这里有一个例子:

```
 <com.android.volley.toolbox.NetworkImageView
        android:id="@+id/networkImageView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
```

最后，将一幅图像加载到`NetworkImageView`中。在`MainActivity`的`onCreate()`方法中添加这段代码。

```
 private ImageLoader imageLoader;
    private NetworkImageView imageView;
    private static final String IMAGE_URL = "https://the_images_url.jpg";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        //...

        imageLoader = VolleySingleton.getInstance(getApplicationContext()).getImageLoader();
        imageView = (NetworkImageView) findViewById(R.id.networkImageView);
        imageView.setImageUrl(IMAGE_URL, imageLoader);

    }
```

为了确保缓存正常工作，我在`VolleySingleton`类中添加了一个`Log`语句。在 Android Studio 中运行项目并检查日志。每次缓存新图像时，设备都会打印出大量缓存的项目。

## 请求优先级

处理网络请求意味着优先考虑需要什么和需要多快响应，因为联网是实时操作。

凌空处理请求从高优先级到低优先级，先进先出的顺序。没有`setPriority()`方法，所以要区分请求的优先级，需要做一些工作。

下面是一个定制的`JsonObjectRequest`类的例子，它可以发出优先级请求。这个类的焦点是被覆盖的`getPriority()`方法。当请求被添加到队列中时，这个方法将返回由`setPriority()`分配给它的优先级，或者将返回在类内分配的`Priority.HIGH`，但是它不会搜索这个类之外的优先级。

相同的结构适用于所有其他请求类型。

```
public class CustomPriorityRequest extends JsonObjectRequest {

    Priority priority = Priority.HIGH;

    public CustomPriorityRequest(int method, String url, JSONObject jsonRequest, Response.Listener<JSONObject> listener, Response.ErrorListener errorListener) {
        super(method, url, jsonRequest, listener, errorListener);
    }

    public CustomPriorityRequest(String url, JSONObject jsonRequest, Response.Listener<JSONObject> listener, Response.ErrorListener errorListener) {
        super(url, jsonRequest, listener, errorListener);
    }

    @Override
    public Priority getPriority() {
        return priority;
    }

    public void setPriority(Priority p){
        priority = p;
    }
}
```

用这个新类发出请求与标准请求是一样的。您可以在将请求添加到队列之前设置其优先级。

```
 CustomPriorityRequest customPriorityRequest = new CustomPriorityRequest(
           Request.Method.GET, URL, null,
           new Response.Listener<JSONObject>(),
           new Response.ErrorListener());

   customPriorityRequest.setPriority(Request.Priority.IMMEDIATE);
   requestQueue.add(customPriorityRequest);
```

一些可用的优先级是:优先级。低，优先级。正常，优先。高优先级，立即。

## 取消请求

另一个有用的工具是取消请求的能力。当用户关闭应用程序或执行导致不使用截击请求响应的操作时，取消请求是有用的。执行队列中剩余的请求是没有意义的，所以我们在`onStop()`方法中取消它们。

在凌空取消请求的最简单的方法是给请求添加一个标记，当需要取消排队的请求时，调用`cancelAll()`方法。这将取消用该特定标记指定的所有请求。

```
request.setTag("TAG");

requestQueue.cancelAll("TAG");
```

一旦`cancelAll()`从主线程执行，剩余的响应将不会被提交。

## 分享这篇文章