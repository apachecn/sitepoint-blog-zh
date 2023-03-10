# 如何用 PHP 上传大文件

> 原文：<https://www.sitepoint.com/upload-large-files-in-php/>

用 PHP 从 web 表单上传文件很容易。在线手册提供了一个[处理文件上传](http://www.php.net/manual/en/features.file-upload.php)的章节，还有几篇关于 sitepoint.com 的文章，包括凯文·杨克的[如何用 PHP 处理文件上传](https://www.sitepoint.com/article/handle-file-uploads-php)。

最受欢迎的用途之一是图像上传。您的用户可以从表单中提交照片，而不必求助于 FTP 或其他复杂的方法。HTML5 和 Flash 也允许拖放，所以随着浏览器的发展，操作可能会变得更容易。

这就是问题开始的地方。相机制造商不断吹嘘他们拥有比竞争对手更多的百万像素。当然，这些都是垃圾——除非你是专业摄影师或者需要打印非常大的图像，否则任何超过 400 万像素的东西都是毫无意义的，镜头质量要重要得多。然而，即使是低端紧凑型也有 12MP，手机也有 5MP 以上。结果是，典型的快照大小可以轻松达到 6MB。

默认情况下，PHP 允许上传最大 2MB 的文件。你可以要求用户在上传之前调整他们的图片大小，但是让我们面对现实吧:他们不会的。幸运的是，我们可以在必要时提高限制。

两个 PHP 配置选项控制最大上传大小:`upload_max_filesize`和`post_max_size`。例如，对于 10 兆字节的文件大小，两者都可以设置为“10M”。

但是，您还需要考虑完成上传所需的时间。PHP 脚本通常在 30 秒后超时，但是一个 10MB 的文件在一个健康的宽带连接上上传至少需要 3 分钟(记住上传速度通常比下载速度慢五倍)。此外，操作或保存上传的图像也可能导致脚本超时。因此，我们需要将 PHP 的`max_input_time`和`max_execution_time`设置为 300(以秒为单位指定 5 分钟)。

这些选项可以在服务器的`php.ini`配置文件中设置，这样它们就可以应用于所有的应用程序。或者，如果您使用 Apache，您可以在应用程序的`.htaccess` 文件中配置设置:

```
 php_value upload_max_filesize 10M
php_value post_max_size 10M
php_value max_input_time 300
php_value max_execution_time 300 
```

最后，您可以在 PHP 应用程序中定义约束:

```
ini_set('upload_max_filesize', '10M');
ini_set('post_max_size', '10M');
ini_set('max_input_time', 300);
ini_set('max_execution_time', 300); 
```

PHP 还提供了一个 [`set_time_limit()`](http://www.php.net/manual/en/function.set-time-limit.php) 函数，所以你不需要直接设置`max_execution_time`。

在 PHP 代码中设置选项可能更实用，因为当应用程序需要大量上传时，可以延长执行时间并增加文件大小。其他表单将恢复到默认的 30 秒超时和 2MB 限制。

用 PHP 上传大文件还有其他小技巧吗？

## 分享这篇文章