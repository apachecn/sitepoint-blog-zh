# 使用 WordPress 作为一个无头 CMS

> 原文：<https://www.sitepoint.com/wordpress-headless-cms/>

内容管理系统(CMSs)非常有用，允许你根据需要创建、编辑和管理你的内容。

但是传统的 CMS，比如 WordPress，虽然功能强大，但并不能满足所有人的需求。在某些情况下，需要更高的灵活性。例如，您可能希望将 CMS 与不同的编码方法集成在一起，而不是经常使用前端。在这种情况下，WordPress 可能仍然是答案，因为我们可以改变我们使用它的方式。我们可以把它用作所谓的**无头 CMS** 。

在这篇文章中，我们将讨论如何使用 WordPress 作为一个无头 CMS。但是在进入这个话题之前，让我们先完全弄清楚什么是无头 CMS，它能为我们做什么。

## 定义了一个无头 CMS

用最简单的术语来说，无头 CMS 就是没有前端的 CMS。因此，它只包括 API 和存储和管理内容、组织数据和处理工作流所需的后端。没有所述内容的前端显示。自然，这意味着任何前端模板在一个无头 CMS 中都变得不必要。

因此，与传统 CMS 形成鲜明对比的是，无头 CMS 只提供内容管理后端和 API。由于没有前端，一个无头的 CMS 不能用于真正意义上的内容“发布”。另一方面，传统的内容管理系统，如 WordPress，经常围绕内容发布功能来构建他们的整个存在。总的来说，这是无头 CMS 和传统 CMS 的最大区别。

现在，摆脱前端自然会给日常用户带来一系列挑战。最大的缺点是，在没有真正的前端的情况下，几乎没有任何合适的方法来计算内容或输出在呈现时的外观。

此外，在缺乏适当的安全措施的情况下，一个无头的 CMS 会造成一些严重的安全问题。这是因为，在后端，有必要确保不同的用户角色只有适当的权限，以防止对敏感数据的不必要的访问。

也就是说，无头 CMS 的主要优势是什么？更重要的是，什么时候应该考虑使用一个？

## 无头 CMSs 的优势

也许与 headless CMS 架构相关的最大和最明显的优势是它为开发人员提供了极大的灵活性和控制力。您可以控制开发的几乎每个方面，处理如何管理数据，如何在后端存储内容，甚至决定最适合您需求的前端解决方案。

有了这样的灵活性，再加上每个 headless CMS 都有自己的 API 来管理对后端的调用，您可以节省大量的开发时间。在使用无头 CMS 时，重用现有模块、推送更新和错误修复以及执行其他类似任务更容易。

此外，值得注意的是，构建在无头 CMS 架构之上的应用程序和网站往往更容易扩展。高度的可伸缩性是一个明显的优势。后端已经与前端分离，因此最终用户的停机时间被最小化。

除此之外，无头 CMS 通常与大多数平台兼容，因为前端本身是缺失的，可以根据用户的需要进行集成。由于没有 headless CMS 自带的内容发布解决方案，DDoS 攻击的机会也减少了。

## 为什么你可能使用 WordPress 作为一个无头的 CMS

我们已经看到了无头 CMS 带来的各种优势。

一般来说，在传统的 CMS 可能不适合或者本质上限制太多的情况下，无头 CMS 是更好的选择。以移动开发为例。您可以在一个 headless CMS 中轻松地使用 API 调用将内容传送到 iOS 或 Android 平台。同样，您可以在应用程序中使用 JavaScript 框架，并依靠 headless CMS 架构将内容推送到各种平台。

显然，对于更小和更简单的项目，传统的 CMS 仍然是一条路要走。但是，如果您需要对后端进行更好的控制，并且希望使用 API 调用将您的内容推送到各种平台，或者甚至希望集成一个完全独立于后端的定制前端机制(或者根本不希望有前端解决方案)，那么 headless CMS 是一个理想的选择。

就使用 WordPress 作为一个无头 CMS 而言，它已经内置了 REST API，这意味着我们已经对 API 部分进行了排序。此外，我们可以利用熟悉的 WordPress 后端来管理内容。

显然，这意味着只需几个基本步骤，我们就可以拥有自己的 WordPress 形式的无头 CMS。考虑到 WordPress 非常受欢迎这一事实，将它用作一个无头 CMS 也意味着我们的 CMS 可以在各种各样的硬件和软件组合上运行良好，并且还可以进行定期维护和安全更新。

虽然有各种各样的无头 CMS 平台和选项，而且其中很多本身就很棒，但是 WordPress 仍然有一个非常普遍的优势。它已经存在很长时间了，只需要一个简单的 MySQL 和 PHP 栈就可以运行。你可以通过使用 WordPress 来减少开销和云存储费用，而不是其他可能有大量需求的无头 CMS。

## 使用 WordPress 作为一个无头 CMS

WordPress 有三个主要部分:

