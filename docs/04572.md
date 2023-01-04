# 辛纳特拉和波旁——我的网络开发之路

> 原文：<https://www.sitepoint.com/sinatra-bourbon-web-development-way/>

当你想到 Sinatra 和 Bourbon 的时候，网页开发可能不是你首先想到的。(这当然不是谷歌建议的第一件事，而且[我也不是第一个注意到](http://www.koffeinfrei.org/2012/03/24/sinatra-with-bourbon)的人。)但是当我开始一个 web 应用程序项目时，Sinatra 和 Bourbon 走到一起就像，嗯，你明白了。

## 早期决定

无论您是从后端还是前端着手，为后端 web 开发选择一个框架是最关键的早期决策之一。对于许多 Ruby 开发人员来说，多年来最明显的选择是 Rails，它有着巨大的社区和全面的“约定胜于配置”的便利性。但是最近，这个框架已经激发了最多才多艺的模仿者，比如节点的[Express](http://expressjs.com)和 PHP 的[Laravel](http://laravel.com)，已经成为 [Sinatra](http://www.sinatrarb.com) 。

同样，在前端，有几个流行的框架，如 Zurb 基金会或 T2 的 Twitter Bootstrap，只要你遵循他们的惯例，它们就能处理你所有的前端问题。但是对于那些想要最灵活和最轻的解决方案的人来说，很少有解决方案像 Thoughtbot 的套件一样不引人注目，同时又像 thought bot 的套件一样全面:[波本酒](http://bourbon.io)、[纯酒](http://neat.bourbon.io)、[苦味酒](http://bitters.bourbon.io)和[再斟酒](http://refills.bourbon.io)。

最近，当我开始一个新项目时，我喜欢从这两个强大而低调的包的组合开始。唯一棘手的部分是让他们很好地合作。但是一旦你解决了这个问题，这种结合就没有尽头了。

## 辛纳特拉优势

Sinatra 是一种比 Rails 更精简、更轻量级的 web 应用程序架构方式。Sinatra 的优势之一是其简单性和非个人化的开发方法。如果您喜欢将整个应用程序构建成一页 Ruby 代码，我们非常欢迎您这样做。正是这种明显的简单性导致一些人忽略了 Sinatra，而青睐它更全面的表亲 Rails。但是，当你想发展成更复杂的代码组织时，Sinatra 将与你同行。

一般来说，框架的设计是为了让一个项目快速、轻松地从你的头脑中走出来，走向世界。但是当您开始着手发布和维护您的 web 应用程序时，您可能会发现早期选择的局限性。当你的独特产品找到市场时，试图提前太久预测它将需要去哪里通常不是一个好主意。

对于像 Rails 这样非常固执己见的框架，如果您决定做出 Rails 构建代码的方式本身不支持的更改，您可能会发现您的工作很适合您。尽管这些年来 Rails 在灵活性方面做得越来越好，但是一旦您购买了一个试图为您做所有事情的全面框架，您为满足特定需求而做出的任何偏离都有可能使最初吸引您使用该框架的便利性复杂化。

另一方面，Sinatra 的简单性鼓励你以你想要的方式开发应用程序的 api，结合 gems 和其他适合你的目的的 Ruby 工具。因为 Sinatra 并不试图在幕后表演很多魔术，所以你用 Sinatra 开发的一个应用，在一个有自己见解的开发者手里，更透明，更通用。

## 波旁酒的优势

像 Sinatra 一样，Bourbon 采取了一种方法，允许前端开发人员选择对特定应用最有意义的部分，并留下可选的部分。基于 Hampton Catlin 流行的用于编写预编译 CSS 的 [Sass](http://sass-lang.com) 扩展语言，Bourbon 提供了一切，从方便的 mixins、网格系统、排版格式到基线设计选择和不断增长的标准小部件和组件库。最酷的是，所有这些都是可选的。

在前端工作时，跨浏览器兼容性、客户端处理能力和网络相关延迟都是优化用户体验的关键。虽然全面的框架试图将许多决策权从开发人员手中拿走，但这通常是以在一个简单的站点中强加过多的东西为代价的。这可能会导致下载量过大、服务器拥塞，以及在最关键的时候绘画或页面刷新变慢。

使用 Bourbon 方法，您可以将优化集中在应用程序中最重要的地方。通过有意识地挑选每个组件，您有机会自己决定是否希望网站上的每个页面都带有 CSS 和 JavasScript，这些组件只需要出现在不到 10%的访问者会看到的路径上。

## 让他们一起愉快地玩耍

一旦您决定对 web 应用程序采用精益方法，在集成 Sinatra 和 Bourbon 时，需要记住几个棘手的问题。

Sinatra 为您的开发流程增加价值的方法之一是控制您的资产渠道和路线。这是一个简单的公式，但它并不完全是波旁期望的开箱即用。但是，让辛纳特拉为你的波旁网站服务并不难。

我喜欢为我的 Sinatra 应用程序使用这样的代码结构，这使得我在工作时很容易保持我的 Sass 和 Bourbon 代码为最新，也使得部署到托管站点如 [Heroku](http://www.heroku.com) 非常方便:

```
views\
   sass\
       base\
           ...
       bourbon\
           ...
       neat\
           ...
       main.scss
       normalize.scss
   layout.haml
   index.haml
Gemfile
app.rb
config.ru
```

首先要做的是将所需的 Ruby gems 包含在您的 gem 文件中，然后运行`bundle install`来下载它们并使它们对您的应用程序可用。这是我的入门 gem 文件:

### Gemfile

```
source 'https://rubygems.org'

gem 'sinatra'
gem 'haml'
gem 'sass', ">=3.3.0"

# dev
gem 'shotgun'
```

您可以看到，我已经指定 Sass 版本高于 3.3.0，以正确支持 Bourbon 库。我还包括 HAML 宝石，我最喜欢的写 HTML 模板的速记，以及 Sinatra。在开发环境部分，我还喜欢使用 Shotgun，它支持在我对任何文件进行更改时立即重新加载我的 Sinatra 应用程序。

我包含了一个`config.ru`文件，使机架启动和部署更加方便。以下是该文件的基本内容:

### config.ru

```
require 'sass/plugin/rack'
require './app'

use Sass::Plugin::Rack

run Sinatra::Application
```

有了这些，我的服务器将知道如何集成 rack 和 Sass，也知道我的应用程序在哪里，这样它就可以被启动。这应该与支持机架和乘客的托管服务一起工作。

下一步是创建一个基本的 Ruby `app.rb`文件来设置 Sinatra，并告诉它在哪里可以找到我的布局和模板:

### app.rb

```
require 'sinatra'
require 'haml'
require 'sass'

configure do
  set :scss, {:style => :compressed, :debug_info => false}
end

get '/css/:name.css' do |name|
  content_type :css
  scss "sass/#{name}".to_sym, :layout => false
end

get '/' do
  @product = "Short Description of Product"
  haml :index, :layout => :default_layout
end
```

require 命令拉入 gem，configure 块只是告诉 Sass 我喜欢如何编写和输出我的 CSS。在第一个 get 块中有一个小技巧，只是为了让 Sinatra 知道如何将我的 Sass 目录中的文件转换为符号，并将其解释为 CSS。最后一个 get 块创建了一个实例变量来传递给我的第一个模板，并告诉 Sinatra 使用默认布局用 HAML 处理模板。

所以我现在需要的是我的`views/`目录中的模板和布局:

### views/index.haml

```
%h1= @product
```

### views/default_layout.haml

```
!!!
%html
  %head
    %title Sinatra App
    %link{:rel => 'stylesheet', :type => 'text/css', :href => 'css/main.css'}
  %body
    %section
      = yield
```

我的 sass 目录中有一个基本的`main.scss`文件，用于导入我可能想要使用的所有 Bourbon 文件，还有一个由 Thoughtbot 推荐的第三方重置工具 [Normalize](http://necolas.github.io/normalize.css/) :

### views/sass/main.scss

```
@import "normalize";
@import "bourbon/bourbon";
@import "base/base";
@import "neat/neat";
```

运行`shotgun`命令启动本地服务器 http://localhost:9393，在浏览器中导航到该 URL 应该会显示一个包含最少 CSS 的基本页面。

但是波旁威士忌的所有力量都在那里，就在你的辛纳特拉申请表上面。为了演示波旁威士忌的整合，我可以将一种更简单的波旁威士忌笔芯 Hero 组件合并到我的`index.haml`文件中:

### views/index.haml

```
.hero
  .hero-inner
    %a.hero-logo{:href => "}
      %img{:src => "https://raw.githubusercontent.com/thoughtbot/refills/master/sourimg/placeholder_logo_1.png", :alt=>"Logo Image"}
      .hero-copy
        %h1= @product
        %p
          A few reasons why this product is worth using, who it's for, and why they need it.
    %button
      Learn More
```

并将相关的 Sass 添加到新的`hero.scss`文件中:

### views/sass/hero.scss

```
.hero {
  $hero-background-top: #7F99BE;
  $hero-background-bottom: #20392B;
  $hero-color: white;
  $gradient-angle: 10deg;
  $hero-image: 'https://raw.githubusercontent.com/thoughtbot/refills/master/sourimg/mountains.png';

  @include background(url($hero-image), linear-gradient($gradient-angle, $hero-background-bottom, $hero-background-top), no-repeat $hero-background-top scroll);

  background-repeat: no-repeat;
  background-position: top;
  background-size: cover;
  padding-bottom: 4em;

  .hero-logo img {
    height: 3em;
    margin-bottom: 2.5em;
  }

  .hero-inner {
    @include outer-container;
    @include clearfix;
    padding: 3em;
    margin: auto;
    text-align: center;
    color: $hero-color;
    a {
      color: $hero-color;
      &:hover {
        color: darken($hero-color, 20%);
      }
    }
    .hero-copy {
      text-align: center;
      h1 {
        font-size: 1.6em;
        margin-bottom: .5em;

        @include media($large-screen) {
          font-size: 1.8em;
        }
      }

      p {
        font-family: $serif;
        margin: auto;
        margin-bottom: 3em;
        font-weight: 200;
        line-height: 1.4em;

        @include media($large-screen) {
          font-size: 1.1em;
          max-width: 40%;
        }
      }
    }
  }
}
```

不要忘记更新您的`main.scss`文件以要求新的 Sass 文件:

### views/sass/main.scss

```
@import "normalize";
@import "bourbon/bourbon";
@import "base/base";
@import "neat/neat";
@import "hero";
```

当您修改这些文件并重新加载浏览器时，您应该会看到您的页面带有格式化的英雄图像和基本内容，使用了 Bourbon 的所有便利。当你检查你的 CSS 文件时，你会看到只有对这个组件来说必不可少的部分被编译和包含。

## 按你的方式做

波旁酒和辛纳屈酒感觉像是完美的搭配。两者都允许您以自己的方式开发 web 应用程序，同时根据您的需要提供或多或少的支持。想并入 [jQuery](http://www.jquery.com) ？想加点[棱角分明的](https://angularjs.org/)？喜欢用[骨干](http://backbonejs.org)还是其他？这种结合仍然是一个很好的开始，并且随着产品的发展，你不会后悔这个决定。

为了让你开始，我在 GitHub 上为我自己的 web 应用程序添加了[Sinatra 和 Bourbon 代码作为样板](https://github.com/mdavidgreen/sinatra-bourbon),供你克隆和使用。玩得开心！

## 分享这篇文章