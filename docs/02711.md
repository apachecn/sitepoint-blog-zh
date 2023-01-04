# 地理编码器:显示地图并在 Rails 中查找位置

> 原文：<https://www.sitepoint.com/geocoder-display-maps-and-find-places-in-rails/>

![](img/9de929f3c0cdcd38e240b1cdb0022908.png)

世界之大。说真的，我得说它真的很大。不同的国家，不同的城市，不同的人，不同的文化…但是，互联网把我们所有人联系在一起，这真的很酷。我可以和住在千里之外的朋友交流。

因为这个世界很大，你可能需要在你的应用程序中跟踪许多不同的地方。幸运的是，有一个很好的解决方案可以帮助你通过坐标、地址找到位置，甚至可以测量地点之间的距离并找到附近的地点。所有这些基于位置的工作被称为“地理编码”。在 Ruby 中，一种地理编码解决方案叫做[地理编码器](https://github.com/alexreisner/geocoder)，这就是我们今天的嘉宾。

在此应用程序中，您将学习如何

*   将 Geocoder 集成到 Rails 应用程序中
*   调整地理编码器的设置
*   启用地理编码，以便能够根据地址获取坐标
*   启用反向地理编码以根据坐标获取地址
*   测量位置之间的距离
*   添加静态地图以显示所选位置
*   添加动态地图以允许用户选择所需的位置
*   添加根据坐标在地图上查找位置的功能

到本文结束时，您将对 Geocoder 有一个扎实的了解，并有机会使用方便的 Google Maps API。我们可以开始了吗？

源代码可以在 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/Geocoder) 获得。

