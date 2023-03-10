# 将 Jasmine 集成到 Rails 中进行可靠的 JavaScript 测试

> 原文：<https://www.sitepoint.com/integrate-jasmine-into-rails-for-solid-javascript-testing/>

![jasmine_vertical](img/6727dd801e070ce9ce7824cd76937ff7.png)

Jasmine 是一个以行为驱动开发风格为 JavaScript 代码创建测试的框架。在本文中，您将学习如何将 Jasmine 集成到一个 [Ruby on Rails](http://rubyonrails.org) 应用程序中。

Jasmine 有一个清晰的语法，目的是提供关于被测试代码的描述性行为。使用 [RSpec](http://rspec.info) 或`Minitest::Spec`的人会对 Jasmine 很熟悉。即使您没有使用过这两个框架，为 Jasmine 创建测试也不会有任何困难。

阅读完本文后，您将能够将 Jasmine 集成到您的 Rails 应用程序中，为您的 JavaScript 文件创建测试，在您的应用程序中测试您的 [Rails UJS](https://github.com/rails/jquery-ujs) 功能，并能够将 Jasmine 作为您现有应用程序的一部分运行(对吗？？)持续集成现有构建系统。

## 装置

要在 Rails 应用程序中安装一个正常工作的 Jasmine，您需要将 [jasmine-gem](https://github.com/jasmine/jasmine-gem) 放到`Gemfile`中。确保它在`development`和`test`组内，如下所示:

```
group :development, :test do
  gem "jasmine"
end 
```

然后运行以下步骤安装 gem:

```
bundle install 
```

一旦 gem 安装完成，运行 Jasmine 生成器，如下所示:

```
rails generate jasmine:install 
```

生成器将在终端上输出如下内容:

```
create  spec
create  spec/javascripts/helpers/.gitkeep
create  spec/javascripts/support/jasmine.yml
create  spec/javascripts/support/jasmine_helper.rb 
```

这些是 jasmine 运行的必要文件。关于配置 jasmine 安装的详细信息将在配置部分解释。

## 使用

一旦安装步骤完成，您应该能够运行 Jasmine 了。Jasmine 可以以几种不同的方式运行:可以在浏览器中运行 Jasmine，也可以在持续集成(CI)模式下运行。使用 CI 模式的好处是您可以将它集成到您的构建系统中。

### 在浏览器中运行

在浏览器中运行 Jasmine 之前，您需要启动 Jasmine 服务器:

```
rake jasmine 
```

然后将您的浏览器指向以下 URL:

```
http://localhost:8888/ 
```

为了查看测试结果，gem 提供了另一个生成器来创建测试示例。从另一个终端会话中，运行以下命令:

```
rails generate jasmine:examples 
```

下面是生成的示例测试结果的屏幕截图:

![jasmine-tests-result](img/94e15af5e58bf5eca46903d8c669aea0.png)

可以看到页面右上角有一个按钮。它有几个选项可以改变 Jasmine 运行测试的方式。让我们进入详细解释的可用选项。

#### 引发异常

通过单击此选项，它将重定向到以下 URL:

```
http://localhost:8888/?catch=false 
```

该选项禁用 JavaScript 源代码和 Jasmine 测试文件中的错误捕获。如果没有此选项，测试文件和源文件中的所有错误都被包装在 catch 块中。

启用这个选项的好处是允许 Jasmine 测试结果在出现错误时被故意破坏。因此，你知道失败是由于一个错误，而不是失败的期望。

#### 预期失败时停止规范

通过单击此选项，它将重定向到以下 URL:

```
http://localhost:8888/?throwFailures=true 
```

这个选项立即停止失败的规格。让我们来看看下面的代码示例:

```
it("should not be possible to play", function() {
  player.stop();
  expect(player.isPlaying).toBeFalsy();
  expect(player.currentlyPlayingSong).not.toEqual(song);
}); 
```

如果没有此选项，失败的规格将输出失败规格的所有预期失败消息:

```
..F..
Failures:
          Player when song has been paused should be possible to resume

          Expected true to be falsy.
          Error: Expected true to be falsy.

          Expected Song({  }) not to equal Song({  }).
          Error: Expected Song({  }) not to equal Song({  }). 
```

使用这个选项，失败的规范只输出第一个预期失败消息，然后停止执行失败的规范。

```
..F..
Failures:
          Player when song has been paused should be possible to resume

          Expected true to be falsy.
          Error: Expected true to be falsy. 
```

#### 以随机顺序运行测试

通过单击此选项，它将重定向到以下 URL:

```
http://localhost:8888/?random=true 
```

此选项使测试能够在每次运行时以随机顺序运行。如果没有此选项，每次测试运行时，所有测试都将以相同的顺序运行。

启用这个选项的好处是揭示测试之间的依赖性，因此，您可以减少测试依赖性，并且每个测试都将具有良好的隔离性。

### 作为持续集成运行

您可以使用无头浏览器将 Jasmine 集成到您的[持续集成](https://www.sitepoint.com/continuous-integration-with-jenkins-1/)工作流中。幸运的是，gem 已经支持与无头浏览器的集成。

要在 CI 模式下运行 Jasmine，请按如下方式运行:

```
rake jasmine:ci 
```

无头浏览器集成使用 [PhantomJS](http://phantomjs.org/) 。如果您的系统中没有安装 PhantomJS，它会在您可以在 CI 模式下运行它之前尝试下载它。

上面的命令输出如下所示:

```
Waiting for jasmine server on 49948...
jasmine server started
.....
5 specs, 0 failures 
```

默认情况下，jasmine 会尝试在 CI 模式下找到一个随机打开的端口。要设置端口，您可以在 **jasmine_helper.rb** 中配置端口:

```
Jasmine.configure do |config|
   config.ci_port = 1234
end 
```

## 配置

默认配置应该可以满足您的需求。但是，如果你需要不同的配置，有两个文件可以用来完成这个任务: **jasmine.yml** 和 **jasmine_helper.rb** 。

在我们继续下一步之前，您需要知道，如果从浏览器运行，一些配置选项并不适用。在这种情况下，从浏览器运行时使用“选项”菜单:

Jasmine 负载配置的顺序如下:

1.  Jasmine 首先读取 **jasmine.yml** 配置文件。
2.  Jasmine 读取 **jasmine_helper.rb** 并覆盖由
    定义的配置 **jasmine.yml** 。

### **茉莉. yml**

以下是 **jasmine.yml** 中可用配置的示例，提供了默认值:

```
# spec/javascripts/support/jasmine.yml
random: false
show_console_log: false
stop_spec_on_expectation_failure: false 
```

### **jasmine_helper.rb**

以下是 **jasmine_helper.rb** 中可用配置的示例，提供了默认值:

```
# spec/javascripts/support/jasmine_helper.rb
Jasmine.configure do |config|
  config.random = false
  config.show_console_log = false
  config.stop_spec_on_expectation_failure: false

  config.show_full_stack_trace = false
  config.prevent_phantom_js_auto_install = false
  config.server_port = 8888

  # ci port is random by default
  # config.ci_port = 1234
end 
```

## 测试

在 Rails 应用程序中使用 Jasmine 测试 JavaScript 文件应该相当简单。它通常使用与 Jasmine 相同的标准，但是对于 Jasmine 在 Rails 中的安装，需要考虑一些特殊的事情。

### 测试 JavaScript

rails 应用程序中 JavaScript 的测试文件位于 **spec/javascripts** 文件夹中。对于每个 javascript 文件，您需要将测试文件放在与文件相同的路径中。例如，如果您的应用程序中有以下 javascript 文件:

**app/assets/JavaScript s/jasmine _ examples/player . js**

将等级库文件放在以下路径中:

**spec/JavaScript s/jasmine _ examples/player spec . js**

Jasmine 将在下一次测试运行中包含这个测试。没有运行测试所需的配置。为了测试您的 JavaScript 文件，您需要参考 [Jasmine 文档](http://jasmine.github.io)。

### UJS 铁路公司

Rails UJS 可能很难测试，因为响应可以是任何格式，比如 JavaScript、JSON 或 HTML。不管响应格式如何，您仍然可以使用 Jasmine 测试 Rails UJS。

你需要识别 3 个部分:
–UJS 链接。
–响应。
——副作用。

一旦你确定了上述需求，你就可以创建虚拟响应，触发 UJS，并断言副作用。例如，下面的代码测试了一个从页面中移除 DOM 元素的 UJS 链接:

```
it("removes the associated element", function(){
  var response = { status: 200 };
  $('a.remove#123').trigger('ajax:success', response, 'xhr');
  expect($('li#123')).toEqual([]);
}); 
```

从上面的例子中，可以使用`ajax:success`来触发 UJS 链接。可以被触发的完整事件列表可以在 [Rails UJS 文档](https://github.com/rails/jquery-ujs/wiki/ajax)中看到。

## 插件

有时候你需要一些工具来帮助你提高工作效率。这一部分是对社区创建的一些插件的简要介绍。关于每个插件的更多详细信息，请访问官方插件库。

在安装任何插件之前，您需要正确设置配置，以便插件可以在测试运行之前正确加载。在应用程序根路径中，为插件创建一个文件夹:

```
mkdir spec/javascripts/plugins/ 
```

在 **jasmine.yml** 中，将上述文件夹添加到`helpers`部分:

```
helpers:
  - 'plugins/**/*.js' 
```

### 茉莉-Jquery

[jasmine-jquery](https://github.com/velesin/jasmine-jquery) 在 jasmine 中提供 jquery 相关的匹配器。这个匹配器提供了许多方便的 jQuery 相关匹配器，因此您可以有更好的测试效率。在这里下载最新发布的
T3 插件。

看一下下面的例子:

```
expect($('<div>some text</div>')).toHaveText(/some/) 
```

与标准的 Jasmine 匹配器一样，您也可以使用前缀`.not`将其倒置:

```
expect($('<div>some text</div>')).not.toHaveText(/other/) 
```

### 茉莉花火柴

Jasmine-Matchers 是一个在 Jasmine 中提供额外匹配器的工具。除此之外，它还提供了一个比默认更友好的期望失败消息。在这里下载最新发布的[插件](https://github.com/JamieMason/Jasmine-Matchers/releases)。

下面的测试使用 Jasmine 默认匹配器:

```
it('should distribute evenly', function() {
  expect(basket.items % 2 === 0).toEqual(true);
}); 
```

它会打印以下失败消息:

```
Expected false to equal true 
```

使用 jasmine 匹配器，测试看起来像:

```
it('should distribute evenly', function() {
  expect(basket).toHaveEvenNumber('items');
}); 
```

带有更友好的失败消息:

```
Expected member "items" of { items : 25 } to be even number. 
```

### 茉莉夹具

Jasmine-Fixture 是一个使用 CSS 选择器创建 DOM 的插件，因此你可以更容易地与 DOM 交互。它还通过在每次测试运行后删除插件创建的 DOM 来帮助您避免测试污染。在这里下载最新发布的[插件](https://github.com/searls/jasmine-fixture/releases)。

如果您没有使用 jasmine-fixture，您可以这样做来与 DOM 交互:

```
beforeEach(function(){
  $('<div id="toddler"><div class="hidden toy"><input name="toyName" value="cuddle bunny"></div></div>').appendTo('body');
});

afterEach(function(){
  $('#toddler').remove()
}); 
```

使用 jasmine-fixture，您只需要这样做:

```
beforeEach(function(){
  affix('#toddler .hidden.toy input[name="toyName"][value="cuddle bunny"]')
}); 
```

## 结论

jasmine-gem 支持将 Jasmine 集成到 Rails 应用程序中。gem 提供的特性为如何运行测试提供了灵活性，无论是从浏览器还是作为持续集成选项。Jasmine 也可以根据您的需要进行配置，比如运行测试行为，以及配置插件。jasmine-jquery、jasmine-matchers 和 jasmine-fixtures 等插件的使用可以帮助您的 JavaScript 测试更加高效。

## 分享这篇文章