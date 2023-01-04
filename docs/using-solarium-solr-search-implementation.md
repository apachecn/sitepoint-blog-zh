# 使用 Solarium 和 SOLR 进行搜索-实施

> 原文：<https://www.sitepoint.com/using-solarium-solr-search-implementation/>

这是关于结合 Apache 的 SOLR 搜索实现使用 Solarium 的四篇系列文章中的第三篇。

在第一部分中，我介绍了关键概念，我们安装并设置了 SOLR。在[第二部分](https://www.sitepoint.com/using-solarium-solr-search-solarium-gui/)中，我们安装并配置了 Solarium，这是一个使我们能够使用 PHP 与 SOLR“对话”的库，就像它是一个本地组件一样。

现在我们终于准备好开始构建搜索机制了，这是这一期的主题。

## 基本搜索

让我们看看如何实现一个真正简单的搜索:

```
$query = $client->createSelect();
$query->setQuery(Input::get('q')); 
```

> `Input::get('q')`只是 Laravel 获取一个名为`q`的`GET`或`POST`变量的方法，你会记得，这是我们的搜索表单元素的名称。

或者更好的是，使用占位符来转义搜索短语:

```
$query->setQuery('%P1%', array(Input::get('q'))); 
```

> 占位符由%符号表示。字母“P”表示“将此作为短语转义”。绑定的变量作为数组传递，数字表示希望绑定的参数在数组中的位置；记住(也许不寻常)1 表示第一项。

要运行搜索:

```
$resultset = $client->select($query); 
```

您现在可以使用`getNumFound()`方法检索结果的数量，例如:

```
printf('Your search yielded %d results:', $resultset->getNumFound()); 
```

`$resultset`是`Solarium\QueryType\Select\Result\Result`的一个实例，它实现了`Iterator`接口——因此您可以如下迭代结果:

```
foreach ($resultset as $document) {
    . . .
} 
```

每个结果都是`Solarium\QueryType\Select\Result\Document`的一个实例，它提供了两种访问单个字段的方法——或者作为公共属性，例如:

```
<h3><?php print $document->title ?></h3> 
```

或者，您可以遍历可用字段:

```
foreach($document AS $field => $value)
{
    // this converts multi-value fields to a comma-separated string
    if(is_array($value)) $value = implode(', ', $value);

    print '<strong>' . $field . '</strong>: ' . $value . '<br />';        
} 
```

请注意，多值字段(如`cast`)将返回一个数组；所以在上面的例子中，它只是将这些字段折叠成一个逗号分隔的列表。

好了，这是如何做的概述——现在让我们把它插入到我们的示例应用程序中。

我们将让搜索响应一个`GET`请求而不是`POST`请求，因为当我们来看分面搜索时会更容易——尽管网站搜索使用`GET`实际上很常见。

因此，主控制器上的索引路径(毕竟我们的应用程序只有一个页面)变成如下所示:

```
/**
 * Display the search form / run the search.
 */
public function getIndex()
{

    if (Input::has('q')) {

        // Create a search query
        $query = $this->client->createSelect();

        // Set the query string 
        $query->setQuery('%P1%', array(Input::get('q')));

        // Execute the query and return the result
        $resultset = $this->client->select($query);

        // Pass the resultset to the view and return.
        return View::make('home.index', array(
            'q' => Input::get('q'),
            'resultset' => $resultset,
        ));

    }

    // No query to execute, just return the search form.
    return View::make('home.index');

}
```

现在让我们修改视图–`app/views/home/index.blade.php`–以便它显示搜索结果以及结果计数，方法是在搜索表单下面添加以下内容:

```
@if (isset($resultset))    
<header>
    <p>Your search yielded <strong>{{ $resultset->getNumFound() }}</strong> results:</p>
    <hr />
</header>

@foreach ($resultset as $document)

    <h3>{{ $document->title }}</h3>

    <dl>
        <dt>Year</dt>
        <dd>{{ $document->year }}</dd>

        @if (is_array($document->cast))
        <dt>Cast</dt>
        <dd>{{ implode(', ', $document->cast) }}</dd>              
        @endif

        </dl>

        {{ $document->synopsis }}

@endforeach
@endif 
```

尝试运行一些搜索。很快，你可能会注意到一个主要的限制。例如，尝试搜索“星球大战”，注意前几个结果，然后搜索“马克·哈米尔”。没有结果-看起来搜索只考虑了标题属性，而没有考虑演员。

为了改变这种行为，我们需要使用 **DisMax** 组件。DisMax 是析取 Max 的缩写。析取意味着它跨多个字段进行搜索。最大值意味着如果一个查询匹配多个字段，则最大值相加。

要表明我们希望执行 DisMax 查询:

```
$dismax = $query->getDisMax(); 
```

然后，我们可以让搜索在多个字段中查找，用空格将它们隔开:

```
$dismax->setQueryFields('title cast synopsis'); 
```

现在，如果你再次尝试搜索“马克·哈米尔”,你会看到搜索会选择演员和片名。

我们可以通过将权重附加到字段上，使 DisMax 查询更进一步。这使您可以优先考虑某些字段，例如，您可能希望标题匹配比匹配大纲中的单词获得更高的分数。请看下面一行:

```
$dismax->setQueryFields('title^3 cast^2 synopsis^1'); 
```

这表明我们希望 cast 字段的匹配权重比 synopsis 高得多——高 2 个数量级——并且 title 字段更高。对于您自己的项目，您可能希望试验各种查询，尝试并计算出最佳权重，这可能非常具体地针对所讨论的应用程序。

综上所述，我们可以通过如下修改`app/controllers/HomeController.php`来实现对多个字段的搜索:

```
 // Set the query string      
  $query->setQuery('%P1%', array(Input::get('q')));

        // Create a DisMax query
  $dismax = $query->getDisMax();

  // Set the fields to query, and their relative weights
  $dismax->setQueryFields('title^3 cast^2 synopsis^1');

  // Execute the query and return the result
  $resultset = $this->client->select($query); 
```

### 指定要返回的字段

如果您运行搜索，那么对于每个 resultset 文档，遍历这些字段，您会看到默认情况下我们添加到索引中的每个字段都会被返回。此外，SOLR 添加了与搜索结果相关联的`_version_`字段和`score`以及唯一标识符。

> *分数*是表示结果相关性的数值。

如果您希望改变这种行为，有三种方法可以使用:

```
$query->clearFields(); // return no fields

$query->addField('title');  // add 'title' to the list of fields returned

$query->addFields(array('title', 'cast')); // add several fields to the list of those returned 
```

请注意，您可能需要将`clearFields()`与`addField()`或`addFields()`结合使用:

```
$query->clearFields()->addFields(array('title', 'cast')); 
```

就像在 SQL 中一样，您可以使用星号作为通配符，这意味着选择所有的字段:

```
$query->clearFields()->addFields('*'); 
```

### 排序搜索结果

默认情况下，搜索结果将按分数降序返回。在大多数情况下，这可能是你想要的；“最佳匹配”首先出现。

但是，如果您愿意，可以按如下方式更改此行为:

```
$query->addSort('title', 'asc'); 
```

语法可能看起来很熟悉；和 SQL 很像。

## 页码

您可以指定`start`位置——即从哪里开始列出结果——以及要返回的`rows`的编号。可以把它想象成 SQL 的`LIMIT`子句。例如，要获取前 100 个结果，您应该这样做:

```
$query->setStart(0);
$query->setRows(200); 
```

有了`getNumFound()`的结果和这些函数，实现分页应该很简单，但为了简洁起见，我不打算在这里赘述。

## SOLR 分面搜索入门

分面搜索本质上允许您基于一个或多个标准“深入”搜索结果。在线商店可能是最好的说明，在那里你可以通过类别、格式(例如平装书对精装书对电子书)、是否有现货或价格范围来细化产品搜索。

让我们用一个基本的方面来扩展我们的电影搜索；我们将允许人们通过电影的 MPGG 分级(一个指定电影适当年龄范围的证书，例如“R”或“PG-13”)来缩小电影搜索范围。

要创建基于字段的方面，请执行以下操作:

```
$facetSet = $query->getFacetSet();

$facetSet->createFacetField('rating')
    ->setField('rating'); 
```

在运行搜索时，现在可以根据字段的值对结果集进行细分，并且还可以显示特定值的计数。

```
$facet = $resultset->getFacetSet()->getFacet('rating');
foreach($facet as $value => $count) {    
    echo $value . ' [' . $count . ']<br/>';
} 
```

这将为您提供以下内容:

```
Unrated [193]
PG [26]
R [23]
PG-13 [16]
G [9]
NC-17 [0] 
```

方面不一定要使用单个不同的值。您可以使用范围-例如，您可能在一个电子商务网站上有价格范围。为了说明我们电影搜索的范围，我们将允许人们将搜索范围缩小到特定十年的电影。

下面是创建方面的代码:

```
$facet = $facetSet->createFacetRange('years')
    ->setField('year')
    ->setStart(1900)
    ->setGap(10)
    ->setEnd(2020); 
```

这表明我们想要在`year`字段上创建一个基于范围的方面。我们需要指定起始值(1900 年)和结束值；即本十年的结束。我们还需要设置差距；换句话说，我们希望增量为 10-10 年。要在搜索结果中显示计数，我们可以这样做:

```
$facet = $resultset->getFacetSet()->getFacet('years');
foreach($facet as $range => $count) {    
    if ($count) {
        printf('%d's (%d)<br />', $range, $count);
    }
} 
```

这将导致以下结果:

```
1970's (12)
1980's (6)
2000's (8) 
```

请注意，facet 将包含每个可能的值，因此在显示它之前检查计数是否非零非常重要。

## 分面搜索:过滤

到目前为止，我们已经在搜索结果页面上使用了 facets 来显示计数，但是除非我们允许用户在其上过滤他们的搜索，否则这种用法是有限的。

在搜索回调中，让我们首先检查是否应用了 MPGG 分级过滤器:

```
if (Input::has('rating')) {
    $query->createFilterQuery('rating')->setQuery(sprintf('rating:%s', Input::get('rating')));     
} 
```

实际上，就像主搜索查询一样，我们仍然可以对搜索词进行转义，而不是使用`sprintf`:

```
if (Input::has('rating')) { 
    $query->createFilterQuery('rating')->setQuery('rating:%T1%', array(Input::get('rating')));    
} 
```

记住，1 表示我们希望使用参数数组的第一个元素——它是*而不是*一个从零开始的数组。`T`表示我们希望将值作为术语进行转义(与短语的`P`相反)。

十年过滤稍微复杂一些，因为我们是基于一个范围而不是一个离散值进行过滤。我们只指定了一个值——在`Input::get('decade')`中——但是我们知道上限只是十进制加九的开始。例如，“80 年代”用值 1980 表示，范围是 1980 到(1980 + 9) = 1989。

范围查询采用以下形式:

```
field: [x TO y] 
```

所以应该是:

```
year: [1980 TO 1989] 
```

我们可以这样实现:

```
if (Input::has('decade')) {
    $query->createFilterQuery('years')->setQuery(sprintf('year:[%d TO %d]', Input::get('decade'), (Input::get('decade') + 9)));            
} 
```

或者，我们可以用助手来代替。要获取 helper 类的实例:

```
$helper = $query->getHelper(); 
```

要使用它:

```
if (Input::has('decade')) {     
    $query->createFilterQuery('years')->setQuery($helper->rangeQuery('year', Input::get('decade'), (Input::get('decade') + 9)));
} 
```

虽然这可能看起来相当学术，但了解如何创建 Solarium helper 的实例是值得的，因为它对其他事情非常有用，比如地理空间支持。

## 分面搜索:视图

既然我们已经介绍了如何设置分面搜索、如何列出分面以及如何基于它们运行过滤器，我们就可以设置相应的视图了。

打开`app/views/home/index.blade.php`并修改搜索结果部分，增加一列，其中包含我们的方面:

```
@if (isset($resultset))    
<div class="results row" style="margin-top:1em;">
    <div class="col-sm-4 col-md-4 col-lg-3">
        <?php $facet = $resultset->getFacetSet()->getFacet('rating'); ?>
        <div class="panel panel-primary">
            <div class="panel-heading">
                <h3 class="panel-title">By MPGG Rating</h3>
            </div>
            <ul class="list-group">
                @foreach ($facet as $value => $count)
                    @if ($count)
                    <li class="list-group-item">
                        <a href="?{{ http_build_query(array_merge(Input::all(), array('rating' => $value))) }}">{{ $value }}</a>
                        <span class="badge">{{ $count }}</span>
                    </li>
                    @endif
                @endforeach
            </ul>    
        </div>

        <?php $facet = $resultset->getFacetSet()->getFacet('years'); ?>
        <div class="panel panel-primary">
            <div class="panel-heading">
                <h3 class="panel-title">By Decade</h3>
            </div>
            <ul class="list-group">
                @foreach ($facet as $value => $count)
                    @if ($count)
                    <li class="list-group-item">
                        <a href="?{{ http_build_query(array_merge(Input::all(), array('decade' => $value))) }}">{{ $value }}'s</a>
                        <span class="badge">{{ $count }}</span>
                    </li>
                    @endif
                @endforeach
            </ul>    
        </div>
    </div>
    <div class="col-sm-8 col-md-8 col-lg-9">

        <!-- SEARCH RESULTS GO HERE, EXACTLY AS BEFORE -->

    </div>
</div>
@endif 
```

我们正在做我们在分面搜索部分讨论过的事情；获取方面集，遍历每一项并显示它以及该特定值的结果数。

每个 facet 项都是一个链接，单击该链接将刷新页面，但会应用过滤器。它通过将适当的值合并到当前“活动的”GET 参数集来实现这一点；因此，如果您已经对一个方面进行了过滤，那么单击不同方面集中的一个项目将通过包含适当的查询参数来维护该过滤器。它还将维护您的原始查询，该查询在输入数组中设置为“q”。

这种方法有一些限制——首先，除了手动改变地址栏中的查询参数，没有办法“重置”过滤器——但它的目的是演示如何使用多个方面。我将把改进它作为额外的练习留给你！

## 分享这篇文章