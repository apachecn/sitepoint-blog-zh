# 使用 Expect 和 Rsync 进行备份

> 原文：<https://www.sitepoint.com/backing-up-using-expect-and-rsync/>

Rsync 本身就是在本地和远程服务器之间移动数据的强大工具。当与 Expect 结合使用时，交互式会话可以在命令行上自动化，以达到非常有用的目的。

我发现最强大的应用程序是每天晚上对远程服务器进行热备份的过程。虽然运行光盘备份是为了离线存档和数据保存，但我发现大多数最有用的备份都保存在通过 Expect 和 Rsync 管理的安全远程服务器上。

expect——位于[http://expect.nist.gov/](http://expect.nist.gov/)——在用户干预不必要或不合理时，简单地支持对命令行提示的自动响应。因此，可以运行夜间备份，在出现提示时启用远程身份验证和密码输入(当“预期”时–因此得名)。在命令行上运行 man expect 可以找到更多信息。

这些文件附加有。exp 扩展名，正如您将在下面的脚本中看到的，使用顶部的 shebang 调用 Expect 程序。我将这些脚本的权限设置为只能由 root 用户执行。

rsync——发现于[http://samba.anu.edu.au/rsync/](http://samba.anu.edu.au/rsync/)是一个用于数据同步、文件复制等的工具。在命令行上运行 man rsync 可以找到更多信息。

Rsync 具有优势，因为它将在主副本完成后执行差异备份，通过检查日期戳和文件大小来备份已更改的文件，从而最大限度地减少带宽和备份所需的时间。可以使用远程备份服务器配置运行磁带或光盘备份驱动器，以便为您的生产 web 服务器进一步释放夜间资源。

结合使用时，可以为 web 服务器(以及其他系统)实施直接备份方案。

下面是正在使用的脚本。它们从备份服务器调用到目标服务器，数据存储在“热备份”中以便于访问:

脚本 1–MySQL 数据库备份

 `#!/usr/bin/expect
set timeout 19900
spawn /bin/bash
expect -re "]# "
send "rsync -avzb -e ssh [root@domain.com](mailto:root@domain.com):/var/db/mysql/dumps/ /backup/servers/comain.com/mysqlr"
expect -re "password:"
sleep 2
send "rootpasswordr"
expect -re "total size is"
expect -re "]# "
send "exitr"`

现在，为了备份域数据(网站)，运行第二个脚本:

脚本 2–域备份

 `#!/usr/bin/expect
set timeout 19900
spawn /bin/bash
expect -re "]# "
send "rsync -avzb -e ssh [root@domain.com](mailto:root@domain.com):/home/sites /backup/servers/domain.com/homer"
expect -re "password:"
sleep 2
send "rootpasswordr"
expect -re "total size is"
expect -re "]# "
send "exitr"`

为了解构这一点:

1)调用 Expect(您的路径可能不同，通过在命令行上发出‘where is Expect’来揭示它)

2)通过 ssh 向远程服务器发送命令:

rsync 选项有-a(存档，保留权限和其他项目之间的链接)，v(详细)，z(压缩)和 b(备份-向预先存在的目标文件添加~文件，一种备份版本控制)

3)ssh 密码是“预期的”，或者在其他情况下，可以将授权密钥复制到远程服务器，以绕过 ssh 的密码提示。

4)需要一个密码——通过 Expect 发送到服务器进行身份验证，然后执行操作。

5)脚本完成并关闭。

我将这些文件命名为 mysql.backup.exp 和 domains.backup.exp，并将它们输入到 cron 中，以便按每日计划运行。

虽然这不应该是唯一的备份过程，但拥有一个可用的活动服务器来快速恢复或查看备份数据会非常有用。

还可以编写附加脚本来备份关键配置文件(即/etc 目录)和服务器的其他区域。

## 分享这篇文章