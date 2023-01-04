# OkHttp，来自 Square 的 Android HTTP 客户端

> 原文：<https://www.sitepoint.com/consuming-web-apis-in-android-with-okhttp/>

大多数应用程序需要通过网络连接到外部服务来访问和交换数据。这通常是通过应用程序中的 REST APIs 和 HTTP 客户端实现的。 [OKHttp](http://square.github.io/okhttp/) 是来自 Square 的 Android HTTP 客户端库，它减少了所需的步骤，意味着你可以将更多的时间花在应用程序的重要领域。

## 为什么是 OkHttp？

这是一个简单易用的库，加载数据更快，节省带宽。它消除了网络测试的需要，从常见的连接问题中恢复，并且在连接失败时，OkHttp 可以使用不同的路由重试请求。

OkHttp 支持 Android 2.3 及以上版本，根据目前[版本使用情况统计](https://developer.android.com/about/dashboards/index.html)超过 99%的市场。

## 在你的应用中使用 OkHttp

OkHttp 最近更新到了 3.0 版本，目前是 3.3.1 版本。我提到这一点是因为 OkHttp 在这个版本中做了关键的更新，以前版本中的方法已经过时。

你可以在 [GitHub](https://github.com/sitepoint-editors/OkHttp-Basic-Networking) 上找到我将要介绍的所有例子。

## 添加依赖关系

打开 *build.gradle(Module: app)* 添加以下依赖项，或者查看 [OkHttp 站点](http://square.github.io/okhttp/#download)获取最新更新。

```
dependencies {
    //...
    compile 'com.squareup.okhttp3:okhttp:3.3.1'
} 
```

在 *AndroidManifest.xml* 中添加互联网权限。

```
<uses-permission android:name="android.permission.INTERNET"/> 
```

## 与 OkHttp 联网

这篇文章的主要重点是构建下面的网络调用。

*   得到
*   邮政
*   用 OkHttp 上传文件
*   监控请求和响应进度
*   用 OkHttp 处理 cookies

**注意**:我尽量不写多余的代码，而是[干](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)代码。我觉得我已经尽了最大努力来优化我的代码如何处理与 OkHttp 的联网。这包括将代码分成两类。`ApiCall`处理 POST 和 GET 调用，`RequestBuilder`构造请求 URL 和`RequestBody`

## 建立工作关系网

是时候创建一些 [API 调用](https://en.wikipedia.org/wiki/Representational_state_transfer#Applied_to_web_services)了，特别是 GET 和 POST 网络请求。创建下面的 java 类来处理每个请求。

```
public class ApiCall {

    //GET network request
    public static String GET(OkHttpClient client, HttpUrl url) throws IOException {
        Request request = new Request.Builder()
                .url(url)
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    //POST network request
    public static String POST(OkHttpClient client, HttpUrl url, RequestBody body) throws IOException {
        Request request = new Request.Builder()
                .url(url)
                .post(body)
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }
} 
```

在`ApiCall`类的两个方法中都有一个`OkHttpClient`的实例，这意味着您必须在每次网络调用时传递一个客户端实例。你可以构造不同的方法，以便每次都构建一个新的客户端，但这不是一个聪明的解决方案，我建议你阅读这个 [StackOverflow 回答](http://stackoverflow.com/questions/25853019/android-correct-way-to-use-okhttp-singleton-for-parallel-queries-with-cookies#28388662)，以证明这是一个更好的解决方案。

在这两种方法中，`HttpUrl url`参数代表请求的 URL。它被表示为一个`HttpUrl`的实例，但也可以是一个`String`:

```
public static String POST(OkHttpClient client, String url, RequestBody body) throws IOException {
    //...
} 
```

您可以在这两种方法中应用它，并且它具有与`HttpUrl`实例相同的结果。要提到的一个重要事实是，URL 字符串的结构可能不正确，这可能会导致[malformedurexception](https://developer.android.com/reference/java/net/MalformedURLException.html)。

最后要提的是`POST`方法中的`RequestBody`。它代表一个参数集合，以名称/值对的形式在`POST`请求的主体中发送。

## 构建请求 URL

接下来，您需要创建一个 Java 类来构造网络请求的 URL 和`POST`方法的`RequestBody`。

```
public class RequestBuilder {

    //Login request body
    public static RequestBody LoginBody(String username, String password, String token) {
        return new FormBody.Builder()
                .add("action", "login")
                .add("format", "json")
                .add("username", username)
                .add("password", password)
                .add("logintoken", token)
                .build();
    }

    public static HttpUrl buildURL() {
        return new HttpUrl.Builder()
                .scheme("https") //http
                .host("www.somehostname.com")
                .addPathSegment("pathSegment")//adds "/pathSegment" at the end of hostname
                .addQueryParameter("param1", "value1") //add query parameters to the URL
                .addEncodedQueryParameter("encodedName", "encodedValue")//add encoded query parameters to the URL
                .build();
        /**
         * The return URL:
         *  https://www.somehostname.com/pathSegment?param1=value1&encodedName=encodedValue
         */
    }

} 
```

java 类`RequestBuilder`包含为 POST 调用和`HttpUrl`实例构建`RequestBody`的例子。这些例子包括为一些假定的登录动作构建一个`RequestBody`和一个`HttpUrl`，通常用于 GET 请求。

## 回到活动

到目前为止，这些步骤是为从活动中处理网络和利用创建的类做准备。首先，在一个活动类中创建一个全局`OkHttpClient`。

```
public class MainActivity extends AppCompatActivity {

    private OkHttpClient client;
    //...
} 
```

用`onCreate()`方法初始化客户端。

```
 @Override
    protected void onCreate(Bundle savedInstanceState) {
        //...
        client = new OkHttpClient();
    //...
} 
```

您必须异步处理 Android 中的网络调用，否则当应用程序试图在主线程上执行网络操作时，会抛出一个[NetworkOnMainThreadException](https://developer.android.com/reference/android/os/NetworkOnMainThreadException.html)。为了避免这种异常，使用 [`AsyncTask`](https://developer.android.com/reference/android/os/AsyncTask.html) 。

将这些方法添加到您的活动中:

```
private void loadContent() {
    new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            try {
                response = ApiCall.GET(client, RequestBuilder.buildURL());
                //Parse the response string here
                Log.d("Response", response);
            } catch (IOException e) {
                e.printStackTrace();
            }
            return null;
        }
    }.execute();
}

private void attemptLogin(String url) {
    new AsyncTask<String, Void, Void>() {
        @Override
        protected Void doInBackground(String... params) {

            try {
                response = ApiCall.POST(
                        client,
                        params[0],
                        RequestBuilder.LoginBody("username", "password","token"));

                Log.d("Response", response);
            } catch (IOException e) {
                e.printStackTrace();
            }
            return null;
        }
    }.execute(url);
} 
```

这里显示的两个函数代表了`RequestBuilder`类中的两个例子。它们只是为了演示的目的，你可以用不同的方式构造它们。

抛开`AsyncTask`的实现不谈，请注意处理网络的代码。在前面两个类中实现的所有代码都被构造在一行中，它返回网络请求的响应体，没有多余的代码。相当酷！

```
ApiCall.GET(client, RequestBuilder.buildURL()); 
```

检索到响应后，可能需要进行一些解析，因为 API 通常会返回`JSON`或`XML`提要。

## 上传文件

在这一节中，我将介绍用`OkHttp`上传文件。需要的一种常见上传类型是`multipart/form-data`。下面的图片上传例子说明了 OkHttp 如何处理这个问题，但是对于其他不同的文件类型也是有效的。

```
//Upload request body
public static MultipartBody uploadRequestBody(String title, String imageFormat, String token, File file) {

    MediaType MEDIA_TYPE = MediaType.parse("image/" + imageFormat); // e.g. "image/png"
    return new MultipartBody.Builder()
            .setType(MultipartBody.FORM)
            .addFormDataPart("action", "upload")
            .addFormDataPart("format", "json")
            .addFormDataPart("filename", title + "." + imageFormat) //e.g. title.png --> imageFormat = png
            .addFormDataPart("file", "...", RequestBody.create(MEDIA_TYPE, file))
            .addFormDataPart("token", token)
            .build();
} 
```

这个函数返回一个从`RequestBody`派生的`MultipartBody`对象，所以把这个方法放在`RequestBuilder`类中是个好主意。

既然上传请求的主体已经完成，那么如何运行上传调用呢？上传是对 API 的`POST`调用，因此与前面的调用相同。

```
 File file = new File(""); //provide a valid file
 ApiCall.POST(client, url, RequestBuilder.uploadRequestBody("title", "png", "someUploadToken", file)); 
```

记得异步调用它。该调用返回上传操作的验证，无论成功与否。

## 下一步是什么

以上是 OkHttp 的基础知识，但接下来我将提到一些可能对 OkHttp 有用的技巧和工具。

### 监控请求进度

上传媒体时，监控进度的能力通常很有用。不久前，我找到了解决这个问题的方法。为了用这个解决方案监控请求的进度，您创建了一个`RequestBody`的实例，然后将它作为一个参数传递到类的构造函数中。

```
//Monitored request
File file = new File("");
MultipartBody body = RequestBuilder.uploadRequestBody("title", "png", "someUploadToken", file);

CountingRequestBody monitoredRequest = new CountingRequestBody(body, new CountingRequestBody.Listener() {
    @Override
    public void onRequestProgress(long bytesWritten, long contentLength) {
        //Update a progress bar with the following percentage
        float percentage = 100f * bytesWritten / contentLength;
        if (percentage >= 0) {
            //TODO: Progress bar
        } else {
            //Something went wrong
        }
    }
}); 
```

我把代码改成了 OkHttp 3。这里有一个链接，链接到经过适当修改的 GitHub 中的类。

### 用 OkHttp 处理 Cookies

可以用`CookieJar`处理 OkHttp 中的 cookies。下面的例子是如何在 OkHttp 中处理 cookies 的例子。

```
public class CookieStore implements CookieJar {

    private final Set<Cookie> cookieStore = new HashSet<>();

    @Override
    public void saveFromResponse(HttpUrl url, List<Cookie> cookies) {
        /**
         *Saves cookies from HTTP response
         * If the response includes a trailer this method is called second time
         */
        //Save cookies to the store
        cookieStore.addAll(cookies);
    }

    @Override
    public List<Cookie> loadForRequest(HttpUrl url) {
        /**
         * Load cookies from the jar for an HTTP request.
         * This method returns cookies that have not yet expired
         */
        List<Cookie> validCookies = new ArrayList<>();
        for (Cookie cookie : cookieStore) {
            LogCookie(cookie);
            if (cookie.expiresAt() < System.currentTimeMillis()) {
                // invalid cookies
            } else {
                validCookies.add(cookie);
            }
        }
        return validCookies;
    }

    //Print the values of cookies - Useful for testing
    private void LogCookie(Cookie cookie) {
        System.out.println("String: " + cookie.toString());
        System.out.println("Expires: " + cookie.expiresAt());
        System.out.println("Hash: " + cookie.hashCode());
        System.out.println("Path: " + cookie.path());
        System.out.println("Domain: " + cookie.domain());
        System.out.println("Name: " + cookie.name());
        System.out.println("Value: " + cookie.value());
    }
} 
```

为了基于`CookieStore`类中指定的策略处理 cookies，您必须将这些策略应用到`OkHttpClient`中，如下所示。

```
OkHttpClient client = new OkHttpClient.Builder()
    .cookieJar(new CookieStore())
    .build(); 
```

现在您已经更新了`OkHttpClient`来接受 cookie，所有的网络请求 cookie 都将由在`CookieStore`类中实现的策略来处理。

这个例子对于会话 cookie 来说已经足够了，但是对于那些希望通过后续的应用程序启动来保存 cookie 的人来说，这个策略是不够的。通过使用基于`SharedPreferences`的 [PersistentCookieJar](https://github.com/franmontiel/PersistentCookieJar) 实现，可以解决这个问题。要使用带`OkHttpClient`的包装，请遵循他们的[使用指南](https://github.com/franmontiel/PersistentCookieJar#usage)。

## 一切都好吗？

在本文中，我介绍了 OkHttp 的入门知识，并分享了在使用它时可能会派上用场的建议。我希望你发现这个库节省了你的时间，并且很想知道你用它创造了什么。

## 分享这篇文章