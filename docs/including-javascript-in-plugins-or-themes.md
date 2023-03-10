# 以正确的方式在插件或主题中包含 JavaScript

> 原文：<https://www.sitepoint.com/including-javascript-in-plugins-or-themes/>

在撰写本文时，超过 33，000 个插件和 2，600 个主题在[WordPress.org](http://wordpress.org/)上可用，我们大概可以添加更多这个平台上没有的。他们可以做很多不同的事情，并且经常使用 JavaScript 来提供一些特性。

![JavaScript and WordPress](img/4327cfcb9c8a74e2da2cd286afa68d1e.png)

基本上，将 JavaScript 代码包含到一个主题或插件中并不困难:WordPress 产生的只是 HTML，所以使用带有代码的`script`标签直接进入其中或进入一个通过`src`属性链接的文件中可能就足够了——如果你是一个人，单独开发。

事实是，你并不孤单。使用 WordPress 的人可以在你的旁边安装插件或者主题，这些工具的开发者也可以使用 JavaScript。而且，WordPress 本身也在使用 JavaScript。问题是，如果每个人都不加注意地包含他们的脚本，结果页面会变得不必要的沉重。

## 问题是

举个例子会更清楚，我举一个我很了解的:我的。

我开发了 [WP Photo Sphere](http://wordpress.org/plugins/wp-photo-sphere/) 插件，允许用户在帖子中添加全景照片。为此，我需要不同的 JavaScript 文件:用于显示全景图的库和检索全景图显示位置的特定标签的文件。此外，在最后一个脚本中，我使用了 jQuery，所以我需要包含它。

如果我不关心用户，我可以将这三个脚本插入网站的所有页面，这意味着访问者将不得不下载这些文件，即使页面并不需要它。

此时，问题已经很显著了，但是当我们想到 jQuery 时，问题变得更大了。如果两个使用这个库的插件都有这种行为怎么办？用户不仅会下载一个没用的文件，还会下载两次！

页面越重，加载的时间就越长。加载时间太长的网站比速度更快的网站的访问量要少。如果你的插件或主题使得加载时间过长，它们将不会被使用。这无疑是优化工具的巨大动力，对吗？

## 插入脚本的不可避免的功能

为了避免两次或多次插入同一个文件的问题，WordPress 为我们提供了一个很棒的功能:`wp_enqueue_script()`。**当你想插入脚本的时候，这个功能一定要成为你最好的朋友。**

顾名思义，这个函数会将你的脚本添加到一个队列中，并在适当的时候将它们包含到页眉或页脚中，因此不会在页面中间添加`script`标签。

单独使用时，`wp_enqueue_script()`函数至少需要两个参数:脚本名和它的 URL。例如，如果我想包含我的插件需要的脚本，我将这一行添加到它的主文件中(我们将在下面看到正确的方法)。

```
wp_enqueue_script('test', plugin_dir_url(__FILE__) . 'test.js');
```

然而，三个额外的参数让你对你的脚本将被包含的方式有更多的控制，尤其是最后一个参数是一个布尔值:默认情况下，这个布尔值被设置为`false`，你的脚本将被包含在`head`标签中，但是如果你将其设置为`true`，那么你的脚本将被包含在页脚中。

第四个参数在您制作不同版本的脚本时非常有用:它是一个包含版本号的字符串，将连接到 URL 的末尾。添加版本号可以确保访问者获得正确的脚本版本，而不考虑缓存。

第三个参数是最有趣的参数之一，因为它允许您指示脚本的依赖关系。例如，如果您的脚本需要库 jQuery，您可以使用这个参数。

```
wp_enqueue_script('test', plugin_dir_url(__FILE__) . 'test.js', array('jquery'));
```

正如您所看到的，依赖关系必须被表示到一个数组中，即使只有一个依赖关系。您在该数组中指定的名称是由于第二个函数而注册的脚本的名称:`wp_register_script()`。

第二个函数的用法与第一个函数的用法完全相同:您可以指定脚本的名称、URL、依赖项、版本号以及是否必须包含在页脚中。最后三个参数是可选的，但其他参数是必需的。

```
wp_register_script('test', plugin_dir_url(__FILE__) . 'test.js', array('jquery'), '1.0', true);
```

注册一个脚本不会包括它。您仍然需要使用`wp_enqueue_script()`函数来包含您的脚本，但是它的使用被简化了:您只需要指出脚本的名称。

```
wp_enqueue_script('test');
```

也许你在问自己`wp_register_script()`的用处，因为`wp_enqueue_script()`可以做所有的工作。这个函数在这里指出一个脚本应该被包含的方式，但是如果没有必要就不包含它。

一个例子会更清楚，所以假设您有两个 JavaScript 文件要包含。第一个文件是一个库，第二个文件使用这个库来做一些事情。这个库需要 jQuery 才能正常工作，所以我们注册它如下:

```
wp_register_script('mylib', plugin_dir_url(__FILE__) . 'lib/mylib.js', array('jquery'));
```

此时，库还没有包括在内，但是如果我们想要包括主文件，那么我们需要包括这个库:我们将它表示为一个依赖项。

```
wp_enqueue_script('myfile', plugin_dir_url(__FILE__) . 'myfile.js', array('mylib'));
```

所有的魔法都出现了。包含了`myfile.js`文件，但是它需要`mylib.js`文件才能工作:这是包含这个文件的依赖项的工作。但是！`mylib.js`文件需要 jQuery，所以 jQuery 也包括在内。一行包含三个文件。

但这并不是`wp_register_script()`函数的唯一优势:如果你注册了你的脚本，其他插件将能够使用它。所以如果你想制作各种插件，其中一个定义了一些库，其他的就可以包含它们。

`wp_enqueue_script()`函数最大的优点是它不会两次包含同一个脚本。例如，假设两个文件需要 jQuery:这两个文件将它表示为一个依赖项，当第一个文件被包含时，WordPress 将包含这个库。那么包含第二个脚本的时间到了。WordPress 将 jQuery 视为依赖项，但是已经包含了这个库，所以不需要再次包含它:WordPress 不会这样做。

当您尝试将两个名称不同但 URL 相同的脚本入队时，也会出现相同的行为。在下面的例子中，WordPress 将包含文件一次。

```
wp_enqueue_script('myfile', plugin_dir_url(__FILE__) . 'myfile.js', array('mylib'));
wp_enqueue_script('myotherfile', plugin_dir_url(__FILE__) . 'myfile.js');
```

但是，要小心:这种行为是有限制的:如果您在至少一个`wp_enqueue_script()`调用中指定了一个版本号，那么该脚本将被包含两次，即使这个版本号是相同的。下面的例子看起来和上一个一样，但是它将包含文件两次。

```
wp_enqueue_script('myfile', plugin_dir_url(__FILE__) . 'myfile.js', array('mylib'), '1.0');
wp_enqueue_script('myotherfile', plugin_dir_url(__FILE__) . 'myfile.js', array(), '1.0');
```

也就是说，一般来说，您没有任何理由用不同的名称将一个脚本排队。但是有必要澄清这个案子。

如你所见，**这两个函数是管理依赖关系的最好方法**:你注册它们，WordPress 会自动处理剩下的。

注意，我们使用名称“ *jquery* 来表示我们希望将 jquery 包含在我们自己的文件中，但是我们没有注册它。这个名字已经被 WordPress 注册了:当你下载 CMS 的时候，它包含了 jQuery。

这个库不是唯一的:你可以在[这个页面](http://codex.wordpress.org/Function_Reference/wp_enqueue_script#Default_Scripts_Included_and_Registered_by_WordPress)中找到 WordPress 注册的所有脚本。你可以直接或者通过一个依赖项将它们入队，酷的是你不必提供文件，所以每个使用这个脚本的插件或者主题将使用相同的文件:在我们的例子中，如果另一个插件使用 jQuery，这个库将只被包含一次。

## 不要让你的脚本到处排队！

`wp_enqueue_script()`函数确保一个脚本不会被包含两次，并将其正确地包含在页眉或页脚中。但是如果显示的页面甚至不需要您的脚本呢？

### 使用操作/过滤 API

以[向内容编辑器](https://www.sitepoint.com/adding-a-media-button-to-the-content-editor/)添加媒体按钮为例:只有当用户在编辑器上时才需要 JavaScript 文件。事实上，当一个普通的访问者在阅读一篇文章时，他们不需要这个脚本:包含它是没有用的，会使页面变得更重。

这就是为什么，当你想在页面中包含一个脚本时，问自己以下问题:什么时候需要这个脚本？当然，这个问题的答案会根据你的脚本做什么而不同。

但是一旦你找到了这个答案，试着找一个相应的动作。WordPress Codex 为我们提供了一个可用行为的列表，所以你可以在这里找到你想要的。找到了吗？很好，创建一个新的函数来将你的脚本入队，比如在你插件的主文件或者在`functions.php`文件或者你的主题中。

```
function enqueue_my_scripts() {
    wp_enqueue_script('script0', plugin_dir_url(__FILE__) . 'lib/myscript.js', array('jquery'));
    wp_enqueue_script('script1', plugin_dir_url(__FILE__) . 'another-script.js');
}
```

然后，让它在您的操作被触发时运行:

```
add_action('the_right_action', 'enqueue_my_scripts');
```

例如，当我们想要添加一个媒体按钮时，我们使用了`wp_enqueue_media`动作，这非常完美:

```
add_action('wp_enqueue_media', 'include_media_button_js_file');
```

如果您尝试在上面链接的参考资料中搜索这个操作，您可能什么也没有找到:在编写这一行时，`wp_enqueue_media`没有列出。这个列表并不详尽，但它是一个很好的起点。

如果您没有找到正确的动作，也许您可以找到另一个具有相近行为的动作，或者也许您应该尝试在过滤器中搜索[。](http://codex.wordpress.org/Plugin_API/Filter_Reference)

### 使用 WordPress 功能的力量！

通常，插件是为了一个特定的原因，一个不能使用动作/过滤器 API，或者不完全。例如，一个插件可以修改文章中的一些元素，但不是所有的元素: [shortcodes](https://www.sitepoint.com/unleash-the-power-of-the-wordpress-shortcode-api/) 就是一个典型的例子。

假设你的插件需要一些替换短代码的代码使用的脚本。没有任何动作或过滤器可以精确地锁定你的短码。但是有一个地方你可以确定你的短代码已经被找到，在那里你可以确定你的脚本是需要的。

这个地方是 WordPress 每次找到你的短码时调用的回调函数。调用这个回调函数，除非需要，否则你的脚本不会被包含进来。

但是，如果您的短代码被发现两次或更多次呢？答案很简单:没有。

实际上，试着调用两次`wp_enqueue_script()`函数，用同一个脚本:这个脚本只会被包含一次，这就是为什么这个函数是一个非常好的工具。

所以你可以把这个调用插入到你的回调函数中，或者插入到你的插件或者主题的其他部分，在那里你可以确定你的脚本是需要的:即使它们被需要几次，它们也只会被包含一次。

注意，根据你的`wp_enqueue_script()`调用完成的地方，要求 WordPress 将它们包含到`head`标签中可能为时已晚:你唯一的选择将是页脚。如果你的脚本因为一个模糊的原因必须放在`head`标签中，考虑一下。

也许你现在有一个疑问:什么时候太晚了？

每个主题都必须使用两个特定的函数:`wp_head()`和`wp_footer()`。当前者被调用时，WordPress 将自动在`head`标签中添加所有需要的行:如果你在`head`标签中请求包含你的脚本，它们将在`wp_head()`被调用时被包含，所以如果你在`wp_head()`调用后请求包含，你的脚本将不会被包含在`head`标签中。

但是 WordPress 是智能的，你的脚本仍然会被包含:当调用`wp_footer()`函数时，当必须包含在这个地方的脚本被包含时，你将能够在页脚中检索它们。

所以你有了答案:如果你绝对想在`head`标签中包含你的脚本，在`wp_head()`调用之前请求包含，它应该在主题中`head`标签的末尾。这个`wp_footer()`调用应该在文档的最末尾，在`body`标签的末尾之前。

## 结论

你的脚本对于你的插件或者主题在特定的地方做什么是有用的，但是把它们包含在不需要它们的页面中会使同样的页面变得不必要的沉重。

WordPress 为我们提供了几个工具，让我们只在需要的时候适当地包含我们的脚本。当您想使用这些工具时，唯一要问的问题是:何时何地需要我的脚本？剩下的取决于答案:找到一个动作或过滤器，将`wp_enqueue_script()`调用放到正确的函数中，使用这个函数的能力，或者将所有这些结合起来。

你可以在这里的[测试插件中找到上面描述的一些例子。它将一个脚本和一个“库”入队，并使用另一个无用的脚本创建一个短代码。](http://jeremyheleine.me/downloads/sitepoint-scripts.zip)

## 分享这篇文章