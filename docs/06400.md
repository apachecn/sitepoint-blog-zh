# Git 挂钩的乐趣和利润

> 原文：<https://www.sitepoint.com/git-hooks-fun-profit/>

当运行某些命令时，Git 会在`.git/hooks`目录中搜索合适的钩子脚本，如果找到，就会执行这些脚本。您将在那里找到一小组示例脚本(您可以通过重命名它们以删除前缀`.sample`并设置它们的可执行位来激活它们)，并且可以在 [githooks(5)手册页](https://www.kernel.org/pub/software/scm/git/docs/githooks.html)中找到钩子的完整列表。

这篇文章提出了一些可以简化开发并帮助提高效率的挂钩。

## 棉绒格子

真的没有借口，但是我承认我以前也这样做过一两次:提交语法错误的代码。但是，如果可以在提交过程中自动执行 lint 检查来防止这种情况，会怎么样呢？如果您在每次提交之前手动运行 lint 检查，那么自动化可以防止偶尔的遗忘。

下面的 shell 代码可以保存为`.git/hooks/pre-commit`(或者如果预提交钩子代码已经存在，则追加)，以便在提交时触发自动检查:

[壳]#！/bin/bash

git diff–cached–name-STATUS–diff-filter = ACMR |读取状态文件时；如果[[ "$FILE" =~ ^.+(PHP | Inc)$]]；那么
PHP-l " $ FILE " 1>/dev/null
如果[ $？-ne 0]；然后
回显“由于文件有语法错误而中止提交”> & 2
退出 1
fi
fi
done[/shell]

`git diff`报告提交之间的变化，上面的选项只返回在分段提交中添加(A)、复制(C)、修改(M)或重命名(R)的文件。扩展名为`.php`或`.inc`的文件是 lint 检查的目标，失败的检查将以非零返回代码退出脚本，从而中止提交本身。您的代码可能有问题，但至少您知道它有所有的分号。

## 拼写检查提交消息

开发人员应该一丝不苟，注重细节(至少我们在找工作时都是这样写简历的)，所以拼写错误的提交信息可能会令人尴尬。

提交消息让读者大致了解提交中做了哪些更改，因此有可能被从其他开发人员到遵从性审计员的大量读者阅读。即使你只为自己编写代码，写专业的信息也是一个需要养成的好习惯，自动化“第二双眼睛”可以帮助减少你的团队信息中的错误。

下面的代码可以另存为`.git/hooks/post-commit`(或者追加)；它调用 Aspell 并输出可疑单词列表。如果有错误，您可以通过运行`git commit --amend`立即修复提交消息。

[壳]#！/bin/bash

ASPELL=$(哪个 aspell)
if [ $？-ne 0]；然后
回显“Aspell 未安装-无法检查拼写”>2
退出
fi

AWK=$(which awk)
如果[ $？-ne 0]；然后
echo“Awk 未安装——无法过滤拼写错误”>&2
WORDS = $($ ASPELL list<" $ 1 ")
else
WORDS = $($ ASPELL list<" $ 1 " | $ AWK)！_[$0]++')
fi

if[-n " $ WORDS "]；然后
回显“在提交消息中发现可能的拼写错误:“$ WORDS>2
fi[/shell]

您还可以使用从项目源代码中提取的标识符来编译一个补充字典(可能由后签出挂钩触发)，并使用`--extra-dicts`将它传递给 Aspell 以减少误报的数量。但是解析代码和管理字典文件超出了本文的范围，所以我将它作为一个练习留给您以后去探索。

## 检查标准

我不知道有任何科学研究明确证明格式化标准减少了阅读代码时的认知摩擦。令人恼火的是，大多数标准都只针对唾手可得的果实:像这样大写，把括号放在这里而不是那里，等等。在我看来，实施良好架构设计和解决特定互操作性问题的标准更有价值。

但是如果你的同事认为一行 81 个字符是第八大罪，那么确保你的代码符合一个被采纳的格式标准可能有助于缓和政治摩擦。下面的代码可以用作提交后钩子(`.git/hooks/post-commit`)来自动检查格式冲突。

[壳]#！/bin/bash

dir = $(git rev-parse–show-top level)
电子邮件= $(git config user . email)
tmpfile = $(mktemp)

git diff–cached–name-STATUS–diff-filter = ACMR |读取状态文件时；如果[[ "$FILE" =~ ^.+(PHP | Inc)$]]；然后
phpcs–standard = Zend " $ FILE ">>$ TMPFILE
fi
完成

if[-s $ TMPFILE]；然后
mailx-s“PHPCS:编码标准违规”$ EMAIL<$ TMPFILE
fi
RM $ TMPFILE[/shell]

该脚本使用 PHP 代码嗅探器来扫描提交中的每个 PHP 文件是否违规，然后发送一封电子邮件，列出发现的违规。如果你正在使用类似于[完成任务](http://en.wikipedia.org/wiki/Getting_Things_Done)这样的时髦生产力方法来管理你的电子邮件，你可以设置一个电子邮件过滤器，将收到的电子邮件直接路由到你的待办事项文件夹，以确保你及时修复违规问题。

## 自动运行编写器

到目前为止，我们已经看到了专注于提交和开发过程的钩子，但是也有一些有用的钩子可以在 Git 工作流的其他点执行，甚至是部署。假设您在生产服务器上建立了一个远程存储库，并且您的部署方法是推送到它。

进一步假设您使用 Composer 来管理您的应用程序的依赖项，并且您只在存储库中保存您自己的代码。这意味着在推送之后，您仍然有额外的工作要做，要么手动复制依赖项，要么 ssh 到服务器来运行`composer.phar update`。

为了让您的生活更轻松，将它放在远程存储库的`.git/hooks/post-receive`文件中作为接收后挂钩将自动运行 Composer。

[壳]#！/bin/bash

DIR = $(git rev-parse–show-toplevel)

if[-e " $ DIR/composer . JSON "]；然后
if[-d " $ DIR/vendor "]；然后
composer.phar 安装
else
composer.phar 更新
fi
fi[/shell]

## 结论

在这篇文章中，我分享了几个钩子，希望它们可以简化你开发应用程序的方式，让你更有效率。请随意分享你是否在利用这个强大的功能，甚至在下面的评论中分享一些你自己的想法。

以下是一些额外的提示、技巧和灵感资源:

*   [Git–定制 Git: Git 挂钩](http://git-scm.com/book/en/Customizing-Git-Git-Hooks)
*   [Mark 的博客——缺少 Git 挂钩文档](http://longair.net/blog/2011/04/09/missing-git-hooks-documentation/)
*   [Meta Magic——管理项目、用户和全局 Git 挂钩](http://benjamin-meyer.blogspot.com/2010/06/managing-project-user-and-global-git.html)
*   [Ben Kulbertis——用 Git 和 Git 挂钩同步 MySQL 数据库](http://ben.kulbertis.org/2011/10/synchronizing-a-mysql-database-with-git-and-git-hooks/)

## 分享这篇文章