# AWS PHP SDK 入门

> 原文：<https://www.sitepoint.com/getting-started-with-the-aws-php-sdk/>

## AWS PHP SDK 入门

亚马逊 AWS S3 是一个强大的互联网存储解决方案。它可以用来存储和检索任何数量的数据，在任何时间，从网上的任何地方。借助 S3，您可以访问高度可扩展、可靠、安全、快速、廉价的 web 应用基础设施。

在接下来的几周内，我将创建一系列教程，向你展示如何使用亚马逊 S3 和其他亚马逊 AWS 服务做出惊人的事情。本文旨在让您开始使用 Amazon Web Services。

Amazon 已经为许多不同的平台提供了强大的 SDK 支持，包括 PHP。在本文中，您将通过实际例子了解如何开始使用 AWS SDK for PHP。

## 先决条件

大多数 web 主机都有一个 PHP 设置，无需任何额外的配置就可以支持 SDK，但是如果您对下面的任何示例有任何问题，请参阅本文末尾的参考资料。

除了一个普通的老网站主机，你还需要一个亚马逊 AWS 账户。设置一个在:[http://aws.amazon.com](http://aws.amazon.com)

此外，你需要确保你的账户中启用了亚马逊 AWS S3 服务。我的所有示例都将利用该服务。

## 安装和配置

**1)下载**
从在你的网络服务器上获取 SDK 文件开始。有几种方法可以实现这一点。我最喜欢的是下载 SDK 并将文件上传到你的网络服务器。很简单，对吧？您还可以使用 PEAR 和其他一些方法来安装 SDK。你可以在这里下载最新版本:[http://pear.amazonwebservices.com/get/sdk-latest.zip](http://pear.amazonwebservices.com/get/sdk-latest.zip)

出于本文的目的，我将把 SDK 上传到我的网站根目录下的一个名为“awssdk”的文件夹中。

2)重命名配置文件

上传文件后，您需要更新您的配置文件。第一步是将配置文件的名称从 config-sample.inc.php 改为:**config.inc.php**

**3)配置文件设置**
现在打开配置文件，插入你自己的凭证。该文件中定义了 10 个或更多常量。现在，您只需要更新其中的三个:

```
define('AWS_KEY', 'ENTER YOUR AWS KEY HERE');
define('AWS_SECRET_KEY', 'ENTER YOUR AWS SECRET KEY HERE');
define('AWS_ACCOUNT_ID', 'ENTER YOUR AWS ACCOUNT ID HERE');
```

找到这些值很容易:

*   只需登录您的 AWS 管理控制台。
*   点击最顶端导航菜单中的“账户”。
*   单击“安全凭证”。
*   向下滚动找到您的 AWS 密钥和秘密密钥。您的密钥将被隐藏，直到您单击“查看”。
*   进一步向下滚动，找到您的帐号。

## 你好水桶

首先在我上传 SDK 的同一个文件夹中创建一个名为 fun.php 的文件。到目前为止，您的目录结构应该是这样的:

```
/awssdk/sdk.class.php
/awssdk/config.inc.php
/awssdk/fun.php
/awssdk/utilities
/awssdk/services
/awssdk/lib
/awssdk/extensions
/awssdk/.... (a few more directories)
```

我们将在 fun.php 完成所有的编码工作

**1)包括 AWS SDK**

为了使用 SDK，它需要包含在我们的 PHP 代码中。如果您像我上面演示的那样设置文件结构，下面的代码应该可以包含 SDK:

```
<?php
require_once 'sdk.class.php';
```

**2)创建一个存储桶**

在 AWS SDK 中可以做的最简单的测试是在 S3 创建一个 bucket。S3 存储桶就像文件存储的“域”。一旦我们有了一个 bucket，我们就可以开始使用 PHP 创建、编辑和删除文件。代码如下:

```
<?php
require_once 'sdk.class.php';

// Create the s3 Object from the SDK
$s3 = new AmazonS3();

// Run the Method: Create Bucket.
// Arg 1: 'my-unique-bucket-name' is the name of your new bucket.
// Arg 2: The geographical region where your data will be stored.
// Arg 3: Tells Amazon to make your files readable by anyone like regular files hosted on a web server.
$response = $s3->create_bucket('my-unique-bucket-name', AmazonS3::REGION_US_E1, AmazonS3::ACL_PUBLIC);

// The response comes back as a Simple XML Object
// In this case we just want to know if everything was okay.
// If not, report the message from the XML response.
if ((int) $response->isOK()) {
    echo 'Created Bucket';
} else {
    echo (string) $response->body->Message;
}
```

创建 S3 存储桶时，需要记住以下几点:

