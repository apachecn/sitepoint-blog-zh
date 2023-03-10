# 折腾 PECL::cvsclient

> 原文：<https://www.sitepoint.com/messing-with-peclcvsclient/>

出于兴趣，我一直在黑萨拉·戈莱蒙的 [cvsclient](http://pecl.php.net/cvsclient) 分机。能够通过 HTTP 访问 CVS(可能是为了更新 HTML 表单接口，或者用 XML-RPC / SOAP 公开)是一个非常值得做的事情。

请注意，cvsclient 是 beta (0.2)，目前只有有限的功能，用于读取存储库和检查给定文件的日志。除了源代码之外，几乎没有任何文档。

如果您运行的是 Linux 并设置了 PEAR 包管理器，安装 cvsclient 作为共享库只需要输入“pear install cvsclient-beta ”,然后添加。所以作为扩展名保存到 php.ini 文件中。如果你用的是 Windows……你可能需要请求这个网站的所有者。

能够连接到 Sourceforge 的 [WACT CVS 库](http://sourceforge.net/cvs/?group_id=85372);

 `$cvs_server = 'cvs.sf.net';
$cvs_root = '/cvsroot/wact';
if ( !$cvs = cvsclient_connect($cvs_server,$cvs_root) ) {
die('Could not connect');
}
echo "Connected to servern";`

`$cvs_user = 'anonymous';
$cvs_pass = '';
if ( !cvsclient_login($cvs,$cvs_user,$cvs_pass) ) {
die ('Could not login');
}
echo "Logged inn";`

PHP 还暴露了另外两个函数，cvsclient_retrieve()和 cvsclient_log()，前者似乎是对 CVS 中目录内容的迭代，而后者可用于获取单个文件的日志，例如:

 `$module = 'wact';
$path = '.';
while ($file = cvsclient_retrieve($cvs,$module,$path) ) {
echo $file;
}`

和

 `$file = 'README';
print_r (cvsclient_log($cvs,$module,$file));`

无法访问 WACT 模块根目录下的目录——这是我的错还是当前版本的限制，我不确定。另外，*似乎没有办法从 cvsclient_retrieve()中获取文件名。再说一次，我可能没有抓住要点。*

无论如何——虽然现在还不能投入生产，但绝对值得关注。祝作者好运。

## 分享这篇文章