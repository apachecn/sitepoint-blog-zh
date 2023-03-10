# 使用 SOLR 和日光浴室进行地理空间搜索

> 原文：<https://www.sitepoint.com/geospatial-search-solr-solarium/>

在最近的[系列文章](https://www.sitepoint.com/using-solarium-solr-search-setup/)中，我详细研究了 Apache 的 SOLR 和日光浴室。

概括一下:SOLR 是一个搜索服务，有很多功能，比如分面搜索和结果突出显示，它作为一个网络服务运行。[Solaris um](http://www.solarium-project.org)是一个 PHP 库，允许你集成 SOLR——无论是本地的还是远程的——与它交互，就像它是你的应用程序的本地组件一样。如果你对这两个都不熟悉，那么我的系列是这里的，我强烈建议你去看一看。

在这篇文章中，我将看看 SOLR 的另一个值得讨论的地方；地理空间搜索。

![locationsearch](img/9d0f74e516ad2ec763a96d4ac26aa156.png)

## 一个例子

本文附带了一个简单的示例应用程序。你可以从 [Github](https://github.com/lukaswhite/geospatial-search-with-solr) 获得它，或者[点击这里](http://geospatial-solr.demos.lukaswhite.com/)查看它的运行情况。

在我们深入研究之前，让我们先看看一些背景。

## 什么是地理空间搜索？

有时候，你想搜索的东西有地理位置。通常，这提供了重要的背景。我能够搜索“意大利餐馆”当然很好，但我很饿——另一个大陆上的餐馆，尽管可能很好，但也没什么帮助。相反，更有用的是能够运行一个搜索，要求“给我看看意大利餐馆，但在 5 英里内”。或者，“告诉我最近的十家意大利餐馆”。这就是地理空间搜索的用武之地。

## 地理空间搜索和点

在地理空间应用中，我们经常谈论“点”；即特定的地理位置。具体来说，我们真正谈论的是一对纬度和经度。纬度和经度定义了地球上的一个点，可能在几米之内。

当你开发任何涉及地理点的东西时，其中一个挑战是，你需要某种方式让那些不考虑经纬度的人理解它们——我很确定我们大多数人都是这样。[地理定位](http://en.wikipedia.org/wiki/Geolocation)在这里派上了用场，因为它可以用来确定“你在哪里”的经纬度，没有地名的歧义。(如果你想采取后一种方法，我已经在之前[写过了。)](https://www.sitepoint.com/where-on-earth-are-you/)

因此，当你在做任何地理相关的工作时，第一个挑战是如何确定起点，即从哪里开始搜索。在我们的示例应用程序中，我们将两面下注，采用三种方法。我们将使用 HTML5 地理定位功能来允许用户的浏览器定位他们。为了方便和简单起见，我们将包括一些主要城市的任意列表，单击这些列表时，将从一些硬编码的值填充纬度和经度。最后，为了覆盖所有的基础，对于我们当中的地理极客，我们将包括文本字段，用户可以在其中手动输入他们的纬度和经度。

## 设置模式

为了让我们的 SOLR 核心设置支持地理位置，我们需要对模式进行一些调整。

我们需要做的第一件事是将`location`字段类型添加到`schema.xml`:

```
<fieldType name="location"  class="solr.LatLonType" subFieldSuffix="_coordinate"/>
```

请注意，该字段由子字段组成；即纬度和经度。我们需要确保我们有合适的类型来满足这些需求:

```
<fieldType name="tdouble" class="solr.TrieDoubleField" precisionStep="8" omitNorms="true" positionIncrementGap="0"/>
```

如您所见，它基本上是一个类型为`double`(具体来说是`tdouble`，由 Java 类`solr.TrieDoubleField`在内部表示)的字段。

这两个`<fieldType>`声明都需要放在您的`schema.xml`的`<fields>`元素中。

既然已经设置了类型，您可以定义一个新字段来保存纬度和经度。在下面的例子中，我称之为`latlon`:

```
<field name="latlon"        type="location" indexed="true"  stored="true"  multiValued="false" />
```

重要的是`multiValued`被设置为`false`——不支持多个纬度/经度对。

您还需要为组件设置一个动态字段；即纬度和经度。`_coordinate`是指我们在上面定义`location`字段类型时指定的后缀。

```
<dynamicField name="*_coordinate"  type="tdouble" indexed="true"  stored="false"/>
```

`<field>`和`<dynamicField>`声明都放在`<fields>`部分。

您的模式现在已经设置为支持纬度/经度对，并且我们已经添加了一个名为`latlon`的字段。接下来，让我们看看如何填充该字段。

您将在[示例应用程序的存储库](https://github.com/sitepoint-examples/geospatial-search-with-solr/blob/master/solr/schema.xml)中找到一个示例`schema.xml`文件。

## 分配位置数据

在为位置字段赋值时，您需要这样做:

```
$doc = {lat},{long}
```

所以，使用日光浴室:

```
$doc->latlon = doubleval($latitude) . "," . doubleval($longitude);
```

有关具体的示例，请参考“填充数据”一节。

## 使用 Solarium 在 SOLR 进行地理空间查询

你可能还记得，在《SOLR》系列的第三部中，我们看了《日光浴室的助手》。基本上，这些充当语法糖，使您能够创建更复杂的查询，而不必太担心底层的 SOLR 查询语法。

下面是一个如何给搜索查询添加额外过滤器的例子，给定一个`$latitude`和一个`$longitude`，将结果限制在`$distance`公里以内:

```
$query->createFilterQuery('distance')->setQuery(
	$helper->geofilt(
		'latlon', 
		doubleval($latitude),
		doubleval($longitude),
		doubleval($distance)
	)
);
```

如果你喜欢以英里为单位工作，你只需要将`$distance`乘以`1.609344`:

```
$query->createFilterQuery('distance')->setQuery(
	$helper->geofilt(
		'latlon', 
		doubleval($latitude),
		doubleval($longitude),
		doubleval($distance * 1.609344))
	)
);
```

如果您想返回搜索结果中的距离，您需要将`geodist`函数添加到字段列表中，使用与`geofilt`过滤器相同的值。同样，您可以使用助手:

```
$query->addField($helper->geodist(
	'latlon', 
	doubleval($latitude), 
	doubleval($longitude)
	)
);
```

添加字段别名要有用得多，就像在 SQL 中一样，以后可以用它来检索值。别名的约定是前缀和后缀加下划线，如下所示:

```
$query->addField('_distance_:' . $helper->geodist(
	'latlon', 
	doubleval($latitude), 
	doubleval($longitude)
	)
);
```

现在，您可以在搜索结果中显示距离:

```
<ul>
	<?php foreach ($resultset as $document): ?>
	<li><?php print $doc->title ?> (<?php print round($document->_distance_, 2) ?> kilometres away)</li>
	<?php endforeach; ?>
</ul>
```

为了根据距离对结果进行排序，您需要使用一些技巧。您实际上需要使用一个查询，而不是使用`setSort`；这然后被用于根据距离对结果进行“评分”。基本的 SOLR 查询如下所示:

```
{!func}geodist(fieldname,lat,lng)
```

要在 Solarium 中做到这一点，同样使用一个助手:

```
$query->setQuery('{!func}' . $helper->geodist(
	'latlon', 
	doubleval($latitude), 
	doubleval($longitude)
));
```

这样做的最终结果是分数将反映接近度；分数越低，地理位置越近。

因此，要按距离对结果进行排序，首先是最近的:

```
$query->addSort('score', 'asc');
```

足够的理论；让我们建造一些东西。

## 构建我们的示例应用程序

我创建了一个简单的示例应用程序，人们可以在这里搜索他们最近的机场，你可以在 Github 的`solr`文件夹中找到[。这里有一个在线的](https://github.com/sitepoint-examples/geospatial-search-with-solr)[演示](http://geospatial-solr.demos.lukaswhite.com/)。

它使用 [Silex](http://silex.sensiolabs.org/) 作为框架，以及[树枝](http://twig.sensiolabs.org/)作为模板。您不需要深入了解这两方面的知识就能理解，因为应用程序的大部分复杂性都来自于 SOLR 集成，这将在本文中介绍。

### 推广数据

我们使用的数据来自优秀的 OpenFlights.org 服务。您将在存储库中找到数据文件，以及一个用于填充搜索索引的简单脚本，如下所示运行它:

```
php scripts/populate.php
```

以下是相关部分:

```
// Now let's start importing
while (($row = fgetcsv($fp, 1000, ",")) !== FALSE) {

	// get an update query instance
	$update = $client->createUpdate();

	// Create a document
	$doc = $update->createDocument();    

	$doc->id = $row[0];
	$doc->name = $row[1];
	$doc->city = $row[2];
	$doc->country = $row[3];
	$doc->faa_faa_code = $row[4];
	$doc->icao_code = $row[5];
	$doc->altitude = $row[8];

	$doc->latlon = doubleval($row[6]) . "," . $row[7];

	// Let's simply add and commit straight away.
	$update->addDocument($doc);
	$update->addCommit();

	// this executes the query and returns the result
	$result = $client->update($update);

	$num_imported++;

	// Sleep for a couple of seconds, lest we go too fast for SOLR
	sleep(2);

}
```

### 构建搜索表单

我们将从一个简单的表单开始，该表单包含经度和纬度字段，以及一个下拉列表，用户可以使用该列表指定距离限制:

```
<form method="get" action="/">

	<div class="form-group">
		<a href="#/" id="findme" class="btn btn-default"><i class="icon icon-target"></i> Find my location</a>
	</div>

	<div class="form-group">
		<label for="form-lat">Latitude</label>
		<input type="text" name="lat" id="form-lat" class="form-control" />
	</div>

	<div class="form-group">
		<label for="form-lat">Longitude</label>
		<input type="text" name="lng" id="form-lat" class="form-control" />
	</div>

	<div class="form-group">
		<label for="form-dist">Within <em>x</em> kilometers</label>
		<select name="dist" id="form-dist" class="form-control">		            			            	
			<option value="50">50</option>
			<option value="100">100</option>
			<option value="250">250</option>
			<option value="500">500</option>		            			            	
		</select>
	</div>

	<div class="form-group">
		<button type="submit" class="btn btn-primary"><i class="icon icon-search"></i>  Search</button>		          	
	</div>
</form>
```

接下来，让我们实现“find me”按钮，该按钮使用 HTML5 地理定位(如果用户的[浏览器支持它](http://caniuse.com/#feat=geolocation))来填充搜索表单。

```
function success(position) {
	$('input[name="lat"]').val(position.coords.latitude);
	$('input[name="lng"]').val(position.coords.longitude);		  
}

function error(msg) {
	alert(msg);
}

$('#findme').click(function(){
	if (navigator.geolocation) {
		navigator.geolocation.getCurrentPosition(success, error);
	} else {
		error('not supported');
	}
});
```

> 用户将需要授予我们的应用程序权限来定位它们，所以实际上最好是在某种用户交互时运行，比如在点击按钮时，而不是在页面加载时。

最后，我们将提供一个“默认”城市列表；用户可以单击一个按钮来自动填充纬度和经度字段。

以下是 HTML，为了简洁起见，只显示了有限数量的城市:

```
<ul id="cities">
	<li><a href="#/" data-lat="52.51670" data-lng="13.33330">Berlin, Germany</a></li>
	<li><a href="#/" data-lat="-34.33320" data-lng="-58.49990">Buenos Aires, Argentina</a></li>
```

相应的 JavaScript 非常简单:

```
$('#cities a').click(function(e){
	$('input[name="lat"]').val($(this).data('lat'));
	$('input[name="lng"]').val($(this).data('lng'));
});
```

接下来，我们将实施搜索。

### 搜索页面

让我们从定义一条单一路线开始；对于我们的示例应用程序中唯一的页面。它将显示搜索表单，并在提交表单通过 GET 参数提供 latutude 和经度时显示结果。

```
// Display the search form / run the search
$app->get('/', function (Request $request) use ($app) {

	$resultset = null;

	$query = $app['solr']->createSelect();
	$helper = $query->getHelper();

	$query->setRows(100);

	$query->addSort('score', 'asc');

	if (($request->get('lat')) && ($request->get('lng'))) {

		$latitude = $request->get('lat');
		$longitude = $request->get('lng');
		$distance = $request->get('dist');

		$query->createFilterQuery('distance')->setQuery(
				$helper->geofilt(
					'latlon', 
					doubleval($latitude),
					doubleval($longitude),
					doubleval($distance)
				)
			);

		$query->setQuery('{!func}' . $helper->geodist(
			'latlon', 
			doubleval($latitude), 
			doubleval($longitude)
		));

		$query->addField('_distance_:' . $helper->geodist(
			'latlon', 
			doubleval($latitude), 
			doubleval($longitude)
			)
		);

		$resultset = $app['solr']->select($query);

	}

	// Render the form / search results
	return $app['twig']->render('index.twig', array(
		'resultset' => $resultset,
	));

});
```

样板代码非常简单——定义路线、获取相关参数并呈现视图。

运行搜索的代码利用了我们前面看到的代码。本质上，它执行以下操作:

1.  创建一个过滤查询，将搜索限制在由`$latitude`和`$longitude`指定的点的`$distance`公里范围内；这三个参数都作为`GET`参数提供
2.  使用`geodist`助手告诉 Solarium 我们对哪个字段感兴趣(我们之前定义的 latlon 字段),以便对结果进行排序
3.  添加一个伪字段`_distance_`,这样我们可以将它合并到我们的搜索结果中
4.  运行查询并将其结果分配给视图。

### 显示结果

下面是模板中负责显示搜索结果的部分:

```
{% if resultset %}
	{% for doc in resultset %}
	<article>
		<h4><i class="icon icon-airplane"></i> {{ doc.name }}</h4>
		<p><strong>{{ doc.city }}</strong>, {{ doc.country}} ({{ doc._distance_|number_format }} km away)</p>
	</article>
	<hr />
	{% endfor %}
{% endif %}
```

这很简单。请注意`_distance_`字段是如何出现在我们的搜索结果文档中的，还有`name`和`country`字段。我们使用 Twig 的 [number_format](http://twig.sensiolabs.org/doc/filters/number_format.html) 过滤器来格式化距离。

这就是全部内容——您可以在[库](https://github.com/sitepoint-examples/geospatial-search-with-solr)中找到完整的示例。

当然，这个例子只是基于距离的搜索。当然，您可以将基于文本的搜索与地理空间搜索结合起来——我将把它作为一个练习。

## 摘要

在本文中，我展示了如何使用 SOLR——结合 PHP 库 Solarium 来执行地理空间搜索。我们已经研究了一些理论，然后开始建立我们的模式，构造我们的查询并将其付诸实践。

反馈？评论？把它们留在下面！

## 分享这篇文章