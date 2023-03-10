# 了解*NIX 登录脚本

> 原文：<https://www.sitepoint.com/understanding-nix-login-scripts/>

您是否曾经遇到过这样的情况:您需要设置一个环境变量或运行一个程序来改变您的 shell 或桌面环境，但是不知道从哪里调用它最合适？

这是常见的情况。许多任务需要环境变量才能正常运行，从运行 [Debian 打包实用程序](http://manpages.ubuntu.com/manpages/precise/en/man1/dch.1.html)到[管理 IaaS](http://docs.hpcloud.com/eucalyptus/4.2.0/#shared/setting_vars_euca2ools.html) 以及这之间的一切。

有时候一个程序通常只需要在你第一次登录的时候运行一次，比如`xrandr`命令。此外，程序偶尔会被注入外壳，如 [rbenv](https://github.com/sstephenson/rbenv) 、 [rvm](https://rvm.io/rvm/install) 或 SitePoint 自己的 [envswitch](https://github.com/sitepoint/envswitch) 实用程序。

这些设置不应该从任何地方加载。有时，在判断最佳地点之前，需要考虑许多因素。

让我们看看 Debian GNU/Linux Jessie 安装中的一些常见选项，并尝试理解它们。

## /etc/profile

默认情况下，Debian 提供了`/etc/profile`，它直接用于设置`$PATH`(用于声明命令搜索路径)。

```
if [ "`id -u`" -eq 0 ]; then
    PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
else
    PATH="/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games"
fi
export PATH
```

为了方便起见，根用户(用户 ID 为 0)为其他人定义了不同的路径。这是因为[系统二进制(sbin)位置](http://www.pathname.com/fhs/pub/fhs-2.3.html#SBINSYSTEMBINARIES)是为系统管理或必须以 root 身份运行的程序保留的理想位置。根用户的游戏路径被省略了，因为除非绝对必要，否则你不应该以根用户的身份运行程序。

接下来，`/etc/profile`处理`$PS1`的设置，它用于设置主要提示字符串。除非 shell 是 Bash，否则定义的默认值是`'$ '`(或者`'#'`代表 root)。如果 shell 是 Bash，那么`/etc/bash.bashrc`将被用来处理它(以及其他事情)。我们很快就会谈到`/etc/bash.bashrc`。

所以在这一点上，我们可以推断出`/etc/profile`是在登录过程中被所有 shells 读取的(即被 login 命令读取)。`/etc/profile`没有使用更有效的 Bash 内置变量`${UID}`来确定用户 id，而是调用 ID 命令。由于 Bash 支持反斜杠转义的特殊字符，如`\u`(用户名)和`\h`(主机名)，这是许多其他 shell 所不支持的，所以没有定义花哨的 shell 提示符，而是使用了特定于 Bash 的配置。`/etc/profile`应该尽量兼容 POSIX，以便兼容用户可能为自己安装的任何 shell。

Debian GNU/Linux 经常预装 Dash，这是一个基本的 shell，只旨在实现 POSIX(和一些 Berkeley)扩展。如果我们修改/etc/profile(先做个备份！)让`PS1='$ '`行设置一个不同的值并模拟一个 Dash 登录(通过`dash -l`命令)，我们可以看到 Dash 使用我们定义的提示。然而，如果我们调用不带`-l`参数的 dash 命令，`/etc/profile`将不会被读取，Dash 将返回到默认值(顺便说一下，这是我们修改它之前的原始 PS1 值)。

关于`/etc/profile`需要注意的最后一件有趣的事情是结尾的以下片段:

```
if [ -d /etc/profile.d ]; then
    for i in /etc/profile.d/*.sh; do
        if [ -r $i ]; then
            . $i
        fi
    done
    unset i
fi
```

换句话说，匹配`/etc/profile.d/*.sh` glob 的任何可读内容都是来源。这一点很重要，因为它表明实际上并不需要直接编辑`/etc/profile`(所以请恢复您之前做的备份！).上面定义的任何变量都可以在一个单独的文件中被覆盖。这样做的一个好处是，它允许系统升级自动添加对`/etc/profile`的更改，因为 Debian 的 Apt 包管理系统通常不会修改配置文件。

## ~/.bash_profile，~/。bash_login 和~/。轮廓

`/etc/profile`的一个潜在问题是它位于系统范围的路径中。这意味着那里的更改会影响系统中的所有用户。在个人计算机上，这似乎不是什么大问题，但是对其进行更改需要 root 权限。由于这些原因，每个 Bash 用户帐户都可以创建一个文件`~/.bash_profile`、`~/.bash_login`或`~/.profile`作为源，并且使用找到的第一个文件(按列出的顺序搜索)，而忽略任何剩余的文件。其他外壳——比如 Dash——支持类似的东西，但只看`~/.profile`。这允许用户为特定于 Bash 的情况创建一个`.bash_profile`，如果她有时切换到 Dash 或其他 shell 作为她的登录 shell(比如通过`chsh -s` dash 命令)，`~/.profile`可以保留给那个用例。

人们需要记住这一点的重要性。默认的 Debian 框架目录(`/etc/skel`，用于存放要复制到新用户帐户主目录的文件和目录)包括一个`.profile`文件，但不包括`.bash_profile`或`.bash_login`文件。此外，Debian 使用 Bash 作为默认的用户 shell。因此，许多用户习惯于将他们的 Bash 登录 shell 设置放在`.profile`中。

我见过像 [RVM](https://rvm.io/rvm/install) 这样的项目的安装说明，指导用户创建一个`.bash_profile`文件，但是这很危险，因为它会破坏用户的 shell 环境！即使用户没有修改`.profile`，她也可能会利用默认的`~/.profile`功能，将`~/bin`添加到`$PATH`环境变量中。这已经行不通了。一个可能提高安全性的选择是在创建用户账户之前，在`/etc/skel`中添加`.bash_profile`作为`.bashrc`的符号链接。

如果我们看看 Debian Jessie 的默认`.profile`脚本，我们可以看到下面的片段:

```
# if running bash
if [ -n "$BASH_VERSION" ]; then
    # include .bashrc if it exists
    if [ -f "$HOME/.bashrc" ]; then
        . "$HOME/.bashrc"
    fi
fi
```

这类似于我们在`/etc/profile`中看到的，其中`/etc/bash.bashrc`是 sourced if found，shell 是 Bash。这一点的重要性将在下一节讨论。

## /etc/bash.bashrc 和~/。bashrc

启动时，Bash 将依次读取`/etc/bash.bashrc`和`~/.bashrc`，但前提是启动时是一个交互式 shell，而不是登录 shell(这通常意味着通过 xterm 启动)。这是 Bash shell 的标准行为。然而，Debian 分别从`/etc/profile`和`~/.profile`登录脚本中获取这些文件。这有效地改变了行为，使得`/etc/bash.bashrc`和`.bashrc`(如果它们存在)总是在 Bash shell 启动时被调用，而不管它们是否是登录 shell。不要指望这种行为在不同的发行版中是相同的。

`.bashrc`是添加命令别名的好地方。事实上，有些人有太多的别名，以至于他们宁愿把它们保存在一个单独的文件中。Debian 默认的`.bashrc`会寻找`~/.bash_aliases`，如果文件存在的话就会找到它，所以你可以把你所有的 Bash 别名都放在那里。如果用户愿意的话，`.bashrc`也是覆盖 shell 变量的最佳位置，比如`$PS1`或`$HISTSIZE`(要保留的命令历史数量)。Debian 的默认`.bashrc`有 100 多行，但是读起来很简单，评论也很好。顾名思义，`.bashrc`不应该来自非 Bash shells。

## ~/.xsession 和~/。xsessionrc

如果你是一个 GNU/Linux 桌面用户，通过显示管理器本地登录(相对于通过 getty 登录程序)，`/etc/profile`和`~/.profile` [不能工作](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=636108)。一些显示管理器错误地直接获取这些文件——比如 Gnome 显示管理器——但是其他的 DMs，比如 LightDM，却不这样做。幸运的是，你还有其他选择。

当一个 X Window 系统会话启动时(不管是使用虚拟终端的显示管理器还是 startx),`/etc/X11/Xsession`shell 脚本将被执行。这基本上相当于登录 shells 使用的`/etc/profile`，只适用于 X，没有源代码，而是直接执行。它也相当复杂。类似于`/etc/profile`如何从`/etc/profile.d`读取脚本，`/etc/X11/Xsession`在`/etc/X11/Xsession.d`下获取脚本。该目录中的所有脚本都以数字开头，因此脚本将按编号顺序加载。

Debian Jessie 包含了一个名为`40x11-common_xsessionrc`的文件。它所做的只是检查`~/.xsessionrc`是否可读，并且(如果是)获取它。这使得`~/.xsessionrc`成为加载环境变量或在启动时运行只适用于 X 会话的一次性实用程序(如`xrandr`或`xmodmap`)的完美地方。如果你愿意的话，你也可以用它来获得`/etc/profile`和`~/.profile`，这样在那里指定的任何环境变量也将被你的会话管理器继承(如果它们还没有被继承的话)。注意`.xsessionrc`默认不存在，所以必须创建。

如果我们继续浏览`/etc/X11/Xsession`中的文件，我们会发现`50x11-common_determine-startup`决定了要加载的会话管理器
。如果`~/.xsession`文件存在并且可执行，它将作为`99x11-common_start`的一部分被保存并在以后执行。由于`~/.xsession`是用来运行会话管理器的，当这个脚本终止时，X 会话将退出，你将返回到你的显示管理器登录界面。

和`~/.xsessionrc`一样，`~/.xsession`默认是不存在的，想要就要自己创建一个。您可以创建一个简单的`.xsession`脚本，如下所示:

```
# Start our session manager of choice.
#
exec x-session-manager
```

其中`x-session-manager`默认为通过`update-alternatives`命令配置的内容。通过这种方式，您可以很容易地将会话管理器从系统默认设置中移除，只需将`x-session-manager`替换为`/usr/bin/startxfce4`(切换到 XFCE)，其他用户帐户将完全不受影响。

当然，许多显示管理器提供了直接从登录屏幕选择公共会话管理器的能力，所以这个文件通常是不必要的。然而。xsession 提供了很大的灵活性，您可以在这里调用任何程序，而不仅仅是会话管理器。例如，您可以在 while 循环中调用 chromium 或 iceweasel 来实现基本的 kiosk 模式设置。

## ~/.bash_logout

我们在前面讨论了用户运行交互式 Bash 登录 shell 时读取的文件，但是如果您想在注销时运行一个程序呢？对于这个用例，`~/.bash_logout`是你的朋友。Debian 中包含的默认设置仅用于清空屏幕(我认为从安全角度来看这很重要)，但稍加想象，它还可以用于其他目的——例如，在你离开机器之前显示几秒钟的提醒。

主要的限制因素是`.bash_logout`只有在退出交互式 shell 时才被读取，并且不能假设它会在退出 X 会话时被加载。

## 其他选项

这大概包括了您可以使用的最常见的选项。其他选项可能存在，这取决于您的安装(比如`/etc/environment`)，但是我认为它们不太可能存在于其他平台上，也很少需要接触它们。

## 例子

那么，您应该将系统范围的环境变量放在哪里呢？如果您希望一个环境变量影响每个用户，`/etc/profile.d/somefile.sh`是一个不错的选择。然而，这假设您使用的是来源于`/etc/profile`的登录管理器。如果没有，您可以(作为管理员)添加一个脚本到`/etc/X11/Xsession.d/`来获得`/etc/profile`。

如果您希望脚本找到个人目录位置并将其添加到您的路径中，您需要考虑该目录是否会频繁移动。如果您向`.profile`添加代码来实现这一点，用户将需要注销并再次登录，以使路径反映用户会话期间的目录更改。如果您改为将代码添加到`.bashrc`，这意味着每次用户打开 xterm 时都会执行该代码——如果执行时间超过半秒左右，这可能并不理想。所以这是一个权衡利弊的问题。

如果您只想要一个用于个人登录会话的环境变量，该怎么办？如果它只涉及 X 个会话，您可以将其添加到`~/.xsessionrc`。这样做的好处是，它通常可用于通过 X 会话管理器启动的所有程序，因为它是在启动 X 会话管理器之前设置的，因此是继承的。例如，某些图形驱动程序可以通过运行以下命令来禁用 vsync

```
export vblank_mode=0
```

所以把它放在`.xsessionrc`中会影响所有的程序。

然而，如果该行被添加到`.bashrc`，只有通过 xterm 启动的程序会受到影响；通过窗口管理器启动器启动的程序将正常运行。您可以将它添加到`.profile`中，并从`.xsessionrc`中获取`.profile`，但是这样一来，即使 X 服务器没有运行，您也不必导出环境变量。

希望您现在对 Debian GNU/Linux 系统上的登录和注销脚本有了更好的理解。如果你对这些登录和注销脚本有任何特别有趣或创造性的使用，请在评论中告诉我们，以及你是如何做的。

在这个系列的下一篇文章中，我将讨论**点文件**管理选项。

## 分享这篇文章