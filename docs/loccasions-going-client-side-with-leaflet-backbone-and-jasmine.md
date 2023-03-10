# 地点:去客户端与传单，骨干，茉莉

> 原文：<https://www.sitepoint.com/loccasions-going-client-side-with-leaflet-backbone-and-jasmine/>

我们终于来到了地图的时刻。在[系列](https://www.sitepoint.com/series/loccasions-2/)的最后几篇文章中，我承诺过“在下一篇文章中，我们将讨论地图”和“我将降低税收”，但我没有兑现。在这篇文章中，我将至少实现其中一个承诺。

将地图添加到这个应用程序几乎完全是客户端的任务。因此，这篇文章(以及后面的一两篇)将是一公吨的 javascript 和一顶针的 Ruby。

## 库、框架和地图，天啊！

正如我在设置这个应用程序时提到的，我使用 T2 主干作为 javascript 的框架，使用 T4 茉莉作为我的客户端测试框架。我做出这个选择的理由是，我非常喜欢这两个框架。

然而，我没有使用 gem 来生成主干文件或者神奇地将主干连接到服务器。我们的计划是从头开始编写主干类，在配置中添加内容，以便在需要时让它们与服务器对话。我绝对不反对对主干和 rails 使用 gem，除了看起来对哪个做什么有混淆(如果你去[rails-主干](https://github.com/ivanvanderbyl/rails-backbone)回购，它告诉你使用`gem "backbone-rails"`，但是如果你去[主干-rails](https://github.com/ivanvanderbyl/rails-backbone) 回购，它告诉你使用`gem "rails-backbone"`。我在一个无限循环中看着他们两个，只有我的妻子切断了我办公室的电源。).

同样，我也不会涉及骨干的基本知识，因为[已经](http://liquidmedia.ca/blog/2011/01/Backbone-js-part-1/) [已经](http://thomasdavis.github.com/tutorial/Backbone-introduction.html) [完成](http://Backbonetutorials.com/)。如果你不理解 Backbone，花一些时间学习基础知识，这应该比你准备这篇文章要多。

对于 Jasmine，我使用 [jasmine gem](https://github.com/pivotal/jasmine-gem) ,因为它由 Pivotal 支持，而且它们很棒。然而，对于临时情况，我将使用 gem 的一个[特定分支](https://github.com/pivotal/jasmine-gem/tree/1.2.rc1)，它能够通过资产管道运行规范和 javascript 源文件。毕竟，我们使用的是 Rails 3.1。

如今，基于网络的地图一直都是 javascript。选择正确的 js-map 框架有点令人生畏，特别是因为我职业生涯的大部分时间都在使用它(ESRI 的 ArcGIS Server，如果你感兴趣的话。你不是。)

*   谷歌地图退出了，因为谷歌有可能在许可问题上发起疯狂的攻击。
*   雅虎出局了，因为坦白说，因为它死了。
*   我简单看了一下 [OpenLayers](http://openlayers.org/) ，可能还会使用它，因为它是真正的开源，我喜欢它。
*   我还尝试使用[maps action](http://mapstraction.com/)，它抽象出了提供者，让您可以动态地更改提供者。然而，当我尝试将 OpenLayers 与 Mapstraction 一起使用时，我不知道如何更改主题，所以我继续前进。
*   最后，我选定了[传单](http://leaflet.cloudmade.com/)，主要是因为它看起来很棒，而且我喜欢目前为止的 api。

最后一点是在应用程序中添加了一个出血和我们正在服用的血液稀释剂。我使用过几次 Backbone，你通常需要模板来在 HTML 中呈现你的模型和收集数据。如果可以避免的话，我真的不想引入另一种模板语言(除了 haml)，所以我找到了 [haml-coffee](https://github.com/9elements/haml-coffee) 。这个 gem 允许您使用 HAML 编写模板，然后在客户端的 javascript 中使用它们。在下一篇文章中，我们将至少通过一个场景来清楚地展示这一点。

## 设置

咻！现在，让我们用所有新的客户端美味来设置应用程序。首先，下载我们需要的所有代码:

*   [传单](http://leaflet.cloudmade.com/download.html)把这个放到**vendor/assets/JavaScript s/fleet/fleet . js**传单也有 CSS 样式表和图片。将这些(css 文件和图片目录)放入**供应商/资产/样式表/传单**
*   [Backbone](http://documentcloud.github.com/backbone/backbone-min.js) 把这个放到**vendor/assets/JavaScript s/Backbone/Backbone-min . js**
*   [下划线](http://documentcloud.github.com/underscore/underscore-min.js)将此放入**供应商/资产/JavaScript/主干/下划线-最小. js**

我还创建了一个**vendor/javascripts/vendor . js**文件来加载这些文件。资产管道会自动完成，但是，嗯，我是个控制狂。

```
// vendor/assets/javascripts/vendor.js
    //= require leaflet/leaflet
    //= require Backbone/underscore-min
    //= require Backbone/Backbone-min
```

类似地，我为传单 css 创建了一个**vendor/样式表/vendor.css** 文件:

```
/*
     * This is the vendor.css in our vendor/assets/stylesheets dir
     *=require leaflet/leaflet
     *
    */
```

最后，在我们的 gem 文件中添加一些宝石:

```
group :assets do
  ...
  gem 'haml-coffee'
end

group :test, :development do
  ...
  gem 'jasmine', :git => 'git://github.com/pivotal/jasmine-gem.git', :branch => '1.2.rc1'
end
```

前面提到的(我很喜欢用这个词)`haml-coffee`和`jasmine`(记住，我们用的是分支)宝石。哦，我相信你记得去`bundle install`，对吗？

现在，是时候设置 jasmine 了，所以运行`rails g jasmine:install`在应用程序中设置 Jasmine 支持。Jasmine 自带服务器，输入`rake jasmine`即可启动。作为 Foreman 的超级用户(记得[的上一篇文章](https://www.sitepoint.com/loccasions-hiring-a-foreman-occasions/))，我们将把它添加到我们的 Procfile 中:

```
web: rails s
db: mongod --dbpath=/Users/ggoodrich/db/data
test: guard
jasmine: bundle exec rake jasmine
```

下一次你`foreman start`的时候，茉莉将会跑步。Jasmine 服务器运行在 [http://localhost:8888](http://localhost:8888) ，但是在我们添加一些规范之前，它不会很有趣。

Spec 文件被添加到 Jasmine 的**spec/JavaScript/support/Jasmine . yml**文件中。因为我们使用的是支持资产管道的分支，所以我们的 jasmine.yml 文件与当前版本中使用的有点不同。这是我为临时事件设置的一个[要点](https://gist.github.com/461e8d238a38562e92d2#file_jasmine.yml)。我做的一个改变是引入我们的 coffeescript 文件(这是分支给我们的)并手动添加我们的供应商 JavaScript(jQuery、下划线、主干和传单)。另一个变化是文件的最后一行，将**应用/资产**标识为由资产管道提供服务的路径。

最后，我想在我的 javascript 测试中需要时利用 [Sinon](http://sinonjs.org/) 进行模仿和存根。这里有一个很好的针对 Sinon [的 Jasmine 插件。将这两个文件下载到我们的**spec/JavaScript s/helpers/**目录中。您需要添加**spec/JavaScript s/helpers/jquery . js**文件(您可以从](https://github.com/froots/jasmine-sinon) [jQuery 站点](https://code.jquery.com/jquery-1.7.1.min.js)或从 jquery-rails gem 复制它),因为 Jasmine 还不会加载 jQuery(他们正在处理它……).

我建议通读一下这个关于 Jasmine 和 Sinon 的系列,熟悉一下这一切是如何结合在一起的。你肯定会注意到它对定位的影响。

咻…就像，嗯，非常难做的馅饼一样简单。

## 客户端目录结构，以及喜欢它们的女性

每两篇骨干入门文章，就有一篇关于[构造](http://Backbonetutorials.com/organizing-Backbone-using-modules/) [你的](http://weblog.bocoup.com/organizing-your-Backbone-js-application-with-modules) [骨干](http://jguimont.com/post/11890935147/structuring-and-organizing-a-Backbone-js-app-with)代码。文件的结构是一个只与开发相关的问题，因为资产管道会将所有的 javascript 放入一个 application.js 文件中。在通读了一些文章后，我得出了这样的结论:

![](img/c1c44e02b19403044cc14aca0e721240.png "backbone_structure")

我们的，嗯，脊梁

简而言之，集合、模型、视图和模板都有自己的目录。lib 目录用于不属于主干结构的代码。

正如我提到的，Rails 3.1 中的资产管道将为您加载所有这些内容，但我希望控制加载内容的顺序。因此，我们需要对您的**app/assets/JavaScript/application . js**文件进行更改，如下所示:

```
//= require jquery
//= require jquery_ujs
//= require vendor
//= require ./app
//= require ./router
//= require_tree ./lib
//= require_tree ./models
//= require_tree ./collections
//= require_tree ./views
//= require_tree ./templates
```

## 设置完成，现在做什么？

有了所有的客户端 whatzits，我们可以从主干的角度来看我们的事件页面的设计。这篇文章将清空 Events#index 页面，如下图所示。

![](img/91cc07af1b495978654ede6bda90e1b4.png "events_show_backbone")

你看到了什么

使用主干处理该页面的一种方法是将页面分割成视图，如下所示:

![](img/9bebd5d4a562ed373a3d555e29f6dd90.png "events_show_backbone_marked_up")

脊梁看到了什么

这就是我们所采用的方法，应该足以让我们开始编写测试了。

## 先生们，现在在 3 号舞台上，为 JAAASSSMMIIIIINE 鼓掌

让我们从地图视图开始。这是我们的第一个茉莉测试

```
// spec/javascripts/views/mapView_spec.js

describe("MapView", function() {
  describe("initialize", function() {

    beforeEach(function() {
      loadFixtures("map.html");
    });

    it("should use the #map element by default", sinon.test(function() {

    }));

    it("should create a map", sinon.test(function() {

    }));

  });

});
```

您可以看到 Jasmine 看起来非常像任何 BDD 框架语法。有“描述”块(可以嵌套)和“it”块来测试特定的行为。另外，我们的`beforeEach`允许我们加载一个夹具文件。在这种情况下，我们的 fixture 文件向页面添加了一个`div#map`(实际上，它只是`<div id='map'></div>`)，用于保存测试地图。客户端测试通常依赖于标记的结构，所以能够加载 fixture 文件(Jasmine 将在测试后卸载)真的很好。

我们的地图视图测试很简单。首先，确保视图使用了`#map` DOM 元素。第二，确保它创建了一个“地图”。

顺便提一下，你可能已经注意到了`it`函数是用`sinon.test()`包装的。这为测试创建了一个“沙箱”。如果 MapView 有任何依赖项(确实有)，我们将根据需要删除/模仿它们。沙箱使得在测试完成时恢复任何存根或模仿对象变得容易。

然而，什么是地图呢？我之前提到过我们会使用传单，但我不确定我们不会更换地图提供商。因此，我们应该将地图隐藏在视图的抽象后面。此外，我真的不需要为我的测试引导一个实际的传单地图。因此，我创建了“MapProvider”的概念，并在初始化时将其传递给视图。我们可以用 Sinon 来嘲弄/打击供应商，避免测试。

MapProvider 接口/协议现在非常简单:

```
App.MapProviders.Leaflet = ->
  # Create new map
  createMap: (elementId) ->

  addBaseMap: ()->

  addLayerToMap: (layer) ->

  setViewForMap: (options) ->
```

只有四个函数，命名是为了使它们的目的非常明显。如果你感兴趣，传单提供者的实现是[这里的](https://gist.github.com/461e8d238a38562e92d2#file_app.map_providers.leaflet.js.coffee)。

让我们完成两个地图视图测试:

```
it("should use the #map element by default", sinon.test(function() {
    // Arrange
    var mp = new App.MapProviders.Leaflet();
    var mapSpy = this.stub(mp, "createMap");
    var setViewSpy = this.stub(mp,"setViewForMap");

    //Act
    var view = new App.MapView({
      mapProvider: mp
    });

    //Assert
    expect(view.el.id).toEqual("map");
    mapSpy.restore();
    setViewSpy.restore();
    </code></pre>

  }));

  it("should create a map", sinon.test(function() {
    //Arrange
    var mp = new App.MapProviders.Leaflet();
    var mapProviderMock = this.mock(mp);
    mapProviderMock.expects("createMap").withArgs("map").once();
    mapProviderMock.expects("setViewForMap").once();

    //Act
    var view = new App.MapView({
      mapProvider: mp
    });

    //Assert
    mapProviderMock.verify();

  }));
```

这两个测试显示了不同类型测试。第一个测试确保 MapView 做正确的事情，前提是它的依赖做正确的事情。我们并不真正关心 MapProvider 为这个测试做了什么，因为它与测试的结果无关。因此，我们将这些方法剔除，这样可以阻止调用传单 API，同时确保它们不会抛出错误。

第二个测试是“基于期望的单元测试”的例子。在这里，我们确实关心 MapView 如何与其依赖项交互。我们**期望**它调用某些方法，并且我们要求我们的模拟对象验证那些方法确实被调用了。

重新加载我们的 Jasmine 测试页面(http://localhost:8888，还记得吗？)我们看到:

![](img/825157ebb233a90866c06d4cf8d50297.png "jasmine_fail")

失败

我们可以解决。

## 我是地图[查看]！

有朵拉的粉丝吗？没有吗？*咳嗽*对，我也没有。下面是 MapView 的实现:

```
###
app/assets/javascripts/views/mapView.js
###

class App.MapView extends Backbone.View
  el: "div#map",
  initialize: ->
    @mapProvider = this.options.mapProvider
    @initialCenter = this.options.initialCenter || { latitude: 51.505, longitude: 0.09 }
    @render()
  setInitialView: ->
    @mapProvider.setViewForMap
      latitude: @initialCenter.latitude,
      longitude: @initialCenter.longitude
      zoomLevel: 13
  render: ->
    @mapProvider.createMap(@el.id)
    @setInitialView()
```

一旦添加了这个文件，重新加载 Jasmine 测试页面看起来就像:

![](img/114e4fa1c39567b7c828256d227ded9e.png "jasmine_pass")

及格

## 你知道去地图的路吗，何塞？

好了，我们有了一个地图视图。我们如何让我们的页面使用它？我的意思是，当我在本地访问我的“/events”页面时，我看不到地图。这就是 Backbone 的“路由器”(或者称为控制器的艺术家)发挥作用的地方。在这种情况下，我们希望将“根路径”或“/”路由到它知道创建我们的 MapView 的地方。我写了几个规范来满足这个要求:

```
// spec/javascripts/appRouter_spec.js

describe("AppRouter", function() {
  describe("index", function() {
    beforeEach(function() {
      loadFixtures("map.html");
      this.mapViewStub = sinon.spy(App.MapView.prototype, "initialize");
      window.bootstrapEvents = [];
    });
    afterEach(function(){
      this.mapViewStub.restore();
    });

    it("should create a map view", function() {
      this.router = new App.Router();
      this.router.index();
      expect(this.mapViewStub).toHaveBeenCalled();
    });

  });
  describe("/", function () {
    it("should respond to empty hash with index", function() {
      this.router = new App.Router();
      this.routeSpy = sinon.spy();
      try {
        Backbone.history.start({silent:true, pushState:true});
      } catch(e) {}
      this.router.navigate("elsewhere");
      this.router.bind("route:index", this.routeSpy);
      this.router.navigate("", true);
      expect(this.routeSpy).toHaveBeenCalledOnce();
      expect(this.routeSpy).toHaveBeenCalledWith();
    });
  });
});
```

在这种情况下，我们使用第三个 Sinon stubby/mocky 东西称为“间谍”。间谍就像一个 mock，但是你不能对它设置返回值。间谍的存在只是为了记录它是否被调用，以及调用它的参数是什么。这对于我们的第一个测试来说很好，我们确保 MapView 上的 initialize 方法被调用。第二个测试使用一个 spy 来确保，当用户到达“根路由”时，调用 AppRouter#index 函数。

重新加载 Jasmine 页面会导致相应的失败，所以让我们编写适当的类:

```
###
app/assets/javascripts/router.js.coffee<
###

class App.Router extends Backbone.Router
  routes:
    "" : "index"
  index: ->
    if $('#map').length > 0
      @mapView = new App.MapView(
        mapProvider: new App.MapProviders.Leaflet()
      )
```

完成了。规格通过了。

## 启动我

我们需要做的最后一件事是引导我们的主干应用程序代码。换句话说，我们需要告诉 Backbone 让路由器就位，创建所有视图，以及它需要做的任何事情。我为此创建了一个小的脚本文件:

```
###
app/assets/javascripts/app.js.coffee
###

window.App =
  start: ->
    new App.Router()
    Backbone.history.start(
      pushState: true, root: "/events"
    )

$(App.start)
```

这个文件定义了我的应用程序名称空间(`App`)并创建了我们的`start`函数。start 函数只是创建我们的路由器，并告诉 Backbone 开始处理路由。如果你想了解`Backbone.history.start`到底在做什么，请看这里的。

## 更新

Henry 先生(根据以下评论)指出了以下几点:

*   将 div #地图添加到 events/index.html.haml 中
*   将#map { height: 350px }添加到 app/assets/样式表/events.css.scss

这是带有地图的页面:

![](img/91cc07af1b495978654ede6bda90e1b4.png "events_show_backbone")

你看到了什么

在这一页上，我们还有许多工作要做。然而，这是一个很好的停止点，我想玩一会儿地图。

## 我的博客跑遍了所有地方，我得到的只有这张恶心的地图

我知道，光是弄一张地图就要做很多工作。然而，我们现在也有了:

*   我们客户端代码的结构。
*   用测试驱动客户端代码的方法(TDD/BDD/ETC)
*   一张地图！

我们将在下一篇文章中完成活动索引页面，然后强调活动页面的不同之处。之后，Loccasions 的 v0.0.1 应该差不多完成了。

## 分享这篇文章