工作演示可以在[sitepoint-geocoder.herokuapp.com](https://sitepoint-geocoder.herokuapp.com)找到。

## 准备应用程序

对于这个演示，我将使用 Rails 5 beta 3，但是 Geocoder 同时支持 Rails 3 和 Rails 4。创建一个名为 Vagabond 的新应用程序(我们会你真的不用这么叫它，但我觉得这个名字有点合适):

```
$ rails new Vagabond -T 
```

假设我们希望我们的用户分享他们去过的地方。我们不会关注像认证，添加照片，视频等东西。，但您可以稍后自行扩展此应用。现在，让我们添加一个名为`places`的表，包含以下字段:

*   `title` ( `string`)
*   `visited_by`(`string`)–以后可以用`user_id`替换，并标记为外键
*   `address`(`text`)–用户访问过的地方的地址
*   `latitude`和`longtitude`(`float`)——这个地方的确切坐标。应用程序的第一稿应该会根据提供的地址自动获取它们。

创建并应用适当的迁移:

```
$ rails g model Place title:string address:text latitude:float longitude:float visited_by:string
$ rake db:migrate 
```

在继续之前，让我们添加 [bootstrap-rubygem](https://github.com/twbs/bootstrap-rubygem) ，它将 [Bootstrap 4](http://v4-alpha.getbootstrap.com/) 集成到我们的应用程序中。我不会在本文中列出所有的样式，但是您可以参考源代码来查看完整的标记。

*Gemfile*

```
[...]
gem 'bootstrap', '~> 4.0.0.alpha3'
[...] 
```

奔跑

```
$ bundle install 
```

现在创建一个控制器、一条路线和一些视图:

*places_controller.rb*

```
class PlacesController < ApplicationController
  def index
    @places = Place.order('created_at DESC')
  end

  def new
    @place = Place.new
  end

  def create
    @place = Place.new(place_params)
    if @place.save
      flash[:success] = "Place added!"
      redirect_to root_path
    else
      render 'new'
    end
  end

  private

  def place_params
    params.require(:place).permit(:title, :address, :visited_by)
  end
end 
```

*config/routes.rb*

```
[...]
resources :places, except: [:update, :edit, :destroy]
root 'places#index'
[...] 
```

*views/places/index . html . erb*

```
<header><h1 class="display-4">Places</h1></header>

<%= link_to 'Add place', new_place_path, class: 'btn btn-primary btn-lg' %>

<div class="card">
  <div class="card-block">
    <ul>
      <%= render @places %>
    </ul>
  </div>
</div> 
```

*views/places/new . html . erb*

```
<header><h1 class="display-4">Add Place</h1></header>

<%= render 'form' %> 
```

现在是分音:

*views/places/_ place . html . erb*

```
<li>
  <%= link_to place.title, place_path(place) %>
  visited by <strong><%= place.visited_by %></strong>
</li> 
```

*views/places/_ form . html . erb*

```
<%= form_for @place do |f| %>
  <fieldset class="form-group">
    <%= f.label :title %>
    <%= f.text_field :title, class: "form-control" %>
  </fieldset>

  <fieldset class="form-group">
    <%= f.label :visited_by %>
    <%= f.text_field :visited_by, class: "form-control" %>
  </fieldset>

  <fieldset class="form-group">
    <%= f.label :address, 'Address' %>
    <%= f.text_field :address, class: "form-control" %>
  </fieldset>

  <%= f.submit 'Add!', class: 'btn btn-primary' %>
<% end %> 
```

我们为控制器设置了`index`、`new`和`create`动作。太好了，但是我们如何根据提供的地址获取坐标呢？为此，我们将利用 Geocoder，所以继续下一节！

## 集成地理编码器

添加新宝石:

*Gemfile*

```
[...]
gem 'geocoder'
[...] 
```

然后跑

```
$ bundle install 
```

开始使用 Geocoder 非常简单。继续将下面一行添加到您的模型中:

*models/place.rb*

```
[...]
geocoded_by :address
[...] 
```

那么，这意味着什么呢？这一行为我们的模型配备了有用的地理编码器方法，除了其他方法之外，还可以用来根据提供的地址检索坐标。通常这样做的地方是在回调内部:

*models/place.rb*

```
[...]
geocoded_by :address
after_validation :geocode
[...] 
```

有几件事你必须考虑:

*   您的模型必须提供一个返回完整地址的方法——其名称作为参数传递给`geocoded`方法。在我们的例子中，这将是一个`address`列，但是您可以使用任何其他方法。例如，如果你有一个单独的列叫做`country`、`city`和`street`，下面的实例方法可能被引入:

    def full _ address
    [国家，城市，街道].compact.join('，')
    end

然后只传递它的名字:

```
geocoded_by :full_address 
```

*   您的模型还必须包含两个名为`latitude`和`longitude`的字段，它们的类型设置为`float`。如果列的调用方式不同，只需覆盖相应的设置:

    地理编码者:地址，纬度::纬度，经度::经度

*   Geocoder 也支持 MongoDB，但是需要稍微不同的设置。在这里阅读更多和[在这里(覆盖坐标'名称)](https://github.com/alexreisner/geocoder#model-configuration)。

有了这两条线，坐标将根据提供的地址自动填充。多亏了 [Google 地理编码 API](https://developers.google.com/maps/documentation/geocoding/intro) ，这才成为可能(尽管 Geocoder 也支持其他选项——我们稍后会谈到)。更重要的是，你甚至不需要一个 API 密匙来让它工作。

不过，正如您可能已经猜到的，Google API 有其使用限制，所以如果地址没有改变或根本没有显示，我们就不想查询它:

*models/place.rb*

```
[...]
after_validation :geocode, if: ->(obj){ obj.address.present? and obj.address_changed? }
[...] 
```

现在，只需为您的`PlacesController`添加`show`动作:

*places_controller.rb*

```
[...]
def show
  @place = Place.find(params[:id])
end
[...] 
```

*views/places/show . html . erb*

```
<header><h1 class="display-4"><%= @place.title %></h1></header>

<p>Address: <%= @place.address %></p>
<p>Coordinates: <%= @place.latitude %> <%= @place.longitude %></p> 
```

启动您的服务器，提供一个地址(如“俄国，莫斯科，克里姆林宫”)并导航到新添加的地方。坐标应该会自动填充。要检查它们是否正确，只需将它们粘贴到[页面](https://www.google.com/maps)的搜索字段中。

另一个有趣的事情是，用户甚至可以提供 IP 地址来检测坐标——这根本不需要对代码库进行任何更改。先补充一个小提醒:

*views/places/_ form . html . erb*

```
[...]
<fieldset class="form-group">
  <%= f.label :address, 'Address' %>
  <%= f.text_field :address, class: "form-control" %>
  <small class="text-muted">You can also enter IP. Your IP is <%= request.ip %></small>
</fieldset>
[...] 
```

如果您在本地机器上开发，IP 地址将是类似于`::1`或`localhost`的东西，显然不会被转换成坐标，但是您可以提供任何其他已知的地址(对于 Google 来说是`8.8.8.8`)。

## 配置和 API

Geocoder 支持许多选项。要生成默认初始化文件，请运行以下命令:

```
$ rails generate geocoder:config 
```

在这个文件中，您可以设置各种东西:要使用的 API 键、超时限制、要使用的度量单位等等。此外，您可以在这里更改“查找”提供程序。默认值为

```
:lookup => :google, # for street addresses
:ip_lookup => :freegeoip # for IP addresses 
```

Geocoder 的文档很好地列出了所有可能的提供商及其使用限制，所以我不会把它们放在这篇文章里。

值得一提的是，即使你不需要 API 键来查询 Google API，也建议你这样做，因为你可以获得一个扩展的配额，还可以跟踪你的应用程序的使用情况。导航到[console.developers.google.com](https://console.developers.google.com)，创建一个新项目，并确保启用 Google Maps 地理编码 API。

接下来，只需复制 API 密钥并将其放入初始化器文件中:

*config/initializer/geocoder . Rb*

```
Geocoder.configure(
  api_key: "YOUR_KEY"
) 
```

## 显示静态地图

谷歌地图的一个很好的特性是能够根据地址或坐标将静态地图添加到你的网站中。目前，我们的“显示”页面看起来不是很有帮助，所以让我们在那里添加一个小地图。

为此，您将需要一个 API 密钥，因此如果您在上一步中没有获得它，现在就可以获得它。需要记住的一点是，必须启用 Google 静态地图 API。

现在只需调整您的视图:

*views/places/show . html . erb*

```
[...]
<%= image_tag "http://maps.googleapis.com/maps/api/staticmap?center=#{@place.latitude},#{@place.longitude}&markers=#{@place.latitude},#{@place.longitude}&zoom=7&size=640x400&key=AIzaSyA4BHW3txEdqfxzdTlPwaHsYRSZbfeIcd8",
              class: 'img-fluid img-rounded', alt: "#{@place.title} on the map"%> 
```

差不多就是这样——不需要 JavaScript。静态地图支持各种参数，如地址、标签、地图样式等等。务必阅读[文档](https://developers.google.com/maps/documentation/static-maps/intro)。

页面现在看起来更好了，但是表单呢？如果用户不仅能输入地址，还能通过在交互式地图上精确定位来输入坐标，那就方便多了。进行下一步，我们一起来做！

## 添加对坐标的支持

现在忘掉地图吧——让我们简单地允许用户输入坐标而不是地址。必须根据纬度和经度获取地址本身。这要求 Geocoder 的配置稍微复杂一些。这种方法使用一种称为“反向地理编码”的技术。

*models/place.rb*

```
[...]
reverse_geocoded_by :latitude, :longitude
[...] 
```

这听起来可能很复杂，但想法很简单——我们获取这两个值，并基于它获取地址。如果您的地址列的名称不同，请提供如下名称:

```
reverse_geocoded_by :latitude, :longitude, :address => :full_address 
```

此外，您可以向此方法传递一个块。当你有单独的列来存储国家和城市的名称，街道等时，这是很有用的。：

```
reverse_geocoded_by :latitude, :longitude do |obj, results|
  if geo = results.first
    obj.city    = geo.city
    obj.zipcode = geo.postal_code
    obj.country = geo.country_code
  end
end 
```

更多信息可在[这里](https://github.com/alexreisner/geocoder#advanced-geocoding)找到。

现在添加一个回调:

*models/place.rb*

```
[...]
after_validation :reverse_geocode
[...] 
```

但是有几个问题:

*   如果没有提供或修改坐标，我们不想进行反向地理编码
*   我们不想同时执行正向和反向地理编码
*   我们需要一个单独的属性来存储用户通过表单提供的地址

前两个问题很容易解决——只需指定`if`和`unless`选项:

*models/place.rb*

```
[...]
after_validation :geocode, if: ->(obj){ obj.address.present? and obj.address_changed? }
after_validation :reverse_geocode, unless: ->(obj) { obj.address.present? },
                   if: ->(obj){ obj.latitude.present? and obj.latitude_changed? and obj.longitude.present? and obj.longitude_changed? }
[...] 
```

准备就绪后，如果提供了地址，我们将获取坐标，否则如果设置了坐标，我们将尝试获取地址。但是一个地址有一个单独的属性呢？我不认为我们需要添加另一列——让我们使用一个名为`raw_address`的虚拟属性来代替:

*models/place.rb*

```
[...]
attr_accessor :raw_address

geocoded_by :raw_address
after_validation -> {
  self.address = self.raw_address
  geocode
}, if: ->(obj){ obj.raw_address.present? and obj.raw_address != obj.address }

after_validation :reverse_geocode, unless: ->(obj) { obj.raw_address.present? },
                 if: ->(obj){ obj.latitude.present? and obj.latitude_changed? and obj.longitude.present? and obj.longitude_changed? }
[...] 
```

我们可以利用这个虚拟属性进行地理编码。不要忘记更新允许属性的列表

*places_controller.rb*

```
[...]
private

def place_params
  params.require(:place).permit(:title, :raw_address, :latitude, :longitude, :visited_by)
end
[...] 
```

和观点:

*views/places/_ form . html . erb*

```
<h4>Enter either address or coordinates</h4>
<fieldset class="form-group">
  <%= f.label :raw_address, 'Address' %>
  <%= f.text_field :raw_address, class: "form-control" %>
  <small class="text-muted">You can also enter IP. Your IP is <%= request.ip %></small>
</fieldset>

<div class="form-group row">
  <div class="col-sm-1">
    <%= f.label :latitude %>
  </div>

  <div class="col-sm-3">
    <%= f.text_field :latitude, class: "form-control" %>
  </div>

  <div class="col-sm-1">
    <%= f.label :longitude %>
  </div>

  <div class="col-sm-3">
    <%= f.text_field :longitude, class: "form-control" %>
  </div>
</div> 
```

到目前为止还不错，但是没有地图，这个页面看起来还是不完整。进入下一步！

## 添加动态地图

添加一个[动态地图](https://developers.google.com/maps/documentation/javascript/)涉及到一些 JavaScript，所以把它添加到你的布局中:

*layouts/application . html . erb*

```
<script src="https://maps.googleapis.com/maps/api/js?key=YOUR_KEY&callback=initMap"
                                       async defer></script> 
```

注意 API 键是强制的(一定要启用“Google Maps JavaScript API”)。还要注意`callback=initMap`参数。`initMap`是这个库加载后将被调用的函数，所以让我们把它放在全局名称空间中:

*map.coffee*

```
jQuery ->
  window.initMap = -> 
```

显然，我们需要一个容器来放置地图，所以现在添加它:

*views/places/_ form . html . erb*

```
[...]
<div class="card">
  <div class="card-block">
    <div id="map"></div>
  </div>
</div> 
```

功能:

*map.coffee*

```
window.initMap = ->
  if $('#map').size() > 0
    map = new google.maps.Map document.getElementById('map'), {
      center: {lat: -34.397, lng: 150.644}
      zoom: 8
    } 
```

注意`google.maps.Map`需要传递一个 JS 节点，所以这个

```
new google.maps.Map $('#map') 
```

不会工作，因为`$('#map')`返回一个包装的 jQuery 集。要把它变成 JS 节点，你可能会说`$('#map')[0]`。

`center`是一个选项，提供了地图的初始位置-设置适合你的值。

现在，让我们将一个`click`事件绑定到我们的地图，并相应地更新坐标字段。

*map.coffee*

```
lat_field = $('#place_latitude')
lng_field = $('#place_longitude')
[...]
window.initMap = ->
  map.addListener 'click', (e) ->
    updateFields e.latLng
[...]

updateFields = (latLng) ->
  lat_field.val latLng.lat()
  lng_field.val latLng.lng() 
```

为了方便用户，我们也在点击点放置一个标记。这里的问题是，如果您在地图上单击几次，将会添加多个标记，因此我们每次都必须清除它们:

*map.coffee*

```
markersArray = []

window.initMap = ->
  map.addListener 'click', (e) ->
    placeMarkerAndPanTo e.latLng, map
    updateFields e.latLng

placeMarkerAndPanTo = (latLng, map) ->
  markersArray.pop().setMap(null) while(markersArray.length)
  marker = new google.maps.Marker
    position: latLng
    map: map

  map.panTo latLng
  markersArray.push marker

[...] 
```

想法很简单——我们将标记存储在数组中，并在下一次单击时删除它。有了这个数组，你可以跟踪那些在其他条件下被清除的标记。

是时候测试一下了。导航到新页面并尝试点击地图-坐标应该会正确更新。那就好多了！

### 基于坐标放置标记

假设用户知道坐标，但想在地图上找到它们。这个特性很容易添加。引入新的“在地图上查找”链接:

*views/places/_ form . html . erb*

```
[...]
<div class="col-sm-3">
  <%= f.text_field :longitude, class: "form-control" %>
</div>

<div class="col-sm-4">
  <a href="#" id="find-on-map" class="btn btn-info btn-sm">Find on the map</a>
</div>
[...] 
```

现在将一个`click`事件绑定到它，该事件根据提供的坐标更新地图:

*map.coffee*

```
[...]
window.initMap = ->
  $('#find-on-map').click (e) ->
    e.preventDefault()
    placeMarkerAndPanTo {
      lat: parseInt lat_field.val(), 10
      lng: parseInt lng_field.val(), 10
    }, map
[...] 
```

我们将一个对象传递给包含用户定义的纬度和经度的`placeMarkerAndPanTo`函数。请注意，坐标必须转换成整数，否则将会出现错误。

重新加载页面并检查结果！为了进行更多的练习，您可以尝试为地址字段添加一个类似的按钮，并引入错误处理。

## 测量地点之间的距离

我们今天要实现的最后一件事是测量添加位置之间距离的能力。创建新控制器:

*距离 _ 控制器. rb*

```
class DistancesController < ApplicationController
  def new
    @places = Place.all
  end

  def create
  end
end 
```

添加路线:

*config/routes.rb*

```
[...]
resources :distances, only: [:new, :create]
[...] 
```

还有一个观点:

*视图/距离/new.html.erb*

```
<header><h1 class="display-4">Measure Distance</h1></header>

<%= form_tag distances_path do %>
  <fieldset class="form-group">
  <%= label_tag 'from', 'From' %>
  <%= select_tag 'from', options_from_collection_for_select(@places, :id, :title), class: "form-control" %>
</fieldset>

  <fieldset class="form-group">
  <%= label_tag 'to', 'To' %>
  <%= select_tag 'to', options_from_collection_for_select(@places, :id, :title), class: "form-control" %>
</fieldset>

  <%= submit_tag 'Go!', class: 'btn btn-primary' %>
<% end %> 
```

在这里，我们显示了两个包含我们的位置的下拉列表。`options_from_collection_for_select`是一种简便的方法，可以简化`option`标签的生成。第一个参数是集合，第二个是在`value`选项中使用的值，最后一个是在下拉列表中为用户显示的值。

Geocoder 允许[测量地球上任意点之间的距离](https://github.com/alexreisner/geocoder#distance-and-bearing)——只需提供它们的坐标:

*距离 _ 控制器. rb*

```
[...]
def create
  @from = Place.find_by(id: params[:from])
  @to = Place.find_by(id: params[:to])
  if @from && @to
    flash[:success] =
        "The distance between <b>#{@from.title}</b> and <b>#{@to.title}</b> is #{@from.distance_from(@to.to_coordinates)} km"
  end
  redirect_to new_distance_path
end
[...] 
```

我们找到请求的位置并使用`distance_from`方法。`to_coordinates`将记录转换成一个坐标数组(例如，`[30.1, -4.3]`)–我们必须使用它，否则计算将导致错误。

这种方法依赖于 flash 消息，所以稍微调整一下布局:

*layouts/application . html . erb*

```
[...]
<% flash.each do |name, msg| %>
  <%= content_tag(:div, msg.html_safe, class: "alert alert-#{name}") %>
<% end %>
[...] 
```

默认情况下，Geocoder 使用英里作为度量单位，但是您可以调整初始化文件并将`units`选项设置为`km`(千米)。

## 结论

唷，那可真长！我们已经介绍了 Geocoder 的许多特性:正向和反向地理编码、调整选项和测量距离。除此之外，您还学习了如何使用各种类型的谷歌地图，并通过 API 使用它们。

不过，Geocoder 还有其他一些特性我没有在本文中介绍。例如，它支持[在所选地点](https://github.com/alexreisner/geocoder#for-activerecord-models)附近寻找地点，它可以在测量地点之间的距离时提供[方向](https://github.com/alexreisner/geocoder#distance-and-bearing)，它支持[缓存](https://github.com/alexreisner/geocoder#caching)，甚至可以在外使用[。如果你计划在你的项目中使用这个伟大的宝石，一定要浏览文档！](https://github.com/alexreisner/geocoder#use-outside-of-rails)

今天就到这里吧，伙计们。希望这篇文章对你有用并且有趣。不要失去你的踪迹，很快就会见到你！

## 分享这篇文章