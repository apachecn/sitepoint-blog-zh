# 企业程序库提示和技巧

> 原文：<https://www.sitepoint.com/enterprise-library-tips-tricks/>

上周，[微软模式&实践团队](http://msdn.microsoft.com/practices/) [发布了企业图书馆 2.0 的补丁 2554](http://blogs.msdn.com/tomholl/archive/2006/06/30/652955.aspx) 。这个补丁的要点是允许用户在部分信任模式下使用这个库——在这个危险的时代，这是 web 应用程序的常见场景。

我使用这些库已经有一段时间了，所以我想分享一些关于使用这些代码的技巧和诀窍，我发现这些代码没有被很好地记录，也没有被广泛宣传。

**诀窍#1:得到 Libs，伙计**

企业程序库目前有两个版本:[版本 1.1](http://www.microsoft.com/downloads/details.aspx?FamilyID=a7d2a109-660e-444e-945a-6b32af1581b3&DisplayLang=en) (又名 2005 年 6 月)。NET 1.1 和[2.0](http://www.microsoft.com/downloads/details.aspx?familyid=5A14E870-406B-4F2A-B723-97BA84AE80B5&displaylang=en)(又名 2006 年 1 月)版本。NET 2.0。根据需要下载&。

**诀窍 2:做实验，伙计**

我第一次看文档的时候，我在想我到底在搞什么鬼。幸运的是，我偶然发现了企业图书馆动手实验室(适用于 [1.1](http://www.microsoft.com/downloads/details.aspx?familyid=b9bff619-236c-4bbb-9aa1-2e7bc562c7f5&displaylang=en) 和 [2.0](http://www.microsoft.com/downloads/details.aspx?FamilyId=C8CA14D0-05EA-4A44-AE78-F5E4DF6208AF&displaylang=en) )。我发现它们写得很好，很快让学生能够利用图书馆的主要功能。

**诀窍 3:记住为发布而构建**

有趣的是，企业库只作为源代码随安装程序一起发布。这个安装程序确实提供了一个方便的“构建企业库”复选框。但是有一个隐藏的问题——它使用 DEBUG，而不是 RELEASE，build 设置来构建库。这意味着代码没有完全针对生产应用进行优化。

现在，如果你把项目包括进来，并用你的解决方案来编译它们，事情就会变得很好。但是我，我自己，更喜欢静态地包含编译过的程序集，因为我真的不需要担心这个库的内部。

在任何情况下，正确构建库是一个两步过程。首先，您必须运行发布构建脚本。然后，您必须运行脚本来复制程序集。假设已经将库安装到了 c:projectsEntLib，您将需要启动一个命令窗口并在该文件夹中运行以下命令:

`c:ProjectsEntLibBuildLibrary Release
c:ProjectsEntLibCopyAssemblies Release`

[请注意，1.1 版的源代码在[InstallFolder]src 文件夹中，因此您必须去那里找到脚本。另请注意，对于 1.1 版，Visual Studio。必须安装. NET 2003，构建脚本才能运行。]

运行此程序后，所有必需的 dll(和 xml 文档)都可以在企业程序库目录的 bin 文件夹中找到。

**招数#4:安装工具**

这一招真的适用于 1.1 版本。默认情况下，它触发许多性能计数器并写入事件日志。现在，这在开发中一般不是问题。但是一旦将应用程序转移到临时服务器，就会出现一个令人讨厌的“无法写入注册表”错误。

有两种方法可以解决这个问题。首先，[用户可以修改构建选项，使其不使用性能计数器](http://blogs.msdn.com/tomholl/archive/2005/02/18/376187.aspx)。不可否认的是，我从来没有成功地让它工作过。但话说回来，我并没有特别努力。我碰巧喜欢有可用的性能计数器。

另一种选择是使用。NET 的 InstallUtil.exe 实用工具来注册程序集。为此，您需要启动一个可信的命令行窗口，并对您正在使用的每个库的程序集运行以下命令:

`C:WINDOWSMicrosoft.NETFrameworkv1.1.4322InstallUtil.exe c:MyAppFolderbinEnterpriseLibraryAssembly.dll`

根据需要替换程序集的路径。

## 分享这篇文章