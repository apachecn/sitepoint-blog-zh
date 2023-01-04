# 带有 PHP 的 Ubuntu 插件

> 原文：<https://www.sitepoint.com/ubuntu-add-ons-with-php/>

家庭相册越来越大，存储在 Ubuntu 下的大量图片已经成为一个问题。抱着“浪费时间以节省时间”的态度，我开始探索某种[数字工作流程](http://www.microsoft.com/windowsxp/using/digitalphotography/prophoto/workflow.mspx)的选项，以尽可能自动化或至少将鼠标点击次数降至最低。

一个特别的问题是给图像添加评论；大多数存档软件(如 [gthumb](http://gthumb.sourceforge.net/) )都让你将资料*与图像*分开存储，维护他们自己的数据库(对于 gthumb，在`~/.gnome2/gthumb`下)。但是我宁愿让评论留在图片上(例如，使用[Exif](http://en.wikipedia.org/wiki/EXIF)——还有其他方法，但是 Exif 赢得了广泛的支持)。

## 编写 Nautilus 脚本

偶然遇到了 g-scripts 和你可以用自己的脚本扩展 T2 Nautilus(Ubuntu 文件管理器 GUI)的想法。事实上这里有一个简短的教程。g-scripts 上的例子使用 bash 脚本，但是认为这是误用 PHP 的好机会。

## 顶点

除了扩展 Nautilus，还利用了 [Zenity](http://freshmeat.net/projects/zenity) (我在 Gnome 中运行 Ubuntu，而不是 Kbuntu)，它允许你在 shell 脚本中弹出对话框。例如，如果你运行`$ zenity --question --text "Feeling OK?"`，弹出一个对话框，如…

![Zenity Dialog](img/f85077324878f9e2ce64bcc9801d601a.png)

这里有更多关于 Zenity 的内容。事实上，*使用 PHP 实现这一点是个坏主意，因为 Perl 已经用 [UI::Dialog](http://cpan.uwinnipeg.ca/dist/UI-Dialog) 解决了这个问题，它支持多个后端，包括 Zenity 和 KDialog(这意味着你的脚本也可以在 Kbuntu 下工作),但有趣的是，这里将继续使用 PHP。*

## 技巧

无论如何——一些快速的提示(在上面的教程中已经提到了)然后是代码。

*   你把你的脚本保存在`~/.gnome2/nautilus-scripts`下
*   确保你已经安装了 php5-cli 包(`$ sudo apt-get install php5-cli`)
*   您需要在脚本的顶部添加 PHP shebang 行(我省略了。php 扩展)例如`#!/usr/bin/php`
*   确保它们是可执行的
*   要激活脚本，您需要将 Nautilus 指向脚本目录(菜单:Go > Location)。如果创建其他脚本，您可能需要重新加载
*   要运行脚本，右键单击文件并选择脚本菜单

## Exif 操作

总之——成功地将两个脚本放在一起，一个用于在 Zenity 信息对话框中查看 Exif 评论(最简单的方法是使用 [exif_read_data()](http://www.php.net/exif_read_data) 函数),另一个用于使用 Zenity 文本输入对话框编辑评论。对于第二个，我使用了[马丁·盖斯勒的](http://mgeisler.net/)优秀的 [PEL](http://pel.sourceforge.net/) 库，其中有一个我几乎可以复制粘贴的例子，用 Zenity 替换了命令行界面(补充说明:PEL 0.9 似乎不太稳定——如果你得到关于重新定义常数的错误消息，编辑脚本并注释第二个常数赋值)。所以现在右击一幅图像并选择我的`exif_comment_edit`脚本，我得到一个对话框，像…

![Editing an Exif comment](img/49bc9eb606eb68633bccfb917b30e7df.png)

代码…

## exif_view_comment

```
 #!/usr/bin/php
<?php
function ohDearMe($msg) {
    `zenity --error --text="$msg" --title="Exif Info Error"`;
    exit(1);
}

if ( !isset($argv[1]) ) {
    ohDearMe("Image filename required");
    exit(1);
}

$file = getcwd().'/'.$argv[1];

if (!is_readable($file)) {
  ohDearMe(sprintf("Unable to read: %s", $file));
  exit(1);
}

$title = 'Exif Info for '.addslashes(basename($file));

$data = exif_read_data($file);

if ( !$data ) {
    ohDearMe('Unrecognized image format!');
}

if ( !isset($data['ImageDescription']) || trim($data['ImageDescription']) == '' ) {
    ohDearMe('Image contains no comment');
}

$out = $data['ImageDescription'];

`zenity --info --text="$out" --title="$title"`; 

```

## exif _ 编辑 _ 评论

```
 #!/usr/bin/php
<?php
require_once('pel/PelDataWindow.php');
require_once('pel/PelJpeg.php');
require_once('pel/PelTiff.php');

function ohDearMe($msg) {
    `zenity --error --text="$msg" --title="Exif Info Error"`;
    exit(1);
}

if ( !isset($argv[1]) ) {
    ohDearMe("Image filename required");
    exit(1);
}

$in = getcwd().'/'.$argv[1];

if (!is_readable($in)) {
  ohDearMe(sprintf("Unable to read: %s", $in));
  exit(1);
}

$title = 'Exif Info for '.addslashes(basename($in));

$data = new PelDataWindow(file_get_contents($in));

if (PelJpeg::isValid($data)) {

    $jpeg = $file = new PelJpeg();
    $jpeg->load($data);
    $app1 = $jpeg->getSection(PelJpegMarker::APP1);

    if ($app1 == null) {

        $app1 = new PelExif();
        $jpeg->insertSection(PelJpegMarker::APP1, $app1, 2);
        $tiff = new PelTiff();
        $app1->setTiff($tiff);

    } else {
        $tiff = $app1->getTiff();
    }

} elseif (PelTiff::isValid($data)) {

    $tiff = $file = new PelTiff();
    $tiff->load($data);

} else {

    ohDearMe('Unrecognized image format!');

}

$ifd0 = $tiff->getIfd();

if ($ifd0 == null) {

    $ifd0 = new PelIfd();
    $tiff->setIfd($ifd0);

}

$desc = $ifd0->getEntry(PelTag::IMAGE_DESCRIPTION);

if ($desc == null) {

    $description = exec(
        'zenity --entry --title="Exif New Comment" --text="Enter comment" --width=800 2>&1'
        );
    $desc = new PelEntryAscii(PelTag::IMAGE_DESCRIPTION, $description);
    $ifd0->addEntry($desc);

} else {

    $entry_text = addslashes($desc->getValue());
    $description = exec('zenity --entry --title="Exif Existing Comment" --entry-text="'.
        $entry_text.'" --text="Enter comment" --width=800 2>&1');
    $desc->setValue($description);

}

file_put_contents($in, $file->getBytes()); 

```

不言而喻，但无论如何都要说——**使用风险自担！**

## 分享这篇文章