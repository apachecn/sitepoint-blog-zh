# 使用 Rackspace PHP SDK

> 原文：<https://www.sitepoint.com/using-the-rackspace-php-sdk-2/>

作为对我之前文章的回顾，CloudFiles 是一个静态内容的在线存储服务。

Rackspace 为多种编程语言提供了软件开发工具包(SDK)。他们将 PHP SDK 存储在 GitHub 上。PHP SDK 要求 PHP 5 具有以下模块:cURL、FileInfo 和 mbstring。

在本教程中，我们将回顾 PHP SDK 与 CloudFiles 的使用。虽然没有涵盖 API 的所有部分，但这将是一个很好的开始。

下载 SDK 后，您只需要以下文件和文件夹:

*   分享/
*   cloudfiles.php
*   cloudfiles _ exceptions.php
*   cloudfiles_http.php

# 你的第一次飞行

开始时，我们有一些基本任务。我们需要加载 cloudfiles.php 文件，使用 CloudFiles 进行认证，并使用我们的认证令牌创建一个连接。

```
<?php
$your_username = 'jeffk';
$your_api_key = 'longrandomapikey';

require 'cloudfiles.php';

/**
 * Authenticate using your Rackspace Cloud username and your API key obtained from the control panel
 **/
$auth = new CF_Authentication($your_username, $your_api_key);

/**
 * If you use Cloud Files in the U.K. use
 **/
$auth = new CF_Authentication($your_username, $your_api_key, null, UK_AUTHURL);

$auth->authenticate();

/**
 * Now we create the connection with our CF_Authentication instance
 **/
$connection = new CF_Connection($auth);
?>
```

就这么简单。现在我们已经连接好了，下一步是与容器进行交互。

# 容器

我们可以创建、删除、列出和打开不同的容器。为了与任何容器进行交互，我们将使用上面创建的$connection 对象。

让我们创建一个存储文件的容器。

```
<?php
/**
 * To create a container, use the $connection variable and call the create_container method. If the container
 * already exists it will return a container instance and NOT overwrite the container.
 **/
$images = $connection->create_container("images");
?>
```

这将返回一个容器对象。在这个例子中，我们将其命名为`$images`。这将用于操作容器以及其中的任何对象。如果你需要与容器中的任何对象交互，你必须使用`create_container`方法来访问它。由于容器已经存在，它将返回实例，而不会覆盖它。

现在，删除不再需要的容器。附注:您无法移除内含物件(档案)的容器。如果其中有文件，您必须在删除容器之前将它们全部删除。在文章的结尾有一个删除所有对象然后删除容器的代码片段。

```
<?php
/**
 * To delete a container call the delete_container method.
 **/
$connection->delete_container("images");
?>
```

做完这些动作，去拿集装箱怎么样？如果你想要一个包含各种信息的容器数组，你可以使用`get_containers`方法。

```
<?php
/**
 * Get an array of your available containers and display them.
 **/
$containers = $connection->get_containers();

foreach($containers as $container) {
    echo 'Name: '. $container->name;
    echo 'Number of Objects: '. $container->count;
    echo 'Bytes Stores: '.$container->bytes;
}
?>
```

如果您只想知道容器的名称，我们有 list_containers 方法:

```
<?php
$containers = $connection->list_containers();
print_r($containers); // Returns Array([0] => "images")
?>
```

# 使其对 CDN 可用

默认情况下，所有容器都是私有的。为了使它们在内容交付网络上可用，并且可以通过 url 访问，我们必须告诉 API 将容器公开。当然，如果需要的话，我们可以把它归还给私人。

```
<?php
/**
 * To make a container public use the containers instance and call make_public. You can      * optionally include a timestamp for how long the file is cached on the CDN network. The    * default is 86400 (1 day)
 * Calling this method returns the URI the container is available at.
 **/
$public_uri = $images->make_public();

/**
 * You can also get the URI by calling the cdn_uri or cdn_ssl_uri attributes
 **/
$uri => $images->cdn_uri;
$https_uri = $images->cdn_ssl_uri;

/**
 * To set a public container private you can call make_private. However, all the files in this container will still be available until the cache time expires.
 **/
$images->make_private();
?>
```

# 对象，对象，对象

要创建一个对象，我们必须继承我们的容器实例，并使用带有文件名的`create_object`方法。一旦我们这样做了，我们必须发送文件的内容。有两种方法可以做到。最简单的方法是使用`load_from_filename`方法，这样它将为我们收集所有信息。我们指定文件和大小的第二种方式。

```
<?php
$avatar = $images->create_object('jeffs_avatar.jpg');
$avatar->load_from_filename('/home/user/photos/jeffs_avatar.jpg');

$file_name = '/home/user/photos/jeffs_avatar.jpg');
$file_size = (float) sprintf("%u", filesize($file_name));
$fp = open($file_name, 'r');
$avatar->write($fp, $file_size);

?>
```

要获得私有对象，我们可以使用 steam 方法。这对于保护内容非常有效。它不仅仅局限于图片，如果你想限制你的成员访问 pdf 文档，这将是理想的。

```
<?php
$img = $images->get_object('jeff.jpg');

header('Content-Type: '.$img->content_type);
$output = fopen("php://output", "w");
$img->stream($output);
fclose($output);
?>
```

要删除一个对象，我们需要使用我们的容器并调用 delete_object 方法。

```
<?php
$images->delete_object('jeff.jpg');
?>
```

