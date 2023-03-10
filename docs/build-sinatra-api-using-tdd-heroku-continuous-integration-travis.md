# 使用 TDD、Heroku 以及与 Travis 的持续集成来构建 Sinatra API

> 原文：<https://www.sitepoint.com/build-sinatra-api-using-tdd-heroku-continuous-integration-travis/>

![sinatra_travis_heroku](img/ed4de1709b031e0a389f4ac78d947e4a.png)

这篇文章的灵感来自于[这个精彩的视频](http://ascii.io/a/4348)，其中 [Sinatra](http://www.sinatrarb.com/) 的维护者 [Konstantin Haase](http://rkh.im/) 构建了一个完全可用的应用程序，并将其部署在 [Heroku](http://heroku.com/) 上，对 Travis(他工作的地方)进行测试。

我决定做一个类似的例子，通过构建一个 API 服务的每个步骤来演示一个典型的 Sinatra 开发周期工作流——编写您的测试，编写您的代码，将代码推送到 GitHub，并检查它在部署到 Heroku 并与 Travis 持续集成时的工作情况。

这篇文章将从头到尾涵盖以下内容:

*   使用 [MiniTest](http://mattsears.com/articles/2011/12/10/minitest-quick-reference) 编写测试
*   构建一个以 [JSON](http://en.wikipedia.org/wiki/JSON) 格式返回信息的[Sinatra](http://www.sinatrarb.com/)API
*   使用[捆绑器](http://bundler.io/)管理依赖关系
*   使用 [Git](http://git-scm.com/) 管理版本控制
*   在 [GitHub](http://github.com/) 上托管代码
*   在 [Heroku](http://heroku.com/) 上部署和托管站点
*   执行与 [Travis CI](https://travis-ci.org/) 的持续集成

## 定义问题

我想构建的应用程序名为 Number Cruncher。它将返回关于数字的信息，比如它的因子，它是奇数还是偶数，它是否是质数。

我想做的第一件事是对`Integer`类进行猴子修补，添加一个以数组形式返回数字因子的方法。我还想加一个`prime?`方法来测试一个数是不是质数。一旦我做到了这一点，我将使用 Sinatra 来提供一个 API，它将以 JSON 格式返回关于一个数字的信息。

*   Number Cruncher 正在 Heroku 上运行
*   您可以看到持续集成测试在 [Travis](https://travis-ci.org/daz4126/number_cruncher) 上运行
*   代码在 [GitHub](https://github.com/daz4126/number_cruncher) 上

## 测试

在我们写任何代码之前，我们应该写一些测试来覆盖上面的描述。首先，创建一个名为‘number _ Cruncher . Rb’的文件和一个名为‘test . Rb’的文件。然后将以下 MiniTest 安装代码添加到“test.rb”中:

```
ENV['RACK_ENV'] = 'test'
require 'minitest/autorun'
require 'rack/test'
require_relative 'number_cruncher.rb'

include Rack::Test::Methods

def app
  Sinatra::Application
end
```

现在，我们将编写一个规范来测试上面描述的一些特性。将以下代码添加到“test.rb”的底部:

```
describe "Number Cruncher" do

  it "should return the factors of 6" do
    6.factors.must_equal [1,2,3,6]
  end

  it "should say that 2 is prime" do
    assert 2.prime?
  end

  it "should say that 10 is not prime" do
    refute 10.prime?
  end

  it "should return json" do
    get '/6'
    last_response.headers['Content-Type'].must_equal 'application/json;charset=utf-8'
  end 

  it "should return the correct info about 6 as json" do
    get '/6'
    six_info = { number: 6, factors: 6.factors, odd: 6.odd?, even: 6.even?, prime: 6.prime? }
    six_info.to_json.must_equal last_response.body
  end

end
```

第一个测试是针对我计划添加到`Integer`类中的`factors`方法。我们检查整数`6`是否调用了`factors`方法，然后是 6 的因子是否作为数组返回。

接下来，测试也将被添加到`Integer`类中的`prime?`方法。首先，检查 2 是否返回`true`，然后检查 10 是否返回`false`。

最后两个测试是特定于 API 的。第四个测试检查应用程序是否返回 JSON。这是通过执行一个参数为“6”的`get`请求来完成的。检查`last_response.headers`的内容类型是否为“应用程序/json ”;charset=utf-8 '。在最后一个测试中，我们通过比较 JSON 字符串和`last_response.body`方法来检查是否返回了正确的信息。

要运行我们的测试，请在终端提示符下输入以下内容:

```
$ ruby test.rb
```

这会产生以下输出:

```
# Running:
EEEEE
Finished in 0.008729s, 572.8333 runs/s, 0.0000 assertions/s. 
5 runs, 0 assertions, 0 failures, 5 errors, 0 skips
```

我们的五个测试都会产生错误，这是意料之中的，因为我们还没有编写任何代码。让我们看看能否通过这些测试。

## 数字处理器代码

首先向 Integer 类添加`factors`和`prime?`方法。将以下代码添加到“number_cruncher.rb”中:

```
class Integer
  def factors
    square_root = self**0.5
    (1..square_root).map{ |n| [n,self/n] if self/n*n == self }.compact.flatten.sort
  end

  def prime?
    self.factors.size == 2 ? true : false
  end
end
```

尝试再次运行测试。

```
$ ruby test.rb
```

这会产生以下输出:

```
5 runs, 3 assertions, 0 failures, 2 errors, 0 skips
```

那更好——我们的前三个测试通过了，但是最后两个仍然失败，因为我们还没有实现 API。我们会用辛纳屈来做这件事。这涉及到需要`sinatra`和`json`宝石，所以将以下代码添加到‘number _ Cruncher . Rb’中:

```
require 'sinatra'
require 'json'
```

我们还需要一个人们可以用来访问 API 的路径。这是一个简单的`GET`请求，接受一个数字作为参数。它将返回有关该号码的信息:

```
get '/:number' do
  content_type :json
  number = params[:number].to_i
  { number: number, factors: number.factors, odd: number.odd?, even: number.even?, prime: number.prime? }.to_json
end
```

首先，使用 Sinatra 提供的便利的`content_type` helper 方法将内容类型更改为 JSON。然后从`params` hash 中抓取数字，并将其转换为整数(路由中输入的所有内容都是字符串)。然后创建一个关于该数字的散列信息，包括它的因子。使用我们新的`Integer`方法，散列包括数字是奇数还是偶数，以及它是否是质数。使用由`json` gem 提供的`to_json`方法将散列转换成 JSON。因为这是路由处理程序的最后一行，所以会自动返回。

让我们再次运行测试:

```
$ ruby test.rb
```

这一次，我们得到以下输出:

```
5 runs, 5 assertions, 0 failures, 0 errors, 0 skips
```

太好了，我们的代码起作用了！我们可以通过使用*旋度*检查一些关于第五个[费马数](http://en.wikipedia.org/wiki/Fermat_number)的事实来测试它的速度。首先，启动服务器:

```
$ ruby number_cruncher.rb
```

现在，打开另一个终端窗口，输入:

```
$ curl http://localhost:4567/4294967297
```

这给了我们以下信息，支持了欧拉在 1732 年的发现:

```
{"number":4294967297,"factors":[1,641,6700417,4294967297],"odd":true,"even":false,"prime":false}
```

## 使用 Git 进行版本控制

将代码置于版本控制之下总是有用的。Git 在 Ruby 社区中几乎无处不在，这是有充分理由的(太棒了！).首先，确保您的系统上安装了 Git，然后运行以下命令:

```
$git init
```

这应该会给您一个类似于下面的消息:

```
Initialized empty Git repository in /path/to/number cruncher/.git/
```

使用`add .`命令添加目录中的所有文件:。

```
git add .
git commit -m 'initial commit'
```

您将收到一条与下面类似的消息，详细说明哪些文件已被更改:

```
[master (root-commit) 6674d0c] initial commit
 2 files changed, 56 insertions(+)
 create mode 100644 number_cruncher.rb
 create mode 100644 test.rb
```

因为我们使用 Git 进行版本控制，所以将代码放在 GitHub 上是有意义的。如果你还没有创建一个账户，那就去那里[创建一个](https://github.com/signup/free)。通过终端与 GitHub 交互，有一个很有用的 gem 叫做‘hub’。要安装它，只需在终端上输入以下内容:

```
$ gem install hub
```

现在，您应该能够使用以下命令为您的代码创建一个新的存储库了:

```
$ hub create number_cruncher
```

这将为您提供以下输出:

```
Updating origin
created repository: daz4126/number_cruncher
```

将代码推送到我们新的 GitHub 存储库，这样做:

```
$ git push origin master
```

如果一切按计划进行，您将看到类似于以下输出的内容:

```
Counting objects: 4, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 914 bytes, done.
Total 4 (delta 0), reused 0 (delta 0)
To git@github.com:daz4126/number_cruncher.git
 * [new branch]      master -> master
```

## 大错

接下来，我们将使用 Bundler 来管理我们的依赖项。这需要创建一个名为“Gemfile”的文件，其中包含以下代码:

```
source 'https://rubygems.org'
gem "sinatra"
gem "json"
gem "rack-test", :group => :test
```

要使用 Bundler，请在终端中使用以下命令:

```
$ bundle install
```

当 Bundler 获得并安装所有必需的 gem 时，您应该会看到以下信息:

```
Fetching gem metadata from https://rubygems.org/...........
Fetching gem metadata from https://rubygems.org/..
Resolving dependencies...
Using json (1.8.0) 
Using rack (1.5.2) 
Using rack-protection (1.5.0) 
Using rack-test (0.6.2) 
Using tilt (1.4.1) 
Using sinatra (1.4.3) 
Using bundler (1.3.5) 
Your bundle is complete!
Use `bundle show [gemname]` to see where a bundled gem is installed.
```

由于我们对代码做了一些修改，所以在将更新后的代码推送到 GitHub 之前，我们需要将这些修改添加并提交给 git。这可以通过终端中的以下三个命令轻松实现:

```
$ git add .
$ git commit -m 'Added Gemfile and bundled gems'
$ git push origin master
```

## 部署到 Heroku

现在是时候使用 [Heroku](http://heroku.com/) 服务将我们的代码部署到一个活动服务器上了。为此，我们需要创建一个“备份”文件。创建一个名为“config.ru”的文件，其中包含以下代码:

```
require './number_cruncher'
run Sinatra::Application
```

要测试这是否可行，请运行 Heroku 将在终端中使用的命令:

```
$ bundle exec rackup
```

这具有启动服务器的效果，您将会看到类似如下所示的输出:

```
[2013-08-29 13:27:36] INFO  WEBrick 1.3.1
[2013-08-29 13:27:36] INFO  ruby 2.0.0 (2013-06-27) [i686-linux]
[2013-08-29 13:27:36] INFO  WEBrick::HTTPServer#start: pid=4188 port=9292
```

同样，我们对代码做了一些更改，所以使用 Git 进行添加、提交和推送:

```
$ git add .
$ git commit -m 'Added config.ru'
$ git push origin master
```

现在是时候在 Heroku 中创建应用程序了。如果你还没有创建一个账户，那就去那里[弄一个](https://id.heroku.com/signup)。你还需要确保你已经安装了 [Heroku 工具箱](https://toolbelt.heroku.com/)。完成后，在终端中输入以下命令:

```
$ heroku create
```

Git 用于使用`push`将代码部署到 Heroku，如下所示:

```
$ git push heroku master
```

如果一切顺利，您将看到类似于下面的输出:

```
Creating number-cruncher... done, stack is cedar
http://number-cruncher.herokuapp.com/ | git@heroku.com:number-cruncher.git
Git remote heroku added
```

记下为您的应用程序创建的 URL，然后通过访问它来测试它，将您最喜欢的号码作为参数:

http://number-cruncher.herokuapp.com/10

## 与 Travis 的持续集成

持续集成是在一个集中的代码库中运行测试的实践。Travis 通过在每次将代码推送到 GitHub 时对 GitHub 存储库中的代码运行测试来提供这项服务。这意味着您可以随时检查代码的状态。一旦构建成功，您甚至可以配置它来部署您的代码！

首先，安装`travis` gem:

```
$ gem install travis
```

您还需要使用您的 GitHub 帐户登录到 [Travis](https://travis-ci.org/) 。

Travis 通过运行 rake 任务来工作，因此创建一个名为“Rakefile”的文件，其中包含以下代码:

```
task(:default) { require_relative 'test' }
```

这基本上告诉它在 Rakefile 运行时运行“test.rb”文件。您可以通过在终端中输入以下内容来测试这是否可行:

```
$ rake
```

您将看到与我们运行测试时相同的输出:

```
Run options: --seed 58513
# Running tests:
.....
Finished tests in 0.092591s, 54.0009 tests/s, 54.0009 assertions/s.
5 tests, 5 assertions, 0 failures, 0 errors, 0 skips
```

您还需要将“rake”添加到我们的 Gemfile 中，编辑它，如下所示:

```
source 'https://rubygems.org'
ruby '2.0.0'
gem "sinatra"
gem "json"

group :test do
  gem "rack-test"
  gem "rake"
end
```

您需要再次运行`bundle install`，因为我们已经更改了我们的 Gemfile:

```
$ bundle install
```

现在，我们准备为我们的项目设置 Travis。这可以通过在终端中输入以下命令来实现:

```
$ travis init ruby --rvm 2.0.0
```

这会产生以下输出:

```
repository not known to Travis CI (or no access?)
triggering sync: ........ done
.travis.yml file created!
daz4126/number_cruncher: enabled :)
```

将创建一个名为“. travis.yml”的文件，其中包含有关我们应用程序的信息。当您推送至您的 Github 帐户时，Travis 上的构建被初始化，所以现在就做:

```
$ git add .
$ git commit -m 'Created a Rakefile and set up Travis'
$ git push origin master
```

要检查构建的状态，请在终端中运行以下命令:

```
$ travis show
```

Travis 可能需要一段时间来运行构建，因此您可能会收到如下消息:

```
no build yet for daz4126/Sinatra-API
```

但是要有耐心，泡一杯茶，然后回来再试一次:

```
$ travis show
```

希望这一次您得到构建已经成功通过的确认:

```
Job #1.1: Created a Rakefile and set up Travis
State:         passed
Type:          push
Branch:        master
Compare URL:   https://github.com/daz4126/number_cruncher/compare/4f55d5f9cd32...86c181d96f5d
Duration:      30 sec
Started:       2013-09-28 18:22:56
Finished:      2013-09-28 18:23:26
Allow Failure: false
Config:        rvm: 2.0.0
```

您还会收到 Travis 可爱的员工发来的电子邮件，确认构建成功。

## 那都是乡亲们！

我们已经到了本教程的末尾，在这里我们完成了一个完整的开发周期，每次我们想要添加一段新代码时都可以重复这个周期:编写测试、编写代码、提交对 Git 的更改、将代码推送到 GitHub、使用 Travis 测试代码，以及部署到 Heroku。冲洗并重复。

希望你觉得有用。我很乐意在下面的评论中听到任何关于你自己工作流程的反馈。

## 分享这篇文章