*   桶名用于域名，所以只能使用数字、字母和破折号。
*   在所有 Amazon AWS 用户中，存储桶名称必须是唯一的。换句话说，“我的唯一存储桶名称”已经被(我)占用，因此您需要选择自己的唯一存储桶名称。

**2)列出存储桶**

现在您已经创建了一个存储桶，您可以使用以下示例列出您帐户中的所有存储桶:

```
<?php
$s3 = new AmazonS3();
$response = $s3->list_buckets();
foreach ($response->body->Buckets[0] as $bucket) {
    echo (string) $bucket->Name.&quot;<br />&quot;;
}
```

## 你好世界

现在我们有了一个桶，我们准备好迎接这个世界了。以下示例演示了如何在 AWS 存储桶中创建简单的文本文件:

```
<?php
require_once 'sdk.class.php';
$s3 = new AmazonS3();

// Create Object is pretty self explanatory:
// Arg 1: The bucket where your file will be created.
// Arg 2: The name of your file.
// Arg 3: An array of Options.
//        In this case we're specifying the &quot;body&quot; option
//        and the ACL setting to allow this file to be read by anyone.
$response = $s3->create_object('my-unique-bucket-name', 'test1.txt', array(
'body' => 'Hello world. Nice to meet you.',
'acl'=>AmazonS3::ACL_PUBLIC,
));

if ((int) $response->isOK()) echo 'I Created a File!';
```

要测试您的文件是否已上传，请尝试使用其 URL 访问它:http://your-unique-bucket-name-here . S3 . Amazon AWS . com/test1 . txt

## 用例 1:公共内容交付

亚马逊 S3 服务的最基本用途是为图像、视频和其他媒体等网络内容提供存储和交付。你可以使用 S3 提供任何类型的内容，包括流媒体视频和普通的 HTML 网页。亚马逊处理所有的工作，以确保您的文件是安全的，他们总是很快交付，所有您需要做的是让您的文件进入 S3 桶！我们已经展示了如何在 S3 创建一个简单的文本文件。以下示例演示了将文件放入 S3 存储桶的几个附加选项:

**1)从您的服务器上传文件**

对于本例，将名为“file.jpg”的文件上传到 awssdk 文件夹中。我们的目标是将文件从您的服务器上传到 S3 存储桶:

```
<?php
require_once 'sdk.class.php';
$s3 = new AmazonS3();

// Create Object is pretty self explanatory:
// Arg 1: The bucket where your file will be created.
// Arg 2: The name of your file.
// Arg 3: An array of Options.
//        In this case we're specifying the &quot;fileUpload&quot; option (a file on your server)
//        and the ACL setting to allow this file to be read by anyone.
$response = $s3->create_object('my-unique-bucket-name', 'in_the_cloud.jpg', array(
'fileUpload'=>__DIR__.'/file.jpg',
'acl'=>AmazonS3::ACL_PUBLIC,
));

// Check if everything is okay.
if ((int) $response->isOK()) echo 'I Uploaded a File from My Server!';
```

与前面的示例一样，您可以通过在浏览器中预览来验证您的上传:http://my-unique-bucket-name-here . S3 . Amazon AWS . com/in _ the _ cloud . jpg

**2)从另一个网站上传文件**

有时你想从另一个网址获取一个文件到 S3。这真的很简单:

```
<?php
require_once 'sdk.class.php';
$s3 = new AmazonS3();

// Create Object is pretty self explanatory:
// Arg 1: The bucket where your file will be created.
// Arg 2: The name of your file.
// Arg 3: An array of Options.
//        In this case we're specifying the &quot;body&quot; option (opening a file from the web),
//        we're setting the contentType of the file so browsers know its an image,
//        and we're setting the ACL setting to allow this file to be read by anyone.
$response = $s3->create_object('my-unique-bucket-name', 'from_a_url.gif', array(
'body'=>file_get_contents('http://www.google.com/logos/logo_newyear.gif'),
'contentType'=>'image/gif',
'acl'=>AmazonS3::ACL_PUBLIC,
));

// Check if everything is okay.
if ((int) $response->isOK()) echo 'I Uploaded a File from the Web!';
```

**2)生成缩略图**
假设你想从一张图片生成一个缩略图，你知道图片的 URL。这个例子将会起作用:

