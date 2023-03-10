# 如何为 WordPress 创建一个 YouTube 嵌入插件

> 原文：<https://www.sitepoint.com/create-a-youtube-embed-plugin-for-wordpress/>

YouTube 是最受欢迎的视频分享平台。众所周知，他们还让我们在网站上嵌入它的视频。但是仅仅把嵌入的代码粘贴到我们的网站是不够的。它没有响应，并且给我们的页面增加了很多不必要的权重。由于这些问题，用户不太可能播放视频，我们冒着他们离开我们网站的风险。

在本教程中，我将向你展示如何创建一个 YouTube 嵌入插件，它提供一个短代码来添加 YouTube 视频，而不增加页面的重量。

### WordPress 默认嵌入

默认情况下，WordPress 在文章编辑器中将 YouTube URLs 转换成嵌入代码(使用 o embed)。

例如:

```
Check out this video:

http://www.youtube.com/watch?v=dQw4w9WgXcQ
```

..被转换为:

```
Check out this video:

<iframe width="560" height="315" src="http://www.youtube.com/watch?v=dQw4w9WgXcQ" frameborder="0" allowfullscreen></iframe>
```

WordPress 没有对我们的视频显示进行任何其他的优化，比如使其具有响应性，或者动态加载视频以减小页面大小。做一点工作，我们可以对 WordPress 默认处理 YouTube 视频的方式做一些重大改进。

### YouTube Embed 如何增加页面大小？

YouTube 让我们使用标准的`iframe`标签在我们的网站上嵌入视频。这个嵌入代码下载大约 1MB 的资源，以便在页面加载时渲染视频。同样，当我们点击播放按钮播放视频时，大约 500KB 的额外资源被下载！

此图显示了为渲染视频而下载的资源:

![Default YouTube Page Speed Report](img/acdd68c0b8da467e9865add3575bcf26.png)

