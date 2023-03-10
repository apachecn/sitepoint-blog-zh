# 在你的 WordPress 管理器中使用 jQuery 交互

> 原文：<https://www.sitepoint.com/using-jquery-interactions-in-your-wordpress-admin/>

在本教程中，我将概述如何利用 jQuery 交互库来创建更具交互性且易于管理的管理区域和元素。

我们将首先解释这些交互是什么，以及我们将使用的三个主要库的概要；可拖动、可放下和可分类。

在我们介绍了这些库的基础知识之后，我将向您展示如何在现实世界中使用它们来扩展您的管理区域和创建交互式区域。

## 什么是 jQuery 交互？

jQuery 交互是不同库的集合，创建这些库是为了抽象出构建交互界面的一些基本困难。

这些脚本是 jQuery UI(用户界面)家族的一部分，处理拖放和排序等交互；有了这些库，我们可以创建复杂的功能，而不需要重新发明轮子。

## 加载 jQuery 交互库

WordPress 预装了所有的交互库(比如 draggable 和 droppable)。您所需要做的就是将所需的脚本在您的站点的前端或后端排队，然后您将能够以您想要的任何方式利用该库。

WordPress 经常将一些交互库放入管理区。例如，在“widgets”管理屏幕上，draggable 和 droppable 都已经加载了(它提供了用于管理侧栏/ widgets 的 draggable 界面),但是最好还是隐式加载您想要的脚本，以防万一。

您可以通过挂接在`wp_enqueue_scripts`或`admin_enqueue_scripts`挂钩上，将您想要的交互库排队。这些钩子可以让你的脚本分别在前端或后端排队。

你将需要编辑你的子主题的`functions.php`文件或者另一个位置(比如你的插件文件，如果你正在写一个插件的话),并如下所示挂接到适当的钩子上。