```
<?php
require_once 'sdk.class.php';
$s3 = new AmazonS3();

// Create a thumbnail from a URL
$originalImage = imagecreatefromgif('http://www.google.com/logos/logo_newyear.gif');
$new_height = 30;
$width = imagesx($originalImage);
$height = imagesy($originalImage);
$new_width = round(($width * $new_height) / $height);
$imageResized = imagecreatetruecolor($new_width, $new_height);
imagecopyresampled($imageResized, $originalImage, 0, 0, 0, 0, $new_width, $new_height, $width, $height);

// Save the image in a temp file.
$tempfilename = tempnam(__DIR__, &quot;newImage&quot;);
ImageJpeg($imageResized,$tempfilename,100);

// Create Object is pretty self explanatory:
// Arg 1: The bucket where your file will be created.
// Arg 2: The name of your file.
// Arg 3: An array of Options.
//        In this case we're specifying the &quot;body&quot; option (opening the temp image file we created),
//        we're setting the contentType of the file so browsers know its an image,
//        and we're setting the ACL setting to allow this file to be read by anyone.
$response = $s3->create_object('my-unique-bucket-name', 'from_php_script.jpg', array(
'body'=>file_get_contents($tempfilename),
'contentType'=>'image/jpeg',
'acl'=>AmazonS3::ACL_PUBLIC,
));

// Delete the temporary file.
unlink($tempfilename);

// Check if everything is okay.
if ((int) $response->isOK()) echo 'I Uploaded a File Created with PHP!';
```

## 用例 2:受保护的内容交付

在某些情况下，你想给你在 S3 商店的图片增加一点安全性。S3 提供了许多增加安全性的方法，但最简单的方法之一是生成有时间限制的 URL。

有时间限制的 URL 很容易生成，并且它们允许你允许你的用户使用一个安全的 URL 下载内容，只要你想它就会过期。在某些情况下，您可能希望您的内容在几分钟内过期。这对于在网站上显示受保护的图片非常有用。在其他情况下，您可能希望允许文件下载几天或几周，这对于通过电子邮件发送下载链接可能更好。

创建安全文件的第一步是上传一个默认情况下无法在互联网上访问的文件:

```
<?php
require_once 'sdk.class.php';
$s3 = new AmazonS3();

// Create Object is pretty self explanatory:
// Arg 1: The bucket where your file will be created.
// Arg 2: The name of your file.
// Arg 3: An array of Options.
//        In this case we're specifying the &quot;fileUpload&quot; option (a file on your server)
//        and the ACL setting to prevent this file from being read by anyone on the interent.
$response = $s3->create_object('my-unique-bucket-name', 'in_the_cloud_private.jpg', array(
'fileUpload'=>__DIR__.'/file.jpg',
'acl'=>AmazonS3::ACL_PRIVATE,
));

// Check if everything is okay.
if ((int) $response->isOK()) echo 'I Uploaded a PRIVATE File from My Server!';
```

这个文件的常规 URL 应该是:http://my-unique-bucket-name . S3 . amazonaws . com/in _ the _ cloud _ private . jpg

但是如果你尝试这个 URL，你会得到一个拒绝访问的错误。这很好，因为我们希望这个文件受到保护。但是，假设我们想通过电子邮件将该文件的链接发送给某人。我们想让他们更容易下载，但我们不希望这个链接出现在任何人可以随时下载的论坛上。解决方案是创建一个 24 小时后过期的文件的链接。这有多简单:

```
<?php
require_once 'sdk.class.php';
$s3 = new AmazonS3();

//Generate a Secure URL
$secureURl = $s3->get_object_url(
    'my-unique-bucket-name',
    'in_the_cloud.jpg',
    '24 Hours'
);

echo $secureURl;
```

这会生成一个类似这样的 URL:http://my-unique-bucket-name . S3 . amazonaws . com/in _ the _ cloud . jpg？AWSAccessKeyId = akiainuirh 7 ehmgfbwqq & Expires = 1313027791 & Signature = g 2 ill 3 fwpi 0 myvdcupbnef 2 ung % 3D

此链接将在 24 小时内提供对该文件的访问，之后用户将收到一个访问被拒绝的错误。

## 无限可能

希望在尝试了这些例子之后，你会觉得使用亚马逊的 S3 存储服务创建和访问文件很舒服。

我们只是触及了可能的表面。在接下来的几周里，我将带你更深入地了解你可以用 S3 在你的网站和网络应用程序上做出惊人之举的许多方法。

如果你对使用我描述的 S3 有任何问题或建议，请在下面展开讨论！

## PHP 的 AWS SDK 资源

*   [入门指南](http://developer.amazonwebservices.com/connect/entry.jspa?externalID=4261)
*   [代码下载](http://pear.amazonwebservices.com/get/sdk-latest.zip)
*   [文档](http://docs.amazonwebservices.com/AWSSDKforPHP/latest/index.html)

图片 via[Kentoh](http://www.shutterstock.com/gallery-168430p1.html)/[Shutterstock](http://shutterstock.com)

## 分享这篇文章