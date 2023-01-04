# 在 Drupal 7 中构建定制的 cTools 插件

> 原文：<https://www.sitepoint.com/building-custom-ctools-plugins-in-drupal-7/>

cTools 是许多其他人依赖的 Drupal 7 关键模块之一。它提供了许多 API 和功能，使得开发模块变得更加容易。视图和面板只是依赖于它的两个例子。

cTools 提供不同种类的功能。对象缓存、配置导出、表单向导、对话框和插件等等。很多你通常认为是视图或面板的功劳实际上应该归功于 cTools。

![Drupal logo](img/53c06f5d4189f9b7150b0336971f5f32.png)

在这篇文章中，我们将看看 cTools 插件，尤其是我们如何创建自己的插件。简单介绍之后，我们将立即着手定制一个模块，该模块将使用 cTools 插件来更好地定义 Drupal 块(更符合我们在 Drupal 8 中定义它们的方式)。

## 介绍

Drupal 7 中的 cTools 插件(概念上与 Drupal 8 中的插件系统没有太大的不同)旨在轻松定义可重用的功能。也就是说，定义在某些*上下文*中使用的独立业务逻辑的能力。目标是设置一次*上下文*和插件类型，然后允许其他模块自动定义可以在*上下文*中使用的插件。

如果你开发 Drupal 站点已经超过一年，你可能会遇到各种各样的 cTools 插件。我认为我们通常处理的第一个插件类型是`content_type`插件，它允许我们创建自己的定制面板来显示动态内容。这太棒了。您可能在面板的相同领域中遇到的一些其他规则可能是`context`和`access`(可见性规则)。甚至可能是`relationships`和`arguments`。这些都是 cTools 提供的。Panels 通过引入我们通常用来创建面板布局和单个面板样式的`layouts`和`styles`增加了这个列表。这些是我认为比较常见的。

然而，对许多人来说，上述所有内容在某种程度上都是一个黑匣子。我们所知道的是，我们需要定义一个钩子来指定一个目录，然后提供一个包含一些定义和逻辑代码的包含文件，剩下的事情就不可思议了。接下来，我想让我们看看插件类型是如何定义的，这样如果出现这种情况，我们可以创建自己的插件来表示一些可重用的功能。为了演示这一点，我们将创建一个模块，将定义自定义 Drupal 块的讨厌的钩子系统变成一个基于插件的方法，类似于 Drupal 8 正在使用的方法。

