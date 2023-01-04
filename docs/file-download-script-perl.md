# 用 Perl 构建一个文件下载脚本

> 原文：<https://www.sitepoint.com/file-download-script-perl/>

我有一个网站，提供照片给访客下载。我过去只是将文件显示在浏览器窗口中，这样访问者就可以右键单击并将它们保存到他们的计算机上。然而，这个系统很快就出现了两个问题。

首先，一些访问者不明白如何将文件下载到他们的电脑上。第二，我发现一些网站直接链接到我的照片，所以我的照片似乎是这些网站内容的一部分。

除了不礼貌之外，这种所谓的“热”链接也让我花钱，因为我的网络主机对我使用的带宽收费。我的解决方案是创建一个 Perl 脚本来屏蔽我网站上文件的 URL。

在本教程中，我将向您展示如何使用 Perl 构建一个简单的下载脚本。我们将要经历的例子将屏蔽下载 URL，将每次下载记录到一个日志文件中，并向访问者显示一个“另存为”对话框。

##### 要求

为了让这个脚本工作，你所有的照片(或者你提供下载的任何其他文件)必须位于一个目录中。该脚本还必须与要下载的文件目录位于同一服务器上。

##### 我们开始吧！

通过添加 shebang 行开始您的脚本。shebang 行以英镑字符开始，后面是一个感叹号和服务器的 Perl 解释器的路径。“`-wT`”是可选的，但是您应该使用它，因为它打开警告并检查被污染的数据。

```
#!/usr/bin/perl -wT
```

大多数 Linux/Unix 服务器在这个位置都有 Perl 解释器。在 Windows 机器上，一个常见的 shebang 行是“`#!C:perlbinperl.exe`”(注意在这种情况下斜线是反斜杠)。如果您不确定服务器的操作系统或 Perl 的路径，请咨询您的 Web 主机。

接下来的两行指定我们想要使用 CGI 模块，并且我们想要在浏览器窗口中显示任何致命错误。

```
use CGI ':standard'; 

use CGI::Carp qw(fatalsToBrowser);
```

现在，我们将声明 3 个变量:

```
my $files_location;  

my $ID;  

my @fileholder;
```

接下来，我们要指定要下载的文件的服务器路径。该值必须是对服务器上文件夹的绝对引用。该文件夹可以在服务器的文档根目录之外。

```
$files_location = "/home/files/download/photos";
```

如果您的服务器是 Windows 机器，路径将使用反斜杠，如下所示:

```
$files_location = "C:filestodownload";
```

##### 现在，为了好东西

下一行是有趣的开始。我们已经声明了一个名为“`$ID`”的变量。现在我们需要指定它的值。该变量保存要下载的文件的名称。然而，我们希望从浏览器传递它的值，而不是直接分配它的值。有几种方法可以将文件名传递给这个变量，但是，现在我们只指定这个值将来自浏览器。请注意，在本文的结尾，您将能够下载该脚本的完整源代码，以及一个可以用来调用该脚本的 HTML 表单。

```
$ID = param('ID');  
```

现在，假设调用了脚本，但是没有指定文件名。我们不希望脚本只是显示一个空白屏幕。相反，我们想告诉我们的访问者必须指定一个文件名。为此，我们将添加以下几行:

```
if ($ID eq '') {  

print "Content-type: text/htmlnn";  

print "You must specify a file to download.";  

} else {
```

如果指定了文件名，我们希望打开该文件，并将其内容分配给一个名为“`@fileholder`”的临时占位符。如果在打开或关闭文件的过程中有任何错误，我们将需要显示一条错误消息。

```
open(DLFILE, "<$files_location/$ID") || Error('open', 'file');  

@fileholder = <DLFILE>;  

close (DLFILE) || Error ('close', 'file');
```

##### 记录下载

接下来的 3 行是可选的，但是它们对于记录每个文件被下载的次数很有用。这 3 行代码简单地打开名为“`dl.log`”的日志文件(或者显示一个错误)，打印文件名，然后关闭日志文件。和以前一样，您必须指定服务器上的绝对路径。

```
open (LOG, ">>/home/files/dl.log") || Error('open', 'file');  

print LOG "$IDn";  

close (LOG);
```

要做到这一点，有两点必须到位。首先，该目录必须已经存在于您的服务器上。第二，文件“`dl.log`”必须存在于目录中，并且是可写的。要更改 Linux/Unix 服务器上的文件权限，必须使用命令`chmod`，如下所示:`chmod 777 dl.log/`。

##### 发送文件

现在，我们想把文件发送给用户。如果文件是图像、PDF 或浏览器可以识别的其他文件类型，该怎么办？即使我们屏蔽了 URL，访问者的浏览器也会显示该文件。为了让浏览器显示“另存为”对话框，我们需要在发送的文件头中伪造文件类型:

```
print "Content-Type:application/x-downloadn";   

print "Content-Disposition:attachment;filename=$IDnn";  

print @fileholder  

}
```

##### 一些错误处理

我们已经指示脚本调用一个子例程，如果当文件被打开或者下载被记录到日志文件时出错。下一段代码在浏览器窗口中显示错误:

```
 sub Error {  

        print "Content-type: text/htmlnn";  

    print "The server can't $_[0] the $_[1]: $! n";  

    exit;  

  }
```

##### 搞定了。但是它是如何工作的呢？

为了测试它，将脚本上传到您的 cgi-bin 目录，并将其 chmod 为 755。

现在，我们怎么称呼这个脚本？有几种方法可以做到这一点。首先，您可以在网页中创建一个超链接，内容如下:

```
<a href="/cgi-cin/script.cgi?ID=file.zip">Download File</a>
```

或者，您可以创建一个包含字段(复选框、下拉菜单等)的表单。)称为 ID，以及一个动作:`<http://www.yourserver.com/cgi-bin/script.cgi> /cgi-bin/script.cgi`

##### 源代码

下面是一个 HTML 网页的例子，你可以用它作为模板。此示例提供了一个下拉菜单，用户可以通过它选择想要下载的文件。

```
<html><head>  

<title>Download Page</title>  

</head>  

<body>  

<form action="/cgi-bin/script.cgi">  

<p>  

  <select size="1" name="ID">  

  <option value="0001.jpg">File One  

  </option>  

  <option value="file.zip">File Two  

  </option>  

</p>  

  </select>  

<input type="submit" value="Submit" name="B1">  

</form>  

</body>  

</html>
```

以下是完整的脚本:

```
#!/usr/bin/perl -wT  

use CGI ':standard';  

use CGI::Carp qw(fatalsToBrowser);   

my $files_location;   

my $ID;   

my @fileholder;  

$files_location = "/usr50/home/webdata/photos";  

$ID = param('ID');  

if ($ID eq '') {   

print "Content-type: text/htmlnn";   

print "You must specify a file to download.";   

} else {  

open(DLFILE, "<$files_location/$ID") || Error('open', 'file');   

@fileholder = <DLFILE>;   

close (DLFILE) || Error ('close', 'file');   

open (LOG, ">>/usr50/home/webdata/public_html/test.log") || Error('open', 'file');  

print LOG "$IDn";  

close (LOG);  

print "Content-Type:application/x-downloadn";   

print "Content-Disposition:attachment;filename=$IDnn";  

print @fileholder  

}
```

别忘了[完整下载代码](https://www.sitepoint.com/examples/perl/perlscript.zip)！

## 分享这篇文章