*   存储内容的数据库
*   管理面板来管理内容(API)
*   显示内容的 HTML 视图或前端。

自然，作为一个无头 CMS，最后一个选项对我们来说用处不大。当使用 WordPress 作为一个无头 CMS 时，内容仅仅被当作数据。

### 第一步:设置 WordPress

使用新安装的 WordPress 是很重要的。考虑到有各种各样的关于如何安装 WordPress 的教程，并且官方文档在这一步上也相当详细，我们将在这里跳过安装细节。

此外，现在大多数虚拟主机提供商为 WordPress 提供一键安装功能。请记住:将数据库和表的前缀从默认的`wp_`值改为其他值，并使用强密码！

### 第二步:空白主题

还记得我们提到过前端在一个无头的 CMS 中没有什么作用吗？WordPress 有一个庞大的生态系统，以其惊人的主题而闻名。但是我们不需要这些。尽管如此，WordPress 确实需要一个主题来运行，即使前端不会被使用。因此，一个好主意是使用空白主题。

当在 WordPress 上作为一个无头 CMS 工作时，所有需要做的就是添加一个`index.php`文件和一个`style.css`文件来指定主题名称、作者细节等等。让 WordPress 可以使用。

你的主题的`index.php`文件应该将用户重定向到静态站点的主页，从而确保后端不受影响，前端被删除。这里有一个示例 JS 重定向代码，您可以将它放入`index.php`文件中(或者您可以选择使用您自己的任何代码片段):

```
<script type="text/javascript"> window.location = 'http://homepage.com'; </script> 
```

一旦空白主题准备好并上传到`themes`目录，在 WordPress admin 中激活它。就这样，我们离使用 WordPress 作为无头 CMS 又近了一步。

### 第三步:使用 API 请求

现在，作为最后一步，我们只需要发出 AJAX 请求来使用 API 调用。我们已经放置了一个空白主题，所以现在我们可以发出数据请求，以便按照我们希望的方式处理内容。

这样做有各种各样的方法。最简单的方法可能是使用可以处理异步数据请求的 Fetch API。你可以在这里了解更多关于获取 API [的信息。](https://davidwalsh.name/fetch)

然而，某些旧的 web 浏览器仍然不能很好地使用 fetch API，所以最好使用 polyfill 来创建 Fetch API 函数，以防浏览器本身不支持它。

我们的代码可能是这样的:

```
<!DOCTYPE html>
<html>
<head>
  <title>Using WordPress as Headless CMS</title>
</head>
<body>
  <div id="posts-list"></div>

  <!-- polyfill -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js" charset="utf-8"></script>

  <script type="text/javascript"> const pContain = document.getElementById('posts-list')
    fetch('http://localhost:8080/wp/wp-json/wp/v2/posts')
// response
      .then(r => r.json())
      .then(posts => {
        posts.map(post => {
          const pDiv = document.createElement('div')
          pDiv.innerHTML = post.title.rendered
          pContain.appendChild(pDiv)
        })
      }) </script>
</body>
</html> 
```

在上面的代码中，我们只是通过 RESTful API 调用来处理内容。

## 使用 Headless WordPress 做更多事情

出于最实际的目的，当使用 WordPress 作为一个无头 CMS 时，你可能需要添加额外的内容域。为了做到这一点，使用流行的[高级定制字段](http://www.advancedcustomfields.com/) (ACF)插件是一个很好的解决方案。

任何 WordPress 开发者都会告诉你，ACF 可以让你轻松地在内容中添加自定义字段，然后你可以通过 API 调用这些字段。您可以参考 ACF 文档以获得特定于插件的指南。

但是，请注意，当您通过 RESTful API 调用定制字段时，您可能需要使用 [ACF to REST API](https://wordpress.org/plugins/acf-to-rest-api/) 插件，以便正确地构造调用。这个插件可以用来公开 ACF 端点，以供 WordPress REST API 使用。

一个鲜为人知且很少使用的 WordPress 插件在这里也值得一提——[WordPress Headless](https://wordpress.org/plugins/wp-headless/)。这个插件可以让你在几分钟内创建一个无头的 WordPress CMS。它删除了前端，并确保帖子永久链接到编辑器，而不是视图(仅登录用户)。如果你试图快速开始开发，并希望将 WordPress 安装转换成一个无头 CMS，WordPress 无头插件可以做到这一点，而无需你自己设置。显然，API 调用和其他开发任务仍然需要您来处理。

## 结论

当使用 WordPress 作为一个 headless CMS 时，一般的使用场景包括你可能需要使用内容管理的情况，比如团队在后端处理他们自己的数据，等等。如果你希望在前端显示内容作为一个专门的页面，或者让搜索引擎正确地索引你网站的内容，你必须将 WordPress 重新转换回传统的 CMS。

对于所有其他的无头使用目的，这个特定的模型工作起来相当有效，您可以通过定制字段和其他数据结构进一步调整它，以便获得想要的结果。

## 分享这篇文章