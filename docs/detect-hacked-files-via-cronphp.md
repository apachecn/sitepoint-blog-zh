# 通过 CRON/PHP 检测被黑文件

> 原文：<https://www.sitepoint.com/detect-hacked-files-via-cronphp/>

作为一名认证的职业黑客，我寻找一个脚本来帮助我检测未经授权的文件更改。我发现了一个脚本(可能在 php.net 用户提供的笔记中),我修改了它，使它在我的“测试服务器”( Windows)和“生产服务器”( Linux)上都能很好地工作。

逻辑很简单:“为易受攻击的文件(黑客将修改这些文件以在您的服务器上执行代码)建立一个散列值数据库，定期将这些值与实际散列值进行比较，并报告添加、更改和删除的文件。”

显然，遍历服务器目录结构并提供哈希值的代码远比上面的语句复杂。我将检查生产服务器的代码。

## 数据库设置

出于安全考虑，请为此使用单独的数据库，该数据库不与任何其他数据库共享访问凭据。使用 cPanel 创建新的数据库和新用户，使用一个强密码(我推荐使用由[strongpasswordgenerator.com](http://strongpasswordgenerator.com)生成的 16 个字符的密码)和一个无害的名称，如 baseline。然后使用 PHPMyAdmin 的 SQL 创建两个表:

```
    CREATE TABLE baseline (
        file_path VARCHAR(200) NOT NULL,
        file_hash CHAR(40) NOT NULL,
        acct VARCHAR(40) NOT NULL
        PRIMARY KEY (file_path)
    );

    CREATE TABLE tested (
        tested DATETIME NOT NULL,
        account VARCHAR(40) NOT NULL
        PRIMARY KEY (tested)
    );
```

第一个表“baseline”包含一个大字段用于输入您的`path/to/filenames`，一个固定字段用于输入`file_hash`(SHA1 需要 40 个字符)和`acct`，以允许我单独监控帐户或域。将`file_path`设置为主键。

“已测试”表将保存每次扫描的`DATETIME`，帐户与基线的`acct`字段相同，因此它将允许您扫描各种帐户或域，并将它们的数据分开。

## 初始化 PHP 文件:

首先，定义几个常数

*   `PATH`是扫描开始的物理路径，通常是`DocumentRoot`。请记住不要使用 Windows 的反斜杠，因为 Apache 和 PHP 都将寻找正斜杠。
*   数据库访问常量`SERVER`(`'localhost'`)`USER``PASSWORD`和`DATABASE`。

和几个变量

*   要检查的文件扩展名数组。因为不是所有的文件都可以在服务器上执行，所以我只扫描了`.php`、`.htm`、`.html`和`.js`文件，这些文件需要在一个数组中指定。请注意，空数组将强制扫描所有文件(最安全，但使用最多的服务器资源)。
*   要排除的目录。如果你有一个包含恶意软件的目录，你真可耻！在任何情况下，如果出于任何原因需要排除某个目录，您都有机会在数组中列出它们。不要因为你只存储了图片或 pdf 文件就忽略任何目录，因为黑客也可以把他的文件放在那里！
*   初始化您将要使用的变量:需要初始化作为空变量的`$file`数组、`$report`字符串和`$acct`字符串(使用数据库表中的 account/ `acct`名称)。

## 我们开始吧！

```
<?php

//          initialize

$dir = new RecursiveDirectoryIterator(PATH);

$iter = new RecursiveIteratorIterator($dir);

while ($iter->valid())

{

    //          skip unwanted directories

    if (!$iter->isDot() && !in_array($iter->getSubPath(), $skip))

    {

        //          get specific file extensions
        if (!empty($ext))

        {

            //          PHP 5.3.4: if (in_array($iter->getExtension(), $ext))

            if (in_array(pathinfo($iter->key(), PATHINFO_EXTENSION), $ext))

            {

                $files[$iter->key()] = hash_file("sha1", $iter->key());

            }

        } else {

            //          ignore file extensions

            $files[$iter->key()] = hash_file("sha1", $iter->key());

        }

    }

    $iter->next();

}
```

我们刚刚做的是在目录(`$dir`)上使用`RecursiveIteratorIterator()`函数(一个用于遍历递归迭代器的函数)，因为它遍历目录结构。它做的第一件事是检查一个目录是否被禁止迭代，然后根据是否指定了文件扩展名进行分支。结果是一个文件的二维矩阵，(`$files`)，以`path/name.ext`为索引和相应的 SHA1 散列值。

我在这里要注意的是，注释的 echo 语句是在我的 Windows 测试服务器上使用的，没有链接到 SMTP 服务器，但是如果您需要验证正确的功能，您需要取消对它们的注释。

文件数可以由文件数组立即提供:

```
$report .= "Files has " . count($files) . " records.rn";
```

无论是输出到测试监视器还是电子邮件，都已经被赋予了第一个非空值:散列文件计数。

## 上次哈希扫描

接下来要做的是获取数据/最后一次散列扫描完成的时间，并从数据库中获取存储的路径/文件和散列集。

```
$results = mysqli_query($db,"SELECT tested FROM tested WHERE acct = '$acct'
    ORDER BY tested DESC LIMIT 1");

if ($results)

{

    while($result=mysqli_fetch_array($results))

    {

        $tested = $result['tested'];

    }

$report .= "Last tested $tested.rn";

}
```

## 将散列文件与数据库记录进行比较

到目前为止，我们只了解了当前文件计数和上次扫描的日期时间。我们要寻找的价值是识别已更改的文件，即添加、更改或删除的文件。让我们创建一个差异数组。

```
//          identify differences

if (!empty($files))

{

    $result = mysqli_query($db,"SELECT * FROM baseline");

    if (!empty($result))

    {

        foreach ($result as $value)

        {

            $baseline[$value["file_path"]] = $value["file_hash"];

            }

            $diffs = array_diff_assoc($files, $baseline);

            unset($baseline);

        }

    }

//          sort differences into Deleted, Altered and Added arrays

if (!empty($files))

{

    $results = mysqli_query($db,"SELECT file_path, file_hash FROM baseline WHERE acct = '$acct'");

    if (!empty($results))

    {

        $baseline = array();      //          from database

        $diffs = array();         //          differences between $files and $baseline

                                  //          $files is current array of file_path => file_hash

        while ($value = mysqli_fetch_array($results))

        {

            if (!array_key_exists($value["file_path"], $files))

            {

                //          Deleted files

                $diffs["Deleted"][$value["file_path"]] = $value["file_path"];

                $baseline[$value["file_path"]] = $value["file_hash"];

            } else {

                    //          Altered files

                    if ($files[$value["file_path"]] <> $value["file_hash"])

                    {

                        $diffs["Altered"][$value["file_path"]] = $value["file_path"];

                        $baseline[$value["file_path"]] = $value["file_path"];

                    } else {

                            //          Unchanged files

                            $baseline[$value["file_path"]] = $value["file_hash"];

                    }

            }

        }

        if (count($baseline) < count($files))

        {

            //          Added files

            $diffs["Added"] = array_diff_assoc($files, $baseline);

        }

        unset($baseline);

    }

}
```

当完成时，`$diffs`数组将是空的，或者它将包含在多维数组中发现的任何差异，该多维数组通过删除、改变和添加以及路径/文件和每个路径/文件的相关散列对来排序。

## 电子邮件结果

您需要将差异添加到报告和电子邮件中。

```
//          display discrepancies

if (!empty($diffs)) {

$report .= "The following discrepancies were found:rnrn";

foreach ($diffs as $status => $affected)

{

    if (is_array($affected) && !empty($affected))

    {

        ($test) ? echo "<li>" . $status . "</li>" : $report .= "* $status *rnrn";

        ($test) ? echo "<ol>" : '';
        foreach($affected as $path => $hash) $report .= " • $pathrn";

    }

}

} else {

    $report .= "File structure is intact.rn";

}

$mailed = mail('you@example.com', $acct . ' Integrity Monitor Report',$report);
```

## 更新数据库

你还没说完呢！

```
//          update database

//          clear old records

mysqli_query($db,"DELETE FROM baseline WHERE acct = '$acct'");

//          insert updated records

foreach ($files as $path => $hash)

{

    mysqli_query($db,"INSERT INTO baseline (file_path, file_hash, acct)
        VALUES ('$path','$hash', '$acct')");

}

mysqli_query($db,"INSERT INTO tested (tested, acct) VALUES (NOW(), '$acct')");

mysqli_close($db);

?>
```

在第一次通过时，数据库的基线表中将没有任何内容，所有文件都将显示为添加的，所以不要惊慌。

现在你有了代码，你把它上传到哪里？甚至不要考虑将这段代码放在你的网络空间(DocumentRoot 下),因为这将意味着任何人都可以访问你的文件并删除保存的信息以使你的哈希扫描无效。为了简单起见，把它放在你的账户的同一个目录中，这个目录包含了`public_html`(或者类似的)目录。

## 使活动

现在您有了代码，您需要定期激活它。这就是服务器的 CRON 功能的过人之处！只需使用 cPanel 创建一个新的 CRON 作业，设置午夜服务器接近空闲的时间(您不希望干扰或延迟访问者的活动，这也意味着您应该限制自己每天只扫描一次),并使用以下指令:

```
/usr/local/bin/php -q /home/account/hashscan.php
```

其中`/usr/local/bin/php`是服务器的 PHP 可执行文件的位置，`/home/account/hashscan.php`是你的`hashscan.php`脚本的路径(或者你给它取的任何名字)。

## 总结

我们创建了一个新的数据库，有两个表，一个保存日期，一个保存基线散列。我们通过识别需要跟踪的文件类型(通过扩展名)启动了每次扫描，并识别了扫描的起始点(`DocumentRoot`)。

我们扫描了文件，避开了不需要的目录，并将哈希与数据库中的基线进行了比较。结束这个过程时，我们已经更新了数据库表，并显示(在测试服务器上)或通过电子邮件发送(从生产服务器)结果。然后，我们的 CRON 作业会定期激活您的哈希扫描。

[该 ZIP 文件](https://blogs.sitepointstatic.com/examples/detect-hacked-files-via-cronphp/HashAlert.zip)包含上述`CreateTable.sql`、`hashscan.php`和`CRON.txt`文件。

这只是保护你的网站的一部分，因为它只会通知你你指定的文件类型的变化。在你做到这一步之前，你必须确保你的文件没有恶意软件(由你的主机建立的 maldet 扫描可以做到这一点，但要确保你离线保持一个干净的主副本)，确保除了你之外没有人可以通过 FTP 上传(通过使用非常强的密码)，并保持“罐装应用程序”最新(因为他们的补丁正在关闭被黑客和他们的“脚本小子”军团发现和利用的漏洞)。

综上，偏执！也许没有人想抓你，但是有*人想找“刺激”,他们正在寻找容易的猎物。你的目标是避免这种分类。*

## 分享这篇文章