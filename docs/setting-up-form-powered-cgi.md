# 设置一个由 CGI 驱动的表单

> 原文：<https://www.sitepoint.com/setting-up-form-powered-cgi/>

注意:对于本教程，您必须有一个支持 Perl CGI(公共网关接口)脚本的服务器。

您将需要安装一个名为 CGI.pm 的 Perl 模块来运行这个表单。如果你有 telnet 访问，你可以输入“哪个 CGI.pm ”,它会告诉你它在哪里或者你是否有它。如果您没有 telnet 访问，您可以使用另一个 CGI 脚本来检查它。要检查并查看是否已经使用另一个 CGI 脚本安装了它，请创建如下所示的 Perl 脚本:

**#！/usr/local/bin/perl**

# hello . pl——我的第一个 perl 脚本！

使用 CGI

打印“你好，世界！n”；

第一行应该设置为 Perl 在服务器上的位置。你必须修改这个脚本。如果页面上写着“你好，世界！”那么你已经安装了它，但是如果你得到一个错误，那么你没有。要了解如何安装它，请访问[这个网站](http://stein.cshl.org/WWW/software/CGI/cgi_docs.html)。安装后，您可以继续。

现在，您需要下载 CGI 脚本和致谢文件。单击此处下载文件。在文本编辑器中打开文件 **"mailaform.cgi"** 。

好了，脚本的第一行应该设置为 Perl 的位置。把**-w "**留在最后。 **sendmail** 变量应该是服务器上 **sendmail** 的路径。 **$recipient** 应该是你的电子邮件地址或任何将会收到邮件的人。现在针对**$发送方**和**$主题**。发件人应该是您希望所有表单来自的名称，主题应该是电子邮件的主题。

您需要编辑的最后一个变量是 **$thanks** ，这是文件**“thanks . txt .”**的位置，这将是感谢他们使用您的表单的文件。它应该包含 HTML，但仍然保存为文本(。txt)文件。你应该上传这个文件和 chmod 它 777。

现在，你所要做的就是将 **"mailaform.cgi"** 上传到你的 cgi-bin，然后 chmod it 755。
要使用该脚本，制作一个常规的表单，但不要使用 **" < FORM > "** ，而是使用以下代码行来启动它:**<FORM ACTION = " http://www . your server . com/CGI-bin/maila FORM . CGI " METHOD = " POST ">**

## 分享这篇文章