最终的代码(+多一点)可以在[这个库](https://github.com/upchuk/block_plugin)中找到，如果你想继续的话。我希望你熟悉定义[自定义 Drupal 块](https://api.drupal.org/api/examples/block_example!block_example.module/7)的必要步骤。

## 块插件模块

正如我提到的，我想用一个定制的插件类型来说明 cTools 插件的强大之处，它使得定义 Drupal 7 块更加合理。不用实现定义一个块所必需的两个主钩子(`hook_block_info()`和`hook_block_view()`)，我们将能够拥有独立的插件文件，每个插件文件负责与它们自己的块相关的所有逻辑。不再有开关的情况，每次我们需要一个新的块时都要改变钩子的实现。那么我们如何做到这一点呢？

首先，让我们创建我们的`block_plugin.info`文件来开始我们的模块:

```
name =  Block  Plugin description =  Using cTools plugins to define Drupal core blocks
core =  7.x dependencies[]  = ctools
```

很简单。

## 插件类型

为了定义我们的新闻插件类型，在`block_plugin.module`文件中我们需要实现`hook_ctools_plugin_type()`，它负责定义 cTools 将识别的新插件类型:

```
function block_plugin_ctools_plugin_type()  {  return array(  'block'  => array(  'label'  =>  'Block',  'use hooks'  => FALSE,  'process'  =>  'block_plugin_process_plugin'  )  );  }
```

在这个钩子中，我们需要返回一个所有插件类型定义的关联数组，我们需要用插件类型名称的机器名作为键。今天我们只创建一个名为`block`的。如需了解更多关于此处所有可用选项的信息，请随时查阅 cTools 模块中的`plugins-creating.html`帮助文件。在这里重复这些信息没有用。

`process`键定义了一个函数名，每次 cTools 为我们加载一个插件时都会触发这个函数名，它负责在我们使用它之前对插件数据进行整形或处理。这是一种帮助功能，每次都为我们准备插件，所以我们不必麻烦。让我们看看在这个函数中我们能做些什么:

```
function block_plugin_process_plugin(&$plugin, $info)  {  // Add a block admin title  if  (!isset($plugin['admin title']))  { $exploded = explode('_', $plugin['name']); $name =  '';  foreach  ($exploded as $part)  { $name .= ucfirst($part)  .  ' ';  } $plugin['admin title']  = $name;  }  // By default we also show a block title but this can be overwritten  if  (!isset($plugin['show title']))  { $plugin['show title']  = TRUE;  }  // Add a block view function  if  (!isset($plugin['view']))  { $plugin['view']  = $plugin['module']  .  '_'  . $plugin['name']  .  '_view';  }  // Add a block form function  if  (!isset($plugin['configure']))  { $plugin['configure']  = $plugin['module']  .  '_'  . $plugin['name']  .  '_configure';  }  // Add a block save function  if  (!isset($plugin['save']))  { $plugin['save']  = $plugin['module']  .  '_'  . $plugin['name']  .  '_save';  }  }
```

这个回调接收插件数组作为引用和一些关于插件类型的信息。当前的任务是动态地改变或添加数据到插件中。那么我们在上面实现了什么呢？

首先，如果开发者没有为 block 插件定义管理标题，我们会根据插件的机器名自动生成一个。这是为了让我们在 Drupal 块界面中总是有一个管理标题。

其次，我们选择总是显示块的标题，因此我们将插件数组的`show title`键标记为 TRUE。当定义块插件时，开发者可以选择将其设置为 FALSE，在这种情况下，我们不会显示块标题(主题)。

第三、第四和第五，我们为块视图、保存和配置动作生成一个回调函数(如果开发者还没有为给定的插件设置它们的话)。这些回调将分别在实现`hook_block_view()`、`hook_block_configure()`和`hook_block_save()`时使用。我们不会在本文中讨论后两者，但是可以随意查看存储库，看看它们会是什么样子。

这就是我们定义自定义插件类型所需要的全部内容。然而，我们也应该实现`hook_ctools_plugin_directory()`,正如你可能知道的，它负责告诉 cTools 在当前模块的什么地方可以找到某种类型的插件:

```
function block_plugin_ctools_plugin_directory($module, $plugin)  {  if  ($module ==  'block_plugin'  && in_array($plugin, array_keys(block_plugin_ctools_plugin_type()))  )  {  return  'plugins/'  . $plugin;  }  }
```

这也需要由任何其他想要定义`block`插件的模块来实现。

## Drupal 块

现在我们有了插件类型，让我们编写代码，将任何已定义的`block`插件转换成 Drupal 块。将从`hook_block_info()`实现开始:

```
function block_plugin_block_info()  { $blocks = array(); $plugins = block_plugin_get_all_plugins();  foreach  ($plugins as $plugin)  { $blocks[DELTA_PREFIX . $plugin['name']]  = array(  'info'  => $plugin['admin title'],  );  }  return $blocks;  }
```

在这里，我们使用一个助手函数加载所有的插件，并定义块所需的最少信息。这里你也可以添加更多的信息，但是为了简洁，我们保持简单。

我们知道每个插件都有一个机器名(基本上是包含文件的名称)和一个`admin title`,因为如果一个插件不存在，我们会在处理阶段生成一个。`DELTA_PREFIX`是一个简单的常量，我们在其中为块计算机名称定义了我们想要的前缀，因为我们需要重用它，并且如果我们想要的话，应该能够轻松地更改它:

```
define('DELTA_PREFIX',  'block_plugin_');
```

我们之前看到的助手函数如下所示:

```
function block_plugin_get_all_plugins()  {  return ctools_get_plugins('block_plugin',  'block');  }
```

这是一个简单的 cTools 函数包装器。就这一点而言，我们还有以下函数负责根据机器名称加载单个插件:

```
function block_plugin_get_plugin($name)  {  return ctools_get_plugins('block_plugin',  'block', $name);  }
```

这个和之前那个很像。

为了使我们的 Drupal 块定义完整，我们需要实现`hook_block_view()`:

```
function block_plugin_block_view($delta =  '')  { $plugin = block_plugin_plugin_from_delta($delta);  if  (!$plugin)  {  return;  } $block = array();  // Optional title  if  (isset($plugin['title'])  && $plugin['show title']  !== FALSE)  { $block['subject']  = $plugin['title'];  }  // Block content $block['content']  = $plugin['view']($delta);  return $block;  }
```

这里发生了什么？

首先，我们使用另一个助手函数来尝试基于当前块的增量加载一个插件，如果我们没有处理一个插件块，就什么也不做。

其次，我们构建模块。如果用户在插件上指定了一个`title`键，并且`show title`键不为假，我们将块的主题(基本上是它的标题)设置为前者的值。至于实际的块内容，我们简单地调用插件中定义的`view`回调。仅此而已。

让我们快速地看看基于块增量负责加载插件的助手函数:

```
function block_plugin_plugin_from_delta($delta)  { $prefix_length = strlen(DELTA_PREFIX); $name = substr($delta, $prefix_length); $plugin = block_plugin_get_plugin($name);  return $plugin ? $plugin : FALSE;  }
```

这里没什么复杂的。

## 定义块插件

既然我们告诉 cTools 它可以在我们模块的`plugins/block`文件夹中找到`block`插件，让我们继续创建那个文件夹。在其中，我们可以在一个扩展名为`.inc`的文件中添加我们的第一个块，例如`my_block.inc`:

```
<?php

$plugin = array(  'title'  => t('This is my block'),  );  /**
 * Returns a renderable array that represents the block content
 */  function block_plugin_my_block_view($delta)  {  return array(  '#type'  =>  'markup',  '#markup'  =>  'Yo block!'  );  }
```

就像我们对所有其他插件(`content_type`、`context`等)所做的那样，插件定义是在一个名为`$plugin`的变量中以数组的形式出现。对于我们的例子，此时我们所需要的是一个`title`(甚至没有它，因为没有它块根本不会显示标题)。

在它下面，我们定义了回调函数来显示这个块。这个函数的命名很重要。它与我们在处理阶段(`module_name_plugin_name_view`)使用的模式相匹配。如果我们想给它起不同的名字，我们所要做的就是引用`$plugin`的`view`键中的函数名，它就会使用那个名字。

基本上就是这样。我们现在可以清除缓存，转到块管理屏幕，在那里我们可以找到我们的块，并将其添加到一个区域。在页面上显示该块应该会触发该块插件的`view`回调，并呈现内容。

## 结论

在这篇文章中，我们谈了一些关于 cTools 插件的内容，并看到了我们如何定义自己的插件类型。我们使用后者将 Drupal block 系统转换成一个基本的插件系统。这可以进一步扩展，允许插件包含文件中的回调函数替换与块相关的配置挂钩。此外，如前所述，您还可以确保`hook_block_info()`中所有可用的数据都可以在插件中定义。我把这些任务交给你了。

## 分享这篇文章