# 让你成为 npm 忍者的 10 个技巧和诀窍

> 原文：<https://www.sitepoint.com/10-npm-tips-and-tricks/>

![Shuriken embedded in a scroll](img/2c3375b4dbd41b11bb8890f0da94516b.png)

这篇文章由[维尔丹·索蒂奇](https://www.sitepoint.com/author/vildansoftic)、[马特·伯内特](https://www.sitepoint.com/author/mburnett/)和[汤姆·格列柯](https://www.sitepoint.com/author/tgreco/)进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

虽然脸书的新项目令人兴奋，但 Node.js 的持续成功在很大程度上要归功于其最初的包经理 [npm](https://www.npmjs.com/) 。

只需几个简单的 npm 命令就可以初始化一个文件夹( [npm init](https://docs.npmjs.com/cli/init) )、下载包( [npm install](https://docs.npmjs.com/cli/install) )并创建测试( [npm test](https://docs.npmjs.com/cli/test) )和定制脚本( [npm run](https://docs.npmjs.com/cli/run-script) )供您的项目使用。很少有人深入研究，但是有几个 npm 提示和技巧可以彻底改变你的日常开发任务。

***注*** :如果你需要 npm 入门，可以查看我们的[初学者指南](https://www.sitepoint.com/beginners-guide-node-package-manager/)。如果你对 npm 和 Yarn 之间的区别感到困惑，请看我们的帖子: [Yarn vs npm:你需要知道的一切](https://www.sitepoint.com/yarn-vs-npm/)。

## 1.寻求帮助！

npm 在线帮助和 [CLI 命令文档](https://docs.npmjs.com/#cli)非常好，但是在浏览器之间切换并不总是很方便。命令行中提供了所有选项的快速提示:

```
npm help 
```

还可以显示特定 npm 命令的帮助:

```
npm help <command> 
```

比如`npm help install`。

或者，您可以查看快速命令参数参考:

```
npm <command> -h 
```

## 2.npm 命令自动完成

npm 为使用 bash 的系统提供命令自动完成功能(包括 Windows 10 的 Bash):

```
npm completion >> ~/.bashrc 
```

或 Z 外壳:

```
npm completion >> ~/.zshrc 
```

重新加载 shell 配置文件，例如

```
source ~/.bashrc 
```

现在输入`npm ins`并点击`TAB`，就会出现`install`。你再也不需要浪费时间打字了！

## 3.修复全局模块权限

当您尝试安装全局软件包时，类似 Linux 的系统可能会抛出权限错误。你可以在任何 npm 命令前面加上`sudo`,但是这是一个危险的选择。更好的解决方案是将 npm 的默认目录更改为您自己的目录:

```
mkdir ~/.npm-global
npm config set prefix '~/.npm-global' 
```

使用您选择的文本编辑器将以下行添加到`~/.bashrc`或`~/.zshrc`中:

```
export PATH="$HOME/.npm-global/bin:$PATH" 
```

重新加载 shell 配置文件(`source ~/.bashrc`)，然后将 npm 本身重新安装到新的用户拥有的位置:

```
npm install -g npm 
```

这也会将 npm 更新到最新版本。

## 4.不断更新国家预防机制

您可以透露 npm 版本号:

```
npm -v 
```

并在需要时使用以下工具进行更新:

```
npm install -g npm 
```

当新的 Node 主版本发布时，您可能还需要重新构建 C++插件:

```
npm rebuild 
```

更进一步，如果您需要管理 Node.js 和 npm 的多个版本的更新，可以考虑像 [nvm](https://github.com/creationix/nvm) 和 [n](https://github.com/tj/n) 这样的选项。我们也有这方面的教程:[快速提示:使用 nvm 安装多个版本的 Node.js】](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)

## 5.定义 npm 初始化默认值

新项目文件夹用`npm init`初始化。这将提示您关于项目的更多细节，并创建一个初始的`package.json`文件。

如果你厌倦了每次开始一个新项目都要重新输入相同的信息，你可以使用`-y`标志接受一系列缺省值:

```
npm init -y 
```

或者，您可以为 npm 设置一些合理的默认值:

```
npm config set init.author.name <name>
npm config set init.author.email <email> 
```

## 6.复杂的包裹搜索

在撰写本文时，npm 上有超过 350，000 个可用的包，并且每天都有更多的包出现。虽然许多都很棒，但你可能想避免不太流行、有问题或停产的软件包。在[npmjs.com](https://www.npmjs.com/)和 [GitHub](https://github.com/) 搜索是可行的，但是还有其他选择…

### npms

npms 根据项目版本、下载数量、最新更新日期、提交频率、测试覆盖范围、文档、贡献者数量、问题、星级、分叉甚至作者在社区中的地位，对软件包进行排名，以提供总体质量分数。

### npm 发现

[npm Discover](http://www.npmdiscover.com/) 定位通常与其他包一起使用的包，例如 [body-parser](https://www.npmjs.com/package/body-parser) with Express。

### 按 PageRank 排序的包

[Packages by PageRank](http://anvaka.github.io/npmrank/online/) 按照相关的 Google 排名对 npm 包进行搜索和排序。

### 策划的国家预防机制清单

或者，利用别人的搜索成功。在寻找一个健壮的解决方案时，我经常参考来自 [sindresorhus](https://twitter.com/sindresorhus) 的 [Awesome Node.js](https://github.com/sindresorhus/awesome-nodejs) 。

## 7.管理您的包

您已经选择了软件包并安装了依赖项。让我们列出我们所拥有的:

```
npm list 
```

(`ls`、`la`、`ll`可以作为`list`的别名)。

列表显示了一切:包，子包，子包的子包等等。使用以下命令将输出限制为仅顶层包:

```
npm list --depth=0 
```

软件包主页可以通过以下方式打开:

```
npm home <package> 
```

这仅在您的系统可以打开浏览器的情况下有效，在操作系统服务器版本上将会失败。类似地，您可以打开一个包的 GitHub 存储库:

```
npm repo <package> 
```

或其文档:

```
npm docs <package> 
```

或者当前的错误列表:

```
npm bugs <package> 
```

`npm list`当您安装了无关的软件包时会进行报告——这些软件包在您的`package.json`文件中不再被引用。您可以单独使用`npm uninstall`或将它们全部移除:

```
npm prune 
```

如果您添加了`--production`标志或者将`NODE_ENV`环境变量设置为`production`，那么在`package.json`中指定为`devDependencies`的包也将被删除。

## 8.锁定依赖项

默认情况下，当安装带有`--save`或`--save-dev`的软件包时，npm 使用脱字符号(`^`)引用软件包版本号。这将把包固定到它的主版本号。例如，`^1.5.1`允许在`npm update`运行时安装从该版本起的任何东西，但不包括`2.0.0`。

更保守的波浪号(`~`)字符将包固定到次要版本。例如，`~1.5.1`允许在`npm update`运行时安装从该版本起的任何东西，但不包括`1.6.0`。波浪号前缀可以设置为默认值:

```
npm config set save-prefix="~" 
```

对于那些担心任何更新会破坏您的系统的人，您可以将 npm 配置为仅使用确切的版本号:

```
npm config set save-exact true 
```

或者，您可以 [*收缩*您的项目](https://docs.npmjs.com/cli/shrinkwrap)使用:

```
npm shrinkwrap 
```

这会生成一个`npm-shrinkwrap.json`文件，包含您正在使用的依赖项的特定版本。该文件被默认使用，在运行`npm install`时将覆盖`package.json`。

## 9.查找过时的模块

你如何知道一个依赖项何时被更新？我用了好几个月的过程是列出我的依赖项(`npm list --depth=0`)，在[npmjs.com](https://www.npmjs.com/)上搜索包，并手动检查哪些版本号发生了变化。几个小时的乐趣。幸运的是，有一个简单得多的选择:

```
npm outdated 
```

或者`npm outdated -g`对于 npm 本身这样的全局包。

您还可以查看单个包的当前版本:

```
npm list <package> 
```

并检查当前和历史版本:

```
npm view <package> versions 
```

`npm view <package>`显示单个软件包的所有信息，包括其依赖项、关键字、更新日期、贡献者、存储库、许可证等。

## 10.使用开发包

当开发包时，你经常想在其他项目中尝试它们或者从任何目录运行它们(如果你的应用程序支持的话)。无需将软件包发布到 npm 注册表并进行全局安装，只需使用:

```
npm link 
```

从包文件夹中。这将在该包的全局文件夹中创建一个符号链接。使用时，您将看到参考:

```
npm list -g --depth=0 
```

或者

```
npm outdated -g 
```

现在，您可以从命令行运行 package，或者用`require`将它包含在任何项目中。

或者，您也可以在`package.json`中通过文件路径声明依赖关系，例如

```
"dependencies": {
  "myproject": "file:../myproject/"
} 
```

这些是我最喜欢的 npm 技巧，但是我错过了你的一个吗？欢迎评论…

## 分享这篇文章