# 在 Symfony 2 中构建广告管理器

> 原文：<https://www.sitepoint.com/building-ad-manager-symfony-2/>

就这一次不会有什么坏处——我不会写 Sass，而是 Symfony。我不得不在工作中做一点后端工作，最终解决了一个有趣的问题，涉及到相当多的事情，所以我认为写一篇关于它的文章不是一个坏主意。

![](img/77f18980f9a70bb0963a91922dab832a.png)

但首先，让我解释一下。主要想法是建立一个广告经理。你说的*广告经理*是什么鬼？假设你的站点/应用程序上有一些地方可以显示广告。我们的网站上确实有这样的东西，我们的一个团队(部分地)致力于用内容使这些地方变得生动。

由于一些无聊的原因，我不会在这里列出，我们不能使用现有的工具，所以我们注定要从头开始构建一些东西。像往常一样，我们希望在不需要太多编码的情况下做很多事情，同时为最终用户(不是开发人员)保持整体的简单性。我想我们为这个小项目想出了一个相当不错的解决方案。

以下是我们设置的功能:

*   YAML 配置+ FTP 接入；
*   图像、视频或 HTML 内容；
*   能够自定义缓存持续时间；
*   要么滑块(是的，模式吸)或收集随机项目。

这个想法很简单。在 Twig 模板中，我们使用`render_esi`(更多信息[这里是](http://symfony.com/doc/current/book/http_cache.html#using-edge-side-includes))点击一个控制器动作，传递给它一个唯一的键(基本上是广告位的名称)，例如:

```
{{ render_esi(url('ads_manager', { 'id': 'home_sidebar_spot' })) }}
```

然后，该操作获取 YAML 配置文件，获取与给定 id 相关联的数据，并呈现一个模板。此时，模板根据给定项目的内容类型(图像、视频、HTML……)执行一些非常简单的逻辑。

准备好了吗？我们走吧。

## 全局配置

在全局配置(可能是`parameters.yml`文件)中我们需要两个东西:配置文件的路径(`ads.yml`)和一组允许的媒体类型。

```
ads:
    uri: http://location.com/path/to/ads.yml
    allowed_types: ['image', 'video', 'html']
```

## 配置文件

配置文件由负责制作广告的团队维护。YAML 是一种对人友好的语言，非常适合简单的配置。

该文件是这样构建的:

```
home_sidebar_spot:
    cache_public: true
    cache_shared_max_age: 86400
    cache_max_age: 28800
    random: true
    data: 
        - type: "image"
          link: "http://cdn.domain.tld/path/to/file.png"
          target: "http://google.fr/"
          weight: 1
```

其中:

*   `cache_public`定义缓存应该是公共的还是私有的；
*   `cache_shared_max_age`定义服务器网络上缓存的最大年龄；
*   `cache_max_age`定义客户端浏览器缓存的最大年龄；
*   `random`定义该点应被视为滑块(多个项目相继出现)还是静态项目，随机选择；
*   `data`是一个项目数组，如果`random`为`false`，则全部显示；如果`random`为真，则减少为一个项目。

`data`中的每个项目都是一个对象，包括:

*   `type`定义媒体类型，可以是`image`、`video`或`html`；
*   `link`将媒体内容定义为图像文件、视频服务或 HTML 文件的绝对 URL
*   如果`type`为`image`，则`target`定义图像后面链接的目标，否则忽略；
*   `weight`定义了当`data`包含几个项目并且`random`被设置为`true`时的提升。

我们网站上的每个广告位都有这样一个区块，所以基本上有一打这样的区块。

## 控制器

控制器非常简单:它只有一个动作。场景是:

1.  获取配置文件；
2.  parseint
3.  从给定 id 中查找数据；
4.  设置缓存配置；
5.  如果是`random`，减少到一个单项；
6.  渲染视图。

```
<?php
// Namespace and uses
class AdsManagerController extends Controller
{

    /**
     * @Route("/ads_manager/{id}", name="ads_manager")
     */
    public function indexAction ($id)
    {
        // Fetch data
        $data = $this->getData($id);

        // Prepare response
        $response = new Response();

        // Configure cache
        if ($data['cache_public'] === true) {
            $response->setPublic();
        } else {
            $response->setPrivate();
        }

        // Set max age
        $response->setMaxAge($data['cache_max_age']);
        $response->setSharedMaxAge($data['cache_shared_max_age']);

        // Handle the weight random
        if ($data['random'] === true) {
            $data['data'] = [$this->randomItem($data['data'])];
        }

        // If content is HTML, fetch content from file in a `content` key
        foreach ($data['data'] as $item) {
            if (strtolower($item['type']) === 'html') {
                $item['content'] = file_get_contents($item['link']) || $item['link'];
            }
        }

        // Set content
        $response->setContent($this->renderView('FrontBundle:AdsManager:index.html.twig', [
            'allowed_type' => $this->container->getParameter('ads')['allowed_types'],
            'content' => $data,
            'id' => $id
        ]));

        return $response;
    }
}

private function getData($id)
{
    // Get path to Ads configuration
    $url = $this->container->getParameter('ads')['uri'];
    // Instanciate a new Parser
    $parser = new Parser();

    // Read configuration and store it in `$data` or throw if we cannot parse it
    try {
        $data = $parser->parse(file_get_contents($url));
    } catch (ParseException $e) {
        throw new ParseException('Unable to parse the YAML string:' . $e->getMessage());
    }

    // If `$id` exists in data, fetch content or throw if it's not found
    try {
        return $data = $data[$id];
    } catch (\Exception $e) {
        throw new \Exception('Cannot find `' + $id + '` id in configuration:' . $e->getMessage());
    }
}

private function randomItem($array) {
    $weights = array_column($array, 'weight');
    $total   = array_sum($weights);
    $random  = rand(1, $total);
    $sum     = 0;

    foreach ($weights as $index => $weight) {
        $sum += $weight;

        if ($random <= $sum) {
            return $array[$index];
        }
    }
}

?>
```

我知道在避免控制器中的私有方法和将长动作分解成更小的代码块之间存在意见分歧。我选择了后者，但是如果你觉得这是个错误，请随时纠正我。我不是 PHP 开发人员。；)

## 景色

至此，我们的控制器完成了。我们只需要处理视图。我们在模板中有一点逻辑，但它实际上是有意义的，因为它严格地关于如何显示内容，所以这不应该太多。

主要思想是:要么来自`content`的`data`键包含几个项目，在这种情况下我们输出一个滑块(在我们的例子中是引导转盘)，要么它只有一个项目，所以我们只输出一个。无论哪种情况，我们都不直接输出一个项目；我们包含了一个分部，它在出现问题时处理类型检查，并重定向到适当的分部。但是让我们从头开始。

```
{# If there are several items to display #}
{% if content.data|length > 1 %}
  {# Output a carousel #}
  <div class="carousel  slide" data-ride="carousel" data-interval="3000">
    {# Carousel indicators #}
    {% for i in 0..(content.data|length)-1 %}
    {% if loop.first %}
    <ol class="carousel-indicators">
    {% endif %}
      <li data-target=".carousel" data-slide-to="{{ i }}" {% if loop.first %}class="active"{% endif %}></li>
    {% if loop.last %}
    </ol>
    {% endif %}
    {% endfor %}

    {# Carousel items #}
    {% for item in content.data %}
    {% if loop.first %}
    <div class="carousel-inner">
    {% endif %}
      <div class="item{% if loop.first %}  active{% endif %}">
        {% include '@Front/AdsManager/_type.html.twig' with {
          type: item.type, 
          item: item
        } %}
      </div>
    {% if loop.last %}
    </div>
    {% endif %}
    {% endfor %}
  </div>

{# If there is a single item, include it #}
{% else %}

  {% include '@Front/AdsManager/_type.html.twig' with {
    type: (content.data|first).type, 
    item: (content.data|first)
  } %}

{% endif %}
```

让我们看看`_type`部分是什么样子的:

```
{# If type is allowed, include the relevant partial #}
{% if type|lower in allowed_type %}
  {% include '@Front/AdsManager/_' ~ type ~ '.html.twig' with { item: item } %}
{# Else print an error #}
{% else %}
  <p>Unknown type <code>{{ type }}</code> for id <code>{{ id }}</code>.</p>
{% endif %}
```

最后但同样重要的是，我们对特定类型的偏好:

```
{# _image.html.twig #}
<div class="epub">
  <a href="{{ item.target|default('#') }}" class="epub__link">
    <img src="{{ item.link|default('http://cdn.domain.tld/path/to/default.png') }}" 
         alt="{{ item.description|default('Default description') }}" 
         class="epub__image" />
  </a>
</div>
```

```
{# _video.html.twig #}
{% if item.link %}
<div class="video-wrapper">
  <iframe src="{{ item.link }}" frameborder="0" allowfullscreen></iframe>
</div>
{% endif %}
```

```
{# _html.html.twig #}
{{ item.content|default('') }}
```

## 最后的想法

就是这样！最后也没那么难，对吧？然而，当你无法依赖第三方服务时，这是一种简单而又强大的广告管理方式。总有改进的空间，所以请随时建议更新和调整。

干杯！

## 分享这篇文章