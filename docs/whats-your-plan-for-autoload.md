# 你对 __autoload()有什么计划？

> 原文：<https://www.sitepoint.com/whats-your-plan-for-autoload/>

在 PHP 的所有魔法中，我可能最喜欢 __autoload()钩子。它节省了大量繁琐的脚本包含调用，并且可以通过让解析器免于做不必要的工作来大大加快应用程序的速度。尽管它在 PHP5 发布后就出现了，但我还没有找到任何令人信服的应用。它们中的大多数遵循相同的方案:每当实例化一个未定义的类时，一个小的 __autoload()函数试图包含一个 PHP 文件，该文件必须以它的类命名:

```
 function __autoload($name) {
    require_once('classes/'.$name.'.php'); 
  } 
```

然而，这种解决方案是不灵活的，并且有一些缺点。最明显的一个是类名->文件名约束。此外，它意味着所有的类文件都存储在一个文件夹中。对于包含数百个类的项目来说，这是没有选择的，这些类自然地在包目录中排序。总的来说，这些实现似乎还不太成熟，而是 __autoload()的概念证明。这需要一个更好的解决方案。

**智能加载**

如果我们有一个小小的“类查找器”，递归地搜索 PHP 脚本目录并解析每一个脚本的类定义，会怎么样？第一，它知道这些文件夹中的所有类，并告诉我们在哪个文件中可以找到它们。我们可以将它与 __autoload()结合起来，帮助它自己找到任何需要的类。你可能会说，“这太愚蠢了，它会产生太多的开销”。没错！那么，如果我们知道文件结构很少改变，除非开发人员正在处理它，那么我们会在每次搜索后缓存结果吗？当然，我说的是缓存的最佳方式，即生成 PHP 代码形式的类列表，并保存起来以备后用。

说到做到。我写了一个实现这个想法的类，并自命不凡地称之为“SmartLoader”，因为它足够智能，可以在没有任何帮助的情况下找到你的 PHP 应用程序的任何类。你可以在[通用公共许可证](http://creativecommons.org/licenses/LGPL/2.1/ GNU)下在这里下载[。现在让我们仔细看看它是如何工作的:](https://www.sitepoint.com/examples/blogs/smartloader.zip)

**幕后**

步骤 1: SmartLoader 递归地搜索 PHP 脚本，并使用以下正则表达式解析它们是否包含类:

```
(interface|class)s+(w+)s+(extendss+(w+)s+)?(implementss+w+s*(,s*w+s*)*)?{
```

步骤 2:我们现在有了所有可用类的列表以及在哪里可以找到它们。这个列表将被转换成 PHP 代码并写入一个缓存文件。它的内容看起来类似于这样:

```
 // this is a automatically generated cache file.
$GLOBALS['smartloader_classes']['Main'] = 'classes/main.class.php';
$GLOBALS['smartloader_classes']['Iterable'] = 'classes/containers/iterable.class.php';
$GLOBALS['smartloader_classes']['ActiveRecord'] = 'classes/database/activerecord.class.php';
/* etc. */ 
```

步骤 3:每当需要加载一个类时，SmartLoader 都会检查缓存中它的名称，并包含适当的 PHP 文件。如果找不到或加载不到该类，将重新创建缓存，并再次尝试包含。

**准备就绪**

smartloader.class.php 包含自动加载函数以及实际的 SmartLoader 类。唯一要做的事情是定制自动加载方法:

```
 function __autoload($class_name) {
    /* using a static loader object rather than a singleton to reduce overhead */
    static $ldr = null;

    /* initializing loader */
    if(!$ldr) {
      $ldr = new SmartLoader();
    }

      /* defining cache file, make sure write permissions */
      $ldr->setCacheFilename('cache/smartloader_cache.php');

      /* adding directories to parse. better use absolute paths. */
      $ldr->addDir("classes");

      /* what are the endings of your class files? */
      $ldr->setClassFileEndings(array('.php', '.class'));

      /* should SmartLoader follow symbolic links? */
      $ldr->setfollowSymlinks(false);

      /* it should probably ignore hidden dirs/files */
      $ldr->setIgnoreHiddenFiles(true);
    }

    /* load the class or trigger some fatal error on failure */
    if(!$ldr->loadClass($class_name)) {
      trigger_error("Cannot load class '".$class_name."'", E_USER_ERROR);
    }
  } 
```

之后，你只需要在你的脚本中包含 smartloader.class.php，再也不用担心类包含了。

**优势**

*   便利性:SmartLoader 简化了类文件管理。您可以在(包)文件夹中重命名、移动或重新组织它们。只要它们在您的网络空间中，SmartLoader 就可以找到它们。
*   速度:使用 SmartLoader，类文件只有在真正需要的时候才会被加载。这种称为“延迟加载”或“及时加载”(不要与交互式编程混淆)的方法可以为 PHP 节省大量由冗余包含引起的解析和编译。
*   可移植性、向后兼容性:您可以轻松采用 SmartLoader，而不会破坏您现有的应用程序。__autoload()只有在默认情况下实例化不存在的类时才会涉及到。

**潜在陷阱**

*   错误处理:您可以使用任何您喜欢的错误处理方式。嗯，差不多:异常不能通过 __autoload()引发。记住，那是特性，不是 [bug](http://bugs.php.net/bug.php?id=26193) 。这是可能的，虽然有一个可怕的 eval()黑客，但我不会进一步深入。
*   版本控制系统:如果你的网络空间是某个版本控制系统的工作副本，你应该告诉 SmartLoader 忽略隐藏文件。这将提高它的性能，并防止它扫描那些可疑的过时的文件副本。SVN/。CVS 文件夹。
*   我不应该告诉你，但不要让 SmartLoader 扫描符号链接循环。
*   分析:Zend Profiler 被 SmartLoader 弄糊涂了，开始计算它的执行时间的无效结果。
*   操作码缓存:我不太了解 APC、Zend Optimizer 等的内部工作方式，但是我可以想象 SmartLoader 中使用的动态包含会抵消操作码缓存带来的性能提升。我无法证实这一点，因为 Zend Profiler 拒绝与 SmartLoader 一起正常工作(见上文)。我很乐意收到关于这件事的任何反馈。

## 分享这篇文章