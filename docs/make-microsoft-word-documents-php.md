# 如何用 PHP 制作 Microsoft Word 文档

> 原文：<https://www.sitepoint.com/make-microsoft-word-documents-php/>

正如我在上一篇文章中指出的, [PHP 和 WMI——用 PHP](https://www.sitepoint.com/php-wmi-dig-deep-windows-php/) 深入研究 Windows，我们确实生活在一个 PHP 开发人员不得不不时与 Windows 操作系统打交道的世界中。WMI (Windows 管理界面)就是一个这样的例子，微软 Office Interop 是另一个更重要、更常用的例子。

在本文中，我们将看到 Word 和 PHP 之间的简单集成:使用 PHP(及其互操作扩展)基于 HTML 表单中的输入生成 Microsoft Word 文档。

### 准备

首先，请确保在您的 Windows 开发机器上已经设置了一个典型的 WAMP 环境。由于 Interop 纯粹是一个 Windows 特性，我们将不得不在 Windows 下托管 Apache 和 PHP。在这种情况下，我使用的是非常容易安装和配置的 EasyPHP 14.1。

接下来，我们必须安装微软办公软件。它的版本不是那么关键。我使用的是 Office 2013 Pro，但任何 2007 年以后的 Office 版本都可以使用。

然后，我们必须确保安装了开发互操作应用程序的库(称为 PIA，主要互操作程序集)。为了确定这一点，我们可以打开 Windows 资源管理器并导航到:`<Windows Directory>\assembly`，我们将看到一堆已安装的 pia:

![](img/52f50a7f7150fc0b9de8a81b752ecebc.png)

我们看到一个`Microsoft.Office.Interop.Word`条目(在快照中带下划线)。这将是我们在本演示中使用的 PIA。请特别注意它的“程序集名称”、“版本”和“公钥标记”。这些很快就会在我们的 PHP 脚本中使用。

在这个目录中，我们还可以看到其他可用于编程的 pia(包括整个 Office 系列)(不仅适用于 PHP，还适用于 VB.net、C#等)

如果 PIAs 列表没有包含`Microsoft.Office.Interop`的整个包，我们要么重新安装我们的 Office，包含 PIA 特性；或者我们必须手动从微软下载软件包并安装它。详细说明请咨询[本 MSDN 页面](http://msdn.microsoft.com/en-us/library/kh3965hw.aspx)。

**注意:**只有 Microsoft Office 2010 PIA 可再发行软件可供下载和安装。这个包中的 PIA 版本是 14.0.0。版本 15 仅随 Office 2013 安装一起提供。

最后，我们必须启用`php.ini`文件中的 PHP 扩展`php_com_dotnet.dll`并重启服务器。

现在我们可以继续编程了。

### HTML 表单

由于本演示的重点是后端处理，我们将使用简单的 HTML 表单创建一个简单的前端，如下图所示:

![](img/9c2db2cfaaddab28232a57934f797c38.png)

我们有一个“姓名”的文本字段，“性别”的单选按钮组，“年龄”的范围控件和“消息”的文本区域；最后，当然，还有一个“提交”按钮。

将该文件保存为虚拟主机根目录下的一个目录中的“index.html ”,以便我们可以使用类似`http://test/test/interop`的 URI 来访问它。

### 后端

后端 PHP 文件是我们讨论的焦点。我先列出这个文件的代码，然后一步步解释。

```
<?php

$inputs = $_POST;
$inputs['printdate']=''; 
// A dummy value to avoid a PHP notice as we don't have "printdate" in the POST variables. 

$assembly = 'Microsoft.Office.Interop.Word, Version=15.0.0.0, Culture=neutral, PublicKeyToken=71e9bce111e9429c';
$class = 'Microsoft.Office.Interop.Word.ApplicationClass';

$w = new DOTNET($assembly, $class);
$w->visible = true;

$fn = __DIR__ . '\\template.docx';

$d = $w->Documents->Open($fn);

echo "Document opened.<br><hr>";

$flds = $d->Fields;
$count = $flds->Count;
echo "There are $count fields in this document.<br>";
echo "<ul>";
$mapping = setupfields();

foreach ($flds as $index => $f)
{
    $f->Select();
    $key = $mapping[$index];
    $value = $inputs[$key];
    if ($key == 'gender')
    {
        if ($value == 'm')
            $value = 'Mr.';
        else
            $value = 'Ms.';
    }

    if($key=='printdate')
        $value=  date ('Y-m-d H:i:s');

    $w->Selection->TypeText($value);
    echo "<li>Mappig field $index: $key with value $value</li>";
}
echo "</ul>";

echo "Mapping done!<br><hr>";
echo "Printing. Please wait...<br>";

$d->PrintOut();
sleep(3);
echo "Done!";

$w->Quit(false);
$w=null;

function setupfields()
{
    $mapping = array();
    $mapping[0] = 'gender';
    $mapping[1] = 'name';
    $mapping[2] = 'age';
    $mapping[3] = 'msg';
    $mapping[4] = 'printdate';

    return $mapping;
}
```

在设置了`$inputs`变量来保存从我们的表单中提交的值，并为`printdate`创建了一个虚拟值之后——我们稍后将讨论为什么我们需要这样做——我们遇到了这四个关键行:

```
$assembly = 'Microsoft.Office.Interop.Word, Version=15.0.0.0, Culture=neutral, PublicKeyToken=71e9bce111e9429c';
$class = 'Microsoft.Office.Interop.Word.ApplicationClass';

$w = new DOTNET($assembly, $class);
$w->visible = true;
```

PHP 中的 COM 操作需要在“**程序集**中实例化一个“**类**”。在我们的情况下，我们要用文字来操作。如果我们回想一下我们展示的第一个屏幕截图，我们将能够构建单词 PIA 的完整签名:

*   “名称”、“版本”、“公钥令牌”均取自我们浏览到“`c:\Windows\assembly`”时显示的信息。
*   “文化”永远是`neutrual`。

我们要调用的类总是程序集的名称加上“`.ApplicationClass`”。

有了这两个参数集，我们将能够实例化一个 Word 对象。

这个对象可以留在背景中，或者我们可以通过将它的`visible`属性设置为`true`来将它带到前台。

接下来，我们打开要处理的文档，并将“document”实例赋给一个`$d`变量。

在该文档中，为了基于 HTML 表单的输入创建内容，我们有几个选项。

最不利的方式是将 PHP 中的内容全部硬编码，然后输出到 Word 文档中。我强烈反对这样做，原因如下:

1.  不会有任何灵活性。输出中的任何变化都需要修改 PHP 脚本。
2.  它违背了控制和表现的分离。
3.  如果我们要对文档内容应用样式(对齐、字体、样式等)，将会大大增加代码的行数。以编程方式改变样式太麻烦了。

另一种方法是进行“搜索-替换”。PHP 在这方面有强大的内置功能。我们可以创建一个 Word 文档，在要替换的占位符内容周围放置一些特殊的分隔符。例如，我们可以创建一个包含如下内容的文档:

`{{name}}`

在 PHP 中，我们可以简单地用从表单提交中获取的“Name”值替换它。

这很简单，避免了第一种选择的所有缺点。我们只需要找到正确的分隔符，在这种情况下，我们更像是在做一个模板渲染，只不过现在使用的模板是一个 Word 文档。

第三个选项是我的推荐，是 Word 中的高级主题。我们将使用字段来表示占位符，并且在我们的 PHP 代码中，我们将使用各自的表单值直接更新字段。

这种方法灵活、快速，并且符合 Word 的最佳实践。它还避免了在文档中进行全文搜索，这有助于提高性能。请注意，这个选项也有缺点。

Word 自问世以来，从未支持过字段的命名索引。即使我们为在 Word 文档中创建的字段提供了名称，我们仍然必须使用数字下标来访问每个字段。这也解释了为什么我们必须使用一个专用函数(`setupfields`)在字段索引和表单字段名称之间进行手动映射。

要了解如何在 Word 文档中插入字段(点击[此处](http://www.rsywx.net/download/template.docx)获取现成版本)，请查阅相关的 Word 帮助主题和手册。对于这个演示，我们有一个包含 5 个`MERGEFIELD`字段的文档。此外，为了方便访问，我们将文档放在 PHP 脚本所在的目录中。

请注意，字段`printdate`没有对应的表单字段。这就是为什么我们在`$inputs`数组中添加了一个虚拟的`printdate`键。没有这个，脚本仍然可以运行，但是会有通知说索引`printdate`没有出现在`$inputs`数组中。

用表单值更新字段后，我们将使用以下内容打印文档:

```
$d->PrintOut();
```

`PrintOut`方法有几个可选参数，我们使用它最简单的形式。这将打印一份到默认打印机连接到我们的 Windows 机器。

我们也可以选择使用`PrintPreview`来查看输出，然后再决定打印文档。在纯自动化的环境中，我们当然会用`PrintOut`来代替。

在退出 Word 应用程序之前，我们必须等待几秒钟，因为打印作业需要一些时间来完全假脱机。如果没有`delay(3)`，`$w->Quit`会立即执行，打印作业也会被终止。

最后，我们调用`$w->Quit(false)`来关闭 PHP 脚本调用的 Word 应用程序。这里提供的唯一参数是指定我们是否要在退出前保存更改。我们确实对文档进行了更改，但是我们真的不想保存它们，因为我们想为其他用户的输入保留一个干净的模板。

完成代码后，我们可以加载表单页面，输入一些值并提交表单。下图显示了 PHP 脚本的输出以及更新后的 Word 文档:

![](img/67ad9febfb7f624c0f15d1a23e250e58.png)


### 提高编码速度并更多地了解 PIA

PHP 是一种弱类型语言。COM 对象的类型为`Object`。在我们的 PHP 编码过程中，没有办法从一个对象中获得有意义的代码洞察力，不管它是一个 Word 应用程序、一个文档还是一个字段。我们不知道它有什么属性，也不知道它支持什么方法。

这将大大减缓我们的发展速度。为了使它更快，我建议我们先用 C#开发函数，然后将代码移植回 PHP。我推荐一个名为“#develop”的免费 C# IDE，可以在这里下载。比起 VS 系列我更喜欢这个，因为#develop 更小，更干净，更快。

C#代码迁移到 PHP 一点也不可怕。让我给你看几行 C#代码:

```
Word.Application w=new Word.Application();
w.Visible=true;

String path=Application.StartupPath+"\\template.docx";

Word.Document d=w.Documents.Open(path) as Word.Document;

Word.Fields flds=d.Fields;
int len=flds.Count;

foreach (Word.Field f in flds)
{
	f.Select();
	int i=f.Index;
	w.Selection.TypeText("...");
}
```

我们可以看到 C#代码与我们之前展示的 PHP 代码几乎相同。C#是强类型的，所以我们看到一些类型转换语句，我们必须显式地给变量一个类型。

有了给定的变量类型，我们可以享受代码洞察力和代码完成，所以开发速度快得多。

另一种加快 PHP 开发的方法是使用 Word 宏。我们执行我们需要做的相同的动作，并用宏记录它们。宏是用 Visual Basic 编写的，也可以很容易地转换成 PHP。

最重要的是，[微软关于 Office PIA](http://msdn.microsoft.com/en-us/library/15s06t57.aspx) 的官方文档，尤其是每个 Office 应用的命名空间文档，永远是最详细的参考资料。最常用的三种应用是:

*   excel 2013:[http://msdn . Microsoft . com/en-us/library/Microsoft . office . interop . excel(v = office . 15)。aspx](http://msdn.microsoft.com/en-us/library/microsoft.office.interop.excel(v=office.15).aspx)
*   word 2013:[http://msdn . Microsoft . com/en-us/library/Microsoft . office . interop . word(v = office . 15)。aspx](http://msdn.microsoft.com/en-us/library/microsoft.office.interop.word(v=office.15).aspx)
*   PowerPoint 2013:[http://msdn . Microsoft . com/en-us/library/Microsoft . office . interop . PowerPoint(v = office . 15)。aspx](http://msdn.microsoft.com/en-us/library/microsoft.office.interop.powerpoint(v=office.15).aspx)

### 结论

在本文中，我们演示了如何使用 PHP COM 库和 Microsoft Office Interop 功能填充 Word 文档。

Windows 和 Office 在日常生活中被广泛使用。对于任何 PHP + Windows 程序员来说，掌握 Office/Windows 和 PHP 的强大功能是必不可少的。

有了 PHP 的 COM 扩展，掌握这种组合的大门就打开了。

如果你对这个领域的编程感兴趣，请留下你的评论，我们会考虑有更多关于这个主题的文章。我期待看到更多使用这种方法开发的真实应用程序。

## 分享这篇文章