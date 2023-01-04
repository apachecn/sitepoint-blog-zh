# 使用 Solarium 和 SOLR 进行搜索–高级

> 原文：<https://www.sitepoint.com/using-solarium-solr-search-advanced/>

这是使用 Apache 的 SOLR 搜索实现和 Solarium(一个 PHP 库)将它集成到您的应用程序中的系列文章的第四部分，也是最后一部分。

在前三部分中，我们安装并配置了 SOLR 和 Solarium，并开始构建一个搜索电影的示例应用程序。我们还研究了分面搜索。

我们将通过查看 SOLR 的一些更高级的特性，以及如何在 Solarium 中使用它们来结束这个系列。

## 突出 SOLR 的成果

突出显示组件允许您突出显示与您的搜索匹配的文档部分。它显示内容的行为取决于字段——如果是标题，它可能会完整显示匹配的单词，而较长的字段——如摘要或文章正文——它会突出显示单词，但使用片段；就像谷歌的搜索结果一样。

要设置突出显示，首先需要指定要包含的字段。然后，您可以为突出显示的单词或短语设置前缀和相应的后缀。例如，要将高亮显示的单词和短语加粗:

```
$hl = $query->getHighlighting();
$hl->setFields(array('title', 'synopsis'));
$hl->setSimplePrefix('<strong>');
$hl->setSimplePostfix('</strong>'); 
```

或者，要添加背景颜色:

```
$hl = $query->getHighlighting();
$hl->setFields(array('title', 'synopsis'));
$hl->setSimplePrefix('<span style="background:yellow;">');
$hl->setSimplePostfix('</span>'); 
```

或者您甚至可以使用每个字段的设置:

```
$hl = $query->getHighlighting();
$hl->getField('title')->setSimplePrefix('<strong>')->setSimplePostfix('</strong>');
$hl->getField('synopsis')->setSimplePrefix('<span style="background:yellow;">')->setSimplePostfix('</span>'); 
```

一旦在搜索实现中配置了突出显示组件，在搜索结果视图中显示它还需要做一些工作。

首先，您需要通过 ID 从高亮显示组件中提取高亮显示的文档:

```
$highlightedDoc = $highlighting->getResult($document->id); 
```

现在，您可以通过遍历所有突出显示的字段来访问它们，作为突出显示文档的属性:

```
if($highlightedDoc){
    foreach($highlightedDoc as $field => $highlight) {
        echo implode(' (...) ', $highlight) . '<br/>';
    }
} 
```

或者，可以使用`getField()`:

```
if($highlightedDoc){
    $highlightedTitle = $highlightedDoc->getField('title');
} 
```

突出显示的字段不仅仅返回文本，相反，它们将返回文本的“片段”数组。如果没有与特定字段匹配的内容，例如，如果您的搜索与标题匹配，但与提要不匹配，那么该数组将为空。

上面的代码将最多返回一个代码片段。要改变这种行为，您可以使用`setSnippets()`方法:

```
$hl = $query->getHighlighting();
$hl->setSnippets(5);
// . . . as before . . . 
```

例如，假设您搜索单词“star”。其中一个结果有一个概要，内容如下:

> 这一不容错过的电影院活动将展示电视史上最难忘的时刻之一，以及关于两全其美和《星际迷航:下一代》第三季的独家剪辑。《下一代》的背景设定在 24 世纪，是吉恩·罗登伯里在《星际迷航》第一部的 20 多年后创作的。《下一代》成为《星际迷航》系列中播放时间最长的系列，共 7 季 178 集。《星际迷航:下一代——两全其美》是第一次有机会看到两全其美的电影，这是有史以来最伟大的电视剧集之一，是一部在全国范围内精选的电影院精心录制的完整版电影。

突出显示的文档的提要数组将包含三项:

*   关于两全其美和《星际迷航:下一代》的历史和独家剪辑
*   在原版**星际**迷航系列之后。下一代成为该明星最长的系列
*   《迷航》系列，由 7 季 178 集组成。《星际迷航:下一代——最棒的

显示多个片段的一种方法是`implode`它们，例如:

```
implode(' ... ', $highlightedDoc->getField('synopsis')) 
```

这将导致以下结果:

> 关于两全其美的制作和《星际迷航:下一代》的历史和独家剪辑……在最初的《T2》星际迷航系列之后。《下一代》成为《星际迷航》系列中播放时间最长的一部，共 7 季 178 集。**星际**迷航:下一代——最好的

