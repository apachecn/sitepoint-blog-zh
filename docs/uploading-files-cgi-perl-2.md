# 使用 CGI 和 Perl 上传文件

> 原文：<https://www.sitepoint.com/uploading-files-cgi-perl-2/>

##### 创建文件上传脚本

处理浏览器上传文件时发送的数据是一个相当复杂的过程。幸运的是，Perl CGI 库`CGI.pm`为我们做了大部分脏活！

使用 CGI 查询对象的两个方法，param 和 upload，我们可以分别检索上传文件的文件名和文件句柄。使用文件句柄，我们可以读取文件的内容，并将其保存到服务器上文件上传区域的新文件中。

***1。先说第一件事***

在脚本的顶部，我们需要创建 shebang 行。然后，我们将 Perl 解释器置于严格模式，以使我们的脚本尽可能安全，并在脚本中包含 Perl CGI 和 File::Basename 模块。我们还将使用 CGI::Carp 模块来显示网页中的错误，而不是显示一般的“500 Server Error”消息(在生产环境中注释掉这一行是个好主意):

`#!/usr/bin/perl -wT`

`use strict;
use CGI;
use CGI::Carp qw ( fatalsToBrowser );
use File::Basename;`

注意使用`-w`开关让 Perl 警告我们代码中的任何潜在危险。把`-w`放进去几乎总是一个好主意！此外，`-T`开关开启污点检查。这确保了任何不可信的脚本输入，如上传文件的文件名，被标记为被感染；然后我们需要在使用它之前明确地“清理”这些数据。(如果您试图使用污染的数据，Perl 会抛出一个错误。)稍后我会详细介绍这一点。

***2。设定安全极限*T3**

为了防止服务器因上传大量文件而过载，我们将上传文件的大小限制为 5MB 这应该足以处理大多数数码照片:

`$CGI::POST_MAX = 1024 * 5000;`

我们还将为文件名创建一个“安全”字符列表。一些字符，如斜线(`/`)，在文件名中是危险的，因为它们可能允许攻击者将文件上传到他们想要的任何目录。一般来说，字母、数字、下划线、句点和连字符是安全的选择:

`my $safe_filename_characters = "a-zA-Z0-9_.-";`

***3。*上传目录**

我们需要在服务器上创建一个位置来存储上传的文件。我们希望这些文件(照片)在我们的网站上可见，所以我们应该将它们存储在文档根目录下的一个目录中，例如:

`my $upload_dir = "/home/mywebsite/htdocs/upload";`

你需要在网站的文档根目录下创建一个名为“upload”的目录，然后将`$upload_dir`设置为该目录的绝对路径，就像我上面所做的那样。确保您的目录可以被您的脚本读写；在共享的 UNIX 服务器上，这通常意味着将`mode`设置为`777`(例如，通过在命令行发出`chmod 777 upload`命令)。如果你不确定你需要做什么，请咨询你的虚拟主机提供商。

***4。读取表单变量***

下一步是创建一个 CGI 对象(下面我们把它赋给`$query`)；这允许我们访问 CGI.pm 库中的方法。然后，我们可以读入上传文件的文件名，以及用户在表单中输入的电子邮件地址:

`my $query = new CGI;
my $filename = $query->param("photo");
my $email_address = $query->param("email_address");`

如果上传文件时出现问题—例如，文件大于`$CGI::POST_MAX`设置— `$filename`将为空。我们可以对此进行测试，并向用户报告问题，如下所示:

`if ( !$filename )
{
print $query->header ( );
print "There was a problem uploading your photo (try a smaller file).";
exit;
}`

***5。使文件名安全***

我们不一定信任浏览器发送的文件名；攻击者可以操纵这个文件名来做一些讨厌的事情，例如将文件上传到 Web 服务器上的任何目录，或者试图在服务器上运行程序。

我们要做的第一件事是使用 File::Basename 模块中的 fileparse 例程将文件名分成它的前导路径(如果有的话)、文件名本身和文件扩展名。然后，我们可以安全地忽略主导路径。这不仅有助于阻止将文件保存在 web 服务器上的任何位置，而且一些浏览器会将文件的完整路径发送到用户的硬盘上，这对我们来说显然是没有用的:

`my ( $name, $path, $extension ) = fileparse ( $filename, '..*' );
$filename = $name . $extension;`

上面的代码将浏览器传递的完整文件名拆分为名称部分(`$name`)、文件的前导路径(`$path`)和文件名的扩展名(`$extension`)。为了定位扩展名，我们传入正则表达式`'..*'`——换句话说，就是一个文字句点(`.`)，后跟零个或多个字符。然后，我们将扩展名重新连接到名称上，以重建不带任何前导路径的文件名。

