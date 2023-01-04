# 让我们来谈谈:PHP 和 Android 的高效通信，第 2 部分

> 原文：<https://www.sitepoint.com/lets-talk-2/>

本系列的第 1 部分主要关注如何设置 Android 应用程序来发出 HTTP 请求。在第 2 部分中，我们将重点关注在请求的 Android 和 PHP 端实现数据序列化和压缩的使用。

## 确定数据序列化格式

在将数据传输到客户端之前，必须将其序列化为数据流，客户端稍后可以将其转换回有用的数据结构。在 PHP REST 服务中，您需要确定要使用的最合适的数据序列化格式。这包括解析客户机发送的 Accept 头，以确定它更喜欢哪种服务器支持的格式。

如果您使用 PHP 框架来构建 REST 服务，那么您应该参考它的文档来了解如何实现这一点，但是下面是确定响应格式的方法体:

```
<?php
// Define types supported by the server
$supportedTypes = array();
if (extension_loaded("json")) {
    $supportedTypes["application/json"] = "json_encode";
}
if (extension_loaded("msgpack")) {
    $supportedTypes["application/x-msgpack"] = "msgpack_pack";
}

// Get client-supported media types ordered from most to least preferred 
$typeEntries = array_map("trim", explode(",", $_SERVER["HTTP_ACCEPT"])); 
$acceptedTypes = array(); 
foreach ($typeEntries as $entry) { 
    $entry = preg_split('#;s*q=#', $entry); 
    $mediaType = array_shift($entry); 
    $qualityValue = count($entry) ? array_shift($entry) : 1; 
    $acceptedTypes[$mediaType] = $qualityValue; 
}
arsort($acceptedTypes); 

// Find the most preferred media type supported by client and server 
$supportedMediaTypes = array_keys($supportedTypes);
foreach ($acceptedTypes as $mediaType => $qualityValue) { 
    $pattern = "#" . str_replace("*", ".*", $mediaType) . "#"; 
    if ($matches = preg_grep($pattern, $supportedMediaTypes)) { 
        $supportedType = reset($matches); 
        return array($supportedType, $supportedTypes[$supportedType]);
    }
}
return null;
```

这段代码解析`Accept`头的值，按照各自的质量因子对它包含的 MIME 类型进行排序，然后按照这个顺序遍历它们，以确定哪种类型受服务器支持并且具有最高的质量因子。

您可以在您的响应中使用这个值，如下例所示，其中`$contentType`是您的方法的返回值。如果找不到相互支持的格式，则根据 [RFC 2616 第 10.4.7 节](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.4.7)返回适当的响应代码。

```
<?php
if ($contentType) {
    list ($name, $callback) = $contentType;
    header($_SERVER["SERVER_PROTOCOL"] . " 200 OK");
    header("Content-Type: " . $name);
    $data = call_user_func($callback, $data);
}
else {
    header($_SERVER["SERVER_PROTOCOL"] . " 406 Not Acceptable");
}
```

## 确定数据压缩格式

一旦数据被序列化，就必须对其进行压缩，以最大限度地减少将数据从服务器传输到客户端所需的带宽。这个确定数据压缩格式的过程类似于确定数据序列化格式，但它会解析不使用质量因子的`Accept-Encoding`头。如果客户端支持多种压缩格式，这使您能够决定它们的优先顺序。

同样，如果你有一个 PHP 框架可供选择，你应该查阅它的文档，看看是否有一个更流行的方法可以做到这一点，但是我在下面提供了一个原始的 PHP 示例。可以很容易地添加额外的编码，但是我已经包括了 HTTP 响应中最常用的编码，其中 bzip2 具有最高的优先级，因为它具有最好的压缩。