还有许多其他参数可以用来修改高亮组件的行为，这些参数在这里的中有[的解释。](http://wiki.apache.org/solr/HighlightingParameters)

## 将高亮显示集成到我们的电影搜索中

既然我们已经介绍了如何使用高亮显示，那么将它集成到我们的电影搜索应用程序中应该很简单。

在我们运行搜索之前，首先要做的是通过添加以下内容来修改`app/controllers/HomeController.php`:

```
// Get highlighting component, and apply settings
$hl = $query->getHighlighting();
$hl->setSnippets(5);
$hl->setFields(array('title', 'synopsis'));

$hl->setSimplePrefix('<span style="background:yellow;">');
$hl->setSimplePostfix('</span>');

// Execute the query and return the result
$resultset = $this->client->select($query); 
```

然后搜索结果——你会记得在`app/views/home/index.blade.php`中——变成:

```
@if (isset($resultset))    
<header>
    <p>Your search yielded <strong>{{ $resultset->getNumFound() }}</strong> results:</p>
    <hr />
</header>

@foreach ($resultset as $document)

    <?php $highlightedDoc = $highlighting->getResult($document->id); ?>

    <h3>{{ (count($highlightedDoc->getField('title'))) ? implode(' ... ', $highlightedDoc->getField('title')) : $document->title }}</h3>

    <dl>
        <dt>Year</dt>
        <dd>{{ $document->year }}</dd>

        @if (is_array($document->cast))
        <dt>Cast</dt>
        <dd>{{ implode(', ', $document->cast) }}</dd>              
        @endif

    </dl>

    {{ (count($highlightedDoc->getField('synopsis'))) ? implode(' ... ', $highlightedDoc->getField('synopsis')) : $document->synopsis }}

@endforeach
@endif 
```

请注意每个搜索结果实际上是如何在搜索结果文档和突出显示的文档之间混合和匹配字段的——后者实际上是前者的子集。根据您的模式，您可能在突出显示的版本中拥有所有可用的字段。

## 建议–添加自动完成功能

Suggester 组件用于基于不完整的查询输入来建议查询术语。本质上，它检查给定字段上的索引，并提取与特定模式匹配的搜索词。然后，您可以按频率对这些建议进行排序，以增加搜索的相关性。

要设置建议器，我们需要在您的`solrconfig.xml`文件中进行配置。打开它，将下面的 XML 片段放在其他`<searchComponent>`声明附近的某个地方:

```
<searchComponent class="solr.SpellCheckComponent" name="suggest">
    <lst name="spellchecker">
        <str name="name">suggest</str>
        <str name="classname">org.apache.solr.spelling.suggest.Suggester</str>
        <str name="lookupImpl">org.apache.solr.spelling.suggest.tst.TSTLookupFactory</str>
        <str name="field">title</str>  <!-- the indexed field to derive suggestions from -->
        <float name="threshold">0.005</float>
        <str name="buildOnCommit">true</str>
    </lst>
</searchComponent>
<requestHandler class="org.apache.solr.handler.component.SearchHandler" name="/suggest">
    <lst name="defaults">
        <str name="spellcheck">true</str>
        <str name="spellcheck.dictionary">suggest</str>
        <str name="spellcheck.onlyMorePopular">true</str>
        <str name="spellcheck.count">5</str>
        <str name="spellcheck.collate">true</str>
    </lst>
    <arr name="components">
        <str>suggest</str>
    </arr>
</requestHandler> 
```

您会注意到许多对“拼写检查”的引用，但这仅仅是因为 Suggester 组件在内部重用了该功能。

需要注意的重要一点是`<str name="field">`项，它告诉组件我们希望使用标题字段作为我们建议的基础。

重新启动 SOLR，现在您可以尝试通过您的 web 浏览器运行建议查询:

```
`http://localhost:8983/solr/suggest?q=ho` 
```

(您可能需要更改端口号，这取决于您如何设置 SOLR)

输出应该如下所示:

```
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <lst name="responseHeader">
        <int name="status">0</int>
        <int name="QTime">0</int>
    </lst>
    <lst name="spellcheck">
        <lst name="suggestions">
            <lst name="ho">
                <int name="numFound">4</int>
                <int name="startOffset">0</int>
                <int name="endOffset">2</int>
                <arr name="suggestion">
                    <str>house</str>
                    <str>houses</str>
                    <str>horror</str>
                    <str>home</str>
                </arr>
            </lst>
            <str name="collation">house</str>
        </lst>
    </lst>
</response> 
```

如你所见，SOLR 已经为“ho”返回了四个可能的匹配项——** ho * * use，* * ho * * use，* * ho * * rror 和**ho**me。尽管*home* 和*恐怖*在字母表中排在 *house* 之前，但是 *house* 在我们的索引中是最常见的搜索词之一。

让我们使用这个组件为我们的搜索框创建一个自动完成功能，它将在用户键入查询时建议常见的搜索词。

首先，定义路线:

```
public function getAutocomplete()
{
    // get a suggester query instance
    $query = $client->createSuggester();
    $query->setQuery(Input::get('term'));
    $query->setDictionary('suggest');
    $query->setOnlyMorePopular(true);
    $query->setCount(10);
    $query->setCollate(true);

    // this executes the query and returns the result
    $resultset = $client->suggester($query);

    $suggestions = array();

    foreach ($resultset as $term => $termResult) {
        foreach ($termResult as $result) {
            $suggestions[] = $result;
        }
    }

    return Response::json($suggestions);
} 
```

在布局中包含 JQuery UI(和 JQuery 本身):

```
<script src="//code.jquery.com/jquery-1.11.0.min.js"></script>
<script src="//code.jquery.com/ui/1.10.4/jquery-ui.min.js"></script> 
```

包含 JQuery UI 主题:

```
<link rel="stylesheet" type="text/css" href="//code.jquery.com/ui/1.10.4/themes/redmond/jquery-ui.css"> 
```

最后，添加一些 JS 来初始化自动完成:

```
$(function () {
    $('input[name="q"]').autocomplete({
        source: '/autocomplete',
        minLength: 2
    });
}); 
```

这就是全部内容，通过运行一些搜索来尝试一下。

## 基于阵列的配置

如果您愿意，可以使用数组来设置查询，例如:

```
$select = array(
  'query'         => Input::get('q'),
  'query_fields'  => array('title', 'cast', 'synopsis'),
  'start'         => 0,
  'rows'          => 100,
  'fields'        => array('*', 'id', 'title', 'synopsis', 'cast', 'score'),      
  'sort'          => array('year' => 'asc'),      
  'filterquery' => array(
      'maxprice' => array(
          'year' => 'year:[1990 TO 1990]'
      ),
  ),    
  'component' => array(
    'facetset' => array(
      'facet' => array(        
        array('type' => 'field', 'key' => 'rating', 'field' => 'rating'),
      )
    ),
  ),
);

$query = $this->client->createSelect($select); 
```

## 添加更多内核

启动时，SOLR 遍历指定的主目录寻找内核，当它找到一个名为`core.propeties`的文件时，它会识别内核。到目前为止，我们已经使用了一个名为 collection1 的核心，您会看到它包含三个关键项目:

`core.propertes`文件。在最基本的情况下，它只包含实例的名称。

`conf`目录包含实例的配置文件。这个目录至少必须包含一个`schema.xml`和一个`solrconfig.xml`文件。

`data`目录保存索引。此目录的位置可以被覆盖，如果它不存在，将为您创建一个。

因此，要创建一个新实例，请遵循以下步骤:

1.  在您的主目录中创建一个新目录——`movies`(在示例应用程序中)
2.  在其中创建一个`conf`目录
3.  在`conf`目录下创建或复制一个`schema.xml`文件和`solrconfig.xml`文件，并进行相应的定制
4.  在主目录下创建一个名为`core.properties`的文本文件，内容如下:

`name=instancename`

…其中`instancename`是新目录的名称。

请注意，`examples`目录中的 schema.xml 配置包含了对许多文本文件的引用，例如`stopwords.txt`、`protwords.txt`等，您可能也需要复制这些文件。

然后重启 SOLR。

您也可以通过 web 浏览器中的管理 web 界面添加新的核心–单击左侧的核心管理，然后**添加核心**。

## 附加配置

有几个额外的配置文件值得一提。

`stopwords.txt`文件——或者更具体地说，特定语言文件如`lang/stopwords_en.txt`—包含搜索索引器应该忽略的单词，如“a”、“The”和“at”。在大多数情况下，您可能不需要修改这个文件。

根据您的应用程序，您可能会发现需要在`protwords.txt`中添加单词。这个文件包含一个未被“词干化”的受保护单词的列表，也就是说，被简化为它们的基本形式；例如，“问”变成了“问”，“工作”变成了“工作”。有时词干试图“纠正”单词，也许是删除末尾它认为是错误的数字字母。您可能正在处理地理区域，并发现“maine”的词根是“Maine”。

您可以在`synonyms.txt`中指定同义词——意思相同的单词。每行用逗号分隔同义词。例如:

```
GB,gib,gigabyte,gigabytes
MB,mib,megabyte,megabytes
Television, Televisions, TV, TVs 
```

您也可以使用`synoyms.txt`通过同义词映射来帮助纠正常见的拼写错误，例如:

```
assassination => assasination
environment => enviroment 
```

如果您正在使用货币字段，您可能希望更新并关注`currency.xml`，它指定了一些示例汇率——当然这是高度不稳定的。

## 摘要

在本系列中，我们已经研究了 Apache 的 SOLR 搜索实现，并使用 PHP Solarium 库与之交互。我们已经安装并配置了 SOLR 和一个示例模式，并构建了一个用于搜索一组电影的应用程序，展示了 SOLR 的许多特性。我们已经查看了分面搜索，突出显示了结果和 DisMax 组件。希望这能给你足够的基础，让你在应用程序中使用 SOLR 进行搜索。

为了进一步阅读，你可能希望[下载 PDF 格式的 SOLR 参考指南](https://www.apache.org/dyn/closer.cgi/lucene/solr/ref-guide/apache-solr-ref-guide-4.6.pdf)，或者查阅[日光浴室文档](http://wiki.solarium-project.org/index.php/Solarium_3.x_manual)。

## 分享这篇文章