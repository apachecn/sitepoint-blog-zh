# Git 挂钩简介

> 原文：<https://www.sitepoint.com/introduction-git-hooks/>

[Git 挂钩](http://git-scm.com/docs/githooks.html)是在特定动作之前或之后运行的简单脚本。它们对各种任务都很有用，但我发现它们主要用于客户端验证，这样可以防止简单的错误。例如，您可以测试正在提交的文件的语法，甚至可以运行测试。我已经编写了验证 Twig 语法的钩子，按照标准运行 JSHint，等等。

Git 挂钩的设计也非常简单。如果脚本是可执行的，Git 将运行这些钩子，并且 Git 将允许动作(例如，提交或推送)发生，只要钩子没有错误地退出(状态 0)。钩子可以用环境可以使用的任何语言编写。

有两种类型的挂钩:

*   客户端——这些运行在开发者的系统上
*   服务器端——它们运行在托管 Git 存储库的服务器上

本文不讨论服务器端挂钩。但是，请注意，如果您的项目位于 GitHub 之类的服务上，服务器端挂钩通常不适用。在 GitHub 上，与服务器端钩子等价的是使用服务和 web 钩子，它们可以在你的项目设置中找到。

## 钩子文件

每个存储库，包括您默认克隆的存储库，在`.git/hooks`目录中都有示例钩子:

```
git clone git@github.com:symfony/symfony.git
cd symfony
ls .git/hooks
```

在该目录中，您将看到如下内容:

```
applypatch-msg.sample
commit-msg.sample
post-update.sample
pre-applypatch.sample
pre-commit
pre-commit.sample
prepare-commit-msg.sample
pre-push.sample
pre-rebase.sample
update.sample
```

我们将关注在允许提交之前运行的`pre-commit`钩子。

## 一个钩子示例:验证 PHP 语法

我们将从一个用 Bash 编写的非常简单的钩子开始，它验证提交的 PHP 代码具有有效的语法。这是为了防止“快速”但不完整的提交发生。当然，我不鼓励几乎没有测试的“简单提交”，但这并不意味着它们不会发生。

在`.git/hooks`中，我们可以创建一个名为`pre-commit`的新文件。它必须有权限执行:

```
cd .git/hooks
touch pre-commit
chmod +x pre-commit
```

你可以用你最喜欢的编辑器开始写作。首先我们需要 shebang。我最喜欢的方法是使用`/usr/bin/env`,因为这使用了我们想要的应用程序的正确路径，而不是硬编码的和可能无效的路径。现在，我们将让它不断失败，这样我们就可以很容易地测试。

```
#!/usr/bin/env bash
# Hook that checks PHP syntax

# Override IFS so that spaces do not count as delimiters
old_ifs=$IFS
IFS=$'\n'

# Always fail
exit 1
```

PHP 有一个有用的语法验证选项:`-l`。它只有一个文件参数，所以我们必须遍历所有被修改的 PHP 文件。为了简单起见，我们假设任何提交的 PHP 文件总是以`.php`结尾。因为钩子是从存储库的根目录运行的，所以我们可以使用标准的 Git 命令来获取关于变更的信息，比如`git status`。

在`#Always fail`行上面，我们可以使用下面的代码来获取所有被修改的 PHP 文件:

```
php_files=$(git status --short | grep -E '^(A|M)' | awk '{ print $2 }' | grep -E '\.php$')
```

解释:

*   Bash 中的`php_files=`赋值是在没有分隔符的情况下完成的，但是注意引用变量需要使用`$`分隔符
*   `$()`是“获取输出”的语法。使用这个不需要引号。
*   `grep`用于检查添加的(`A`)和修改的(`M`)文件
*   这里用 Awk 打印`$2`。一个完整的`git status --short`行在开头有额外的空间和额外的数据，所以我们想删除它们。Awk 也执行自动去废。
*   `grep`再次被使用，但是现在正在检查以确保行以`.php`结束

现在我们可以用一个`for`循环来验证每个文件:

```
for file in $php_files; do
  if ! php -l "$i"; then
    exit 1
  fi
done
```

这可能看起来有点奇怪，但是`! php -l "$i"`(注意引号以避免空格问题)实际上是检查返回值`0`，而不是`true`或我们通常在其他语言中期望的任何类型的值。仅供参考，大致相同的 PHP 代码如下:

```
foreach ($php_files as $file) {
  $retval = 0;
  $escapedFile = escapeshellarg($file);
  exec('php -l ' . $escapedFile, $retval); // $retval passed in as out parameter reference
  if ($retval !== 0) {
    exit(1);
  }
}
```

为了测试这一点，我故意对`src/Symfony/Component/Finder/Glob.php`做了一个糟糕的更改，来自`git commit -m 'Test'`的输出如下:

```
PHP Parse error:  syntax error, unexpected 'namespace' (T_NAMESPACE) in src/Symfony/Component/Finder/Glob.php on line 12

Parse error: syntax error, unexpected 'namespace' (T_NAMESPACE) in src/Symfony/Component/Finder/Glob.php on line 12

Errors parsing src/Symfony/Component/Finder/Glob.php
```

我让循环尽可能早地退出整个脚本，这最终可能不是我们想要的。事实上，我们可能希望对要解决的问题进行总结，而不是继续尝试提交。任何人最终都会很容易感到沮丧，甚至可能学会使用`git commit --no-verify`来完全绕过钩子。

因此，我们不要用`php -l`退出错误，但我仍然希望让事情易于阅读:

```
for file in $php_files; do
  php_out=$(php -l "$file" 2>&1)
  if ! [ $? -eq 0 ]; then
    echo "Syntax error with ${file}:"
    echo "$php_out" | grep -E '^Parse error'
    echo
  fi
done
```

这里我们捕获了`php -l`的输出(并将标准误差输出强制为标准输出)。我们使用特殊变量`$?`(退出状态代码)和操作符`-eq`检查`php -l`的退出状态。我们声明出现了语法错误(注意字符串中变量的`${}`)。最后，我们给出了错误的相关行，以使输出更加简短(grepping for `'^Parse error'`)，并且我们给出了一个空行，以使其更加易读。

我做了两处错误的修改，尝试提交时的输出如下所示:

```
Syntax error with src/Symfony/Component/Finder/Finder.php:
Parse error: syntax error, unexpected '}' in src/Symfony/Component/Finder/Finder.php on line 118

Syntax error with src/Symfony/Component/Finder/Glob.php:
Parse error: syntax error, unexpected 'namespace' (T_NAMESPACE) in src/Symfony/Component/Finder/Glob.php on line 12
```

现在的行动方针是解决这些问题，测试，并尝试再次提交。

要完成钩子脚本，删除脚本底部的`exit 1`。尝试提交有效的 PHP 文件，它应该正常工作。

## 共享挂钩

挂钩不会随项目一起分发，也不会自动安装。所以你最好的做法是为你的钩子创建一个地方(可以在同一个库中)，并告诉你的合作者使用它们。如果你让他们容易做到这一点，他们更有可能这样做。

一个简单的方法是创建一个`hooks`目录和一个链接它们的简单安装程序`install-hooks.sh`(而不是复制):

```
#!/usr/bin/env bash
for i in hooks/*; do ln -s "${i}" ".git/hooks/${i}"; done
```

任何克隆您的项目的人都可以在克隆后简单地运行`bash install-hooks.sh`。

这还有一个好处，就是让你的钩子处于版本控制之下。

## 其他挂钩

*   `prepare-commit-msg`–如果没有提供，则提供默认提交消息。
*   `commit-msg`–提交消息验证。
*   `post-commit`–在成功提交后运行。
*   `pre-push`–验证遥控器工作后，在`git push`之前运行。它有两个参数:遥控器的名称和它的 URL。
*   `pre-rebase`–在`git rebase`之前运行。
*   `post-checkout`–在成功结帐后运行。
*   `post-merge`–成功合并后运行。

这些钩子通常和`pre-commit`一样工作，尽管它们接受参数。`post-checkout`的一个用例是确保文件总是获得适当的权限(因为 Git 只跟踪可执行文件，不跟踪可执行文件和符号链接):

```
#!/usr/bin/env bash
# Make sure only I can read this file
chmod 0600 my-file-with-secrets
```

对于`commit-msg`，你可能想要确保所有的提交消息都符合一个标准，比如*【子项目】消息*。这里有一个 PHP 版本:

```
#!/usr/bin/env php
<?php
// The message is passed as the the last argument.
$message = file_get_contents($argv[count($argv) - 1]);
if (!preg_match('/^\[[a-z_\-]+\]\s[A-Z]/', $message)) {
  echo 'Message must be of format: [subproject] Message';
  exit(1);
}
?>
```

## 结论

Git 挂钩是自动化项目工作流的强大手段。您可以验证代码、提交消息、确保环境正常，等等。你使用 Git 钩子是为了什么有趣的事情吗？请在评论中告诉我们！

## 分享这篇文章