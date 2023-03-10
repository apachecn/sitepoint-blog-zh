# 通过 CRON/PHP: SuperScan 快速检测被黑文件

> 原文：<https://www.sitepoint.com/quickly-detect-hacked-files-via-cronphp-superscan/>

![protecting your server](img/745f4f05d3cdf957c13ac80afe093c86.png)

作为一名认证的道德黑客，我完全知道预防是防止黑客的最佳策略，但是，如果有人突破，你越早知道，你就能越快采取行动来限制损害。

不久前，[我提交了一个名为 *hashscan*](https://www.sitepoint.com/detect-hacked-files-via-cronphp/) 的脚本，用于跟踪站点变化。该脚本通过每日 CRON 执行，读取指定目录(例如，服务器上帐户的`public_html`目录)的文件，生成哈希(针对具有特定文件扩展名的文件)，并将它们与数据库中存储的上次扫描的哈希进行比较。这是提醒网站所有者注意被黑客添加、修改或删除的文件的好方法。

在本文中，我将展示该脚本的更新版本，名为 *SuperScan* 。

## 超级扫描的好处

主要好处是 SuperScan 将报告帐户中文件的任何更改，无论文件更改是添加、更改还是删除。SuperScan 的设计不是为了压倒网站管理员。它仅提供自上次扫描以来的更改报告(默认为一小时，但可以通过 CRON 进行配置)和摘要报告(默认为每天一次，但同样可以通过 CRON 进行配置)。

因为扫描 1500 文件帐户需要大约 0.75 秒，所以 SuperScan 可以频繁运行，而不会影响服务器性能。

为了支持取证调查，文件的最后修改日期和时间以及最近一次扫描的哈希值(以及先前对已修改文件的扫描)都保存在数据库中。

不需要更改 scanner 文件，因为所有变量都在所需的配置脚本中设置。它在配置脚本中，您可以选择要扫描的特定(或所有)文件扩展名，或者如果所有文件扩展名都要忽略。此外，您可以指定扫描程序不扫描的目录。

虽然 SuperScan 文件可以在 web 空间内测试，但我建议通过 CRON 将它移到 web 空间之外用于生产，以防止偶然的黑客攻击。

最后，一个奇怪的额外好处是,(无扩展)error_log 文件中的变化被捕获，并可以引导网站管理员注意测试过程中遗漏的编码问题。

## 超级扫描逻辑

超级扫描的逻辑流程是:

*   读取数据库中文件的基线信息
*   扫描系统文件并计算它们的哈希值
*   将基线文件与当前文件进行比较，以确定要生成的已更改文件:
    *   添加文件的列表
    *   已更改文件的列表和
    *   已删除文件的列表
*   处理每个已更改的文件列表(更新数据库)
*   准备并发送报告(如果需要)。

## 数据库、变量和工作数组

我没有在这里用细节来烦你，而是在所有的脚本中插入了注释。

因此，简而言之，有三个数据库表:

*   **基线**:这包含`$file_path`，文件的散列和文件的最后修改日期和时间。我还添加了帐户，以便多个帐户可以使用一个数据库)
*   **history** :记录每次扫描中检测到的每一个变化——或没有变化。
*   **已扫描**:记录扫描汇总日期和时间，以及变更数量和关联账户。

**警告#1** :
我不能强调由`configure.php`设置的`$testing`变量会触发大量的输出，所以它必须**只能**用于测试，并且**永远不要**用于 CRON 作业！

**警告#2** :
因为路径/to/file 被用作了一个键，所以它必须是唯一的。这意味着多个账户不能**永远不能**扫描相同的文件。

**警告#3** :
此外，Windows 服务器会使用反斜杠，这种反斜杠会立即变成斜杠，因为它们会导致字符在数据库中丢失。此外，在文件名中使用撇号会导致数据库查询出现问题。

工作数组被设计成利用 PHP 的函数，这些函数访问键(`$file_path`)；这也是文件结构迭代器，所以**从不**改变`$iter‐>key()`。

`$baseline`在扫描开始前被读取，`$current`是扫描的结果，`$added`、`$altered`和`$deleted`数组累加来自`$baseline`的变化，并用于更新下一次扫描的`$baseline`。

## 文件

`superscan.zip`文件包含 7 个文件:

*   `CreateTables.sql`，可用于设置您的表格
*   `ReadMe.txt`，提供了超级扫描脚本的概述
*   `scanner.php`，需要`configure.php`和`scandb.php`的扫描脚本(它连接到你的 MySQL 服务器并返回`$scandb`句柄)
*   `reporter.php`，它将通过 CRON 提供最近扫描的摘要
*   `CRON.txt`，它为`scanner.php`和`reporter.php`提供了示例 CRON 指令

## 清除

检测到文件更改时会创建`$report`，如果不是“负面报告”，则会存储并通过电子邮件发送当您没有收到变更报告时，汇总报告用于“温暖、模糊的感觉”。

在清理过程中，将自动清除历史记录和已扫描表中超过 30 天的记录，以防止数据库无限增长，并销毁大型数组(重置为空)和关闭数据库。

## 摘要

我相信 SuperScan 是对我之前工作的一个巨大改进，是一个有价值的升级。它提供了频繁的文件更改通知，而“负面报告”不会用不必要的“未更改”通知淹没网站管理员。

[从 GitHub 下载超级扫描代码](https://github.com/dklynn/SuperScan)

## 确认

SuperScan 是由 Han Wechgelaer (NL)提出的，他在电子邮件中建议扩展我早期的 hashscan 脚本，以捕获帐户文件的更改历史，并进行更频繁的评估和添加每日总结。

韩好心地提供了一份他在这个项目上的开始，在我们之间，这个项目发展成了 SuperScan。如果没有韩的鼓励和帮助，SuperScan 永远也不会起步，当然也不会成为今天这样出色的工具。

我很想知道你如何找到这个脚本，或者你是否有任何问题或反馈。

## 分享这篇文章