这增加了你页面的加载时间，从而影响你的“页面速度”分数，我们知道这是个坏消息(进一步阅读[这里](https://www.sitepoint.com/speed-wordpress/)和[这里](https://www.sitepoint.com/page-speed-soon-visitors-see-content/))。

### YouTube 嵌入视频的响应性

默认 YouTube 嵌入代码没有响应。视频的宽度为 560px，高度为 315px。因此，如果你的网站响应速度很快，那么用户在屏幕尺寸小于 560 像素的情况下浏览你的网站可能会有不好的用户体验。

### 让我们建立我们的插件！

让我们创建一个插件来解决这些问题。在我们开始编码之前，让我们创建插件目录及其文件。下面是我们的目录和文件结构:

```
--youtube-embed
    -youtube-embed.php
    -mce.js
    -youtube-embed.js
    -youtube-embed.css
```

为了使插件可以安装，我们将下面的代码放到了**youtube-embed.php**文件中:

```
<?php
/**
 * Plugin Name: SitePoint YouTube Embed
 * Plugin URI: https://www.sitepoint.com/
 * Description: An plugin to embed YouTube videos responsively.
 * Version: 1.0
 * Author: Narayan Prusty
 */
```

### 创建短代码

让我们创建一个名为`youtube`的短代码，它接受 YouTube 视频 ID 并生成 HTML 代码，以便在前端查看时显示视频。

下面是创建短代码的代码。将该代码放入`youtube-embed.php`文件:

```
function youtube_embed_callback($atts=null, $content=null)
{
	extract($atts);

	return "<div class='youtube-container-parent'><div class='youtube-container-child'><div class='youtube-video' data-id='". $id ."'></div></div></div>";
}

add_shortcode("youtube", "youtube_embed_callback");
```

这就是你在文章编辑器中嵌入短代码的方式:

![YouTube Default Post Editor](img/404739ad28b9133836aee6decf4a3a1a.png)

当您查看帖子时，您将看不到任何内容，因为我们没有返回`iframe`嵌入代码，而是显示一个带有视频 ID 的`div`。只有当用户想要播放视频时，我们才会动态加载嵌入代码，从而减少页面加载时间。

### 动态加载 YouTube 视频

我们希望当用户点击播放视频时，只呈现视频**。目前，我们没有任何界面来告诉用户这个页面上存在一个视频。因此，让我们编写一些代码，以便在页面加载时获取视频的缩略图，并为其附加一个点击事件侦听器，以播放视频。**

将此代码放在`youtube-embed.js`文件中，以显示视频的占位符图像:

```
window.addEventListener("load", function(){
    var v = document.getElementsByClassName("youtube-video");
    for (var n = 0; n < v.length; n++) {
        var p = document.createElement("div");
        p.innerHTML = '<img class="youtube-thumb" src="//i.ytimg.com/vi/' + v[n].dataset.id + '/hqdefault.jpg"><div class="play-button"></div>';
        p.onclick = youtube_video_clicked;
        v[n].appendChild(p);
    }
}, false);
```

在上面的视频中，我们找到了类名为`youtube-video`的所有 DOM 元素，并检索了`data-id`属性值。然后我们在名为`div`的`youtube-video`类中添加一个`img`标签，指向视频的占位符图像。

要将该脚本入队，请将以下代码放在您的`youtube-embed.php`文件中:

```
function register_youtube_embed_plugin_scripts() 
{
	wp_register_script("youtube-embed-js", plugins_url("youtube-embed/youtube-embed.js"));
	wp_enqueue_script("youtube-embed-js");
}

add_action("wp_enqueue_scripts", "register_youtube_embed_plugin_scripts");
```

现在，当你使用你的短代码查看文章时，你会看到这样的内容:

![YouTube Plugin Preview](img/96839cdc77bd116d6115bad8bd79e27a.png)

我们还想在用户点击占位符图像播放视频时加载`iframe`。为此，将以下代码放在`youtube-embed.js`文件中:

```
function youtube_video_clicked() {
    var iframe = document.createElement("iframe");
    iframe.setAttribute("src", "//www.youtube.com/embed/" + this.parentNode.dataset.id + "?autoplay=1&autohide=2&border=0&wmode=opaque&enablejsapi=1&controls=0&showinfo=0");
    iframe.setAttribute("frameborder", "0");
    iframe.setAttribute("id", "youtube-iframe");
    this.parentNode.replaceChild(iframe, this);
}
```

当用户单击占位符图像时，`iframe`标签被嵌入，autoplay 属性设置为 true。现在，单击占位符图像将播放视频。

### 让视频有反应

为了使视频具有响应性，将这个 CSS 代码放在`youtube-embed.css`文件中:

```
.youtube-container-child
{
	position: relative;
	padding-bottom: 56.25%; /* 16:9 */
	padding-top: 25px;
	height: 0;
}

.youtube-container-child iframe, .youtube-container-child img
{
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
}
```

要将脚本入队，请将以下代码放在`youtube-embed.php`文件中:

```
function register_youtube_embed_plugin_styles() 
{
    wp_register_style("youtube-embed-css", plugins_url("youtube-embed/youtube-embed.css"));
    wp_enqueue_style("youtube-embed-css");
}

add_action("wp_enqueue_scripts", "register_youtube_embed_plugin_styles");
```

现在，我们的视频应该是这样的:

![YouTube Plugin Preview - Improved](img/a8ceb8cf2c5d1fbecd561a68cb672f13.png)

### 创建短代码按钮

输入短代码来添加视频很麻烦，所以让我们创建一个文本编辑器按钮，该按钮将显示输入视频 ID 并将短代码添加到帖子编辑器的提示。

首先，为了给 WordPress 文本编辑器添加一个按钮，我们需要使用 [Quicktags API](https://codex.wordpress.org/Quicktags_API) 。

将此代码放在`youtube-embed.php`文件中以显示按钮:

```
function youtube_shortcode_text_editor_script() 
{
    if(wp_script_is("quicktags"))
    {
        ?>
            <script type="text/javascript">

                QTags.addButton( 
                    "youtube_shortcode", 
                    "Youtube Embed", 
                    youtube_callback
                );

                function youtube_callback()
                {
                	var id = prompt("Please enter your video id");

					if (id != null) 
					{
						QTags.insertContent('[youtube id="'+id+'"][/youtube]');
					}
                }
            </script>
        <?php
    }
}

add_action("admin_print_footer_scripts", "youtube_shortcode_text_editor_script");
```

这里，我们添加了一个名为`youtube_shortcode`的按钮。当用户点击按钮时，`youtube_callback`被触发，接受用户输入并添加短代码。

下面是它在文本编辑器中的样子:

![YouTube Shortcode Button](img/2f99878730210d98d0480647f80b91e3.png)

### 创建可视化编辑器按钮来添加短代码

同样，我们可以在可视化编辑器中添加一个按钮。为此，我们需要创建一个 TinyMCE 插件。

将这段代码放在`youtube-embed.php`文件中，注册一个 TinyMCE 插件:

```
function enqueue_mce_plugin_scripts($plugin_array)
{
    //enqueue TinyMCE plugin script with its ID.
    $plugin_array["youtube_button_plugin"] =  plugin_dir_url(__FILE__) . "mce.js";
    return $plugin_array;
}

add_filter("mce_external_plugins", "enqueue_mce_plugin_scripts");

function register_mce_buttons_editor($buttons)
{
    //register buttons with their id.
    array_push($buttons, "youtube");
    return $buttons;
}

add_filter("mce_buttons", "register_mce_buttons_editor");
```

接下来，我们需要编写 JavaScript 功能来显示一个提示，将 ID 作为输入并添加我们的短代码。为此，将以下代码放在`mce.js`文件中:

```
(function() {
    tinymce.create("tinymce.plugins.youtube_button_plugin", {

        init : function(ed, url) {

            //add new button     
            ed.addButton("youtube", {
                title : "Youtube Embed",
                cmd : "youtube_command",
                image : "https://cdn3.iconfinder.com/data/icons/free-social-icons/67/youtube_square_color-32.png"
            });

            //button functionality.
            ed.addCommand("youtube_command", function() {
                var id = prompt("Please enter your video id");
                if (id != null) 
                {
                    ed.execCommand("mceInsertContent", 0, '[youtube id="'+id+'"][/youtube]');
                }
            });
        },

        createControl : function(n, cm) {
            return null;
        },

        getInfo : function() {
            return {
                longname : "Extra Buttons",
                author : "Narayan Prusty",
                version : "1"
            };
        }
    });

    tinymce.PluginManager.add("youtube_button_plugin", tinymce.plugins.youtube_button_plugin);
})();
```

我们现在添加一个名为`youtube`的按钮，当用户点击按钮时，我们执行`youtube_command`回调。

它看起来是这样的:

![YouTube Shortcode Button in the Visual Editor](img/909b9af6f04604a01ade437c93d1195f.png)

### 结论

在本教程中，我们看到了如何创建一个 YouTube 嵌入插件，可以在不增加页面大小的情况下嵌入 YouTube 视频，并使我们的视频具有响应性。这是一个小但非常有用的插件。

## 分享这篇文章