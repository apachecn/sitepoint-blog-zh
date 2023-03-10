# 赌场:去各种场合

> 原文：<https://www.sitepoint.com/loccasions-getting-to-occasions/>

[上次](https://www.sitepoint.com/loccasions-giving-events-a-backbone/)我们完成了在用户事件页面上显示事件所需的客户端项目。现在我们的重点转向使用 Backbone 异步添加和删除事件。

在我们的 Events 页面截图中，视图中唯一没有实现的部分是 EventFormView。显然，这个视图将负责向用户显示一个允许创建新事件的表单。形式很简单:

![](img/142027911005389d92f6950ff1e4cb4c.png "event_form_view")

事件表单

我首先想改的是名字。将“Form”放在视图名称中对我来说似乎很脆弱，所以让我们将其更改为我们正在做的事情，即创建一个事件:CreateEventView。

CreateEventView 应该:

*   包含一个表单。
*   创建一个事件。

我认为值得指出的是，我们没有测试事件连接(意思是，表单提交事件是如何处理的)，因为那将是测试主干框架。

我们的测试看起来像:

```
describe("CreateEventView", function() {
  beforeEach(function() {
    loadFixtures("eventForm.html");
    this.view = new App.CreateEventView();
    this.form = $(this.view.el).find("form")[0];
  });
  it("should provide a form", function() {
    expect($(this.view.el).find("form").length).toEqual(1);
  });

  describe("creating an event", function() {
    beforeEach(function() {
      window.eventCollection = new Backbone.Collection();
      this.createStub = sinon.stub(window.eventCollection, "create");
      $(this.form).find("#event_name").val("Test Event");
      $(this.form).find("#event_description").val("Test Event Description");
      this.view.createEvent();
    });

    it("should call create on the EventCollection", function() {
      expect(this.createStub).toHaveBeenCalled();
    });

  });
});
```

运行这些测试时，jasmine 规格变红，这是理所应当的。查看“创建事件”规范，我们将一些数据放入表单，然后确保调用了`window.eventCollection.create()`。Backbone 在集合上提供了`create`方法，方便创建对象并将其添加到集合中。很好。

这些测试在处理表单时突出了一个问题。为了创建一个`App.TestEvent`，我们必须从表单中解析出属性值。有许多实用程序可以将表单序列化为 json，但是我认为我们现在应该自己处理。

不幸的是，我们不能只使用像 jQuery 的`form.serializeArray()`方法这样简单的东西，因为表单中的值不是事件的属性。一个例子是 Rails 用来帮助发现 CSRF 攻击的“authenticity _ token”。我们不希望在活动中出现这种情况。我要写一个实用的方法来做我认为我们需要的事情。测试结果:

```
describe("parsing form attributes", function() {
  it("should have the correct attribute values", function() {
    $(this.form).find("#event_name").val("Test Event");
    $(this.form).find("#event_description").val("Test Event Description");
    var attributes = this.view.parseFormAttributes().event;
    expect(attributes.name).toEqual("Test Event");
    expect(attributes.description).toEqual("Test Event Description");
  });
});
```

实施:

```
parseFormAttributes: ->
  _.inject(
    @form.serializeArray(),
    (memo, pair) ->
      key = pair.name
      return memo unless /^event/.test(key)
      val = pair.value
      if key.indexOf('[') > 0
        parentKey = key.substr(0, key.indexOf('['))
        childKey = key.split('[')[1].split(']')[0]
        if typeof memo[parentKey] == "undefined"
          memo[parentKey] = {}
        memo[parentKey][childKey] = val
      else
        memo[key] = val
      return memo
  ,{})
```

随着`parseFormAttributes()`的到位，我们可以完成`createEvent()`。以下是完整的 CreateEventView:

```
App or= {}
App.CreateEventView = Backbone.View.extend(
  el: "#edit_event"
  initialize: ->
    @form = $(this.el).find("form")
  events:
    "submit form" : "handleFormSubmission"
  handleFormSubmission: (e) ->
    e.stopPropagation()
    @createEvent()
    false
  createEvent: ()->
    evento = new App.Event(@parseFormAttributes().event)
    has_id = @form.attr("action").match(//events/(w*)/)
    if has_id
      evento.id = has_id[1]
      evento.save()
    else
      eventCollection.create(evento)
  parseFormAttributes: ->
    _.inject(
      @form.serializeArray(),
      (memo, pair) ->
        key = pair.name
        return memo unless /^event/.test(key)
        val = pair.value
        if key.indexOf('[') > 0
          parentKey = key.substr(0, key.indexOf('['))
          childKey = key.split('[')[1].split(']')[0]
          if typeof memo[parentKey] == "undefined"
            memo[parentKey] = {}
          memo[parentKey][childKey] = val
        else
          memo[key] = val
        return memo
    ,{})
)
```

最后，我们必须告诉路由器创建这个视图和其他视图。

```
// spec/javscripts/router_spec.js
describe("index", function() {
    beforeEach(function() {
      ...
      this.createViewSpy = sinon.stub(App, "CreateEventView").returns(this.mockView);
      this.router.index();
    });

    afterEach(function() {
      ...
      App.CreateEventView.restore();
    });

    ...

    it("should create the CreateEventView", function() {
      expect(this.createViewSpy).toHaveBeenCalled();
    });
  });
});
```

(如果还不清楚上面的`...`意味着我已经省略了上一篇文章中的现有代码…只需在这里添加代码行)

回想一下上一篇文章，我们需要在路由器的`index`方法中添加一个方法，就像这样:

```
# app/assets/javascripts/router.js.coffee
index: ->
  @eventListView = new App.EventListView({collection: window.eventCollection or= new App.EventsCollection()})
  @eventListView.render()
  @createEventView = new App.CreateEventView()
  if $('#map').length > 0
    @mapView = new App.MapView(App.MapProviders.Leaflet)
    @mapView.render()
```

更新:一位机警的读者(见评论)发现了文章中的一些遗漏，这使得文章更难完成。不好意思！我真的很感激人们能找到这样的东西。

您需要确保您的 EventsController#create 方法看起来像:

```
def create
  event = current_user.events.build(params[:event])
  event.save!
  respond_with(event) do |format|
    format.html { redirect_to events_path }
  end
end
```

此外，请确保它位于 EventsController 类定义的顶部:

```
respond_to :html, :json
```

否则，将返回错误的 HTTP 状态，并且 Backbone 不会更新视图。(谢谢尼古拉斯！)

如果你进入 http://localhost:3000 并点击“我的活动”页面，你应该能够添加活动，并看到它们出现在我们的列表中。

## 删除事件

添加事件的阴阳(听起来有点脏……)是删除事件。我不知道是否应该将 events#index 页面上的删除功能作为列表的一部分。虽然我可以看到想要删除的用例，但我也可以看到让它们点击到事件页面来删除事件，作为一个更明确的你更好地知道你到底在做什么的 UI 流。让我们假设我们的用户不太喜欢点击，并且已经足够成熟，能够处理从列表中删除事件。

## 一次一个事件

不久前，我决定将 events#show 页面从 events#index 页面中分离出来，而不是尝试一种[单页面应用](http://en.wikipedia.org/wiki/Single-page_application)的方法。这个决定引出了如何在每个页面上执行正确的 javascript 的问题。在 event#index 案例中，我们有一个 EventsCollection 和关于列出和创建事件的视图。对于 events#show 页面，我们将重点关注围绕当前事件的事件和视图列表。

经过一番搜索，我找到了 Jason Garber 的这篇文章，这篇文章详细阐述了(无与伦比的 Paul Irish)解决这个问题的方法。您应该阅读这篇文章以了解完整的细节，但是该方法的关键是创建一个实用程序类，该类基于写在 body 元素上的一些 data-*属性来调用 load 方法。在这篇文章的引导下，我们将*应用程序/视图/布局/应用程序. haml.html* 中的 body 元素修改如下:

```
%body{:"data-controller" => controller_name, :"data-action" => action_name }
```

有了这个，我改变了*应用程序/资产/JavaScript/应用程序. js . coffe*以包含我们新的 util 类:

```
window.App =
  common:
    init: ->
  events:
    init: ->
    index: ->
      window.eventCollection = new App.EventsCollection(bootstrapEvents)
      new App.EventsShowRouter()
      Backbone.history.start
        root: "/events"
    show:
      new App.EventRouter()
      ev_id =  location.href.match(//events/(.*)/)[1]
      Backbone.history.start
        root: "/events/"+ev_id

UTIL =
  exec: ( controller, action )->
    ns =  App
    action or= "init"

    if ( controller != "" && ns[controller] && typeof ns[controller][action] == "function" )
      ns[controller][action]()

  init: ->
    body = document.body
    controller = body.getAttribute( "data-controller" )
    action = body.getAttribute( "data-action" )

    UTIL.exec( "common" )
    UTIL.exec( controller )
    UTIL.exec( controller, action )

$(UTIL.init)
```

作为这一更改的一部分，我将`App.Router`重命名为`App.EventsRouter`，创建了一个*app/assets/JavaScript s/routers*文件夹，并将新重命名的 *eventsRouter.js.coffee* 复制到该目录中。我还必须将规范重命名为 *eventsRouter_spec.js* ，并修改这两个文件，将`App.Router`改为`App.EventsRouter`。每次更改后，我都重新运行我的 Jasmine 套件并修复一些东西，直到套件通过。我喜欢考试！

对这一变化的最后适应是改变*app/assets/applications/js*，删除

```
//= require router
```

```
//= require_tree ./routers
```

并从该文件的底部移除`$(App.start)`调用。

所有的规范都应该通过，现有的功能应该可以再次工作。现在，我们可以专注于单个事件及其场合。

## 最后，一个场合的场合

我们可以正式称之为“下坡”一旦我们可以添加事件并在地图上看到它们，我们就非常接近完成了。

进入该页面的方法与事件页面非常相似。因此，我认为这是一个很好的机会，你，Loccasions 读者，尝试创建自己的事件#显示页面。该页面至少应该能够:

*   添加场合
*   删除场合
*   列出该事件的所有场合

首先，我的看起来是这样的:

![](img/5b82282854df3ba0bdee3e25f6d43f31.png "occasions_page")

争取做到这一点，但要让它成为“你的”

同样，我们有三个主干视图区域:地图、事件列表和创建新事件的表单。在这个视图中，我把列表放在地图的右边，只是为了与众不同。为了额外加分，你也可以用不同的方式设计你的页面。

为了得到更多的线索，我开发了一个应用程序。事件显示页面的 EventRouter(在上面的 UTIL 代码中提到过)。在那之后，几乎就是复制事件规范，改变它们以应对各种场合，然后让这些规范通过。如果你被卡住了，去 [git 库](http://github.com/ruprict/loccasions)看看我最后在哪里。

我想在我准备从临时职位中休息一下之前，我们在这个系列中还有大约 2 个职位。下一篇文章将涉及与地图的交互，我们将采用事件表单，并将其与一些地图功能相集成。最后一篇文章将回顾我本可以做得更好的地方(哇…那可能是一个很长的地方)以及在局部情况下还可以做些什么。

## 分享这篇文章