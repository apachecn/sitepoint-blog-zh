# 在 Rails 中使用 CoffeeScript

> 原文：<https://www.sitepoint.com/using-coffeescript-in-rails/>

CoffeeScript 于 2009 年首次发布，目前仍在不断开发中，它是一种用于编写 JavaScript 的替代语法，正迅速受到欢迎。因为 CoffeeScript 可以编译成 JavaScript(而不是汇编或其他像许多语言一样的字节码)，所以它已经可以在支持 JavaScript 的浏览器和 node.js 这样的平台上使用。

今天，我将向您展示如何将 CoffeeScript 添加到 Rails 应用程序中——在这种情况下，我们将继续在我的[构建您的第一个 Rails 应用程序](https://www.sitepoint.com/building-your-first-rails-application-models/)系列中的“shorty”URL shorten er 的基础上进行构建——使用 CoffeeScript 和[咖啡师](http://github.com/Sutto/barista)添加一些基本的 JavaScript，这是一个为 Rails 应用程序添加无缝 CoffeeScript 支持的瑰宝。

## 入门指南

要开始学习咖啡师，我们首先需要将所需的宝石添加到我们的`Gemfile`中。因此，打开该文件并添加:

```
gem 'barista'
```

如果您使用的是 Ruby 1.8.7 或更低版本，您可能还需要将 JSON gem 添加到您的`Gemfile`中，如下所示:

```
gem 'json'
```

最后，您需要一个 JavaScript 解释器来运行 CoffeeScript 编译器。如果你在 OSX 上，Ruby 包装器(在`coffee-script` gem 中提供)将使用内置的系统解释器，但是如果你在另一个平台上，你将会想要检查 [ExecJS 自述文件](https://github.com/sstephenson/execjs/blob/master/README.md)，其中介绍了可用的选项。一个简单的选择是将 v8 嵌入到您的应用程序中，方法是将以下内容添加到您的`Gemfile`:

```
gem 'therubyracer', :require => nil
```

接下来，运行以下命令，强制 bundler 更新 gem，使它们可用于您的 Rails 应用程序，然后为 barista 生成一个配置:

```
bundle install
rails g barista:install
```

打开`config/initializers/barista_config.rb`，你现在应该会看到一组选项，让你配置咖啡师如何工作——随便看看。接下来，我们将开始使用 Barista 向我们的应用程序添加 CoffeeScript。

## 写一些代码

在这篇文章中，我们将使用 CoffeeScript 做一些相对简单的事情。使用 jQuery，我们将嵌入一个`iframe`来预览文本条目下面的 url。

我们需要做的第一件事是删除现有的 JavaScript 文件，并用 jQuery 替换它们。为此，我们将以下内容添加到我们的`Gemfile`:

```
gem 'jquery-rails'
```

然后运行:

```
bundle install
rails g jquery:install
```

如果它询问冲突，回答‘是’,这样现有的文件就被重写了(你*正在使用 SCM*,对吗？)这将告诉 Rails 为您的应用程序使用 jQuery 而不是 Prototype。

接下来，在文本编辑器中打开`app/coffeescripts/shorty.coffee`(您可能需要先创建文件夹)并添加以下代码:

```
$(document).ready ->
  preview = $("#preview-url")
  $('#url_url').keyup ->
    current_value = $.trim @value
    if current_value is ''
      preview.hide().attr 'src', ''
    else
      preview.show().attr 'src', current_value
```

这段代码是我们的实现——当我们更改 url 文本字段值时，它会自动更新`iframe`。接下来，打开`application/views/layouts/application.html.erb`并更改:

```
<%= javascript_include_tag :defaults %>
```

到

```
<%= javascript_include_tag :defaults, 'shorty' %>
```

这将告诉 Rails 也包含 shorty JavaScript 文件(从页面中的`shorty.coffee`文件自动编译而来)。

最后，我们需要将实际的`iframe`添加到页面中。在新的 url 视图(`app/views/urls/new.html.erb`)中，将以下代码添加到页面底部:

```
<iframe id='preview-url' style='width: 600px; height: 400px; display: none'></iframe>
```

注意:出于演示的目的，我们将 CSS 设置为内联。通常，您可以在普通的 css 样式表中这样做。

最后，保存所有文件，并使用`rails server`再次启动 web 服务器。

打开您的浏览器，您现在应该能够访问`http://localhost:3000/`，页面应该如预期加载。开始输入，你会看到它会自动显示/隐藏网址，因为它试图尽快加载。如您所见，Barista 在后台自动负责将 CoffeeScript 文件编译成 JavaScript 等价物，并且每当文件发生变化时都会这样做。

如果你打开并查看`public/javascripts/shorty.js`，你会看到使用 CoffeeScript 编译器，Barista 已经自动将上面的代码转换成类似于:

```
/* DO NOT MODIFY. This file was compiled Wed, 13 Apr 2011 01:10:20 GMT from
 * /path/to/your/app/app/coffeescripts/shorty.coffee
 */

(function() {
  $(document).ready(function() {
    var preview;
    preview = $("#preview-url");
    return $('#url_url').keyup(function() {
      var current_value;
      current_value = $.trim(this.value);
      if (current_value === '') {
        return preview.hide().attr('src', '');
      } else {
        return preview.show().attr('src', current_value);
      }
    });
  });
}).call(this);
```

## 调试问题

如果以上对你不起作用，还有几件事要检查。查看您的应用程序日志，寻找与咖啡师相关的错误信息。您会发现最常见的问题:

*   由于缺少编译器，编译失败
*   如果您正在部署到其他地方(例如 Heroku)，您可能需要嵌入一个类似 V8 的解释器(通过 gem `therubyracer`)或者将生成的 JavaScript 提交到您的存储库中

回到入门步骤，再次查看 ExecJS 链接，您将找到替代的 JavaScript 主机。

## 更进一步

既然你已经有了基本的支持，我建议你自己出去玩一玩。正如您所看到的，因为 CoffeeScript 被编译成 JavaScript，所以我们可以使用所有现有的库，比如 jQuery，只需很少的努力，就可以轻松地使用 jQuery 和您自己的 JavaScript 来:

*   这样，只有当 URL“看起来有效”时才会被加载(例如，当前版本试图预览所有内容)
*   使用计时器/其他检测变化的方法来避免我们每次输入字符时都改变它
*   展开 URL，使用类似于 [Embed.ly](http://embed.ly/) 的东西来预览内容

要了解更多关于 CoffeeScript 的知识，请阅读 coffee script 网站上的精彩互动教程，感受一下它是如何处理转换的。

如果你添加了更多的功能，请在下面评论，让我们知道你是如何做的/你是如何发现这个过程的。

## 分享这篇文章