# 探索 Drupal 8 中的缓存 API

> 原文：<https://www.sitepoint.com/exploring-cache-api-drupal-8/>

Drupal 8 相对于它的前身有很多改进，我们对它又爱又恨。除了核心视图、配置管理或有用的翻译服务等重要系统之外，还有一些不太为人知的变化，但了解和使用这些变化同样重要。其中一个改进是缓存 API，它解决了 Drupal 7 中的许多性能问题。

![drupal8wide](img/077a30fb14c973a02eea34dedb6e8ea1.png)

在本文中，我想介绍一下新的缓存 API。为此，我们将看看如何在我们的定制模块中使用它，因为我们被鼓励在 Drupal 8 中做更多的事情。

此外，我还准备了一个模块形式的小演示，您可以安装它来测试缓存 API 的影响。这是一个简单的页面，在其呈现逻辑中进行外部 API 调用(对一个[虚拟 JSON 端点](http://jsonplaceholder.typicode.com/posts))并缓存其结果。然后，页面显示发生这种情况所需的实际时间，对比外部调用时间和缓存的版本时间。

## 新的缓存 API

![Cache concept.](img/d9b4a56583246b4095b730b2ad037ac6.png)

### 垃圾箱

新的缓存 API(使用默认的[数据库后端](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Cache%21DatabaseBackend.php/class/DatabaseBackend/8)存储)存储在多个*bin*中，这些 bin 映射到以前缀`cache_`开头的表。当与缓存交互时，我们总是从请求缓存箱开始:

```
$cache = \Drupal::cache();
```

其中`$cache`将是代表默认 bin ( `cache_default`)的`DatabaseBackend`对象的实例。要请求特定的 bin，我们在构造函数中传递名称:

```
$render_cache = \Drupal::cache('render');
```

其中`$render_cache`将表示渲染缓存 bin(这是 Drupal 8 中的新特性，旨在全面提高渲染性能)。

如您所见，我们使用`\Drupal`类静态地请求缓存服务。如果我们在类内部工作，最好的做法是从容器注入[服务。您可以通过将相关的缓存 bin 服务(如`cache.default`)指定为服务的参数来实现这一点。](http://www.webomelette.com/drupal-8-dependency-injection-service-container-and-all-jazz)[在这里](https://api.drupal.org/api/drupal/core!core.services.yml/8)您可以获得所有核心服务的列表，包括与缓存相关的服务。

但是为了简洁起见，我们将在这里静态地使用它。

### 检索缓存的项目

一旦我们知道我们想要使用哪个 bin(对于定制模块，这通常是默认的*bin)，我们就可以检索和存储缓存项。*

```
$cache = \Drupal::cache()->get('my_value');
```

就这么简单。`$cache`将是一个`stdClass`对象，包含一些关于缓存项的元数据以及在`$cache->data`属性下可用的实际数据。`my_value`参数是缓存 ID。

要记住的一件重要的事情是，使用不带第二个参数的`get()`方法不会返回已经失效的缓存项(通过编程或过期)。将布尔值`true`作为第二个参数传递将强制它返回数据。

### 存储缓存项目

虽然在缓存中存储新的项目和检索它们一样简单，但是我们在这样做的时候有更多的选择。为了存储一个条目，我们使用了`set()`方法(而不是之前的`get()`),这个方法有两个强制参数和两个可选参数:

*   缓存 ID(稍后我们可以通过它来引用该项的字符串)
*   数据(PHP 值，如字符串、数组或对象，自动序列化并存储在表中——大小不应超过 1MB)
*   过期时间(该缓存项将自动失效的未来时间戳，或基本上意味着该缓存项永不过期的`-1`。最佳实践是使用`Drupal\Core\Cache\CacheBackendInterface::CACHE_PERMANENT`常量来表示这个值)
*   标签(缓存标签的数组，以后可以通过它来识别该项)

举个例子:

```
Drupal::cache()->set('my_value', $my_object, CacheBackendInterface::CACHE_PERMANENT, array('my_first_tag', 'my_second_tag'));
```

这将设置一个标记有 2 个标签的永久缓存项，并将`$my_object`的序列化版本存储为数据。

### 缓存失效和移除

缓存失效意味着各个项目不再是*新鲜的*，并且就它们保存的数据而言是不可靠的。它们将在下一次垃圾收集时被移除，这也可以使用 CacheBackend 对象上的`garbageCollection()`方法来调用。

如上所述，当存储一个缓存项时，我们可以指定一个过期时间。当这段时间过去时，缓存项变得无效，但仍然存在于 bin 中，并且可以被检索。然而，我们也可以使用 CacheBackend 对象上的`invalidate()`、`invalidateMultiple()`或`invalidateAll()`方法来手动使项目无效。

使用`delete()`、`deleteMultiple()`或`deleteAll()`方法可以完全删除项目。这些操作也只发生在 CacheBackend 正在包装的 bin 上，并完全删除相应的表记录。

### 缓存标签

Drupal 8 中缓存 API 的另一个很酷的新特性是缓存标签(setter 方法中的第四个参数)。标签的作用是识别跨多个容器的缓存项目，以便正确地失效。目的是能够准确定位包含相同对象、页面等数据的多个缓存项。例如，节点既可以出现在页面上，也可以出现在视图中(存储在不同 bin 中的不同缓存项中，但都用相同的`node:nid`格式标签进行标记)。这允许在该节点发生更改时使两个缓存项无效，而不必知道缓存 id。

要使用标签手动使缓存无效，我们可以在`\Drupal\Core\Cache\Cache`类上静态使用`invalidateTags()`方法:

```
\Drupal\Core\Cache\Cache::invalidateTags(array('node:5', 'my_tag'));
```

这将调用缓存失效器服务，并使所有标记有`node:5`和`my_tag`的缓存项失效。

此外，对于 Drupal 实体，我们不必创建自己的标签，而是可以从实体系统中检索它们:

*   `\Drupal\Core\Entity\EntityInterface::getCacheTags()`
*   `\Drupal\Core\Entity\EntityTypeInterface::getListCacheTags()`

这使得 Drupal 实体的标签保持一致。

## 演示缓存 API

正如我之前提到的，我创建了一个小模块，让我们看到缓存数据的好处。你可以在[这个 git 库](https://github.com/upchuk/cache_demo_d8)中找到这个模块，但是这里是它的症结所在:

请注意，在这个例子中，我静态地访问缓存后端服务以节省一些空间。对于依赖注入方法(正确的方法)，请看一下存储库代码。

向`/cache-demo`路径添加新路线的路线文件:

```
cache_demo_page:
  path: 'cache-demo'
  defaults:
    _controller: '\Drupal\cache_demo\Controller\CacheDemoController::index'
    _title: 'Cache demo'
  requirements:
    _permission: 'access content'
```

以及在`src/Controller/CacheDemoController.php`里面返回页面的控制器类:

```
<?php

/**
 * @file
 * Contains \Drupal\cache_demo\Controller\CacheDemoController.
 */

namespace Drupal\cache_demo\Controller;

use Drupal\Core\Cache\CacheBackendInterface;
use Drupal\Core\Controller\ControllerBase;
use Drupal\Core\Url;
use \GuzzleHttp\Client;

/**
 * Cache demo main page.
 */
class CacheDemoController extends ControllerBase {

  public function index(Request $request) {
    $output = array();

    $clear = $request->query->get('clear');
    if ($clear) {
      $this->clearPosts();
    }

    if (!$clear) {
      $start_time = microtime(TRUE);
      $data = $this->loadPosts();
      $end_time = microtime(TRUE);

      $duration = $end_time - $start_time;
      $reload = $data['means'] == 'API' ? 'Reload the page to retrieve the posts from cache and see the difference.' : '';
      $output['duration'] = array(
        '#type' => 'markup',
        '#prefix' => '<div>',
        '#suffix' => '</div>',
        '#markup' => t('The duration for loading the posts has been @duration ms using the @means. @reload',
          array(
            '@duration' => number_format($duration * 1000, 2),
            '@means' => $data['means'],
            '@reload' => $reload
          )),
      );
    }

    if ($cache = \Drupal::cache()->get('cache_demo_posts') && $data['means'] == 'cache') {
      $url = new Url('cache_demo_page', array(), array('query' => array('clear' => true)));
      $output['clear'] = array(
        '#type' => 'markup',
        '#markup' => $this->l('Clear the cache and try again', $url),
      );
    }

    if (!$cache = \Drupal::cache()->get('cache_demo_posts')) {
      $url = new Url('cache_demo_page');
      $output['populate'] = array(
        '#type' => 'markup',
        '#markup' => $this->l('Try loading again to query the API and re-populate the cache', $url),
      );
    }

    return $output;
  }

  /**
   * Loads a bunch of dummy posts from cache or API
   * @return array
   */
  private function loadPosts() {
    if ($cache = \Drupal::cache()->get('cache_demo_posts')) {
      return array(
        'data' => $cache->data,
        'means' => 'cache',
      );
    }
    else {
      $guzzle = new Client();
      $response = $guzzle->get('http://jsonplaceholder.typicode.com/posts');
      $posts = $response->json();
      \Drupal::cache()->set('cache_demo_posts', $posts, CacheBackendInterface::CACHE_PERMANENT);
      return array(
        'data' => $posts,
        'means' => 'API',
      );
    }
  }

  /**
   * Clears the posts from the cache.
   */
  function clearPosts() {
    if ($cache = \Drupal::cache()->get('cache_demo_posts')) {
      \Drupal::cache()->delete('cache_demo_posts');
      drupal_set_message('Posts have been removed from cache.', 'status');
    }
    else {
      drupal_set_message('No posts in cache.', 'error');
    }
  }

}
```

在`index()`方法中，我们快速检查 url 中是否存在`clear`查询参数，并调用负责删除缓存项的`clearPosts()`方法。如果没有，我们计算`loadPosts()`方法返回它的值需要多长时间(可以是来自缓存或 API 的帖子)。我们使用 Guzzle 进行 API 调用，当我们这样做时，我们也直接存储结果。然后，我们只输出以毫秒为单位的调用持续时间，并根据是否存储了缓存打印 2 个不同的链接(以允许我们清除缓存项并再次运行 API 调用)。

当您第一次导航到`cache-demo`时，会进行 API 调用，100 篇文章会存储在缓存中。然后，您可以重新加载页面，看看从缓存中检索这些帖子需要多长时间。这样做后，您将有一个链接来清除缓存(通过使用`clear`查询字符串刷新页面),然后是另一个链接，它刷新没有`clear`查询字符串的页面，然后再次调用 API。诸如此类来测试持续时间的对比。

## 结论

在本文中，我们看到了在 Drupal 8 中使用缓存 API 是多么容易。有一些非常简单的类方法，我们可以用来管理缓存项，它变得太简单了，我们不能在我们的自定义模块中使用它。我鼓励您去看看，尝试一下这个 API，亲自看看它有多容易使用。

## 分享这篇文章