```
<?php
// Determine what encodings the client supports 
$clientEncodings = array_map("trim", explode(",", $_SERVER["HTTP_ACCEPT_ENCODING"])); 

// Determine what encodings the server supports 
$serverEncodings = array(); 
if (extension_loaded("bz2"))  {
    $serverEncodings["bzip2"] = "bzcompress"; 
} 
if (extension_loaded("zlib"))  {
    $serverEncodings["gzip"] = "gzdeflate"; 
    $serverEncodings["deflate"] = "gzcompress"; 
} 

// Return an encoding supported by both if one is found 
foreach ($serverEncodings as $encoding => $callback)  {
    if (in_array($encoding, $clientEncodings))  {
        return array($encoding, $callback); 
    } 
} 
return array();
```

在下面的例子中，`$encoding`是这个方法的返回值。如果找到支持的数据压缩格式，则根据 [RFC 2616 第 14.11 节](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.11)将其名称返回到适当的报头中。

```
<?php
if ($encoding) {
    list ($name, $callback) = $encoding;
    header("Content-Encoding: " . $name);
    $data = call_user_func($callback, $data);
}
```

虽然一些数据序列化格式在应用压缩之前可能看起来更有效，但这并不一定意味着在应用压缩之后也是如此。例如，在压缩之前，MessagePack 通常比 JSON 更紧凑，但是在大多数情况下，压缩之后就没有 JSON 紧凑了。因此，您应该在真实数据上测试不同的序列化和压缩组合，以确定哪种组合最适合您的应用程序。作为一般的经验法则，使用 JSON 和 gzip 或(最好是)bzip2 作为比较的基线。

## 发送响应

至此，您已经发送了响应代码、数据序列化和压缩格式。您可以发回的另一个有用的头表明了响应正文中的数据量。最后，您将发送回可选压缩的序列化数据流。

```
<?php
header("Content-Length: " . mb_strlen($data));
echo $data;
```

## 解压缩响应

当我们上次离开`DataModel.getData()`时，它正在执行 HTTP 请求以获取我们需要的数据。现在我们的 PHP REST 服务已经返回了一个响应，我们需要对它进行解压缩和反序列化，然后才能对它做一些有用的事情。

解压缩响应所需的确切代码因使用的压缩方案而异。Android 原生支持 gzip 和 deflate。令人惊讶的是，bzip2 是三种解压缩格式中最有效的，也是最难使用的，因为它本身不被支持。增加对它的支持的最简单的方法是通过 Apache Commons Compress 库。下面是处理这些压缩方案的代码:

```
if (httpResponse.getStatusLine().getStatusCode() != 200) {
    // An error occurred, throw an exception and handle it on the calling end
}
String encoding = httpResponse.getFirstHeader("Content-Encoding").getValue();
java.io.InputStream inputStream = null;
if (encoding.equals("gzip")) {
    inputStream = AndroidHttpClient.getUngzippedContent(httpResponse.getEntity());
} else if (encoding.equals("deflate")) {
    inputStream = new java.util.zip.InflaterInputStream(httpResponse.getEntity().getContent(), new java.util.zip.Inflater(true));
} else if (encoding.equals("bzip2")) {
    inputStream = new org.apache.commons.compress.compressors.bzip2.BZip2CompressorInputStream(httpResponse.getEntity().getContent());
}
String content;
try {
    content = new java.util.Scanner(inputStream).useDelimiter("\A").next();
} catch (java.util.NoSuchElementException e) {
    // An error occurred, throw an exception and handle it on the calling end
}
```

如果选择使用 bzip2，那么在如何使用 Apache Commons 压缩库方面有两种选择。第一步是将库 JAR 文件添加到您的项目中。这样做不那么繁琐，升级也像替换 JAR 文件和重建项目一样简单，但这会导致更大的 Android APK 文件，因为它包含了整个 JAR 内容。下面是使用这种方法的方法:

1.  [下载](http://commons.apache.org/compress/download_compress.cgi)其中一个二进制文件并解压。
2.  在 Eclipse 中，单击 Project 菜单并选择 Properties 选项。
3.  在出现的窗口中，从左侧列表中选择 Java 构建路径。
4.  在右窗格中，单击“库”选项卡。
5.  单击添加外部 jar 按钮。
6.  定位并选择`commons-compress-*#*.jar`文件，其中 *#* 为当前版本。

第二种选择是获取 bzip2 压缩所需的单独源文件，并手动将它们复制到您的项目中。这是一个更繁琐的过程，但你的 Android APK 文件大小将明显较小。下面是使用这种方法的方法:

1.  [下载](http://commons.apache.org/compress/download_compress.cgi)源 tarball 并解压。
2.  在 Eclipse 中，右键单击项目的 src 目录，选择 New > Package，并输入包名`org.apache.commons.compress.compressors`。
3.  重复步骤 2，但是这次使用包名`org.apache.commons.compress.compressors.bzip2`。
4.  导航到提取的源 tarball 的`src/main/java`目录。
5.  将这些文件复制到项目的相关包中:

*   `org/apache/commons/compress/compressors/CompressorInputStream.java`
*   `org/apache/commons/compress/compressors/bzip2/BZip2Constants.java`
*   `org/apache/commons/compress/compressors/bzip2/BZip2CompressorInputStream.java`
*   `org/apache/commons/compress/compressors/bzip2/CRC.java`
*   `org/apache/commons/compress/compressors/bzip2/Rand.java`

## 反序列化响应

一旦响应被解压缩，您需要将它反序列化成一个可用的数据对象。在 JSON 的例子中，Android [使用 Java 实现](http://developer.android.com/reference/org/json/package-summary.html)。解析 JSON 数据数组的代码可能如下所示:

```
import org.json.*;

// ...
// String content = ...
JSONArray contacts = (JSONArray) new JSONTokener(content).nextValue();
JSONObject contact = contacts.getJSONObject(0);
String email = contact.getString("email");
```

## 缓存响应

本文主要关注的是如何最小化请求期间传输的数据量。在同一资源可能被多次请求的情况下，使通信更有效的另一种方法是让客户端(例如 Android 应用程序)[缓存响应](http://developer.android.com/guide/topics/data/data-storage.html#InternalCache)，然后向 web 服务指示它最后访问的是哪个版本的资源。

有两种方法可以做到这一点，在 [RFC 2616 第 14 节](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)的小节中都有描述。第一种方法是基于时间的，其中服务器在其响应中返回一个`Last-Modified`头(第 29 小节),客户端可以在对同一资源的后续请求中在`If-Modified-Since`头(第 25 小节)中发送该值。第二种方法是基于散列的，其中服务器通过`ETag`报头(第 19 小节)在其响应中发送散列值，客户端可以在后续请求中的`If-None-Match`报头(第 26 小节)中发送该值。在这两种情况下，如果资源尚未更新，它将返回 304(未修改)响应状态。

使用第一种方法，您只需在 PHP 端包含一行类似下面这样的代码，其中`$timestamp`是一个 UNIX 时间戳，表示请求的资源最后一次被修改的时间。在许多情况下，可以使用`strtotime()`函数从格式化的日期字符串中获得 UNIX 时间戳。

```
<?php
header("Last-Modified: " . date("D, d M Y H:i:s", $timestamp) . " GMT");
```

在 Android 端，`DataModel.getData()`将被修改成这样:

```
// Fetch the Last-Modified response header value from a previous request from persistent storage if 
// available
// String lastModified = ... 
// HttpGet httpRequest = ...
httpRequest.addHeader("If-Modified-Since", lastModified);
HttpResponse httpResponse = this.httpClient.execute(httpRequest);
switch (httpResponse.getStatusLine().getStatusCode()) {
    case 304:
        // Use the cached version of the response
        break;
    case 200:
        // Handle the response normally
        lastModified = httpResponse.getFirstHeader(
            "Last-Modified").getValue();
        // content = ...
        // Store both of the above variables in persistent storage
        break;
}
```

## 总结

现在，您已经知道如何在 PHP 和 Android 中实现用于序列化和压缩目的的内容协商以及响应缓存。我希望这篇文章对您有用，并且您的移动应用程序会因此更加高效。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章