您将调用`wp_enqueue_script`函数来加载您的脚本。因为我们想要将 WordPress 预装的脚本入队，我们只需要传递正确的库名(你可以通过[到 wp_enqueue_scripts](https://codex.wordpress.org/Function_Reference/wp_enqueue_script) 的 codex 页面获得所有包含的脚本的列表)。

因为我们将看到 draggable、droppable 和 sortable，这里是您需要排队的名称:

*   jquery-ui-可拖动
*   jquery-ui-可删除
*   jquery-ui-sortable

### 在前端排队

创建一个函数，并将其挂在`wp_enqueue_scripts`钩子上。

```
//enqueue draggable on the front end
function enqueue_scripts_front_end(){
wp_enqueue_script('jquery-ui-draggable');
}
add_action('wp_enqueue_scripts','enqueue_scripts_front_end');
```

### 在后端排队

创建一个函数，并将其挂在`admin_enqueue_scripts`钩子上。

```
//enqueue droppable on the back end
function enqueue_scripts_front_end(){
wp_enqueue_script('jquery-ui-droppable');
}
add_action('admin_enqueue_scripts','enqueue_scripts_front_end');
```

像这样的入队将让 WordPress 处理库的加载和它的所有先决条件(因为大多数交互库有几个它依赖的相关文件)。

### 可拖动、可放下和可排序的快速摘要

总的来说，交互库允许你创建与对象的交互和移动，而不需要你自己做所有的艰苦工作。解释这些库和它们能做的一切超出了本教程的范围，但是这里是我对这些库的想法。

#### 可拖动的

顾名思义，您可以创建可拖动的移动，通常用于将元素从一个区域拖动到另一个区域(通常在列表之间)。

#### 可抛弃的

你的拖挂物将被丢弃的区域。你可以在很多地方放下你的东西，或者只放一个。

一般来说，在可拖放的 API 中，你可以移动你的项目和做其他动作(比如检测你的可拖动对象何时进入或离开一个正确的区域等)。

#### 可分类的

这让你可以轻松地移动你的物品，而不必自己放置它们。当你有一个项目的集合，并且它们的位置是至关重要的时候，这是非常好的。

## 一个真实的例子——一个可拖放、可拖动和可排序的相关页面模块

让我们来看一个场景，在这个场景中，您可能希望利用交互库为您的管理员用户(或者如果您管理多个站点，则为您自己)创建动态的有用控件。

页面包含两个您可以编辑的区域。页面的`title`和页面的`content`。

虽然这通常是您在页面上需要的全部内容，但有时您可能希望有额外的信息或功能。

例如，如果您希望页面以可控的方式链接到其他区域，您要么必须在内容区域的底部使用短代码，要么必须使用特殊的页面模板。

我们能做的是为我们的页面创建新的功能，允许管理员用户定义到网站其他页面的链接。这样，用户将能够管理页面底部的链接，而根本不必编辑`content`区域。

在这种情况下，创建一个拖放式的解决方案以及订单将是完美的。

### 我们需要做什么的总结

在我们的场景中，我们将扩展我们的`pages`管理功能；我们将创建一个界面，用户可以选择附加页面链接到当前页面的底部。这个界面将是一个简单的拖放界面，可以选择重新排序页面链接。

下面是我们将如何进行的分解:

1.  创建你的 CSS 和 JS 文件
2.  为页面创建新的元框
3.  添加“可拖动”元素的列表
4.  添加“可删除”元素
5.  保存相关页面
6.  CSS 元素
7.  jQuery 元素
8.  输出相关页面

我们将一步一步地讨论每一个问题，列出我们需要做的事情。

### 1–创建您的 CSS 和 JS 文件

在开始之前，我们需要分别为自定义样式和脚本加载一个额外的 CSS 和 JS 文件。

这些文件只需要在管理后端运行，所以您需要将这两个脚本和样式加入到如下的`admin_enqueue_scripts`钩子中。

```
//enqueue the admin scripts / styles for the related pages functionality
function enqueue_related_pages_scripts_and_styles(){
wp_enqueue_style('related-pages-admin-styles', get_stylesheet_directory_uri() . '/admin-related-pages-styles.css');
wp_enqueue_script('releated-pages-admin-script', get_stylesheet_directory_uri() . '/admin-related-pages-scripts.js', array('jquery','jquery-ui-droppable','jquery-ui-draggable', 'jquery-ui-sortable'));
}
add_action('admin_enqueue_scripts','enqueue_related_pages_scripts_and_styles');
```

您将把所有的样式和脚本放到这两个文件中。它们将与我们将要编写的 PHP 代码结合使用。

### 2–为页面创建新的元框

我们需要首先创建一个新的元框，将持有我们的相关页面功能。首先创建一个函数，并将它附加到`add_meta_boxes`钩子上。在这个函数中，我们将调用`add_meta_box`函数，它定义了新的元框、它所涉及的文章类型以及其他相关信息。

将以下代码放入您的`functions.php`或适用的文件中。

```
//Add a meta box to the 'pages' post type
function add_related_pages_meta_box_to_pages() {
add_meta_box(
'related_pages_meta_box', //unique ID
'Related Pages', //Name shown in the backend
'display_related_pages_meta_box', //function to output the meta box
'page', //post type this box will attach to
'normal', //position (side, normal etc)
'default' //priority (high, default, low etc)
);
}
add_action('add_meta_boxes','add_related_pages_meta_box_to_pages');
```

[关于 add_meta_box 功能的更多信息，您可以查看其 codex 页面](https://codex.wordpress.org/Function_Reference/add_meta_box)(查看您如何定制 meta box 的各种元素)。

现在我们定义我们的函数叫做`display_related_pages_meta_box`。该函数将用于显示元框，因此它接受一个值`$post`。

```
//defines the output for our related pages meta box
function display_related_pages_meta_box($post){
}
```

### 3–添加“可拖动”元素列表

在我们设置了元框后，我们需要创建一个网站上所有页面的列表。

我们需要收集所有的页面，然后将它们可视化地显示为我们可以为界面设置为“可拖动”的块元素。

在`add_related_pages_meta_box_to_pages`函数中添加以下代码。

```
//create nonce
wp_nonce_field('pages_meta_box','pages_meta_box_nonce');

//collect related pages (if we already have some)
$related_pages = get_post_meta($post->ID,'related_pages',true);

//collect all pages on the website
$page_args = array(
'posts_per_page' => -1,
'post_type' => 'page',
'post_status' => 'publish',
'orderby' => 'post_date',
'order' => 'DESC',
'fields' => 'ids'
);
$pages = get_posts($page_args);
//if we have pages to display
if($pages){
echo '<div class="related_pages">';
//left container (all pages)
echo '<div class="left_container">';
echo '<p> Listed below are the pages of your website.</p>';
echo '<p>Drag these to the other container to add them as related page links </p>';
//loop through all pages
foreach($pages as $page){
//collect their id and name and create the page item
$page_id = $page;
$page_name = get_the_title($page_id);
echo '<div class="page_item" data-page-id="' . $page_id . '">';
echo '<div class="page_title">' . $page_name . '</div>';
echo '<div class="remove_item"> Remove </div>';
echo '</div>';
}
echo '</div>';
//end left container
echo '<div class="clearfix"></div>';
echo '</div>';
}
```

让我们来分析一下我们到底在做什么。

我们首先为页面设置 nonce(nonce 是一种安全方法，确保当我们提交表单时，我们知道它是安全的)。检查代码页上的`wp_nonce_field` [功能。总的来说，nonce 将输出一个带有值的隐藏输入类型。第一个参数是 nonce 的管理员名称，第二个参数是隐藏输入的名称(以便以后可以检索和检查)。](https://codex.wordpress.org/Function_Reference/wp_nonce_field)

我们继续将`related_pages`的元值收集到一个变量中。这是一个 post 元字段，包含一个 JSON 编码的字符串，表示我们希望保存的所有相关页面。我们现在收集它，以便以后可以在我们的可删除部分使用它。

我们使用`get_post_meta`通过传入页面的 ID、元字段的名称并指定 true 来获取元值，这样我们只返回一个字段。[看看抄本上的函数定义。](https://codex.wordpress.org/Function_Reference/get_post_meta)

我们通过向`get_posts`函数提供参数来创建页面列表(在这种情况下，它返回代表文章的文章 ID 列表)。

我们遍历所有的页面，收集它们的 ID 和标题。

使用这些信息，我们创建了一个页面项目，其 data-page-id 属性与页面 id 相对应(这将在 jQuery 中稍后介绍)。我们添加了页面标题和一个删除按钮。

### 4–添加“可删除”元素

我们需要创建一个区域来放置可拖动的项目。一旦项目被放在这里，他们可以按照我们喜欢的排序。

返回到`add_related_pages_meta_box_to_pages`函数，寻找写着`//end left container`的注释。我们将在这个注释之后添加额外的代码，它将紧接在`left_container`的结尾之后，但是紧接在`clearix` div 和`related_pages` div 的结尾之前。

```
//Right container
echo '<div class="right_container">';
echo '<p>Drag pages from the left container onto this container </p>';
//if we have previous saved related pages
if(!empty($related_pages)){
$related_pages_array = json_decode($related_pages);
foreach($related_pages_array as $related_page){
//page information
$page_id = $related_page;
$page_name = get_the_title($page_id);

echo '<div class="page_item" data-page-id="' . $page_id . '">';
echo '<div class="page_title">' . $page_name . '</div>';
echo '<div class="remove_item active"> Remove </div>';
echo '<input type="hidden" name="related_pages[]" value="' . $page_id . '"/>';
echo '</div>';
}
}
echo '<div class="droppable-helper"></div>';
echo '</div>';
```

让我们看一下这段代码，这样你就可以看到我们在做什么。

我们创建合适的容器来存放可排序/可拖动的页面项目。

我们查看之前收集的`related_pages`变量，它包含我们已经保存的页面项目的列表(在页面的第一次编辑时，或者如果我们没有选择任何内容，它将是空的)。

如果该变量不为空，我们使用`json_decode`对其进行解码。这个函数将一个 JSON 编码的字符串值转换成一个数组，这样我们就可以遍历每个数组了([如果你有兴趣的话可以看看 PHP 文档](http://php.net/manual/en/function.json-decode.php))。

现在我们用`foreach`遍历相关页面数组，收集表示页面的 ID。此外，我们还使用`get_the_title`函数收集页面名称([查看 codex 获取更多信息](https://codex.wordpress.org/Function_Reference/get_the_title))。

对于每个相关页面，我们创建一个页面项目；这个页面项目有一个类`page_item`和一个名为`data-page-id`的数据属性，它包含页面的 ID。

在项目内部，我们指定一个 remove 按钮(稍后将使用它，以便我们可以删除项目)。

此外，在项目内部，我们定义了包含页面本身值的隐藏字段输入。这将是提交时传递给表单的值(我们将保存该值)。注意，这个名字是带有一个`[]`的`related_pages[]`。这很重要，因为这意味着当我们处理表单时，我们将提供一组页面。

最后，在页面项目之外，我们创建一个可拖放的辅助项目，我们将使用它来使项目更容易地拖放到这个区域中(当您将一个项目从左边的容器拖动到右边的容器时，它将激活并显示您应该将项目拖动到哪里)。

### 5–保存相关页面

至此，我们已经创建了两个容器，一个用于所有页面的列表，另一个用于这些页面被拖动和排序的位置。

目前没有 jQuery，他们不会做任何事情。然而，一旦你设置正确，你就可以将列表中的项目拖放到另一个列表中，这样你将得到一系列隐藏的输入，这些输入将在点击保存时传递。这是我们需要定义保存函数的地方，这样我们就可以把这些值放入一个元值中。

将以下内容复制到您的`functions.php`文件或类似文件中。

```
//save our related pages meta information for pages
function save_meta_information_for_page($post_id){

//test for existence of nonce
if(!isset($_POST['pages_meta_box_nonce'])){
return $post_id;
}
//verify nonce
if(!wp_verify_nonce($_POST['pages_meta_box_nonce'],'pages_meta_box')){
return $post_id;
}
//if not autosaving
if(defined('DOING_AUTOSAVE') && DOING_AUTOSAVE){
return $post_id;
}
//check permissions
if(!current_user_can('edit_page',$post_id)){
return $post_id;
}

//SAFE to save data, let's go
$related_pages_value = '';
//collect related pages (if set)
if(isset($_POST['related_pages'])){
$related_pages_array = $_POST['related_pages'];
$related_pages_value = json_encode($related_pages_array);
}

//update post meta
update_post_meta($post_id,'related_pages',$related_pages_value);

}
add_action('save_post_page','save_meta_information_for_page');
```

让我们分解一下我们到底在做什么。

首先我们创建一个名为`save_meta_information_for_page`的函数，并将它附加到`save_post_page`钩子上。只有在保存/更新页面时，这个钩子才会触发。你可以使用通用的`save_post`钩子(并检查里面的文章类型)，但是这个更新的动作让你针对特定的文章类型([在 save_post codex 页面](https://codex.wordpress.org/Plugin_API/Action_Reference/save_post#Custom_Post_Type:_.27book.27)了解如何使用它)。

在这个函数中，我们先执行安全检查，然后再做其他事情。

我们首先测试 nonce 的存在，然后验证它是否正确(如果失败，我们就不继续)。

之后，我们检查以确保我们不是自动保存，我们只对通过保存按钮的整页更新感兴趣。

最后，我们确保用户可以访问这个页面，并使用`current_user_can`功能编辑它。此功能确定当前登录的用户是否可以执行某些操作。在我们的例子中，如果用户可以编辑页面，他们会被拒绝([在它的 codex 页面](https://codex.wordpress.org/Function_Reference/current_user_can)上了解更多信息)。

一旦我们确定页面是安全的，我们就可以开始处理了。

我们定义了一个空变量`$related_pages_value`,它将用于保存我们的相关页面信息。

我们检查全局变量`$_POST`,看看是否有任何隐藏的名为`related_pages`的输入字段通过。如果我们有它们，我们把它们赋给一个变量(它将作为一个数组出现)。我们调用`json_encode`函数将这个数组转换成一个长字符串，然后我们可以保存它([如果你感兴趣的话，看看 json_encode 函数](http://php.net/manual/en/function.json-encode.php))。

最后一步，我们调用`update_post_meta`函数来保存这个新创建的表示页面值的字符串([查看 codex 条目，这里是](https://codex.wordpress.org/Function_Reference/update_post_meta))。这将确保当我们稍后返回页面时，我们可以检索这些值，并自动将它们放在相关页面的右侧列中。

### 6–CSS 元素

总的来说，你需要一些基本的 CSS 来完成这个教程。

我们的系统将由两个容器组成，一个用于所有页面的列表，另一个用于我们想要的相关页面。

在这些列表中，我们将拥有代表页面本身的页面项目。这些项目将有一个名称和一个删除按钮(当它们被拖到相关页面区域时)。

请打开您之前加入的名为`admin-related-pages-styles.css`的管理 CSS 文件，并添加以下内容。

```
/*Clearfix for floating elements*/
.clearfix:before,
.clearfix:after {
content: " ";
display: table;
}
.clearfix:after{
clear: both;
}

/*left and right containers*/
.related_pages .left_container,
.related_pages .right_container{
float: left;
min-height: 80px;
width: auto;
background: #eee;
padding: 15px;
-webkit-box-sizing: border-box;
-moz-box-sizing: border-box;
box-sizing: border-box;
display: block;
padding-bottom: 50px;
border: solid 1px transparent;
margin-right: 15px;
}
/*simple description field before page items*/
.related_pages .description{
width: 320px;
}
/*page item itself*/
.related_pages .page_item{
padding: 12px;
background: #ccc;
height: 40px;
margin-bottom: 10px;
width: 320px;
display: block;
-moz-box-sizing: border-box;
box-sizing: border-box;
position: relative;
border: solid 1px transparent;
position: relative;
z-index: 1;
}
/*title for the pages*/
.page_item .page_title{
position: relative;
width: calc(100% - 85px);
height: 100%;
white-space: nowrap;
overflow: hidden;
text-overflow: ellipsis;
}

/*removing an item from related pages*/
.page_item .remove_item{
display: none;
position: absolute;
top: 0px;
right: 0px;
height: 100%;
line-height: 40px;
padding: 0px 10px;
box-sizing: border-box;
background: #aaa;
width: 80px;
text-align: center;
cursor: pointer;
}
.page_item .remove_item.active{
display: block;
}

/*page item while dragging*/
.related_pages .page_item.ui-draggable-dragging,
.related_pages .page_item.ui-sortable-helper {
border: solid 1px #aaa;
background: #eee;
}

/*dragging item over droppable zone*/
.right_container.hover-over-draggable{
border: solid 1px #aaa;
}

/*placeholder text for sorting items*/
.related_pages .my-placeholder{
height: 40px;
border: dashed 1px #aaa;
margin-bottom: 10px;
-moz-box-sizing: border-box;
box-sizing: border-box;
width: 320px;
}
/*placeholder for when hovering over droppable*/
.related_pages .droppable-helper{
width: 320px;
height: 40px;
border: dashed 1px #aaa;
margin-bottom: 10px;
display: none;
-moz-box-sizing: border-box;
box-sizing: border-box;
}
.droppable-helper.active{
display: block;
}
```

另一方面，你的风格和设计可以有所不同；您可以随意更改它(因为基本上我们所做的只是将项目从一个列表添加到另一个列表)。

### 7–jQuery 元素

现在所有的 PHP 都已经设置好了，我们可以看看 jQuery 以及我们打算如何使用可拖动、可删除和可排序的库。

首先进入名为`admin-related-pages-scripts.js`的管理 jQuery 文件，添加以下代码。

```
//trigger on document ready
jQuery(document).ready(function($){
});
```

此文档就绪已在无冲突模式下加载。我们所有的代码都将进入这个函数，并附加到`$`项。

#### 左侧可拖动区域

`draggable`库是广泛的，所以请[随意查看 API 的选项、方法和事件的完整列表](http://api.jqueryui.com/draggable/)。

左侧容器有一个所有页面项目的列表。当我们在左侧容器中找到我们喜欢的页面时，我们会将它拖到右侧容器中。放下后，该项目将被复制到此列表中，准备进行排序和保存。

```
/*Draggable Area*/
$('.left_container .page_item').draggable({
helper: 'clone',
revert: 'invalid',
scope: 'related_pages_scope',
cursor: 'move',
zIndex: 5
});
```

我们首先找到`left_container`项及其内部名为`page_item`的项(正如您所记得的，它们是代表每个页面的项)。

我们调用`draggable`函数(它是我们之前排队的可拖动库的一部分)。这个函数有几个选项，但是我只指定了几个来获得我想要的功能。

*   `helper`–“克隆”意味着它会创建一个与你拖动的项目完全相同的副本(这样我们就可以在移动它的时候看到它)
*   `revert`–“无效”意味着除非我们将这个东西放入正确的容器中，否则它会以动画的方式反弹回来(助手将释放，该项目将返回到与拖动项目相同的位置，让您知道什么也没有发生)
*   `cursor`–“移动”是开始拖动时鼠标光标的样式。这是一种审美选择
*   `scope`–‘related _ pages _ scope’是一个特殊的名称，可拖放的和可拖动的将共享这个名称。此选项有助于将区域链接在一起，以便它们能够识别何时可以成功放下可拖动对象
*   `zIndex`–5 是我给我们拖动的`page_item`元素的值。在我们的 CSS 中，我们已经将`page_item`设置为 z-index 1，所以当我们拖动这些项目时，它们会比我们现有的页面项目更高

当你有了这个设置，你应该能够拖动你的页面项目。

![jQuery Interactions](img/0089ee6246ea14b2216adfd3fe3a78ba.png)

#### 右手侧可放下区域

`droppable`库是广泛的，所以请[随意查看 API 的选项、方法和事件的完整列表](http://api.jqueryui.com/droppable/)。

右边的容器是我们将拖动所有页面项目的地方。每个项目将被放在这里，然后再按所需的顺序排序。从添加以下代码开始。

```
/*Droppable Area*/
$('.right_container').droppable({
accept: '.page_item',
scope: 'related_pages_scope',
hoverClass: 'hover-over-draggable',
tolerance: 'touch',
drop: function(event,ui){

//define items for use
var drop_helper = $('.right_container').find('.droppable-helper');
var page_item = ui.draggable.clone();

//on drop trigger actions
page_item.find('.remove_item').addClass('active');
page_item.append('<input type="hidden" name="related_pages[]" value="' + page_item.attr('data-page-id') + '"/>');

//add this new item to the end of the droppable list
drop_helper.before(page_item);
drop_helper.removeClass('active');

trigger_remove_page_item_action();

},
over: function(event,ui){
//when hovering over the droppable area, display the drop helper
$('.right_container').find('.droppable-helper').addClass('active');

},
out: function(event,ui){
$('.right_container').find('.droppable-helper').removeClass('active');
}
});
```

我们首先找到`right_container`，然后调用它的`droppable`函数。这个函数有几个选项和方法，其中一些我已经在下面定义了。

*   `accept`–’。“page_item”是此可删除对象将接受的项目的类名。在我们的例子中，我们希望这个 droppable 可以获取我们的页面项目
*   `scope`–‘related _ pages _ scope’是可拖放元素和可拖动元素共享的名称。这将两个列表连接在一起，并使拖放项目成为可能
*   `hoverClass`–‘hover-over-draggable’是当我们有一个正确的可拖动的悬停在容器上时，将被添加到`right_container`中的类名。这将让我们做一些事情，比如突出显示容器或者启用辅助单元。
*   `tolerance`–“触摸”意味着一旦可拖动的物体接触到可拖放区域，它就能够被拖放(默认情况下，大部分物体必须在要拖放的区域内)。这主要是一个外观和感觉的东西。
*   `drop`–该选项是当一个正确的可拖动对象被放到可拖放对象上时触发的事件。这是我们大部分功能发生的地方
    *   这个函数可以访问`ui`项，它包含对被拖动项的引用。我们通过克隆 draggable 来收集被拖动到局部变量中的页面项。
    *   我们在可删除列表中找到了`droppable-helper`。
    *   现在我们可以访问 draggable 的副本，我们在其中添加了一个新的隐藏输入字段，并使用 append 提供页面 ID。
    *   我们在项目中找到了`remove_item`按钮，并将其设置为活动状态(这样以后我们可以根据需要移除它)
    *   我们获取`droppable-helper`项，并在它之前插入我们选择的相关页面。
    *   我们得到了`droppable-helper`,并确保当我们放下可拖动的项目时，我们移除了它的活动类(因为我们只希望当我们悬停在可拖动的项目上时看到这个辅助项目)
    *   我们调用名为`trigger_remove_page_item_action`的函数，该函数用于将移除事件绑定到移除按钮上(因此，如果我们愿意，我们可以稍后将它们从页面列表中移除)
*   `over`–该选项是当一个正确的可拖动对象悬停在一个正确的可放下对象上时触发的事件。主要我们使用这个函数来激活我们的可拖放的助手项目
    *   当悬停在可投放区域时，找到我们的`droppable-helper`物品并激活它
*   `out`–该选项是当一个正确的可拖动对象悬停在一个正确的可放下对象(它已被移除)之外时触发的事件。我们用它来禁用我们的可删除助手项
    *   当悬停在可投放区域外时，找到我们的`droppable-helper`物品并将其禁用

这个 droppable 的全部目的是获取 draggable，将页面项的值注入其中，然后将它追加到我们的相关页面列表中。您应该能够对此进行扩展，使它变得如您所愿的那样奇特或复杂。

![jQuery Interactions](img/337793af491715280706f4ac2390c772.png)

#### 右侧可分类区域

可排序的库是广泛的，所以请随意查看 API 的选项、方法和事件的完整列表。

既然我们可以将条目从左边的容器移动到右边的容器，我们可以开始对右边的容器(相关的页面条目)进行排序。

```
/*Sortable Area*/
$('.right_container').sortable({
items: '.page_item',
cursor: 'move',
containment: 'parent',
placeholder: 'my-placeholder'
});
```

我们首先再次找到包含相关页面项目的`right_container`。然后我们调用 sortable 函数并定义选项。

*   `items`–’。“page_item”是将被排序的项目的名称
*   `cursor`–“移动”是移动项目时使用的光标类型
*   `containment`–“父项”将限制可排序项目向父项的移动(这将使您无法将项目移出正确的容器
*   `placeholder`–‘my-placeholder’是对项目排序时使用的 CSS 名称。当你排序一个项目时，可排序插件会在项目列表中添加一个临时项目。这个临时项目将上下移动其他项目。对于本教程，我已经提供了样式，使它看起来类似于可拖放的助手，这样你就可以看到排序。

![jQuery Interactions](img/cfabdccb7fbea002f969fda6d4ea3067.png)

#### 其他功能

我们还需要添加一个函数来删除现有的页面项目(例如，您可能已经拖动了一个页面，但现在不再需要它)。

将以下内容添加到 JS 文件的底部。

```
//Remove page item functionality
function trigger_remove_page_item_action(){
$('.remove_item').on('click',function(){
$(this).parents('.page_item').remove();
});
}
trigger_remove_page_item_action();
```

### 8–输出相关页面

现在我们已经将相关页面保存到了一个页面中，我们可以创建一个函数来在适用页面的底部显示这些页面。

```
//Displays the related pages information at the bottom of the page
function display_related_pages_information($html){

global $post;
if($post){
//execute if we are on a page
if(get_post_type($post) == 'page'){
//determine if this page has any related page set
$related_pages = get_post_meta($post->ID,'related_pages',true);
if($related_pages){
//begin related pages output
$html .= '<article class="related_pages">';
$html .= '<h2>Related Pages</h2>';
$related_pages_array = json_decode($related_pages);
foreach($related_pages_array as $related_page){
//get page information
$page_id = $related_page;
$page_name = get_the_title($page_id);
$page_permalink = get_permalink($page_id);
//output related page
$html .= '<section>';
$html .= '<a href="' . $page_permalink . '">';
$html .= $page_name;
$html .= '</a>';
$html .= '</section>';
}
$html .= '</article>';
}
}
}

return $html;
}
add_filter('the_content','display_related_pages_information');
```

让我们分析一下我们在这里做什么。

我们首先创建一个名为`display_related_pages_information`的函数，并将其添加到`the_content`过滤器中。该过滤器将使我们能够访问页面的内容部分(我们将把我们的相关页面放在主页内容区域之后)。

我们将可以访问包含页面`content`的 html 的`$html`项。我们将在上面添加我们的输出代码。

我们获取全局的`$post`项，然后检查 post 类型是否为`page`。如果我们在一个页面上，我们将执行我们的相关页面输出。

我们从元值`related_pages`中获取相关页面，如果我们已经设置了页面，就开始输出。

我们创建一篇文章和一个标题，然后为每个相关的网页，我们收集他们的 ID，名称和永久链接。有了这些信息，我们就可以创建一个指向所需页面的简单链接。

我们返回过滤器底部的`$html`变量，当我们查看页面时，如果它们有相关的页面，它们应该看起来像下面这样。

![jQuery Interactions](img/a16cb1b2312ab8e16e9e23d9fdea94f6.png)

最棒的是，你不需要编辑任何模板文件，相关页面只有在设置好的情况下才会显示。

## 结论

希望到现在为止，您已经更好地掌握了 jQuery 交互库的复杂性，以及如何创建更具交互性和更易于管理的管理功能，特别是当涉及到三个主要的不同库时:dragable、dropable 和 sortable。如果您有任何意见或疑问，请告诉我。

## 分享这篇文章