清理文件名的下一步是删除所有不在安全字符列表(`$safe_filename_characters`)中的字符。我们将使用 Perl 的替换操作符(`s///`)来完成这项工作。在这个过程中，我们会将文件名中的任何空格转换为下划线，因为下划线在 URL 中更容易处理:

`$filename =~ tr/ /_/;
$filename =~ s/[^$safe_filename_characters]//g;`

最后，为了确保我们的文件名现在是安全的，我们将它与我们的`$safe_filename_characters`正则表达式进行匹配，并提取匹配的字符(应该是所有的字符)。我们也需要这样做来解开变量`$filename`。此变量被污染，因为它包含浏览器传递的潜在不安全数据。解除被污染变量的唯一方法是使用正则表达式匹配来提取安全字符:

`if ( $filename =~ /^([$safe_filename_characters]+)$/ )
{
$filename = $1;
}
else
{
die "Filename contains invalid characters";
}`

(注意，上面的`die`函数不应该被执行，因为我们已经使用前面的替换删除了不可靠的字符。不过，谨慎一点也无妨！)

***6。获取文件句柄***

正如我上面提到的，我们可以使用 upload 方法来抓取上传文件的文件句柄(实际上是指向 CGI.pm 创建的一个临时文件)。我们这样做:

`my $upload_filehandle = $query->upload("photo");`

***7。保存文件***

现在我们已经有了上传文件的句柄，我们可以读取它的内容，并将其保存到文件上传区域的一个新文件中。我们将使用上传文件的文件名(现已完全杀毒)作为新文件的名称:

`open ( UPLOADFILE, ">$upload_dir/$filename" ) or die "$!";
binmode UPLOADFILE;`

 `while ( )
{
)打印 UPLOADFILE
}` 

`close UPLOADFILE;`

注意上面第一行末尾的骰子功能；如果写文件出错，这个函数会停止脚本运行并报告错误消息(存储在特殊变量`$!`)。同时，`binmode`函数告诉 Perl 以二进制模式编写文件，而不是文本模式。这可以防止上传的文件在非 UNIX 服务器(如 Windows 机器)上损坏。

***8。感谢用户***

我们现在已经上传了文件！最后一步是向用户显示一封感谢信，并显示他们上传的照片和电子邮件地址:

`print $query->header ( );
print <<END_HTML;
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "DTD/xhtml1-strict.dtd">
<html xml:lang="en" lang="en">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>Thanks!</title>
<style type="text/css">
img {border: none;}
</style>
</head>
<body>
<p>Thanks for uploading your photo!</p>
<p>Your email address: $email_address</p>
<p>Your photo:</p>
<p><img src="/upload/$filename" alt="Photo" /></p>
</body>
</html>
END_HTML`

##### 完成的剧本

你完成的 CGI 脚本应该是这样的:

`#!/usr/bin/perl -wT`

 `使用严格；
使用 CGI
使用 CGI::Carp qw(fatalsToBrowser)；
使用 File::Basename；

$ CGI::POST _ MAX = 1024 * 5000；我的$ safe _ filename _ characters = " a-zA-Z0-9 _。-";
我的$ upload _ dir = "/home/my website/htdocs/upload "；

my $query =新 CGI
my $ filename = $ query->param(" photo ")；
my $ email _ address = $ query->param(" email _ address ")；

如果(！$filename )
{
打印$ query->header()；
打印“上传您的照片时出现问题(尝试较小的文件)。”；
退出；
}

my ( $name，$path，$ extension)= file parse($ filename，，)..*' );
$文件名= $名称。$分机；
$ filename = ~ tr//_/；
$ filename = ~ s/[^$safe_filename_characters]//g；

if($ filename = ~ /^([$safe_filename_characters]+)$/)
{
$ filename = $ 1；
}
else
{
die“文件名包含无效字符”；
}

my $ upload _ file handle = $ query-> upload(" photo ")；

打开(上传文件，" > $上传目录/$文件名" )或关闭" $！"；
bin mode upload file；

while ( )
{
)打印 UPLOADFILE
}

关闭上传文件；` 

`print $query->header ( );
print <<END_HTML;
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "DTD/xhtml1-strict.dtd">
<html xml:lang="en" lang="en">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>Thanks!</title>
<style type="text/css">
img {border: none;}
</style>
</head>
<body>
<p>Thanks for uploading your photo!</p>
<p>Your email address: $email_address</p>
<p>Your photo:</p>
<p><img src="/upload/$filename" alt="Photo" /></p>
</body>
</html>
END_HTML`

将该文件保存在硬盘上，并将其命名为`upload.cgi`。

现在我们已经创建了服务器端脚本，我们可以将脚本和表单都放在服务器上，并测试文件上传。

**Go to page:** [1](https://sitepoint.com/uploading-files-cgi-perl) | [2](https://sitepoint.com/uploading-files-cgi-perl-2/) | [3](https://sitepoint.com/uploading-files-cgi-perl-3/)

## 分享这篇文章