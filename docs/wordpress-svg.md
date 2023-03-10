# WordPress SVG 支持:如何在 WordPress 中启用 SVG

> 原文：<https://www.sitepoint.com/wordpress-svg/>

矢量图像在网络上越来越常见。SVG 为标准图像提供了一种可扩展、响应迅速的替代方案，其主要优势是在任何设备上看起来都清晰锐利，并且文件大小通常更小。

跨浏览器支持现在也很全面，当与[图像标签](http://caniuse.com/#feat=svg-img)一起使用或者在 CSS 中作为[背景属性](http://caniuse.com/#feat=svg-css)的一部分时，所有现代浏览器都支持 SVG。

如果你正在使用 WordPress，你可能熟悉媒体库以及它如何作为你的媒体资产的中央存储库。处理文件上传的管理器最初[被限制为批准的文件类型列表](https://codex.wordpress.org/Uploading_Files)。

今天的教程将集中在我们如何增加 SVG 媒体支持，以及如何改进 WordPress 对 SVG 的处理、操作和显示。

## 背景故事——为什么 WordPress 不支持 SVG？

你可能想知道为什么 SVG 从来没有出现在 WordPress 支持的文件类型列表中，答案完全是关于安全性的。

SVG 不像其他格式(如`png`、`jpg`、`gif`)那样是光栅图像，它们实际上是向量，正因为如此，它们有可能被用来携带恶意的 JavaScript。比约恩·约翰森有一篇[很棒的文章指出了这一切是如何运作的。简单明了的是，JavaScript 可以注入其中，并在显示 SVG 时执行。](https://bjornjohansen.no/svg-in-wordpress)

WordPress 核心开发者已经讨论了很长时间(3-4 年)SVG 的包含，主要的症结是它可能带来的潜在安全风险。媒体管理器在几个级别上使用，从完整的站点管理员到编辑和贡献者，这意味着有几个组可能会将恶意 SVG 文件上传(无意或有意)到站点。

很可能暂时不会有任何动作，可悲的是，这意味着 SVGs 不会很快成为默认上传选项。

![svg media library reject alert](img/4768d5e7c4fe2bc74654b936415c19c3.png)

## 将 SVG 格式添加到允许的文件类型

谢天谢地，因为 WordPress 非常灵活，我们可以调整允许上传到媒体库的文件类型。我们只需要连接到`upload_mimes`过滤器并添加我们的 SVG 支持。您可以将这段代码添加到您的子主题的`functions.php`文件中，或者将其创建为一个插件。

```
//add SVG to allowed file uploads
function add_file_types_to_uploads($file_types){

    $new_filetypes = array();
    $new_filetypes['svg'] = 'image/svg+xml';
    $file_types = array_merge($file_types, $new_filetypes );

    return $file_types;
}
add_action('upload_mimes', 'add_file_types_to_uploads');
```

我们正在做的是挂钩到允许的文件类型的数组，并添加 SVG 扩展名(即`image/svg+xml`)作为可接受的文件类型。

这就是我们要让 WordPress 上传这些图片所需要做的一切。现在，当你进入媒体库时，你可以直接将你的 SVG 拖放到库中，它们将作为标准文件出现。

![wordpress svg library](img/5ce516ff28a17634f7bf3dd806386bf6.png)

## 改进媒体库中的 SVG 显示

因为 SVG 不是默认的文件类型，所以它们的显示和处理是“按原样”提供的，这意味着它们都将在管理仪表板中工作，但您不一定拥有最简化的用户体验。

SVG 优化缺乏的一个领域是库的“网格视图”。

在此视图中查看 SVG 时，每个 SVG 都显示为通用占位符，显示默认图标和文件名(如前图所示)。虽然这仍然有效，但它并没有真正帮助你，因为除非你记住了 SVG 的文件名，否则你不会知道哪个是哪个。它应该显示的是图像本身，而不是占位符。

这就是我们现在要看的，优化“网格”,这样我们就能得到视觉上更有用的东西。

### 在网格视图中手动更新 SVG

WordPress 的媒体库是动态构建的，当页面加载时异步获取所有上传的文件，当你滚动时获取更多的文件。在这种情况下，实际上没有任何过滤器或动作可以用来调整文件的显示方式。您需要使用 JavaScript 来手动进行更改。

我们要寻找的是用图像本身替换为 SVGs 显示的默认图标和文字。这样做将使 SVG 库的管理更加简单。

计划是对媒体库中的每个附件执行一个功能。该函数将只对适用的 SVG 图像执行，并将触发对自定义 PHP 函数的 AJAX 调用。在这个函数中，我们将返回 SVG 图像 URL 来替换我们的默认图标。

### 突变观察者

媒体库的第一个复杂性是它都是动态构建的。我们不能在页面加载时或者用`setTimeout`可靠地触发一个函数，因为我们永远不知道它什么时候准备好了。我们需要做的是当 DOM 被更新并且我们的附件被添加到页面时触发我们的函数。

这是实现`Mutation Observers`的完美用例

“突变观察器”为开发人员提供了一种方式来对 DOM 中的变化做出反应，并获得其节点发生的所有变化(突变)的列表。这些观察器取代了过时的`mutation events`,拥有相当全面的浏览器支持，可以在所有现代系统上工作，甚至可以追溯到 IE11。

## 改进 SVG 显示的管理 CSS 调整

有几个地方我们需要应用一些光线样式，以确保我们的 SVG 图像在整个 WordPress 后端看起来很棒。唯一值得注意的是，在较旧的浏览器(IE8-IE10)中，SVG 不喜欢调整大小，当显示为图像时，需要设置高度和宽度属性。对于我们的目的和大多数浏览器来说，这些 CSS 调整就足够了。

我们将遇到的主要问题是没有给 SVG 分配高度或宽度值，导致它们折叠(并变得不可见)。将 SVGs 的宽度和高度设置为 100%可以解决这个问题。

### 媒体库网格布局

较新的默认网格布局为管理和查看附件提供了一个易于使用的界面。虽然它很棒，工作也很好，但它在展示 SVG 方面做得不是很好，默认为带有文件名的图标。

我们需要的是将 SVG 图像本身显示为预览，使我们更容易管理 SVG 集合。

这样做的困难在于，库是动态构建的，这意味着我们需要调整 JavaScript 中的所有内容，使之符合我们的要求。这就是我们的[突变观察者](https://developer.mozilla.org/en/docs/Web/API/MutationObserver)发挥作用的地方。我们可以查找所有新添加的 DOM 节点，然后检查它们是否是附件，如果是，我们可以按照我们想要的方式操作它们。

我们的解决方案包括使用 JavaScript 来检测我们的附件，并将其 ID 传回 PHP，这样我们就可以获取完整的附件 URL。为此，我们需要连接到 WordPress 的 AJAX 功能，并注册一个自定义函数，如下所示。

```
//call our function when initiated from JavaScript
add_action('wp_AJAX_svg_get_attachment_url', 'get_attachment_url_media_library');
```

`wp_AJAX_{name_of_action}`钩子用于创建定制的 AJAX 调用。这意味着无论何时调用 WordPress AJAX，它都会寻找一个与这个钩子同名的`action`。在我们的例子中，当我们稍后调用 AJAX 函数时，我们将传入名为`svg_get_attachment_url`的动作，该动作将依次调用我们的`get_attachment_url_media_library`函数。

如果这是你第一次研究 WordPress AJAX，那么阅读 Codex 文档是一个很好的起点。

我们返回附件 URL 的函数非常简单:

```
//called via AJAX. returns the full URL of a media attachment (SVG) function get_attachment_url_media_library(){

    $url = '';
    $attachmentID = isset($_REQUEST['attachmentID']) ? $_REQUEST['attachmentID'] : '';
    if($attachmentID){
        $url = wp_get_attachment_url($attachmentID);
    }

    echo $url;

    die();
}
```

现在我们已经对 PHP 元素进行了排序，让我们进入 JavaScript 组件并添加以下内容。要实现这一点，您需要将以下内容添加到排队的公共脚本中。

```
//create a mutation observer to look for added 'attachments' in the media uploader
var observer = new MutationObserver(function(mutations){

  // look through all mutations that just occured
  for (var i=0; i < mutations.length; i++){

    // look through all added nodes of this mutation
    for (var j=0; j < mutations[i].addedNodes.length; j++){

        //get the applicable element
        element = $(mutations[i].addedNodes[j]); 

        //execute only if we have a class
        if(element.attr('class')){

            elementClass = element.attr('class');
            //find all 'attachments'
            if (element.attr('class').indexOf('attachment') != -1){

                //find attachment inner (which contains subtype info)
                attachmentPreview = element.children('.attachment-preview');
                if(attachmentPreview.length != 0){

                    //only run for SVG elements
                    if(attachmentPreview.attr('class').indexOf('subtype-svg+xml') != -1){

                        //bind an inner function to element so we have access to it. 
                        var handler = function(element){

                            //do a WP AJAX call to get the URL 
                            $.AJAX({

                                url: AJAXurl,
                                data: {
                                    'action'        : 'svg_get_attachment_url',
                                    'attachmentID'  : element.attr('data-id')
                                },
                                success: function(data){
                                    if(data){
                                        //replace the default image with the SVG
                                        element.find('img').attr('src', data);
                                        element.find('.filename').text('SVG Image');
                                    }
                                }
                            });

                        }(element); 

                    }
                }
            }
        }
    }
  }
});

observer.observe(document.body, {
  childList: true,
  subtree: true
});
```

下面是它的工作原理:

*   我们创建了一个新的“突变观察者”,监听 body 元素的所有变化。我们正在寻找动态加载的附件(最初随着库的打开，另外随着我们滚动以加载更多)。
*   我们遍历所有添加的 DOM 元素，并将它们分配给我们的`element`，我们将在这个元素上进行比较，以检查我们是否在正确的元素上。
*   我们检查我们的元素是否有一个类(有时我们遍历标准文本元素)，然后我们使用它的类来查看它是否有`attachment`类名。我们每个附件都会有这个，所以我们要找的就是这些元素。
*   一旦我们在一个附件上，我们做另一个检查以确保我们在一个单独的附件上(因为有时我们可能在恰好与附件相关的其他元素上，但不是附件本身)，然后搜索任何具有`'attachment-preview`类的子元素将让我们知道我们是否在正确的轨道上。
*   下一步是检查附件预览是否有`subtype-svg+xml`类。我们只想为 SVG 图像触发我们的功能。
*   这是最重要的部分。对于每个附件，我们创建一个新函数并传入`element`作为引用。在这里我们创建了一个`AJAX`调用。在这里，我们提取`data-id`，并调用我们之前注册的 PHP 函数。我们的动作被称为`svg_get_attachment_url`，这涉及到与`wp_AJAX_svg_get_attachment_url`动作挂钩的函数。
*   我们的 AJAX 函数将附件 ID 传递回 PHP，我们用它来获取对象的 URL。我们将它传递回我们的 AJAX 调用，这样就可以将它追加到`element`中，可视化地更新它。

这里有相当多的活动部分，但是它们中的大多数处理寻找正确的 DOM 元素来执行我们的函数。当一切完成后，您将看到 SVG 图像显示在一个网格中:

![WordPress svg library after](img/a55b7763a3ff578f9a506654e0795054.png)

#### 附加说明:

*   这样做的好处是，每当 DOM 改变时就会触发**,比如初始库加载、库滚动(加载新附件)以及将新附件放到库中并上传时。**

*   你看到你的 SVG 图像的速度将完全取决于 WordPress，因为每个 SVG 附件必须执行它自己的 AJAX 调用，然后传回它的 URL，迫使浏览器加载新的图像。我们实际上是在等待两次，这将会减慢速度。虽然所有这些都可以工作，但可能需要几秒钟才能让一切“就位”。

*   另一个有趣的“特性”( **issue** )是，当你将新图像放入媒体库时，所有的附件都会被刷新，这意味着 AJAX 获取和图像渲染必须再次发生。这里没有什么可以调整的，因为它是媒体库动态过程的一部分。

### 媒体库栏布局

当您使用传统的分栏布局查看媒体库时，您会再次注意到 SVG 不能正确显示。

![column media library before](img/095dc9113dd1028c2ed10e5b6d3369c5.png)

之所以看起来像这样，是因为他们没有一个固定的高度或任何 CSS 样式。我们需要对它们进行调整，就像我们对特色图片部分所做的那样:

```
/*adjust SVG images when displayed inside media library column view*/
table.media .column-title .media-icon img[src*='.svg']{
    width: 100%;
    height: auto;
}
```

这将样式您的 SVG 预览，使他们看起来与其他图像内联。你一眼就能看出它们到底是什么。

![column media library after](img/6fe57e054c1398942809e5355d7f0219.png)

### 媒体附件预览

当您在媒体库的“网格”布局中时，您可以单击一个项目，让它在显示附件元数据的模式窗口中打开，这是我们需要调整的另一个区域，以便显示我们的 SVG。

这是它默认的样子，注意使用的默认图标。

![attachment details svg before](img/00f6cefc4ea50483656ba2c35a1c0c93.png)

与其他区域不同，我们已经将 SVG 的完整 URL 作为这个模态的一部分，它显示在`URL`字段下。这里的复杂性在于，虽然我们手头有信息，但因为它是动态的，所以我们需要创建另一个侦听器，在附件预览打开(或导航)时进行查找，并用适用的 SVG 填充媒体窗格。

让我们跳回 javascript 文件，添加以下内容:

```
//Observer to adjust the media attachment modal window 
var attachmentPreviewObserver = new MutationObserver(function(mutations){
    // look through all mutations that just occured
    for (var i=0; i < mutations.length; i++){

        // look through all added nodes of this mutation
        for (var j=0; j < mutations[i].addedNodes.length; j++){

            //get element
            var element = $(mutations[i].addedNodes[j]);

            //check if this is the attachment details section or if it contains the section
            //need this conditional as we need to trigger on initial modal open (creation) + next and previous navigation through media items
            var onAttachmentPage = false;
            if( (element.hasClass('attachment-details')) || element.find('.attachment-details').length != 0){
                onAttachmentPage = true;
            }

            if(onAttachmentPage == true){   
                //find the URL value and update the details image
                var urlLabel = element.find('label[data-setting="url"]');
                if(urlLabel.length != 0){
                    var value = urlLabel.find('input').val();
                    element.find('.details-image').attr('src', value);
                }
            }
        } 
    }   
});

attachmentPreviewObserver.observe(document.body, {
  childList: true,
  subtree: true
});
```

这就是它的工作原理:

*   我们创建一个新的“突变观察者”并将其附加到主体上，寻找所有的变化(包括子节点及其所有子节点)。
*   我们遍历所有的突变和添加的节点。我们正在寻找一个拥有`attachment-details`类或者拥有它的后代的元素。我们有这个条件的原因是，当模式最初被打开时，以及每次模式被更新时(通过点击下一个和上一个按钮)，它被触发。这是因为当您在触发元素中导航时，触发元素本身就是`attachment-details` DOM 元素。
*   一旦我们知道我们在正确的地方，我们需要搜索`URL`元素并提取它。一旦我们有了它，我们用 SVG 图像填充我们的预览。

在我们都完成之后，附件细节模式现在看起来像这样:

![attachment details svg after](img/b47e21572660888faa1946791c2d10f9.png)

### 前端输出

当 WordPress 输出以 SVG 内容为来源的图片时，通常你会发现什么也看不见。这是因为 SVG 没有在属性或 CSS 中设置正确的宽度和高度(考虑到不支持 SVG，这是有意义的)。

为了实现这一点，我们需要添加另一种样式，使我们的 SVG 元素具有响应性。

```
/*sets all SVG's to be responsive. displaying at full width*/
img[src*='.svg']{
    width: 100%;
    height: auto;
}
```

在你把它添加到你的`style.css`之后，所有的 SVG 图像都将被拉伸到全幅，并保持它们的长宽比(在现代浏览器上)。

这是一个标准 SVG 图像的新样式的例子。

![svg output front end](img/dcea720f724d15a573d2215ae3f30f54.png)

需要注意的一点是，由于它是一个选择器，所以它相当脆弱，可以被你的主题或其他插件覆盖。由您来确保这些东西在前端看起来不错，并且具有良好的可伸缩性。

## 把一切都包起来

有了这些改变，您将能够像使用任何其他图像一样使用 SVG 并与之交互，能够快速查看和操作它们，而不必担心哪个是哪个。

希望在某个时候，SVG 会被引入 WordPress core，所有这一切会在后端为你无缝处理，但在那之前，这些变通办法将不得不做。

如需进一步阅读，请查看 Alex Walkers 的文章[和 Maria Antonietta Perna 的文章](https://www.sitepoint.com/designers-guide-working-with-svg/) [Canvas vs. SVG:为工作选择正确的工具](https://www.sitepoint.com/canvas-vs-svg-choosing-the-right-tool-for-the-job/)。

## 分享这篇文章