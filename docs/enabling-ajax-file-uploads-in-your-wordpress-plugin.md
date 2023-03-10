# 在你的 WordPress 插件中启用 AJAX 文件上传

> 原文：<https://www.sitepoint.com/enabling-ajax-file-uploads-in-your-wordpress-plugin/>

在我们的插件中提供上传功能总是一件棘手的事情。我们需要能够为上传提供良好的用户体验(UX)，同时还要关注随之而来的安全问题。如果处理不当，我们可能会将网站置于出现任何安全漏洞的风险之中。

不用从头开始构建整个解决方案，我们可以利用 WordPress 核心代码来加速开发，特别是利用位于`wp-admin`目录中的 [`async-upload.php`](https://core.trac.wordpress.org/browser/tags/4.4.2/src/wp-admin/async-upload.php) 文件。

使用`async-upload.php`文件有几个好处。首先，因为 WordPress core 自己使用它在媒体库中进行异步上传，我们可以确信代码是符合标准的。此外，所有的验证和权限检查都已经完成，所以我们不需要自己去做。

## 要求

如果我们要使用这个脚本，需要遵循几个规则。下面是对它们的分类。

*   **所使用的`file`输入必须将其名称属性设置为`async-upload`**

这是因为一旦验证在`async-upload.php`文件中通过，`wp_ajax_upload_attachment`就会进一步调用使用`async-upload`作为第一个参数的`media_handle_upload`函数。使用任何其他值都不起作用。

*   **我们随 AJAX 请求一起发送的 nonce 必须使用默认的`_wpnonce`键和从`wp_create_nonce('media-form')`函数生成的值**

这是由于在`wp_ajax_upload_attachment`函数内部发生的`check_ajax_referer`形式的验证。

*   **通过 AJAX 请求发送的数据也需要一个名为`action`的键，其值为`upload-attachment`**

这在`async-upload.php`文件中得到验证，该文件仅在值设置正确时触发`wp_ajax_upload_attachment`功能。

## 关于插件

为了更好地说明在插件中构建自定义 AJAX 文件上传功能的想法，我们将创建一个简单的插件来测试它。

出于本教程的目的，我们将创建一个插件，允许注册用户提交某种竞赛的图像。我们将有一个前端提交表单，这意味着上传表单将显示在某个页面上，用户可以直接上传图像。这是实现 AJAX 上传功能的绝佳选择。

因为我们试图保持事情简单，为了本教程的长度，让我们定义一些这个插件会做什么和不会做什么的指导方针。

该插件将能够:

*   允许管理员通过短代码附加到任何页面的形式。
*   向注册用户显示带有 AJAX 上传功能的提交表单。
*   提交后发送电子邮件通知网站管理员。

对于本教程的范围，该插件不会:

*   将任何提交的内容存储到数据库中。
*   在后端查看提交的内容。
*   允许匿名用户上传文件。

## 引导插件

转到`wp-content/plugins`文件夹，创建一个新文件夹，我们所有的插件代码都将存放在这里。在本教程的剩余部分，我将使用名称`sitepoint-upload`，所有函数和钩子回调都使用前缀`su_`。

接下来，创建主插件文件，与文件夹同名，这样更简单。在插件文件夹中，我们还会有一个`js`文件夹，其中暂时包含一个空的`script.js`文件。

这是我们插件的更新目录结构。

```
wp-content/
|-- plugins/
    |-- sitepoint-upload/
        |-- js/
        |   |-- script.js
        |--sitepoint-upload.php
```

让我们在插件主文件`sitepoint-upload.php`中放入一个简单的插件头，然后进入插件页面激活它。这是我的例子:

```
<?php
/*
Plugin Name: Simple Uploader
Plugin URI: https://www.sitepoint.com
Description: Simple plugin to demonstrate AJAX upload with WordPress
Version: 0.1.0
Author: Firdaus Zahari
Author URI: https://www.sitepoint.com/author/fzahari/
*/
```

### 将脚本入队

然后，我们可以将空的`script.js`放入前端，这将用于处理我们的 AJAX 上传功能，以及增强提交表单。

```
function su_load_scripts() {
    wp_enqueue_script('image-form-js', plugin_dir_url( __FILE__ ) . 'js/script.js', array('jquery'), '0.1.0', true);
}
add_action('wp_enqueue_scripts', 'su_load_scripts');
```

我们还将使用函数`wp_localize_script`本地化一些将在`script.js`中使用的数据。我们需要三样东西，一个到两个`admin-ajax.php`的正确 URL，因为我们也要通过 AJAX 提交表单，还有一个到`async-upload.php`文件的 URL。我们需要本地化的第三个项目是 nonce，它将使用`wp_create_nonce`函数生成。

我们的`wp_enqueue_scripts`钩子更新后的回调函数如下所示:

```
function su_load_scripts() {
    wp_enqueue_script('image-form-js', plugin_dir_url( __FILE__ ) . 'js/script.js', array('jquery'), '0.1.0', true);

    $data = array(
                'upload_url' => admin_url('async-upload.php'),
                'ajax_url'   => admin_url('admin-ajax.php'),
                'nonce'      => wp_create_nonce('media-form')
            );

    wp_localize_script( 'image-form-js', 'su_config', $data );
}
add_action('wp_enqueue_scripts', 'su_load_scripts');
```

### 注册提交表单的短代码

然后，我们需要为我们的提交表单注册短代码，这样我们就可以轻松地将它放在任何我们想要的页面中，而不是一遍又一遍地编写相同的标记。我们的表单将具有:

*   用户名的文本输入字段
*   用户电子邮件地址的另一个电子邮件输入字段
*   AJAX 上传的`async-upload`文件输入
*   一堆占位符`div`，将用于电子邮件预览，错误信息和其他项目。

如果用户当前没有登录，我们也将完全禁用提交表单，而显示一个登录链接。

```
function su_image_form_html(){
    ob_start();
    ?>
        <?php if ( is_user_logged_in() ): ?>
            <p class="form-notice"></p>
            <form action="" method="post" class="image-form">
                <?php wp_nonce_field('image-submission'); ?>
                <p><input type="text" name="user_name" placeholder="Your Name" required></p>
                <p><input type="email" name="user_email" placeholder="Your Email Address" required></p>
                <p class="image-notice"></p>
                <p><input type="file" name="async-upload" class="image-file" accept="image/*" required></p>
                <input type="hidden" name="image_id">
                <input type="hidden" name="action" value="image_submission">
                <div class="image-preview"></div>
                <hr>
                <p><input type="submit" value="Submit"></p>
            </form>
        <?php else: ?>
            <p>Please <a href="<?php echo esc_url( wp_login_url( get_permalink() ) ); ?>">login</a> first to submit your image.</p>
        <?php endif; ?>
    <?php
    $output = ob_get_clean();
    return $output;
}
add_shortcode('image_form', 'su_image_form_html');
```

关于上面的短代码回调函数的一些解释:

*   我们注册的短码是`image_form`。
*   我们使用了[输出缓冲](http://php.net/manual/en/book.outcontrol.php),这样我们可以更加灵活地使用我们在短代码回调函数中公开的内容。
*   我们还通过文件输入上的`accept`属性来限制图像的文件选择。请注意，这不会取代实际的文件验证。[(更多信息)](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-accept)
*   对于登录 URL，我们在`wp_login_url`中提供当前页面永久链接，以便用户在成功登录后被重定向回我们的提交页面。

### 为特定用户角色添加`upload_files`功能

为了确保我们的插件正常运行，我们需要改变角色`subscriber`的功能，因为默认情况下，角色为`subscriber`的用户没有上传文件的功能。

```
function su_allow_subscriber_to_uploads() {
    $subscriber = get_role('subscriber');

    if ( ! $subscriber->has_cap('upload_files') ) {
        $subscriber->add_cap('upload_files');
    }
}
add_action('admin_init', 'su_allow_subscriber_to_uploads');
```

注意，`subscriber`角色只有在仍然不具备`upload_files`能力的情况下才会被修改。

现在我们已经完成了插件基础知识，让我们创建一个新页面来显示我们的提交表单。

![Submit Your Image WordPress](img/7c7503bd2a06239d6722d4d871596ca5.png)

这是表单在前端的样子，在默认的 WordPress 安装中，`twentysixteen`主题是活动的。

![Frontend submission form (Logged In)](img/9bbae15757d99043aae21b5b0ab70642.png)

如果我们从网站注销，将会显示通知。

![Frontend submission form (Logged out)](img/7b94609ad5f63b9f52657ede25df3644.png)

看起来我们的插件组合得很好！

## 实现 AJAX 上传

既然我们的基本插件已经被正确配置，我们可以把注意力集中在我们需要做的核心功能上，AJAX 上传。

让我们打开位于`js`文件夹中的`script.js`文件继续。我们首先将整个代码包装在[立即调用的函数表达式(life)](http://benalman.com/news/2010/11/immediately-invoked-function-expression/)中。

接下来，我们将[缓存一些选择器](https://10up.github.io/Engineering-Best-Practices/javascript/#cache-dom-selections)来加速我们的代码。这包括对图像预览`div`、输入文件以及用于显示上传通知的`div`的引用。

```
(function($) {
    $(document).ready(function() {
        var $formNotice = $('.form-notice');
        var $imgForm    = $('.image-form');
        var $imgNotice  = $imgForm.find('.image-notice');
        var $imgPreview = $imgForm.find('.image-preview');
        var $imgFile    = $imgForm.find('.image-file');
        var $imgId      = $imgForm.find('[name="image_id"]');
    });
})(jQuery);
```

从长远来看，缓存的选择器对我们很有用。如前所述，为了通过`async-upload.php`文件中的验证，需要遵循一些规则。为此，我们将通过 AJAX 向`async-upload.php`文件发送一个 POST 请求，请求中包含指定的正确的键或值对。这可以通过使用[表单数据 API](https://developer.mozilla.org/en/docs/Web/API/FormData) 来完成。

我们将首先挂钩文件输入上的`change`事件，如果输入被更改，只有这样我们才会触发 AJAX 上传。

```
$imgFile.on('change', function(e) {
    e.preventDefault();

    var formData = new FormData();

    formData.append('action', 'upload-attachment');
    formData.append('async-upload', $imgFile[0].files[0]);
    formData.append('name', $imgFile[0].files[0].name);
    formData.append('_wpnonce', su_config.nonce);

    $.ajax({
        url: su_config.upload_url,
        data: formData,
        processData: false,
        contentType: false,
        dataType: 'json',
        type: 'POST',
        success: function(resp) {
            console.log(resp);
        }
    });
});
```

现在，让我们保留上面的代码，并测试上传功能以确保我们在正确的轨道上。使用开发人员控制台(取决于使用的浏览器)，检查控制台选项卡的输出。成功上传后，`async-upload.php`文件给出的响应示例如下:

![upload php](img/656f894e388f128b00f53fa6cfd25984.png)

我们还可以通过直接转到`wp-content/uploads`目录来检查文件是否存在。现在我们看到上传功能运行良好，让我们对上传脚本进行一些改进。以下是我能想到的一些改进:

*   在上传过程中显示进度条或文本。
*   成功上传时显示上传的图片预览。
*   如果上传失败，显示错误。
*   为用户提供一种上传新图像来替换当前图像的方式。

下面我们一个一个来看看怎么做。

### 在上传过程中显示进度条或文本

这个其实很简单。我们只需要为 jQuery AJAX 的`beforeSend`定义一个回调。在 AJAX 上传代码的某处，放置如下代码块:

```
beforeSend: function() {
    $imgFile.hide();
    $imgNotice.html('Uploading&hellip;').show();
},
```

我们使用空的`div`和之前定义的类`image-notice`向用户显示进度文本。我们还隐藏了上传过程中的文件输入。

对于支持的浏览器，我们甚至可以显示上传百分比。我们可以做的是用我们自己的对象覆盖原始的 jQuery xhr 对象。将其添加到`$.ajax`配置中:

```
xhr: function() {
    var myXhr = $.ajaxSettings.xhr();

    if ( myXhr.upload ) {
        myXhr.upload.addEventListener( 'progress', function(e) {
            if ( e.lengthComputable ) {
                var perc = ( e.loaded / e.total ) * 100;
                perc = perc.toFixed(2);
                $imgNotice.html('Uploading&hellip;(' + perc + '%)');
            }
        }, false );
    }

    return myXhr;
}
```

这段代码为受支持的浏览器所做的只是在`Uploading`文本后附加上传百分比，这是一个相当不错的改进。对于不受支持的浏览器，不会发生任何事情，这是一个很好的优雅的降级。

### 上传成功时显示上传的图像预览，上传失败时显示错误

根据我们从`async-upload.php`脚本得到的响应，我们将向用户显示不同的消息。如果`success`键被设置为`true`，我们可以向用户显示上传的图像，并隐藏文件输入。如果上传失败，我们会用之前的`image-notice`替换`div`中的文本。

```
success: function(resp) {
    if ( resp.success ) {
        $imgNotice.html('Successfully uploaded.');

        var img = $('<img>', {
            src: resp.data.url
        });

        $imgId.val( resp.data.id );
        $imgPreview.html( img ).show();

    } else {
        $imgNotice.html('Fail to upload image. Please try again.');
        $imgFile.show();
        $imgId.val('');
    }
}
```

`$imgId`是一个隐藏的输入，我们用它来引用上传的图像 ID。我们稍后将在表单提交中使用这个值，所以现在还不用担心。

### 为用户提供一种上传新图像来替换当前图像的方式

我们要做的是提供一个链接，作为用户用新图片替换当前上传图片的方法。我们将更改上传成功时显示的通知:

```
$imgNotice.html('Successfully uploaded.');
```

到

```
$imgNotice.html('Successfully uploaded. <a href="#" class="btn-change-image">Change?</a>');
```

现在我们有了一个带有类`btn-change-image`的 anchor，我们将利用它。然后我们可以在锚上添加一个点击事件监听器，当它被点击时，它将删除当前的图像预览。我们还将隐藏通知消息，并再次显示文件输入，其值已被重置。

```
$imgForm.on( 'click', '.btn-change-image', function(e) {
    e.preventDefault();
    $imgNotice.empty().hide();
    $imgFile.val('').show();
    $imgId.val('');
    $imgPreview.empty().hide();
});
```

我们还需要在单击时重置文件输入值，以便再次触发`change`事件。

```
$imgFile.on('click', function() {
    $(this).val('');
    $imgId.val('');
});
```

在我们进入下一部分之前，让我们再一次运行上传功能，看看是否一切正常。

## 完成插件

我们将通过 AJAX 处理表单提交，因此我们将事件监听器绑定到表单的`submit`事件。

```
$imgForm.on('submit', function(e) {
    e.preventDefault();

    var data = $(this).serialize();

    $.post( su_config.ajax_url, data, function(resp) {
        if ( resp.success ) {
            $formNotice.css('color', 'green');
            $imgForm[0].reset();
            $imgNotice.empty().hide();
            $imgPreview.empty().hide();
            $imgId.val('');
            $imgFile.val('').show();
        } else {
            $formNotice.css('color', 'red');
        }

        $formNotice.html( resp.data.msg );
    });
});
```

基于上面的代码，我们将使用[内置的 WordPress AJAX 动作](https://codex.wordpress.org/AJAX_in_Plugins)在后端处理提交。成功提交后，我们将重置表单，删除图像预览，并将表单通知文本设置为绿色。

对于失败的提交，我们只需将表单通知文本颜色设置为红色。这将允许用户在再次重试之前查看表单数据。

现在，再次打开插件主文件来添加 AJAX 回调。由于我们将`action`的值设置为`image_submission`，我们将需要为`wp_ajax_image_submission`动作添加一个有效的回调。

```
add_action('wp_ajax_image_submission', 'su_image_submission_cb');
```

在回调函数中，首先需要做几件事情。我们需要检查有效的 AJAX nonce，并验证用户输入。对于本教程的范围，我们将简单地向网站管理员发送电子邮件来获取任何新的提交。

以下是 AJAX 回调函数的完整代码:

```
function su_image_submission_cb() {
    check_ajax_referer('image-submission');

    $user_name  = filter_var( $_POST['user_name'],FILTER_SANITIZE_STRING );
    $user_email = filter_var( $_POST['user_email'], FILTER_VALIDATE_EMAIL );
    $image_id   = filter_var( $_POST['image_id'], FILTER_VALIDATE_INT );

    if ( ! ( $user_name && $user_email && $image_id ) ) {
        wp_send_json_error( array('msg' => 'Validation failed. Please try again later.') );
    }

    $to      = get_option('admin_email');
    $subject = 'New image submission!';
    $message = sprintf(
                    'New image submission from %s (%s). Link: %s',
                    $user_name,
                    $user_email,
                    wp_get_attachment_url( $image_id )
                );

    $result = wp_mail( $to, $subject, $message );

    if ( $result ) {
        wp_send_json_error( array('msg' => 'Email failed to send. Please try again later.') );
    } else {
        wp_send_json_success( array('msg' => 'Your submission successfully sent.') );
    }
}
```

出于我们的目的，一个简单的`check_ajax_referer`检查和本地`filter_var` PHP 函数就足以满足我们的用例。我们还将利用`wp_send_json_error`和`wp_send_json_success`函数发送回响应。

这样，我们的插件就完成了，并且功能齐全。要进行验证，请尝试正确填写表格，并查看是否收到带有上传图像链接的电子邮件。

## 进一步的改进

由于本教程的目的是演示如何通过内部`async-upload.php`文件进行 AJAX 上传，我们肯定在一些地方删减了内容。这里有一些建议可以从整体上改进我们的简单插件。

*   向表单中添加更多的字段，以获取提交的任何附加值。
*   将一个单独的 CSS 文件入队，以更好地设计表单、通知和上传进度。
*   将提交的数据保存到数据库中，以便我们可以再次查看。
*   对上传过程进行更多的验证，使其更加安全。

该插件的完整源代码可在 [GitHub](https://github.com/fsylum/sitepoint-upload) 上获得。

## 结论

总之，如果我们知道从哪里入手，在插件中实现 AJAX 上传可以加快速度。通过使用`async-upload.php`文件，我们可以减少实现该功能的开发时间，并获得一些信心，因为 WordPress core 使用相同的文件来处理管理仪表板中的用户上传。

## 分享这篇文章