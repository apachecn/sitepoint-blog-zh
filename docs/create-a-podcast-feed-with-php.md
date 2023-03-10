# 用 PHP 创建一个播客提要

> 原文：<https://www.sitepoint.com/create-a-podcast-feed-with-php/>

在本文中，我将演示如何使用 PHP 生成一个播客提要。我们将创建一个简单的管理界面来配置播客元数据、添加和列出剧集，然后完成播客提要本身的生成(它只是一个 RSS 文档)。

我将把应用程序建立在这个 skeleton Slim 应用程序的基础上，尽管不管你选择什么样的框架，许多原则都是一样的。您可以从 GitHub 下载应用程序本身，或者阅读教程，并在进行过程中构建它。

准备好了吗？我们开始吧！

## 设置应用程序

Slim skeleton 应用程序包含由 Slim 支持的 web 应用程序的基本设置，还引入了用于查询数据库的 NotORM 和用于处理模板的 Twig。我们还将使用 [getID3 库](https://github.com/nass600/getID3 "nass600/getID3 - GitHub")来处理音频文件的元数据，因此打开`composer.json`文件并添加以下需求:

```
"nass600/get-id3": "dev-master"
```

运行`composer.phar install`，应用程序的依赖项将被下载到一个`vendor`目录中。

创建目录`data`和`public/uploads`，并确保它们都可以被 web 服务器写入。`data`目录将存储一些额外的应用程序配置细节，`public/uploads`将保存我们的播客音频上传。

我将使用 MySQL 来存储应用程序的数据，但是您可以选择任何您觉得合适的 RDBMS。最初，数据库只需要存储剧集信息，因此我们的模式相当简单:

```
CREATE TABLE episodes (
    id INTEGER NOT NULL AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    author VARCHAR(255) NOT NULL,
    summary TEXT NULL,
    description TEXT NULL,
    audio_file VARCHAR(255) NOT NULL,
    created INTEGER NOT NULL,

    PRIMARY KEY (id)
);
```

将文件`config/config.php.example`复制到`config/config.php`，并用您的数据库连接凭证更新它。另外，添加`data`和`public/uploads`目录作为配置条目是个好主意，这样我们就可以在代码中的其他地方引用它们，而不用硬编码它们。

## 配置页面

现在我们可以创建一个页面，用于配置播客提要本身。一个提要包含了一系列元数据，比如标题、类别信息、所有者和提要。我将尽可能保持简单，配置接口最终将只是一个表单，将配置的序列化表示保存到一个文件中。

将以下内容剪切并粘贴到`data/configuration.txt`中，并确保该文件可由 web 服务器写入。

```
a:12:{s:5:"title";s:16:"A Sample Podcast";s:8:"language";s:2:"en";s:9:"copyright";s:14:"SitePoint 2013";s:8:"subtitle";s:16:"Podcast subtitle";s:6:"author";s:9:"SitePoint";s:7:"summary";s:31:"Generating a podcast using PHP.";s:11:"description";s:58:"This is a demonstration of generating a Podcast using PHP.";s:10:"owner_name";s:9:"SitePoint";s:11:"owner_email";s:22:"no-reply@sitepoint.com";s:10:"categories";a:4:{i:0;s:30:"Education|Education Technology";i:1;s:18:"Education|Training";i:2;s:21:"Technology|Podcasting";i:3;s:26:"Technology|Software How-To";}s:8:"keywords";s:21:"PHP,podcasts,tutorial";s:8:"explicit";s:2:"no";}
```

现在我们将创建一个非常简单的类来加载和保存配置。将以下内容另存为`lib/SimpleFileConfiguration.php`:

```
<?php
class SimpleFileConfiguration 
{ 
    const DATA_FILE = 'configuration.txt'; 
    public $dataFile; 

    public function __construct(Pimple $c) { 
        $this->dataFile = $c['config']['path.data'] . $this::DATA_FILE; 
    } 

    public function load() { 
        $contents = file_get_contents($this->dataFile); 
        return unserialize($contents); 
    } 

    public function save($configuration) { 
        $contents = serialize($configuration); 
        file_put_contents($this->dataFile, $contents); 
    } 
}
```

将`SimpleFileConfiguration`的实例化添加到`include/services.php`中；这样就可以在整个应用程序中方便地访问它；

```
<?php
$c['PodcastConfig'] = $c->share(function ($c) { 
    return new SimpleFileConfiguration($c); 
}
```

用`/configure`路线创建文件`routes/configure.php`:

```
<?php
$app->get('/configure', function () use ($app, $c) { 
    $config = $c['PodcastConfig']->load(); 
    $app->view()->setData(array( 
        'configuration' => $config 
    )); 
    $app->render('configure.html'); 
}); 

$app->post('/configure', function () use ($app, $c) { 
    $data = $app->request()->post(); 
    $c['PodcastConfig']->save($data); 
    $app->flash('success', 'Configuration Saved'); 
    $app->redirect('/configure'); 
});
```

最后，创建模板文件`templates/configure.html`,它将显示表单以更新配置值。

```
<form method="post" enctype="multipart/form-data" action="/configure">
 <fieldset>
  <legend>Details</legend>

  <label>Title</label>
  <input name="title" type="text" placeholder="Please enter a title..." value="{{ configuration.title }}" class="input-xlarge">

  <label>Language</label>
  <select name="language">
   <option value="en">English</select>
   <!-- You can put more languages here... -->
  </select>

  <label>Copyright</label>
  <input name="copyright" type="text" placeholder="Please enter copyright information..." value="{{ configuration.copyright }}" class="input-xlarge">

  <label>Subtitle</label>
  <input name="subtitle" type="text" placeholder="Optionally enter a subtitle..." value="{{ configuration.subtitle }}" class="input-xlarge">

  <label>Author</label>
  <input name="author" type="text" placeholder="Please enter the Podcast's author..." value="{{ configuration.author }}" class="input-xlarge">

  <label>Summary</label>
  <textarea name="summary" cols="50" rows="2" placeholder="Please enter a summary..." class="input-xlarge">{{ configuration.summary }}</textarea>    

  <label>Description</label>
  <textarea name="description" cols="50" rows="5" placeholder="Please enter a description..." class="input-xlarge">{{ configuration.description }}</textarea>    

 </fieldset>

 <fieldset>
  <legend>Owner Details</legend>

  <label>Name</label>
  <input name="owner_name" type="text" placeholder="Please enter a the podcast owner's name..." value="{{ configuration.owner_name }}" class="input-xlarge">

  <label>E-mail</label>
  <input name="owner_email" type="text" placeholder="Please enter a the podcast owner's e-mail..." value="{{ configuration.owner_email }}" class="input-xlarge">
 </fieldset>

 <fieldset>
  <legend>Categorization</legend>

  <label>Categories</label>
  <select name="categories[]" multiple="true" class="input-xlarge">
   <optgroup label="Arts">
    <option value="Arts|Design">Design</option> 
    <option value="Arts|Fashion & Beauty">Fashion & Beauty</option> 
    <option value="Arts|Food">Food</option> 
    <option value="Arts|Literature">Literature</option> 
...
   </optgroup>
  </select>

  <label>Keywords</label>
  <textarea name="keywords" cols="50" rows="2" placeholder="Optionally enter some keywords (comma-separated)..." class="input-xlarge">{{ configuration.keywords }}</textarea>    

  <label>Explicit content?</label>
  <select name="explicit">
   <option value="no" {% if configuration.explicit == 'no' %}selected="selected"{% endif %}>No</select>
   <option value="yes" {% if configuration.explicit == 'yes' %}selected="selected"{% endif %}>Yes</select>
  </select>
 </fieldset>

 <div class="form-actions">
  <button type="submit" class="btn btn-primary">Save Configuration</button>  	
 </div>
</form>
```

我从苹果定义的列表中复制了可用的播客类别列表提交给 iTunes。其中有些如喜剧类是自成一类，有些有子类。对于有孩子的人，我在选项值中使用了竖线作为分隔符。

## 添加剧集

接下来，我们将创建一个页面，在这里我们可以创建一个新的播客剧集。让我们在`routes/podcast.php`中定义路线:

```
<?php
$app->get('/episode', function () use ($app) { 
    $app->render('episode-add.html'); 
}); 

$app->post('/episode', function () use ($app, $c) { 
    $db = $c['db']; 
    $data = $app->request()->post(); 
    $dir = $c['config']['path.uploads']; 

    $filepath = $dir . basename($_FILES['file']['name']); 
    move_uploaded_file($_FILES['file']['tmp_name'], $filepath); 

    $id = $db->episodes->insert(array( 
        'title'       =>  $data['title'], 
        'author'      =>  $data['author'], 
        'summary'     =>  $data['summary'], 
        'description' =>  $data['description'], 
        'audio_file'  =>  $filepath, 
        'created'     =>  time() 
    )); 

    $app->flash('success', 'Episode Created'); 
    $app->redirect('/podcast'); 
});
```

我在这里保持事情简单；没有验证，上传音频文件是非常基本的，但你得到的想法。我也不打算在这里讨论编辑或删除功能的实现；这是非常简单的事情，您可以在以后自己实现。

现在用表单创建模板文件`templates/episode-add.html`来添加一个新的播客:

```
<form method="post" enctype="multipart/form-data" action="/episode">
 <fieldset>
  <legend>Details</legend>

  <label>Title</label>
  <input name="title" type="text" placeholder="Please enter a title...">

  <label>Author</label>
  <input name="author" type="text" placeholder="Please enter the author..." value="">        

  <label>Summary</label>
  <textarea name="summary" cols="50" rows="2" placeholder="Please enter a summary..."></textarea>    

  <label>Description</label>
  <textarea name="description" cols="50" rows="5" placeholder="Please enter a description..."></textarea>    

  <label>Audio File</label>
  <input name="file" type="file" />

  <div class="form-actions">
   <button type="submit" class="btn btn-primary">Add Episode</button>  	
  </div>
 </fieldset>
</form>
```

## 列出播客单集

要创建一个列出播客中所有剧集的概览页面，我们可以使用 NotORM 从数据库中获取剧集列表，并将结果直接传递给视图。

将以下内容添加到`routes/podcast.php`:

```
$app->get('/podcast', function () use ($app, $c) { 
    $db = $c['db']; 
    $app->view()->setData(array( 
        'podcast' => $db->episodes()->order('created DESC') 
    )); 
    $app->render('podcast.html'); 
});
```

然后创建`templates/podcast.html`:

```
<table class="table table-bordered table-striped"> 
 <thead> 
  <tr> 
   <td>Title</td> 
   <td>Summary</td>   
  </tr>  
 </thead> 
 <tbody> 
{% for episode in podcast %} 
  <tr> 
   <td>{{ episode.title }}</td> 
   <td>{{ episode.summary }}</td>   
  </tr> 
{% endfor %} 
 </tbody> 
</table> 
{% endblock %}
```

我们需要发布一个提要来使播客可用，这意味着我们要使用一些 XML。为此，我将定义路线`/podcast.xml`并使用`DOMDocument`。

```
<?php
$app->get('/podcast.xml', function () use ($app, $c) { 
    $db = $c['db']; 
    $conf = $c['PodcastConfig']->load(); 

    $xml = new DOMDocument(); 
    $root = $xml->appendChild($xml->createElement('rss')); 
    $root->setAttribute('xmlns:itunes', 'http://www.itunes.com/dtds/podcast-1.0.dtd'); 
    $root->setAttribute('xmlns:media', 'http://search.yahoo.com/mrss/'); 
    $root->setAttribute('xmlns:feedburner', 'http://rssnamespace.org/feedburner/ext/1.0'); 
    $root->setAttribute('version', '2.0'); 

    $link = sprintf( 
        '%s://%s/podcast', 
        $app->request()->getScheme(), 
        $app->request()->getHost() 
    ); 

    $chan = $root->appendChild($xml->createElement('channel')); 
    $chan->appendChild($xml->createElement('title', $conf['title'])); 
    $chan->appendChild($xml->createElement('link', $link)); 
    $chan->appendChild($xml->createElement('generator', 'SitePoint Podcast Tutorial')); 
    $chan->appendChild($xml->createElement('language', $conf['language'])); 
...

    foreach ($db->episodes()->order('created ASC') as $episode) { 
        $audioURL = sprintf( 
            '%s://%s/uploads/%s', 
            $app->request()->getScheme(), 
            $app->request()->getHost(), 
            basename($episode['audio_file']) 
        ); 

        $item = $chan->appendChild($xml->createElement('item')); 
        $item->appendChild($xml->createElement('title', $episode['title'])); 
        $item->appendChild($xml->createElement('link', $audioURL)); 
        $item->appendChild($xml->createElement('itunes:author', $episode['title'])); 
        $item->appendChild($xml->createElement('itunes:summary', $episode['summary'])); 
        $item->appendChild($xml->createElement('guid', $audioURL)); 

        $finfo = finfo_open(FILEINFO_MIME_TYPE); 
        $enclosure = $item->appendChild($xml->createElement('enclosure')); 
        $enclosure->setAttribute('url', $episode['audio_file']); 
        $enclosure->setAttribute('length', filesize($episode['audio_file'])); 
        $enclosure->setAttribute('type', finfo_file($finfo, $episode['audio_file'])); 

        $item->appendChild($xml->createElement('pubDate', date('D, d M Y H:i:s O', $episode['created']))); 

        $getID3 = new getID3(); 
        $fileinfo = $getID3->analyze($episode['audio_file']); 
        $item->appendChild($xml->createElement('itunes:duration', $fileinfo['playtime_string'])); 
    } 

    $xml->formatOutput = true; 

    $res= $app->response(); 
    $res['Content-Type'] = 'application/json'; 
   print $xml->saveXML(); 
});
```

提要生成的亮点是:

*   按照苹果网站上的[要求，我们用根元素`rss`创建 XML 文档，并提供必要的渠道信息。我在这里硬编码了`generator`标签；真的，你可以把它设置成任何你喜欢的。](http://www.apple.com/itunes/podcasts/specs.html "Apple - iTunes - Podcasts - Making a Podcast")
*   我们遍历剧集，并为每一集创建一个`item`元素。如果我们为每集都有一个独特的页面——设置起来非常简单——我们可以使用它作为全局唯一标识符(GUID ),但现在我们只使用音频文件本身的 URL，因为显然这将是唯一的。
*   为了创建包含实际音频文件的 URL、文件大小和 MIME 类型的`enclosure`元素，我们使用`filesize()`和 Fileinfo 扩展名来获取 MIME 类型。
*   为了包含音轨的持续时间，我们使用 getID3 库。
*   我们通过设置正确的头并输出 XML 来完成所有工作。

导航到`/podcast.xml`，您应该会看到播客提要的 XML。让它通过几个 feed 验证器([tools.forret.com/podcast/validator.php](http://tools.forret.com/podcast/validator.php "tools.forret.com/podcast/validator.php")、[castfeedvalidator.com](http://castfeedvalidator.com "castfeedvalidator.com")和[feedvalidator.org](http://feedvalidator.org "feedvalidator.org"))进行仔细检查，然后你就可以[把它提交给 iTunes](http://support.apple.com/kb/ht1819 "iTunes Store - How to submit a podcast to the iTunes Store") 了！

## 摘要

在本文中，我展示了如何构建一个简单的应用程序来创建和发布自己的播客。这个实现缺少很多东西，比如编辑和删除剧集、适当的验证和安全性等。它们超出了本文的范围，但是添加起来很简单。请随意从 GitHub 下载源代码并编码！

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章