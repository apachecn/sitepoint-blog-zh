# 监控文件完整性

> 原文：<https://www.sitepoint.com/monitoring-file-integrity/>

问问自己，在管理网站时，如何应对以下情况:

*   文件被无意地添加、修改或删除
*   文件被恶意添加、修改或删除
*   文件损坏

更重要的是，如果这些情况发生了，你会知道吗？如果你的答案是否定的，那么继续读下去。在本指南中，我将演示如何创建一个文件结构配置文件，它可以用来监控文件的完整性。

确定文件是否被修改的最好方法是对其内容进行哈希运算。PHP 有几个可用的散列函数，但是对于这个项目，我决定使用 [`hash_file()`函数](http://php.net/hash_file "PHP: hash_file - Manual")。它提供了各种不同的散列算法，这使得我的代码很容易在以后我决定进行修改时进行修改。

散列法应用广泛，从密码保护到 DNA 测序，无所不包。哈希算法的工作原理是将数据转换为固定大小、可重复的加密字符串。它们被设计成即使对数据进行微小的修改也会产生非常不同的结果。当两个或更多不同的数据产生相同的结果字符串时，这被称为“冲突”每种哈希算法的强度可以通过其速度和冲突概率来衡量。

在我的例子中，我将使用 SHA-1 算法，因为它速度快，发生冲突的概率低，并且已经被广泛使用和测试。当然，欢迎您研究其他算法，并使用任何您喜欢的算法。

一旦获得了文件的散列，就可以存储它以供以后比较。如果稍后对文件进行哈希处理时没有返回与之前相同的哈希字符串，那么我们知道文件已经被更改了。

## 数据库ˌ资料库

首先，我们需要设计一个基本的表来存储文件的哈希值。我将使用以下模式:

```
CREATE TABLE integrity_hashes (
    file_path VARCHAR(200) NOT NULL,
    file_hash CHAR(40) NOT NULL,
    PRIMARY KEY (file_path)
);
```

存储文件在服务器上的位置，因为两个文件不能在文件系统中占据相同的位置，所以这个值总是唯一的，所以它是我们的主文件。我已经将它的最大长度指定为 200 个字符，这应该允许一些冗长的文件路径。`file_hash`存储一个文件的哈希值，它将是阿沙-1 的 40 个字符的十六进制字符串。

## 收集文件

下一步是构建文件结构的概要文件。我们定义开始收集文件的路径，并递归地遍历每个目录，直到我们覆盖了文件系统的整个分支，并选择性地排除某些目录或文件扩展名。当我们遍历文件树时，我们收集我们需要的散列，然后存储在数据库中或用于比较。

PHP 提供了几种导航文件树的方法；为了简单起见，我将使用 [`RecursiveDirectoryIterator`类](http://php.net/recursivedirectoryiterator "PHP: RecursiveDirectoryIterator - Manual")。

```
<?php
define("PATH", "/var/www/");
$files = array();

// extensions to fetch, an empty array will return all extensions
$ext = array("php");

// directories to ignore, an empty array will check all directories
$skip = array("logs", "logs/traffic");

// build profile
$dir = new RecursiveDirectoryIterator(PATH);
$iter = new RecursiveIteratorIterator($dir);
while ($iter->valid()) {
    // skip unwanted directories
    if (!$iter->isDot() && !in_array($iter->getSubPath(), $skip)) {
        // get specific file extensions
        if (!empty($ext)) {
            // PHP 5.3.4: if (in_array($iter->getExtension(), $ext)) {
            if (in_array(pathinfo($iter->key(), PATHINFO_EXTENSION), $ext)) {
                $files[$iter->key()] = hash_file("sha1", $iter->key());
            }
        }
        else {
            // ignore file extensions
            $files[$iter->key()] = hash_file("sha1", $iter->key());
        }
    }
    $iter->next();
}
```

注意我是如何在`$skip`数组中引用同一个文件夹`logs`两次的。仅仅因为我选择忽略一个特定的目录，并不意味着迭代器也会忽略所有的子目录，这可能是有用的，也可能是令人讨厌的，取决于你的需要。

`RecursiveDirectoryIterator`类给了我们几个方法:

*   检查我们是否在使用一个有效的文件
*   `isDot()`确定目录是“`.`还是“`..`
*   `getSubPath()`返回文件指针当前所在的文件夹名
*   `key()`返回完整路径和文件名
*   `next()`再次开始循环

还有几个方法可以使用，但上面列出的大多数方法确实是我们手头任务所需要的，尽管 PHP 5.3.4 中已经添加了返回文件扩展名的`getExtension()`方法。如果你的 PHP 版本支持它，你可以用它来过滤掉不想要的条目，而不是像我一样用 [`pathinfo()`](http://php.net/pathinfo "PHP: pathinfo - Manual") 。

执行时，代码应该用类似于下面的结果填充`$files`数组:

```
Array
(
    [/var/www/test.php] => b6b7c28e513dac784925665b54088045cf9cbcd3
    [/var/www/sub/hello.php] => a5d5b61aa8a61b7d9d765e1daf971a9a578f1cfa
    [/var/www/sub/world.php] => da39a3ee5e6b4b0d3255bfef95601890afd80709
)
```

一旦我们建立了档案，更新数据库就很容易了。

```
<?php
$db = new PDO("mysql:host=" . DB_HOST . ";dbname=" . DB_NAME,
    DB_USER, DB_PASSWORD);

// clear old records
$db->query("TRUNCATE integrity_hashes");

// insert updated records
$sql = "INSERT INTO integrity_hashes (file_path, file_hash) VALUES (:path, :hash)";
$sth = $db->prepare($sql);
$sth->bindParam(":path", $path);
$sth->bindParam(":hash", $hash);
foreach ($files as $path => $hash) {
    $sth->execute();
}
```

## 检查差异

您现在知道了如何构建目录结构的新概要文件，以及如何更新数据库中的记录。下一步是将它整合到某种现实世界的应用程序中，比如带有电子邮件通知、管理界面或任何您喜欢的东西的 cron 作业。

如果您只想收集一个已更改文件的列表，并且不关心它们是如何更改的，那么最简单的方法是将数据从数据库中提取到一个类似于`$files`的数组中，然后使用 PHP 的 [`array_diff_assoc()`函数](http://php.net/array_diff_assoc "PHP: array_diff_assoc - Manual")来剔除不相关的内容。

```
<?php
// non-specific check for discrepancies
if (!empty($files)) {
    $result = $db->query("SELECT * FROM integrity_hashes")->fetchAll();
    if (!empty($result)) {
        foreach ($result as $value) {
            $tmp[$value["file_path"]] = $value["file_hash"];
        }
        $diffs = array_diff_assoc($files, $tmp);
        unset($tmp);
    }
}
```

在这个例子中，`$diffs`将被任何发现的差异填充，或者如果文件结构是完整的，它将是一个空数组。与`array_diff()`不同，`array_diff_assoc()`将在比较中使用关键字，这在发生冲突时对我们很重要，比如两个空文件有相同的哈希值。

如果你想更进一步，你可以加入一些简单的逻辑来确定一个文件是如何被影响的，它是否被删除、修改或添加。

```
<?php
// specific check for discrepancies
if (!empty($files)) {
    $result = $db->query("SELECT * FROM integrity_hashes")->fetchAll();
    if (!empty($result)) {
        $diffs = array();
        $tmp = array();
        foreach ($result as $value) {
            if (!array_key_exists($value["file_path"], $files)) {
                $diffs["del"][$value["file_path"]] = $value["file_hash"];
                $tmp[$value["file_path"]] = $value["file_hash"];
            }
            else {
                if ($files[$value["file_path"]] != $value["file_hash"]) {
                    $diffs["alt"][$value["file_path"]] = $files[$value["file_path"]];
                    $tmp[$value["file_path"]] = $files[$value["file_path"]];
                }
                else {
                    // unchanged
                    $tmp[$value["file_path"]] = $value["file_hash"];
                }
            }
        }
        if (count($tmp) < count($files)) {
            $diffs["add"] = array_diff_assoc($files, $tmp);
        }
        unset($tmp);
    }
}
```

当我们遍历数据库中的结果时，我们进行了几次检查。首先， [`array_key_exists()`](http://php.net/array_key_exists "PHP: array_key_exists - Manual") 用于检查数据库中的文件路径是否存在于`$files`中，如果不存在，则该文件一定已被删除。第二，如果文件存在，但哈希值不匹配，则该文件一定已经被更改或未被更改。我们将每个检查存储到一个名为`$tmp`的临时数组中，最后，如果`$files`的数量大于数据库中的数量，那么我们知道那些剩余的未检查文件已经被添加。

完成后，`$diffs`要么是一个空数组，要么包含多维数组形式的任何差异，可能如下所示:

```
Array
(
    [alt] => Array
        (
            [/var/www/test.php] => eae71874e2277a5bc77176db14ac14bf28465ec3
            [/var/www/sub/hello.php] => a5d5b61aa8a61b7d9d765e1daf971a9a578f1cfa
        )

    [add] => Array
        (
            [/var/www/sub/world.php] => da39a3ee5e6b4b0d3255bfef95601890afd80709
        )

)
```

为了以更加用户友好的格式显示结果，对于管理界面等，您可以循环遍历结果，并以项目符号列表的形式输出它们。

```
<?php
// display discrepancies
if (!empty($diffs)) {
    echo "<p>The following discrepancies were found:</p>";
    echo "<ul>";
    foreach ($diffs as $status => $affected) {
        if (is_array($affected) && !empty($affected)) {
            echo "<li>" . $status . "</li>";
            echo "<ol>";
            foreach($affected as $path => $hash) {
                echo "<li>" . $path . "</li>";
            }
            echo "</ol>";
        }
    }
    echo "</ul>";
}
else {
    echo "<p>File structure is intact.</p>";
}
```

此时，您可以提供一个链接，该链接触发一个用新文件结构更新数据库的操作，在这种情况下，您可以选择将`$files`存储在[会话变量](http://php.net/session "PHP: Sessions - Manual")中，或者如果您不同意这些差异，您可以按照您认为合适的方式来解决它们。

## 摘要

希望本指南能让您对监控文件完整性有更好的理解。在你的网站上有这样的东西是一个无价的安全措施，你可以放心地知道你的文件完全按照你的意图。当然，不要忘记定期备份。你知道…以防万一。

<small>图像通过半匹配/ [快门](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")</small>

## 分享这篇文章