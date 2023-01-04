# GoogleAppEngine 1.9.0。–PHP 更新

> 原文：<https://www.sitepoint.com/googleappengine-1-9-0-php-updates/>

自从我在 GAE 的文章上发表我的[PHP 入门已经有一段时间了，这个平台比任何人预期的都要好。PHP 特性请求仍然排在](https://www.sitepoint.com/google-app-engine-php-getting-started/) [NFR 列表](https://code.google.com/p/googleappengine/issues/list?can=2&q=Type%3DFeature)的首位，而我当时创建的 [Phalcon NFR](https://code.google.com/p/googleappengine/issues/detail?id=9336) 现在排在第 17 位，这不仅表明了 Phalcon 的传播和受欢迎程度，也表明了 PHP 社区的活力和我们希望看到它传播到专业的大规模企业环境中的愿望。

2 月 26 日，GoogleAppEngine 的 SDK 的 PHP 和 Python 端更新为**版本 1.9.0** ，本文将旨在解释关于 PHP 的变化。你可以在这里找到完整的发行说明。

## 更新

### 新的解释器次要版本

> PHP 解释器已经从 PHP 5.4.19 升级到 PHP 5.4.22。

我不确定为什么 GAE 不用最新版本的 PHP，但是任何升级都是好的升级。

5.4.20 修复了 5.4.19 中出现的几十个错误，涉及 segfaults、使用引用可打印模式时不正确的空格字符编码、内存泄漏(特别是在 cURL 中)、一些扩展相关的修复等等。

5.4.21.防止编译时错误触发自动加载，确保内置服务器将所有 http 头视为不区分大小写，改进 DateTime 类中的错误消息，修复 IMAP 的配置脚本等。

最后，5.4.22。修正了更多与内置服务器、FTP、Exif、ODBC、Sockets、XMLReader 和其他组件相关的错误。你可以在[版本 5 的变更日志](http://php.net/ChangeLog-5.php)中看到全部的不同。

5.4 的最新版本。是 5.4.26。我们希望 GAE 尽快采纳，因为它确实包含了一些重要的修正。

### 自动加载 SDK 文件

> SDK 中现在提供了自动加载功能，因此开发人员不再需要明确要求 SDK 文件。

以前，要在 GAE 上为谷歌的特定服务使用 SDK 类，你必须手动包含它们或者编写一个自动加载器来完成。默认情况下，这些类是自动加载的，所以使用像`google\appengine\api\mail\Message`这样的类就像引用它一样简单。

GAE SDK 文档网站上的所有示例都已更新，以反映这一点。

代替

```
require_once 'google/appengine/api/mail/Message.php';
use google\appengine\api\mail\Message;
```

你现在只有

```
use google\appengine\api\mail\Message;
```

### 包括来自存储桶

> *   The extended php.ini setting `google_appengine.allow_include_gs_buckets` allows the inclusion of path filters to improve security.
>     
>     
> *   If the application moves the file uploaded by the user to the Google cloud storage bucket/path, it will now trigger a `E_USER_WARNING` warning message. This is because the code may be included, resulting in the vulnerability of local file inclusion.

这两次升级是同时进行的。

一般来说，[可以包含来自 Google 云存储桶的](http://www.php.net/include/)文件，只要这些桶是在选项`google_appengine.allow_include_gs_buckets`下的`php.ini`文件中定义的。这个设置的值是一个用逗号分隔的字符串，比如:`"bucket_1, bucket_2"`。

第一次更新意味着我们现在可以在 bucket 路径中包含子文件夹，以增加安全性。因此，我们可以只允许包含来自`bucket_1/includes_allowed`的，而不允许包含 bucket_1 中来自**任何地方的**，就像这样:

```
google_app_engine.allow_include_gs_buckets = "bucket_1/includes_allowed"
```

这使您可以将 bucket 用于几个方面——要包含的类和文件，以及其他类型的一般存储。

第二次更新意味着，如果您将上传的文件移动到允许包含的桶中，PHP 将发出警告。这是为了确保你的用户不能上传 PHP 脚本到一个文件夹中，以防止 LFI 攻击。当您看到此警告时，您应该修改您的应用程序，以便它将用户上传移动到存储桶的禁止包含部分。

### 免费的默认 GCS 桶、元数据访问器

> 新创建的 App Engine 应用程序现在有一个自动创建的 Google 云存储桶，作为同一个 Google Cloud 项目的一部分。

以前，如果你想要一个用于文件写入的谷歌云存储桶，你必须单独创建一个，并配置它与你的 GAE PHP 应用一起使用。然而，由于 PHP 应用程序经常需要磁盘读写和移动用户上传的文件，1.9.0 版本会在您开始一个新项目时自动创建一个完全配置的 bucket。

这个桶有 5GB 总存储的免费配额，所以你的应用程序不需要收费，可以通过名称`<app_id>.appspot.com`通过`gs://`协议访问，其中`app_id`是你的应用程序的 ID。您也可以在管理控制台的应用程序设置下找到该名称。

点击了解有关设置和使用它的更多信息[。](https://developers.google.com/appengine/docs/php/googlestorage/setup)

> 修复了空的 Google 云存储文件夹在开发者控制台中不能正确显示的问题。现在使用“/”后缀而不是“$folder”来创建新文件夹，以便正确显示它们。
> 
> 增加了 API 函数`CloudStorageTools::getMetadata()`和`CloudStorageTools::getContentType()`，用于检索 Google 云存储对象的元数据和内容类型。

您存储的 GCS 对象也可以通过新的`CloudStorageTools::getMetadata()`和`CloudStorageTools::getContentType()` API 告诉您它们的一些元数据。要查看`CloudStorageTools`的完整 API 参考，请在这里查看[。](https://developers.google.com/appengine/docs/php/refdocs/files/api.cloud_storage.CloudStorageTools#%5Cgoogle%5Cappengine%5Capi%5Ccloud_storage%5CCloudStorageTools)

### PHP_SELF 和 SCRIPT_NAME 修复

由于两个 bug， [9989](https://code.google.com/p/googleappengine/issues/detail?id=9989) 和 [10478](https://code.google.com/p/googleappengine/issues/detail?id=10478) ，PHP_SELF 和 SCRIPT_NAME 给出了错误的输出。这个问题现在已经解决了。

如果你想知道你可能想在哪里使用它们——它们最常用于路由、自动加载器、工厂模式实现和类似的项目，其中正在执行的脚本必须知道它的环境。

### SDK 的早期版本

SDK 的早期版本增加了对 FTP 和 Zip 扩展的支持，修复了更多 GCS 错误，允许计费应用程序的套接字 API，增加了用于生成公共访问 Google 云存储对象的 URL 的`CloudStorageTools::getPublicUrl()` API，等等。要查看完整列表，请查看[变更日志](https://code.google.com/p/googleappengine/wiki/SdkReleaseNotes)。

## 结论

GoogleAppEngine 的 PHP 支持自宣布以来一直在稳步增长，现在才刚刚升温。一方是 HHVM，另一方是 Zephir，第三方是云中的 Google PHP，一场高生产率的战争似乎正在酝酿之中，无论谁赢，PHP 社区都将受益。你投谁的票？你的 PHP 应用程序使用 GAE 吗？您有什么有趣的部署要展示给我们吗？您想看看更高级的实现示例吗？让我们知道！

## 分享这篇文章