# 结束了。几个工作示例

这里有一个扫描文件夹的例子。jpg 文件，并将它们上传到 Rackspace 云文件。

```
<?php

$api_username = 'jeffk';
$api_key = 'myapikey';

$file_path = '/home/user/images';

require 'cloudfiles.php';

$auth = new CF_Authentication($api_username, $api_key);
$auth->authenticate();

$connection = new CF_Connection($auth);
$images = $connection->create_container("images");
$url = $images->make_public();
echo 'The url is '.$url;

/**
 * Now that we have our connection and access to the container lets get our files.
 **/
if ($h = opendir($file_path)) {
    /**
     * We just opened the directory and now we are going to scan that directory for any files
     **/
    while (false !== ($file = readdir($h))) {

        /**
         * Now we are going to get the extension of the file. If it matches "jpg" then we are going to upload it.
         * Otherwise we are going to ignore it.
         **/
        $get_extension = explode('.', $file);
        if ($get_extension[count($get_extension)-1]=='jpg') {

            /**
             * Lets create the object and send it to CloudFiles
             **/
            $img = $images->create_object($file);
            $img->load_from_filename($file_path.'/'.$file);

            /** Now we delete the image so we don't try to upload it again if we rerun the script **/
            unset($img);
        }
    }
    /**
     * Close the handler for good measure
     **/
    closedir($h);
}
?>
```

下一个示例是删除已经包含对象的容器。

```
<?php
$objects = $images->list_objects();
if (count($objects)>0) {
    foreach($objects as $o) {
        $images->delete_object($o);
    }
}
$connection->delete_container("images");

?>
```

我在这里提供的是一些基本的例子。您可能添加的一些内容是返回对象的 url、错误处理，或者甚至是确保在连接之前有一个或多个对象等待上传。

# 其他需要了解的内容

如果您得到如下所示的错误，您需要更新您的 ca 证书，请参考您的操作系统手册或 web 主机来升级它们。在我的开发环境中成功测试之后，我在生产中遇到了这个问题。

```
* About to connect() to lon.auth.api.rackspacecloud.com port 443
* Trying 212.64.148.13... * connected
* Connected to lon.auth.api.rackspacecloud.com (212.64.148.13) port 443
* successfully set certificate verify locations:
* CAfile: /etc/pki/tls/certs/ca-bundle.crt
CApath: none
* SSL certificate problem, verify that the CA cert is OK. Details:
error:14090086:SSL routines:SSL3_GET_SERVER_CERTIFICATE:certificate verify failed
* Closing connection #0
PHP Fatal error: Uncaught exception 'InvalidResponseException' with message     'Unexpected response (): ' in /usr/share/php/cloudfiles/cloudfiles.php:212
Stack trace:
#0 /var/www/html/cloudtest.php(18): CF_Authentication->authenticate()
#1 {main}
thrown in /usr/share/php/cloudfiles/cloudfiles.php on line 212

Fatal error: Uncaught exception 'InvalidResponseException' with message 'Unexpected     response (): ' in /usr/share/php/cloudfiles/cloudfiles.php:21
```

接下来，Rackspace 会在出现错误时使用异常。将您的代码包装在 try{ } catch() { }块中可以让您得到错误消息，而不是“未捕获的异常…”下面列出了他们使用的不同的异常类，并且他们只扩展了 php 提供的异常类。

```
class SyntaxException extends Exception { }
class AuthenticationException extends Exception { }
class InvalidResponseException extends Exception { }
class NonEmptyContainerException extends Exception { }
class NoSuchObjectException extends Exception { }
class NoSuchContainerException extends Exception { }
class NoSuchAccountException extends Exception { }
class MisMatchedChecksumException extends Exception { }
class IOException extends Exception { }
class CDNNotEnabledException extends Exception { }
class BadContentTypeException extends Exception { }
class InvalidUTF8Exception extends Exception { }
class ConnectionNotOpenException extends Exception { }
```

如果您试图删除一个仍然包含对象的容器，软件开发工具包将抛出一个您必须注意的 NonEmptyContainerException 异常。

```
<?php
/**
* Try to run the code
**/

try {
    $connection->delete_container("images");
}
/**
* If there was an exception thrown, catch it and display the message
**/

catch(NonEmptyContainerException $e) {
    echo $e->getMessage();
}
```

如果我们的容器不是空的，它会显示“容器必须是空的，才能删除它”。如果您没有捕捉到错误，它会提供如下输出:

```
Fatal error: Uncaught exception 'NonEmptyContainerException' with message 'Container    must be empty prior to removing it.' in /Users/kreitje/Development/test/    cloudfiles.php:560 Stack trace: #0 /Users/kreitje/Development/test/index.php(25):   CF_Connection->delete_container('images') #1 {main} thrown in /Users/kreitje/   Development/test/cloudfiles.php on line 560
```

如果开发一个客户会与之交互的应用程序，你会希望捕捉任何异常。有时显示的错误会显示敏感信息。上面，你可以看到我的文件存储在哪里。根据错误，您的 API 凭据可能会显示在浏览器页面上。

在软件开发工具包中，有一个 docs 文件夹，里面有这些方法，还有一些额外的方法。现在你已经有了基础知识，知道文档在哪里，我要求你创建一个与 Rackspace CloudFiles 交互的项目。

Shutterstock 上的 [Artush 图像](http://www.shutterstock.com/gallery-541534p1.html)

## 分享这篇文章