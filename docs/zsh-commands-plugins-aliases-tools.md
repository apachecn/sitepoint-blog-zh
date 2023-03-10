# 75 个 Zsh 命令、插件、别名和工具

> 原文：<https://www.sitepoint.com/zsh-commands-plugins-aliases-tools/>

我每天花很多时间在终端上，我选择的 shell 是[Zsh](https://en.wikipedia.org/wiki/Z_shell)——一个高度可定制的 Unix shell，它包含了一些非常强大的功能。由于我是一个[懒惰的开发人员](https://dev.to/drm317/the-lazy-developer-5goe) <sup>TM</sup> ，我总是在寻找减少打字和自动化所有事情的方法。幸运的是，这是 Zsh 非常擅长的事情。

在这篇文章中，我将与你分享 75 个命令、插件、别名和工具，希望它们能帮你节省一些键盘输入，让你在日常工作中更有效率。

如果你的机器上没有安装 Zsh，那么看看这篇文章，我会告诉你如何安装和运行。

## Zsh 可以做的 15 件事

Zsh 与 Bash 共享了许多方便的特性。以下没有一个是 Zsh 独有的，但是知道这些还是很有好处的。我鼓励您开始使用命令行来执行如下所列的操作。起初，这看起来比使用 GUI 需要更多的工作，但是一旦你掌握了窍门，你就再也不会回头看了。

*   在文件系统的任何地方输入`cd`将会直接把您带回主目录。
*   输入`!!`将调出最后一条命令。如果一个命令失败，这是很方便的，因为它需要管理员权限。在这种情况下，您可以键入`sudo !!`。
*   你可以使用`&&`来链接多个命令。比如`mkdir project && cd project && npm init -y`。
*   使用`||`可以进行条件执行。比如`git commit -m "whatever..." || echo "Commit failed"`。
*   使用带有`mkdir`命令的`-p`开关将允许您根据需要创建父目录。使用大括号扩展可以减少重复。例如，`mkdir -p articles/jim/sitepoint/article{1,2,3}`。
*   在每个命令的基础上设置[环境变量](https://opensource.com/article/19/8/what-are-environment-variables)，比如:`NODE_DEBUG=myapp node index.js`。或者，像这样基于每个会话:`export NODE_DEBUG=myapp`。你可以通过输入`echo $<variable-name>`来检查它的设置。
*   将一个命令的输出通过管道传输到另一个命令中。例如，`cat /var/log/kern.log | less`使长日志可读，或者`history | grep ssh`搜索任何包含“ssh”的历史条目。
*   您可以从终端打开编辑器中的文件。比如`nano ~/.zshrc`(纳米)`subl ~/.zshrc`(崇高文字)`code ~/.zshrc` (VS 代码)。如果文件不存在，在编辑器中按*保存*会创建一个文件。
*   导航是一项需要掌握的重要技能。不要只依赖你的箭头键。比如`Ctrl` + `a`会带你到一行的开头。
*   而`Ctrl` + `e`会带你走到最后。
*   可以用`Ctrl` + `w`删除一个单词(向后)。
*   `Ctrl` + `u`将删除从光标到行首的所有内容。
*   `Ctrl` + `k`将清除从光标到行尾的所有内容。这后三个可以用`Ctrl` + `y`撤销。
*   可以用`Ctrl` + `Shift` + `c`复制文本。这比右击选择*复制*优雅多了。
*   反之，可以用`Ctrl` + `shift` + `v`粘贴复制的文本。

试着记住这些组合键。你会惊讶于它们经常派上用场。

## 15 个自定义别名，提高您的工作效率

别名是常规命令的终端快捷方式。您可以将它们添加到您的`~/.zshrc`文件中，然后重新加载您的终端(使用`source ~/.zshrc`)以使它们生效。

声明(简单)别名的语法如下:

```
alias [alias-name]='[command]' 
```

别名对于经常使用的命令、长命令或具有难以记住的语法的命令非常有用。以下是我经常使用的一些方法:

*   一个`myip`别名，将你当前的公共 IP 地址打印到终端:`alias myip='curl http://ipecho.net/plain; echo'`。
*   一个用于输出 Linux 发行版信息的别名:`alias distro='cat /etc/*-release'`。
*   一个`reload`别名，因为我似乎永远记不起如何重新加载我的终端:`alias reload='source ~/.zshrc'`。
*   一个`undo-git-reset`别名:`alias undo-git-reset-head="git reset 'HEAD@{1}'"`。这将恢复运行`git reset HEAD~`的效果。
*   更新包列表的别名:`alias sapu='sudo apt-get update'`。
*   使用`sudo` : `alias ffs='sudo !!'`重新运行之前命令的别名。
*   因为我很懒，我给`yarn`命令使用了`y`的别名:`alias y='yarn'`。这意味着我可以克隆一个 repo，然后只需键入`y`来获取所有的依赖项。我从斯科特·托林斯基那里学到了这个关于[句法](https://syntax.fm/)的问题。
*   不是我使用的一个，但是这个别名在重新安装一个项目的依赖项:`alias yolo='rm -rf node_modules/ && rm package-lock.json && yarn install'`之前，吹走了`node_modules`文件夹并删除了`package-lock.json`文件。你可能知道，yolo 代表*Y*ou*O*only*L*e*O*nce。
*   打开我的`.zshrc`文件进行编辑的别名:`alias zshconfig='subl $HOME/.zshrc'`。
*   更新 Ruby 版本列表的别名 [rbenv](https://github.com/rbenv/rbenv) 可以安装:`alias update-available-rubies='cd ~/.rbenv/plugins/ruby-build && git pull'`
*   启动当前目录中的服务器的别名(不需要 npm 包):`alias server='python -m SimpleHTTPServer 8000'`。
*   您还可以创建一个别名来在浏览器中打开文档:`alias npmhelp='firefox https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/npm'`。
*   将命令输出传送到`less` : `alias -g L='| less'`的全局别名。你可以这样使用它:`cat production.log L`。
*   将命令输出传送到`grep` : `alias -g G='| grep'`的全局别名。你可以这样使用它:`history G ssh`。
*   您也可以使用函数来创建别名。下面的代码(摘自[这里的](https://dev.to/aspittel/my-terminal-setup-iterm2--zsh--30lm))创建了一个添加、提交和推送代码到 GitHub 的别名:
    `bash
    function acp() {
    git add .
    git commit -m "$1"
    git push
    }` 

网上有很多地方可以找到更多关于别名的想法。比如[这个黑客新闻讨论](https://news.ycombinator.com/item?id=9869231)，或者这个关于[命令行生产力与 Zsh](https://blog.lftechnology.com/command-line-productivity-with-zsh-aliases-28b7cebfdff9) 的帖子。

## 你可以用(天啊)Zsh 做的 15 件很酷的事情

Oh My Zsh 是一个社区驱动的框架，用于管理您的 Zsh 配置，捆绑了数千个有用的功能、助手、插件和主题。如果你打算让 Z shell 成为你的日常驱动，你应该*真的*安装我的 Zsh。

以下是我的 Zsh 可以为你做的 15 件有用的事情:

*   `take`命令将创建一个新的目录*和*进入其中。`take my-project`取代`mkdir my-project && cd my-project`。
*   会给你一个前 20 个命令的列表，以及它们运行的次数。
*   哦，我的 Zsh 简化了你的文件系统导航。例如，`..`是`cd ..`的别名。
*   同理，`...`让你上升两个目录，`....`让你上升三个目录，`.....`让你上升四个目录。
*   导航时可以省略`cd`。例如，输入`/`将直接带您到您的文件系统根目录。
*   也支持部分匹配。例如，输入`/h/j/De`并按下`TAB`，然后按下`Return`，将我带到`/home/jim/Desktop/`。
*   `rd`是`rmdir`的别名，`md`是`mkdir -p`的别名。
*   您可以在终端会话中键入`d`来列出最近使用的目录。
*   然后，您可以使用`cd -n`导航到其中任何一个，其中`n`是电话号码。
*   制表符补全是另一个伟大的功能。例如，输入`ls -`并按下`TAB`将会列出该命令的所有选项，以及对它们的有用描述。这也适用于`cap`、`rake`、`ssh`和`kill`。
*   键入`alias`会列出您当前的所有别名。
*   使用 globbing(Zsh 的一个特性)，您可以列出带有特定扩展名的文件。例如，`ls *.html`会列出当前目录下的所有 HTML 文件。要包含子目录，请更改为:`ls **/*.html`。
*   [全球限定符](https://www.systutorials.com/docs/linux/man/1-zshexpn/#lbBC)允许你通过使用标志来选择文件类型。比如,`ls -l **/*(.x)`会找到当前目录和所有子目录下的所有可执行文件。
*   您可以按修改日期搜索文件。例如，`ls *(m-7)`将列出上周修改的所有文件。
*   您可以按大小搜索文件。例如，`ls *(Lm+1)`将查找所有大小大于 1MB 的文件。

## 为了乐趣和利益使用插件

哦，我的 Zsh 附带了很多插件*。你应该浏览这些，花些时间学习那些对你的工作流程有帮助的东西。*

 *以下是我经常使用的三个插件，它们提供了大量便捷的快捷方式和别名。

### 10 个漂亮的 Git 别名

git 插件提供了许多[别名](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/git#aliases)和几个有用的[函数](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/git#functions)。为什么不仔细阅读这些，并试着记住你的前十条呢？以下是我最常用的。

1.  `g`是`git`的便利别名。这意味着你可以输入像`g clone <whatever>`这样的东西，而不是`git clone <whatever>`。可能只是两次击键，但它们很快就会累积起来。
2.  `gaa`是`git add all`的别名。我一直用这个。
3.  `gb`是`git branch`的别名，它将列出当前回购中的所有分支，并显示您所在的分支。
4.  `gcb`是`git checkout -b`的别名，该命令允许您创建新的分支。
5.  `gcm`是`git checkout master`的别名。这将使您返回到主分支。
6.  `gdca`是`git diff --cached`的别名。这允许您区分您已经提交的任何文件。
7.  `gf`是`git fetch`的别名。
8.  `gm`是`git merge`的别名。
9.  `gp`是`git push`的别名。要同步一个回购的分叉，您可以做:`gf upstream`、`gm upstream/master`，然后是`gp`。
10.  `glog`是`git log --oneline --decorate --graph`的别名，它会给你一个漂亮的 git 分支图。

### 10 个方便的国家预防机制别名

npm 插件提供了完整性和一些有用的别名。

*   `npmg`是`npm install --global`的别名，可以用来在系统上全局安装依赖项。例如，`npmg nodemon`。
*   `npmS`是`npm install --save`的别名，用于安装依赖项，并将它们添加到`package.json`的`dependencies`部分。注意，从 [npm 5.0.0](http://blog.npmjs.org/post/161081169345/v500) 开始，这是运行`npm i <package>`时的默认设置。
*   `npmD`是`npm install --save-dev`的别名，用于安装依赖项，并将它们添加到`package.json`的`devDependencies`部分。
*   `npmO`是`npm outdated`的别名，它将检查注册表以查看是否有任何(或特定的)已安装的软件包当前是过期的。
*   `npmL`是`npm list`的别名，它将列出已安装的软件包。
*   `npmL0`是`npm list --depth=0`的别名，它列出了顶层包。这对于查看哪些模块在全球范围内安装非常有用，而不会让您的终端充斥着巨大的依赖树:`npmL0 -g`。
*   `npmst`是`npm run start`的别名，它是一个 [npm 脚本](https://www.sitepoint.com/guide-to-npm-as-a-build-tool/)，通常用于启动应用程序。
*   `npmt`是`npm run test`的别名，正如您可能猜到的，它用于运行您的测试。
*   `npmR`是`npm run`的别名。该命令本身将列出项目的所有可用 npm 脚本，以及它们的功能描述。与一个脚本名称结合使用，它将运行那个脚本，例如，`npmR build`。
*   `npmI`是`npm init`的别名。这会问你一堆问题，然后根据你的回答创建一个`package.json`。使用`-y`标志自动执行该过程。例如，`npmI -y`。

### 10 个省时的导轨/耙别名

这个插件为 Ruby on Rails 框架和 [Rake 程序](https://ruby.github.io/rake/)增加了完整性，以及一些日志和环境变量的别名。

*   `rc`是`rails console`的别名，它允许您从 CLI 与 Rails 应用程序进行交互。
*   `rdc`是`rake db:create`的别名，它(除非设置了`RAILS_ENV`)为你的应用程序创建开发和测试数据库。
*   `rdd`是`rake db:drop`的别名，它删除你的应用程序的开发和测试数据库。
*   `rdm`是`rake db:migrate`的别名，它将运行任何挂起的数据库迁移。
*   `rds`是`rake db:seed`的别名，它运行`db/seeds.rb`文件来用数据填充您的开发数据库。
*   `rgen`是`rails generate`的别名，会生成样板代码。例如:`rgen scaffold item name:string description:text`。
*   `rgm`是`rails generate migration`的别名，它将生成数据库迁移。例如:`rgm add_description_to_products description:string`。
*   `rr`是`rake routes`的别名，它列出了一个应用程序定义的所有路线。
*   `rrg`是`rake routes | grep`的别名，它将允许您列出和过滤已定义的路线。例如，`rrg user`。
*   `rs`是`rails server`的别名，它启动 Rails 默认的 web 服务器。

## 额外资源

上面列出的插件的主要工作是为经常使用的命令提供别名。请注意，还有很多插件可以为您的 shell 增加额外的功能。

以下是我最喜欢的四个:

*   [sudo](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/sudo) 允许你通过按两次`ESC`来轻松地给你当前或之前的命令加上前缀`sudo`。
*   [自动建议](https://github.com/zsh-users/zsh-autosuggestions)根据历史记录和完成情况，在您键入时建议命令。如果建议是您正在寻找的，按`→`键接受它。一个真正的时间节省者！
*   [command-not-found](https://github.com/robbyrussell/oh-my-zsh/wiki/plugins#command-not-found) :如果一个命令在`$PATH`中没有被识别，这将使用 Ubuntu 的 command-not-found 包来找到它或者提示拼写错误。
*   z 是一个方便的插件，它建立了一个你最常用和最近文件夹的列表(它称之为“frecent”)，并允许你通过一个命令跳转到它们。

别忘了，如果你在候机厅呆了很长时间，花些精力让它看起来更吸引人是值得的。幸运的是，Oh My Zsh 提供了一大堆主题供你选择。我最喜欢的是[不可知论者](https://github.com/agnoster/agnoster-zsh-theme)。

你可以在我的文章 [10 个 Zsh 提示&中找到更多关于主题的技巧:配置、定制&用法](https://www.sitepoint.com/zsh-tips-tricks/)。

## 结论

所以我们有它:75 个 Zsh 命令，插件，别名和工具。我希望您已经学会了一两个技巧，我鼓励您走出 GUI，进入终端。这比看起来容易，也是提高工作效率的好方法。

如果我错过了你最喜欢的插件，或者省时的别名/命令，请在 [Twitter](https://twitter.com/jchibbard?lang=en) 上告诉我。

想从您的工具包中获得更多吗？查看来自威利的 [Visual Studio Code:面向 Web 开发人员的端到端编辑和调试工具](https://www.sitepoint.com/premium/books/visual-studio-code-end-to-end-editing-and-debugging-tools-for-web-developers?utm_source=blog&utm_medium=articles)。

## 分享这篇文章*