# 资产预编译如何工作，第二部分

> 原文：<https://www.sitepoint.com/asset-precompile-works-part-ii/>

![Abstract tooth wheels](img/aea2f50fe66416bb693783271c7f8183.png)

这是资产预编译如何在 Rails 中工作的第 2 部分。在[第 1 部分](https://www.sitepoint.com/asset-precompile-works-part/)中，我们开始深入研究 Rails 对打包资产的内置支持，它如何编译静态资产(图像)，以及如何基于它们的内容生成摘要等等。本文对 Rails 资产管道进行了更深入的介绍。

## 资产类型

如果您还记得，根据链轮有三种类型的资产

*   捆绑资产
*   已处理资产
*   静态资产

我们在第 1 部分中讨论了静态资产。捆绑资产是需要一些处理才能创建的资产。CSS 文件和 Javascript 文件等资产就是捆绑资产的例子。已处理资产是捆绑资产的一部分。简而言之，捆绑资产由不同的已处理资产组成，用于在从捆绑资产中提取处理后存储文件。

例如，`application.js`最初是一个被捆绑的资产，它由不同的被处理的资产(包括它自己)组成，这些资产是根据`Sprockets` [结构](https://github.com/sstephenson/sprockets/#the-directive-processor)在其中定义的。捆绑资产保存在磁盘上，包含已处理资产的内容。捆绑资产充当不同已处理资产的容器。

现在，让我们深入了解一下 Rails 资产预编译是如何完成这项工作的。在本文中，default `application.js`被用作参考资源，文章重点关注`application.js`是如何被预编译的。

继续之前，请在您最喜欢的编辑器中打开以下文件。如果你愿意，你可以点击下面在浏览器中查看它们。

*   [action pack-3 . 2 . 15/lib/链轮/railtie.rb](https://github.com/rails/rails/blob/v3.2.15/actionpack/lib/sprockets/railtie.rb)
*   [action pack-3 . 2 . 15/lib/链轮/assets.rake](https://github.com/rails/rails/blob/v3.2.15/actionpack/lib/sprockets/assets.rake)
*   [action pack-3 . 2 . 15/lib/链轮/static_compiler.rb](https://github.com/rails/rails/blob/v3.2.15/actionpack/lib/sprockets/static_compiler.rb)
*   链轮-2 . 2 . 2/lib/链轮/base.rb
*   链轮-2 . 2 . 2/lib/链轮/index.rb
*   链轮-2 . 2 . 2/lib/链轮/bundled_asset.rb
*   链轮-2 . 2 . 2/lib/链轮/processed_asset.rb
*   链轮-2 . 2 . 2/lib/链轮/context.rb
*   链轮-2 . 2 . 2/lib/链轮/directive_processor.rb

## rake 资源:预编译

为了预编译资产，我们键入`rake assets:precompile`。这是一个 rake 任务，被添加到第 13 行的`actionpack-3.2.15/lib/sprockets/railtie.rb`中，在那里加载了`sprockets/asset.rake`文件。这个代码在`Sprockets::Railtie`中，T3 是`Rails::Railtie`的子类。正如在第 1 部分中所讨论的，Railtie 是 Rails 框架的核心，它提供了几个钩子来扩展 Rails 和/或修改初始化过程。它还用于添加 rake 任务。

在第 59-67 行的`actionpack-3.2.15/lib/sprockets/assets.rake`中，有一个 rake 任务。这就是当我们运行`rake assets:precompile`时被调用的 rake 任务。这个 rake 任务又调用另一个 rake 任务，后者调用`internal_precompile`方法。

在第 50 行的`internal_precompile`中，通过传递不同的选项创建了一个`Sprockets::StaticCompiler`的实例，并将其分配给`compiler`。传递给`Sprockets::StaticCompiler`的`env`是第 23 行`actionpack-3.2.15/lib/sprockets/railtie.rb`中分配给`Rails.application.assets`的`Sprockets::Environment`的实例(我们在第 1 部分中讨论过)。)

`internal_precompile`调用`Sprockets::StaticCompiler`上的`compile`。这是执行所有必要操作的方法，以预编译我们的资产并将它们写入磁盘。

让我们来看看`actionpack-3.2.15/lib/sprockets/static_compiler.rb`中的`compile`方法。在此方法中，您将看到以下行:

```
env.each_logical_path(paths) do |logical_path|
```

`env`是`Rails.application.assets`，它是`Sprockets::Environment`的实例。上面那行`paths`基本上就是`actionpack-3.2.15/lib/sprockets/assets.rake`传给`Sprockets::StaticCompiler`的`Rails.application.config.assets.precompile`。

第 332 行的`sprockets-2.2.2/lib/sprockets/base.rb`中定义了`each_logical_path`。这个方法依次调用定义在`each_logical_path`上的`each_file`。

`each_file`在`paths`上执行一次`each`。`paths`是包含我们资产的目录列表。新创建的 Rails 3.2.15 应用程序的路径可能是这样的，其中没有添加任何额外的 gem。

```
/home/ubuntu/Desktop/work/asset_pipeline_article/app/assets/images
 /home/ubuntu/Desktop/work/asset_pipeline_article/app/assets/javascripts
 /home/ubuntu/Desktop/work/asset_pipeline_article/app/assets/stylesheets
 /home/ubuntu/Desktop/work/asset_pipeline_article/vendor/assets/javascripts
 /home/ubuntu/Desktop/work/asset_pipeline_article/vendor/assets/stylesheets
 /home/ubuntu/.rvm/gems/ruby-1.9.3-p194@exporter-imranlatif/gems/jquery-rails-3.0.1/vendor/assets/javascripts
 /home/ubuntu/.rvm/gems/ruby-1.9.3-p194@exporter-imranlatif/gems/coffee-rails-3.2.2/lib/assets/javascripts
```

根据您的 Rails 应用程序路径，您的绝对路径会有所不同。前 5 条路径来自`app/assets`和`vendor/assets`。最后两条路径分别来自`jquery-rails`和`coffee-rails`宝石。

如果您是一个 gem 作者，并且想要在`Rails.application.config.assets.paths`中包含您的 gem 的资产路径，那么您需要创建一个从`Rails::Engine`继承的引擎类。这里描述的[就是](http://guides.rubyonrails.org/asset_pipeline.html#adding-assets-to-your-gems)。`paths`先分配到`Rails.application.config.assets.paths`，后分配到`Rails.application.assets.paths`。你可以在这里查看将`Rails.application.config.assets.paths`赋值给`Rails.application.assets.paths` [的代码。](https://github.com/rails/rails/blob/v3.2.15/actionpack/lib/sprockets/bootstrap.rb#L12)

回到`sprockets-2.2.2/lib/sprockets/base.rb`中的`each_file`。`each_file`在`paths`上执行`each`，在该块中`each_entry`被调用。

`each_entry`定义在`each_file`的正上方。`each_entry`递归遍历每个目录并收集该路径中的所有文件。从给定路径收集所有文件后，它执行传递给它的块。在`each_entry`方法中可以看到下面一行:

```
paths.sort_by(&:to_s).each(&block)
```

上面的行调用从`each_logical_path`传递给`each_file`方法的块。那个块调用`logical_path_for_filename`，它在同一个文件中定义。

`logical_path_for_filename`调用`matches_filter`，也在同一个文件中定义。`matches_filter`火柴`filename`根据`filters`传递给它。`filters`基本上是`Rails.application.config.assets.precompile`，一个包含不同过滤器的数组，根据这些过滤器应该处理一个资产。

默认情况下，该数组有两项。首先，一个匹配静态资产名称的`Proc`。第二项包含一个匹配 CSS 和 Javascript 文件的`RegExp`对象。

如果文件名不匹配，那么它就没有资产。对于静态资产，比如图像，它应该匹配由`filters`数组中的`proc`定义的过滤器。对于捆绑资产，文件名应该与`filters`中定义的`RegExp`相匹配。

如果我们想要捆绑与正则表达式不匹配的 CSS 或 Javascript 文件，那么我们必须将它添加到配置文件的`Rails.application.config.assets.precompile`中。在`config/environment/production`中，我们通过使用以下代码行来实现这一点:

```
config.assets.precompile += %w( sitepoint.js )
```

`filters`数组中的`proc`只匹配静态资产或没有`.css`或`.js`扩展名的资产。`filters`中的`RegExp`只匹配`application.css`或`application.js`的文件。因为“sitepoint.js”不符合这两个标准，所以它不会被捆绑。将`sitepoint.js`附加到`Rails.application.config.assets.precompile`会强制匹配，并将执行资产捆绑。

在获得传递给`filters_matches`方法的`filename`的成功匹配后，`logical_path_for_filename`将结果返回给从`each_logical_path`传递给`each_file`的块。这个块`yield`的文件名是从`actionpack-3.2.15/lib/sprockets/static_compiler.rb`文件中的`compile`传递给它的另一个块。该块调用在`sprockets-2.2.2/lib/sprockets/index.rb`中定义的`find_asset`。该方法将`options[:bundle]`设置为 true，并从`sprockets-2.2.2/lib/sprockets/base.rb`中的`Base`类调用`find_asset`。

`find_asset`根据文件名解析路径，并将文件的绝对路径分配给`pathname`。

`logical_path`只是文件的名称，即`application.js`。`find_asset`调用`build_asset`，T3 决定如何处理资产。如果没有处理器运行，它就把它当作一个`StaticAsset`。

如果有一些处理器要运行，那么它会将该资产视为一个`BundledAsset`。第一次调用`build_asset`，`options[:bundle]`是`true`，于是就产生了一个`BundledAsset`。`find_asset`在`options[:bundle]`设置为`false`的情况下被再次调用，因此创建了一个`ProcessedAsset`。

记住我们的讨论，即`BundledAsset`实际上被保存到包含不同`ProcessedAsset`实例的磁盘中。每个`BundledAsset`至少有一个`ProcessedAsset`。在单个 CSS 或 Javascript 文件的情况下，比如`sitepoint.js`，`BundledAsset`和`ProcessedAsset`都指向同一个文件。

`ProcessedAsset`的初始化器执行`context.evaluate(pathname)`来获取`pathname`中文件的内容。`sprockets-2.2.2/lib/sprockets/context.rb`中的`evaluate`对`path`中提到的文件进行不同的操作。它聚集`processors`来运行文件。

对于 Javascript 文件，默认情况下，两个`processors`是`Sprockets::DirectiveProcessor`和`Sprockets::SafetyColons`。`Sprockets::DirectiveProcessor`用于在每个 CSS 和 Javascript 文件上运行一个指令处理器。它基本上扫描 CSS 或 Javascript 文件，并根据`Sprockets`语法捕获文件中需要的文件。流行的指令有`require`、`require_tree`、`requite_self`等。

`evaluate`在`processors`数组上运行`each`，并且`processor`的一个新实例被创建。然后在每个处理器模板上调用`render`方法。`Sprockets::DirectiveProcessor`在`sprockets-2.2.2/lib/sprockets/directive_processor.rb`中定义，是`Tilt::Template`的子类。当`render`在`Sprockets::DirectiveProcessor`上被调用时，它也会在`Sprockets::DirectiveProcessor`上调用`evaluate`。在`evaulate`中，`process_directives`被调用，它扫描文件并收集需要在当前文件上运行的指令。

对于我们的参考资产`application.js`,结果可能如下所示:

```
[[13, "require", "jquery"], [14, "require", "jquery_ujs"], [15, "require_tree", "."]]
```

在获得指令并将它们分配给`@directives`后，执行`process_directives`。如您所见，这行代码向每个指令发送了一条`process_<name>_directive`消息:

```
send("process_#{name}_directive", *args)
```

所有这些方法都属于`Sprockets::DirectiveProcessor`。让我们看看调用`process_require_tree_directive`时会发生什么。

`process_require_tree_directive`调用`each_entry`，我们现在知道它从传递给它的路径中返回所有文件的路径。`require_tree`表示给定路径下的所有文件都应该是`required`。您可能已经注意到，在`each_entry`块中，如果一个文件名与当前文件名匹配，那么它会被`next`语句跳过。这种说法的目的是什么？我们稍后将回到这一点。

`context.require_asset`用于将资产分配给`@_required_paths`数组。`@_required_paths`是当前`ProcessedAsset`所依赖的资产路径。在`process_directives`完成后，`process_source`被调用，它通过移除指令处理器来捕获当前`ProcessedAsset`的源。

在所有处理器的`evaluate`完成后，当前`ProcessedAsset`的源被返回并保存到`ProcessedAsset`中的`@source`。

然后，调用来自`sprockets-2.2.2/lib/sprockets/processed_asset.rb`的`build_required_assets`。在`build_required_assets`你会看到下面的代码:

```
@required_assets = resolve_dependencies(environment, context._required_paths + [pathname.to_s]) -
      resolve_dependencies(environment, context._stubbed_assets.to_a)
```

`resolve_dependencies`用于解析当前`ProcessedAsset`的依赖关系。到目前为止，我们只有当前`ProcessedAsset`所需的文件路径。因为`application.js`资产`context._required_paths`可能看起来像这样。

```
{{GEMPATH}}/jquery-rails-3.0.1/vendor/assets/javascripts/jquery.js
{{GEMPATH}}/jquery-rails-3.0.1/vendor/assets/javascripts/jquery_ujs.js
```

`{{GEMPATH}}`代表保存不同 gem 的 gems 目录的绝对路径。你已经注意到在上面的路径中没有`application.js`，因为`require_tree`指令它应该在那里。看看传递给`resolve_dependencies`的参数。我们显式地将当前的`pathname`添加到列表`context._required_paths`中。记住，在我们上次的讨论中，在`process_require_tree_directive`中我们跳过了当前文件。

正如我们所讨论的，一个`BundledAsset`至少会有一个`ProcessedAsset`。默认情况下，我们的文件不包含任何要运行的处理器，所以如果我们希望它的路径包含在`context._required_paths`中，我们必须给每个文件添加一个指令处理器，这显然是不理想的。我们选择了另一条路。在指令处理过程中，我们没有包含文件的路径，而是在从`build_required_assets`调用`resolve_dependencies`时，将它显式地包含到了`context._required_paths`中。如果有一些指令处理器要在这个文件上运行，那么我们就跳过这个文件名，因为我们知道我们是在调用`resolve_dependencies`时手动添加的。

`resolve_dependencies`在传递给它的所有路径上循环，如果它匹配当前`ProcessedAsset`的路径，那么它被附加到`assets`数组。如果文件名不匹配，调用`find_asset`方法执行`elsif`语句。

为了收集源代码并对任何文件运行处理器，我们需要创建一个`ProcessedAsset`的实例。`jquery.js`文件就是这些路径之一。让我们看看它是如何被递归处理并追加到`application.js`的`ProcessedAsset`实例的`assets`数组中的。从`resolve_dependencies`中的`elsif`开始，你会看到下面一行

```
asset = environment.find_asset(path, :bundle => false)
```

如前所述，当我们通过将`bundle`设置为`false`来调用`find_asset`时，这意味着我们正在创建一个`ProcessedAsset`的实例。在`resolve_dependencies`的`elsif`中为`jquery.js`创建一个`ProcessedAsset`。

记住，`ProcessedAsset`的初始化器调用对资产执行不同操作的`context.evaluate`。`jquery.js`是一个 JavaScript 文件，不包含任何依赖项，所以不会通过调用`context.require_asset`向其添加任何依赖项。在处理器已经在`jquery.js`上运行之后，`resolve_dependencies`被调用的方式与前几步调用`application.js`的方式相同。

由于`jquery.js`不依赖于任何资产，`context._required_paths`为空，我们显式添加当前路径名并将数组传递给`resolve_depedencies`。`jquery.js`的依赖只有一个，就是`jquery.js`本身。`if`语句为真，当前的`ProcessedAsset`实例(我们称之为`self`)被添加到`assets`数组中，该数组在`build_required_assets`中被分配给`@required_assets`。对于那些没有任何依赖关系并且是某个`BundledAsset`的一部分的资产，`@required_assets`指向一个项目，这个项目就是资产本身。

在为`jquery.js`创建了一个`ProcessedAsset`的实例并构建了它所需的资产之后，控制权返回到`resolve_depedencies`中的`elsif`，它正在为`application.js`构建所需的资产。`jquery.js`的`ProcessedAsset`实例被分配给`resolve_depedencies`中的`asset`。然后我们遍历`jquery.js`的`required_assets`，并将`ProcessedAsset`的一个实例添加到`assets`数组中。

对`jquery_ujs.js`和其他路径也执行这些步骤。在将`application.js`的所有路径的`ProcessedAsset`的实例附加到`application.js`的`assets`数组之后，我们从`resolve_dependencies`方法返回该数组，该方法被分配给`application.js`的`ProcessedAsset`的实例变量`@required_assets`。

`ProcessedAsset`将结束执行并返回到`BundledAsset`的初始化器。`application.js`的`ProcessedAsset`实例分配给`@processed_asset`，必要的资产分配给`@required_assets`。我们知道每个`ProcessedAsset`都有自己的源，所以我们开始将所有`ProcessedAsset`实例的源分配给`@source`。`to_a`返回`required_assets`，我们得到了`ProcessedAsset`实例的字符串表示，意思是源。这里定义了这种行为。当我们收集了所有`ProcessedAsset`实例的源代码后，我们再次基于当前文件的`content_type`运行一些`processors`。对于 Javascript 文件，我们运行`Sprockets::Processor (js_compressor)`处理器，将源代码传递给它。在处理`@source`并获得新的`@source`之后，基于源/内容计算摘要。

当`BundledAsset`的构造函数完成时，控制最终返回到`actionpack-3.2.15/lib/sprockets/static_compiler.rb`中的`compile`。这是魔法开始的地方，我们带着完成的`BundledAsset`回到这里。接下来的步骤很简单，因为`BundledAsset`保存在磁盘上的方式和`StaticAsset`以`file_name-digest.file_ext`格式保存是一样的。

我在上面描述的整个过程针对`paths`数组中的每个文件继续进行。

## 未回答的问题已回答

您可能想知道为什么我们事先为`StaticAsset`创建了摘要，但是只在执行了所有处理之后才为`BundledAsset`创建摘要。这个问题的答案很简单。正如在第 1 部分中所讨论的，在`StaticAssets`上没有任何处理，所以我们只是将它们复制并粘贴到`public/assets`目录中，并附上基于其内容的摘要。`StaticAsset`的内容如图像等。在复制/粘贴之间不会改变，所以我们可以预先计算它的摘要。而计算`BundledAsset`的摘要需要在计算摘要之前采取一些步骤。

我们在本文的第 1 部分中讨论过,`write_to`方法用于创建一个资产，那么`Sprockets`如何创建同一个文件的两个版本呢？当我们执行`rake assets:precompile`时，在第 59-67 行从`actionpack-3.2.15/lib/sprockets/assets.rake`调用`:all`任务。这个任务有两个 rake 任务，一个被调用来创建资产的摘要版本，另一个被调用来创建资产的非摘要版本。你可以在第 69-71 行和第 73-75 行检查这两个任务。这两个任务都调用`internal_precompile`方法，并相应地设置`digest`参数。对`internal_precompile`的非消化调用不会花费太多时间，因为所有内容都已经被缓存了。

## 最后的想法

我已经尽力解释了 Rails 资产管道的内部编码。阅读代码是一种艺术，同时也是一种乐趣。通过阅读其他开发人员的代码，我们可以提高我们的技术技能。阅读令人敬畏的`Sprockets`代码真是一种享受。

## 分享这篇文章