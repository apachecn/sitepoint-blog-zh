# php-tracer-weaver 简介

> 原文：<https://www.sitepoint.com/introducing-php-tracer-weaver/>

xdebug 鲜为人知的特性之一是它的[函数跟踪](http://www.xdebug.org/docs/execution_trace)。如果你以前没有听说过它，引用手册的话，它*允许你记录所有的函数调用，包括参数和返回值到一个文件*。在使用它一段时间后，我意识到这些信息可以被解析并用于确定函数的参数类型和返回类型。

因此，作为概念上的证明，我编写了一个脚本来解析来自 xdebug 的函数跟踪，并使用它来处理 php 源文件，并注入带有`@param`和`@return`标记的 docblock 注释。我必须将动态分析(函数跟踪)与一些静态分析结合起来，以便能够将不同的子类型整理成它们的父类型，这使得整个处理比最初预期的要复杂一些。

仍然有一些改进的空间，但是上周四在[本地 php 会议](http://www.meetup.com/copenhagenphp/)上，我演示了这个项目，现在可以通过 [github](http://github.com/troelskn/php-tracer-weaver/tree/master) 获得。

使用分为两步。首先运行代码，使用 xdebug 工具生成跟踪。我创建了一个包装器脚本来简化这个过程。只需用`trace.sh foo.php`调用 php 脚本，而不是`php foo.php`。这将正常运行脚本，同时在文件`dumpfile.xt`中生成一个跟踪。

目的是将该工具与现有的测试用例一起使用。如果不使用单元测试，可以只放一个简单的集成测试在一起；重要的是脚本应该出现在代码的各个角落，为分析提供良好的基础。

下一步是分析这些数据，并使用它来生成文档块注释。这是用工具`weave.php`完成的。您可能想要多次调用 weave.php(对于项目中的每个文件，您想要为其生成文档)。

这里有一个例子来说明这个过程:

我们将从一个非常简单的文件开始，没有任何文档块注释:

```
 <?php

class Foo {
  function stuff($x) {
    return 42;
  }
}

class Bar {}

$f = new Foo();
$f->stuff(new Bar()); 
```

第一步是运行文件，使用`trace.sh`:

```
 $ /path/to/php-tracer/weaver/trace.sh foo.php
Running script with instrumentation: foo.php
TRACE COMPLETE 
```

接下来，我们将把文档编回到文件中:

```
 $ /path/to/php-tracer/weaver/weave.php foo.php foo.php 
```

就这样..我们的文件现在看起来像这样:

```
 <?php

class Foo {
  /**
    * @param Bar
    * @return integer
    */
  function stuff($x) {
    return 42;
  }
}

class Bar {}

$f = new Foo();
$f->stuff(new Bar()); 
```

该实现是内存高效的，而不是 cpu 高效的，这意味着它将与大型代码库一起工作，但需要一些时间来处理。

## 分享这篇文章