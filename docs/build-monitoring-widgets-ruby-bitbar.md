# 用 Ruby 和 BitBar 构建监控窗口小部件

> 原文：<https://www.sitepoint.com/build-monitoring-widgets-ruby-bitbar/>

![BitBar-small](img/6b5973051fe67929a5a4a945046e61d1.png)

Ruby 开发人员习惯于寻找打包成宝石的非常有用的开源项目。在本教程中，我们将看看 BitBar，这是一个不是用 Ruby 构建的开源项目，但是当与一小部分 Ruby 结合使用时，它会变得非常强大。

虽然原来的项目不再被积极维护，但该项目的更新分支可以在这里找到:[https://github.com/kamenevn/bitbar](https://github.com/kamenevn/bitbar)

BitBar 为 Mac OS X 用户提供了一种从脚本的标准输出创建菜单栏项目的简单方法。我将向您展示如何创建一个简单的站点 ping 监视器，然后我们将创建一个 Twitter 追随者计数器。

## 安装 BitBar

要安装 BitBar，请访问位于 https://github.com/kamenevn/bitbar 的 GitHub 库。下载 zip 文件(下载按钮在右边栏)。在存档的文件夹中，您会发现一个“Releases”文件夹，其中保存着`BitBar.app`文件。将它放在您的应用程序目录中。打开此应用程序会启动 BitBar。

## BitBar 如何工作:文件结构

BitBar 是一个简单的应用程序，它按照文件名决定的时间间隔运行给定目录中的所有 shell 脚本。当然，在向 BitBar 添加脚本时应该非常小心，尤其是在添加低间隔时间时。

要每 5 分钟运行一个给定的文件，命名结构如下所示:

```
my_script.5m.sh
```

当然，你也想让你的脚本可执行。注意，BitBar 在其文档中将这些脚本称为“插件”。

## BitBar 如何工作:脚本输出

为了给你的 BitBar 插件添加可视文本，你需要发送输出到`STDOUT`。在 Ruby 中，这只是`puts`的输出。默认情况下，BitBar 将循环遍历输出的每一行，除非您输出特定的字符串““”；点击菜单栏中的 BitBar 项后，该行之后的任何内容都将显示在下拉列表中。

BitBar 也允许改变文本颜色和响应点击。单击可以打开一个 URL 或触发另一个 shell 脚本，并且可以设置在后台这样做。

下面是一个简单的 Ruby 脚本，它会在 BitBar 中创建一个指向 StackOverflow 的链接:

```
url = "https://stackoverflow.com"
puts "Go to StackOverflow | href=#{url}"
```

注意分隔输出文本和点击参数的`|`。`href`参数告诉 BitBar 在点击时打开这个 URL。下面是一个更复杂的例子，显示了一些触发另一个可执行脚本的红色文本:

```
puts "Run My Script | color=#ff0000 bash=/path/to/your/script.sh"
```

当然，同样，您会希望使用`chmod`将该脚本设置为可执行的。

最后，如果脚本带有参数，您可以使用`param1`、`param2`和`param3`添加它们。如果您想在单击时在后台执行 bash 脚本，请将参数`terminal=`设置为`false`。例如，您可能希望将当前时间用作在后台运行的脚本的参数:

```
current_time = Time.now.to_i
puts "Run My Script | color=#ff0000 bash=/path/to/your/background_script.sh terminal=false param1=#{current_time}"
```

## 创建站点监视器

现在我们知道了 BitBar 是如何工作的，让我们用 Ruby 创建一个站点监视器，然后我们就可以使用 BitBar 了。作为一个免责声明，你应该知道仅仅从你自己的电脑上检查一个站点的状态并不能给你一个完整的画面。要真正知道你的网站是否宕机，使用监控服务和网站，如[uptimerobot.com](http://uptimerobot.com)。但是这个小部件将提供快速浏览，并在潜在问题发生时发出信号。

根据站点列表的状态，所需的行为是红色或绿色的项目符号。我希望能够将网站列表保存在一个易于更新的 YAML 文件中。当其中一个站点关闭时，我希望能够点击菜单栏中的项目符号，并看到失败的站点。我还希望能够在浏览器中单击该菜单项来访问该站点。

让我们从一些红宝石开始吧！

### 站点检查器类

首先，我们将创建一个名为`SiteChecker`的简单类:

```
# /path/to/bitbar_plugins/site_checker/site_checker.rb
require 'yaml'
require 'net/http'

class SiteChecker

  def initialize(sites)
    @sites = sites
  end

  def up?(server, port=80)
    http = Net::HTTP.start(server, port, {open_timeout: 5, read_timeout: 5})
    response = http.head("/")
    response.code == "200"
  rescue Timeout::Error, SocketError
    false
  end

  def run
    results = {}
    @sites.map {|s| results[s] = up?(s) }
    results
  end
end
```

太好了！现在我们有了一个简单的 Ruby 类，它可以检查任何给定的站点，可以用要检查的站点列表进行初始化，并返回结果的散列。

接下来，让我们编写将使用`SiteChecker`类的实际 BitBar 插件。在名为`site_checker`的 BitBar plugins 文件夹中添加一个文件夹，并在那里保存 **site_checker.rb** 文件。接下来，创建一个名为 **site_checker.3m.sh** 的文件，并将其放在 BitBar plugins 目录中。该文件如下所示:

```
#!/usr/bin/env ruby
# Note: the ruby path above should point to your Ruby installation.
# You can verify this by checking `which ruby`
$:.unshift File.dirname(__FILE__)
require 'site_checker/site_checker.rb'
require 'yaml'
def run
  sites = YAML.load_file(File.join(__dir__, 'site_checker/sites.yml'))["sites"]
  site_checker = SiteChecker.new(sites)
  results = site_checker.run
  if results.values.include? false
    puts "• | color=#ff0000"
    puts "---"
    results.select {|key, val| val == false }.keys.each do |site|
      puts "#{site} down | href=http://#{site}"
    end
  else
    puts "• | color=#82B021"
  end
end
run
```

这个简单的脚本格式化了我们的`SiteChecker`类的输出，供 BitBar 使用。最后，我们需要确保我们已经在 **site_checker** 目录中添加了 **sites.yml** 文件:

```
sites:
  - "google.com"
  - "twitter.com"
  - "yoursite.com"
```

确保你的站点只使用它的域名(没有任何协议)。

最后一步是使脚本可执行。您可以通过运行`chmod +x site_checker.3m.sh`来实现这一点。生成的文件结构应该如下所示:

```
├── site_checker
│   ├── site_checker.rb
│   └── sites.yml
├── site_checker.3m.sh
```

## Twitter 关注者计数

接下来，让我们做一些更复杂的事情。我们将显示我们关注了多少用户，以及有多少用户在 Twitter 上关注了我们。

第一步是[向 Twitter](https://apps.twitter.com/) 注册一个应用。一旦您遵循了这些步骤并成功地创建了项目，您将需要定位应用程序的 API 键和访问令牌。

接下来，在你的 BitBar 插件目录中创建一个名为 **twitter_counter** 的文件夹，以及一个名为 **twitter_counter.10m.sh** 的相关插件文件。

在新创建的 **twitter_counter** 文件夹中，创建一个 **account_detail.rb** 文件。我们将使用它来创建一个简单的类，该类将返回 Twitter 帐户的关注计数信息。

`AccountDetail`类看起来像这样:

```
require 'twitter'
class AccountDetail
  attr_accessor :user

  def initialize(username)
    @client = create_client
    @user = @client.user(username)
  end

  def followers
    user.followers_count
  end
  def followings
    user.friends_count
  end

private

  def create_client
    Twitter::REST::Client.new do |config|
      config.consumer_key        = "YOUR CONSUMER KEY"
      config.consumer_secret     = "YOUR CONSUMER SECRET"
      config.access_token        = "YOUR ACCESS TOKEN"
      config.access_token_secret = "YOUR ACCESS TOKEN SECRET"
    end
  end
end
```

如果您还没有运行`gem install twitter`,请确保您已经运行了。在前面的例子中，我们使用了内置库。Twitter 是一个第三方的宝石，在这个类工作之前必须安装它。

对于我们的用例来说，`AccountDetail`类本质上包装了 Twitter gem 的一个非常小的子部分。接下来，编辑 **twitter_counter.10m.sh** 文件，如下所示:

```
#!/usr/bin/env

$:.unshift File.dirname(__FILE__)
require 'twitter_counter/account_detail'

def run
  @account_detail = AccountDetail.new('your_twitter_name')
  puts "#{@account_detail.followers} followers"
  puts "#{@account_detail.followings} following"
end
run
```

最后，编辑脚本的权限，使其可执行。只要一切设置正确，您应该会在菜单中看到一个简单的小部件，它在您的“关注者”计数和您的“追随”计数之间循环。当然，我们可以使用更多的 Twitter API 来做更有趣的事情！

## 结论

虽然这个工具可能不会在您打算交付给客户的项目中使用，但它肯定会是创建您自己的原生应用程序的一个有用且有趣的工具。以下是更多使用 BitBar 的项目的一些想法:

*   构建状态指示器(与 CircleCI 或 Codeship 集成)
*   条纹销售股票
*   一个简单易用的动作列表，用于执行常见的脚本化任务或宏功能
*   用类似于(Spark)的东西进行可视化绘图[https://github . com/holman/Spark]

在这里和我们分享你的想法吧！黑客快乐！

## 分享这篇文章