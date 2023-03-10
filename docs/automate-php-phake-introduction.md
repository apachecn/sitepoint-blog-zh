# 用 Phake 自动化 PHP 简介

> 原文：<https://www.sitepoint.com/automate-php-phake-introduction/>

作为开发人员，我们经常不得不做一些重复性的工作，比如更新数据库结构、为数据库植入种子、编写 CRUD 代码、运行测试以及将文件上传到服务器。如果我们能自动完成这些平凡的任务，并着手解决更重要的问题，比如让我们的应用程序更安全或对用户更有用，这不是很好吗？

Phake 是一个为 PHP 编写的自动化工具，可以为您完成这些任务。如果你熟悉 Ruby，它基本上是一个 [Rake](http://en.wikipedia.org/wiki/Rake_%28software%29) 克隆。在这个由两部分组成的系列中，我将带您了解如何将 Phake 集成到您的工作流程中。我将带你完成安装，一些使用 Phake 的基础知识，最后是一些真实的例子。

## 装置

Phake 应通过 Composer 进行全局安装:

```
composer global require 'jaz303/phake=*'
```

这使得 Phake 可以从任何文件夹访问，并且不需要您更改项目的`composer.json`文件。

如果您无法访问“composer”命令，[全局安装 Composer](https://getcomposer.org/doc/00-intro.md#globally)。

## 基础

要执行 Phake 任务，您需要创建一个 Phake 文件。`Phakefile`包含您想要执行的任务的配置。如果你以前用过[咕噜](http://gruntjs.com/)，一个`Phakefile`类似于一个`Gruntfile`。

关于`Phakefile`的一个重要注意事项是，它只是一个 PHP 文件，所以你可以像写你的 PHP 项目一样写它。

### 创建任务

您可以通过调用`task()`方法来创建任务。这将任务的名称作为第一个参数，要执行的函数作为最后一个参数。

```
<?php
task('task_a', function(){
  echo "Hi I'm task A!\n"; 
});
```

然后，您可以使用以下命令执行它:

```
phake task_a
```

这将返回以下输出:

```
Hi I'm task A!
```

### 属国

如果一项任务依赖于另一项任务，您可以在主任务之后提供该任务的名称:

```
<?php
task('task_a', function(){
  echo "Hi I'm task A!\n"; 
});

task('task_b', 'task_a', function(){
  echo "Hi I'm task B! I need task A to execute first before I can do my thing!\n";
});
```

要按顺序执行任务，您只需首先调用有依赖关系的任务。在这种情况下,`task_b`依赖于`task_a`,所以我们先调用它:

```
phake task_b
```

执行它将返回以下输出:

```
Hi I'm task A!
Hi I'm task B! I need task A to execute first before I can do my thing!
```

您可以继续添加依赖项:

```
<?php
task('task_a', function(){
  echo "I get to execute first!\n"; 
});

task('task_b', 'task_a', function(){
  echo "Second here!\n";
});

task('task_c', 'task_b', function(){
  echo "I'm the last one!\n";
});
```

通过调用需要调用的最后一个任务来执行它们。在这种情况下，我们希望最后执行的是`task_c`:

```
phake task_c
```

然后，它将返回以下输出:

```
I get to execute first!
Second here!
I'm the last one!
```

注意，使用这种声明依赖关系的方法，调用`task_b`会导致`task_a`首先被调用。如果您不希望发生这种情况，并且您仍然希望单独执行某个特定的任务，而不首先执行它的依赖项，那么您可以使用下面的方法来声明它:

```
task('task_a', function(){
  echo "I get to execute first!\n"; 
});

task('task_b', function(){
  echo "Second here!\n";
});

task('task_c', 'task_a', 'task_b', function(){
  echo "I'm the last one!\n";
});
```

在上面的例子中，我们将`task_a`和`task_b`设置为`task_c`的依赖项。请注意，这里的顺序很重要。因此紧随主任务(`task_a`)之后的任务将首先执行，紧挨着它的任务(`task_b`)将是第二个，然后最后执行主任务(`task_c`)。

使用 Phake，还有另一种定义依赖关系的方法:在定义主任务之后使用 before 或 after 块。在这种情况下，我们的主要任务是`eat`,因此我们在它的声明下定义我们希望在它之前和之后执行的任务:

```
task('eat', function(){
  echo "Yum!";
});

before('eat', function(){
  echo "Wash your hands before you eat\n";
});

after('eat', function(){
  echo "Brushy brush! brush!\n";
});
```

当您执行`eat`时，您会得到以下输出:

```
Wash your hands before you eat
Yum!
Brushy brush! brush!
```

### 分组任务

使用 Phake，您还可以将相关任务分组在一起:

```
group('clean_the_house', function(){
  task('polish_furniture', function(){..});
  task('wash_the_clothes', function(){..});
  task('mop_the_floor', function(){..}); 
});
```

可以使用您指定的组名，后跟一个冒号，然后是您要执行的任务名来调用分组任务:

```
phake clean_the_house:polish_furniture
```

如果您想执行一个组中的所有任务，您可以让最后一个任务依赖于第一个和第二个任务。在下面的例子中，我们想要执行的最后一个任务是`mop_the_floor`任务，所以我们让它依赖于`polish_furniture`和`wash_the_clothes`任务:

```
group('clean_the_house', function(){
  task('polish_furniture', function(){..});
  task('wash_the_clothes', function(){..});
  task('mop_the_floor', 'polish_furniture', 'wash_the_clothes' function(){..}); 
});
```

然后我们简单地从终端调用`mop_the_floor`任务:

```
phake clean_the_house:mop_the_floor
```

这将按以下顺序调用任务:

```
polish_furniture
wash_the_clothes
mop_the_floor
```

### 描述任务

在使用 Phake 一段时间后，您可能会在您的`Phakefile`中积累一堆任务，所以拥有某种文档是个好主意。幸运的是，Phake 附带了一个实用程序，允许我们描述特定的 Phake 任务是做什么的。您可以通过在您想要描述的任务声明之前调用`desc`方法来使用它:

```
desc('Allows you to water the plants');
task('hose', function(){..}); 

desc('Allows you to wash the dish');
task('dish_washer', function(){..});
```

然后，您可以使用以下命令列出 Phakefile 中的可用任务:

```
phake -T
```

它将返回类似如下的输出:

```
hose              Allows you to water the plants
dish_washer       Allows you to wash the dish
```

### 向任务传递参数

为了使任务更加灵活，我们还可以传入参数。这可以通过在函数中声明一个参数来实现。这可用于访问您传递给任务的各个参数:

```
task('brush_teeth', function($args){
  $motion = (!empty($args['motion'])) ? $args['motion'] : 'circular';
  $includes = (!empty($args['includes'])) ? $args['includes'] : '';

  brush($motion, $includes); 

});
```

可以通过在任务名称后面包含一个名称-值对来传递参数。如果希望传入多个参数，可以在第一个参数的值和第二个参数的名称之间使用一个空格来分隔它们:

```
phake brush_teeth motion=horizontal includes=tongue,teeth,gums
```

如果需要传入的参数之间有空格，可以用单引号或双引号括起来:

```
phake brush_teeth motion="circular horizontal and vertical"
```

## 结论

现在我们已经了解了 Phake 的用途以及如何使用它执行任务，我们已经为第二部分中的一些实际应用做好了准备。敬请期待！

## 分享这篇文章