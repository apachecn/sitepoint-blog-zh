# 即将到来的 WordPress 博客预览增加用户参与度

> 原文：<https://www.sitepoint.com/upcoming-wordpress-blog-previews-increase-user-engagement/>

WordPress 最初是一个博客平台，过去一直提供一系列工具和功能来增强你的网站，为你的用户提供访问你的内容的简单方法。

你的最新文章页面(也就是你的最新文章或博客页面)一旦建立，将会让你的用户很容易地看到你最近发表的所有博客。

如果你能给你的用户展示一些你即将发布的文章的预览，那不是很棒吗？我们可以将这一点嵌入其中，这就是我们今天要讨论的内容，以及如何利用它来提高用户参与度。

## 创建未来帖子

WordPress 已经提供了一个非常棒的工具，可以安排你的文章在未来的功能中上线，这个功能被称为`post scheduler`。日程安排器让你选择你即将发布的博客的日期和时间。

鉴于 WordPress 已经提供了一种自动发布文章的方式，我们需要做的是输出一个即将发布的预定文章的列表，你的读者可以从中获得一个样本，以吸引他们以后再来。

## 用户参与即将到来的博客预览

创建一个发布预览看起来很简单，但是总的来说是给你的观众提供一些不同的体验。

如果你的读者能够看到你即将发表的文章，以及文章将于何时发表，他们就更有可能与你接触，并有望成为你网站的固定流量。

我们今天将创建的示例显示了一个即将到来的帖子预览和一个简单的联系操作，但是您可以扩展它，以便提示用户注册您的时事通讯或其他类型的可跟踪参与。

## 你需要知道的是

如果你想充分利用这节课，你应该对 WordPress，插件开发和面向对象设计有一个基本的了解，但是我们不会做任何过于复杂的事情。如果你热衷于温习你的基础知识，用 WordPress 查看[面向对象开发。](https://www.sitepoint.com/object-orientated-development-with-wordpress/)

## 开发这个作为一个儿童主题功能或插件？

因为我们想扩展我们网站的功能，第一个想法应该是“让它成为一个插件！”。然而，由于我们正在尝试扩展我们的新闻订阅源的工作方式，我们只使用动作和过滤器所能达到的效果是有限的。在这种情况下，我们可以定义孩子主题中的所有内容；然而，这也有它自己的缺点，其中之一是，如果我们想改变主题，我们将失去我们的功能。

我们需要做的是使用一种**混合方法**，我们**创建一个插件，使用钩子和过滤器**定义我们所有的功能，然后使用**短代码**、**小部件**和**显示函数**在我们需要的地方处理即将到来的预览的输出(即在我们的主要文章列表之前的博客存档页面上)。

虽然这通常不被接受(你应该将你的所有功能添加到一个插件或者一个主题中)，但是这是一个非常罕见的情况，我们没有钩子或者能力在正确的位置输出插件的功能，所以我们需要修改我们的子主题来使它变得完美。

## 哪个模板将显示我的博客？

这很难确定，因为 WordPress 可以使用几个模板文件来显示你的博客存档列表。

如何显示您的博客列表将取决于您对**【阅读设置】**的设置以及您的主题设计。

您的网站很可能会使用您的主题模板之一来显示您的帖子存档。

取决于你如何设置你的**阅读设置**将决定使用哪个模板。

*   font-page.php
    *   当您将首页阅读设置为**最新文章**时，此模板用于显示您的博客。如果你选择使用**一个静态页面**并以这种方式设置，你将为你的博客列表使用不同的模板。
    *   该模板具有最高优先级
*   home.php
    *   当你将阅读设置为**你的最新帖子**或者当你选择了**一个静态页面**并且正在查看被指定为你的**帖子页面**的页面时，这个模板就会显示出来。
    *   总的来说，它只会在你查看你的博客存档页面时显示。
*   index.php
    *   当你将阅读设置为**最新帖子**或**静态页面**并且当前正在查看你的帖子页面时，这个模板将会显示。
    *   只有当你的主题中没有其他更适用的模板，如`front-page.php`或`home.php`时，这才会被使用，并被用作默认模板

## 开发我们的插件-让我们开始吧！

### 成品

当你完成后，你会看到类似下面的东西。

文章预览向您展示了下一篇即将发表的文章的示例，显示了标题、快速摘要以及文章将于何时发表的信息。

![Post Preview Completed Image](img/8e45074c62879a017550b156f5ebfce4.png)

这篇文章与你最近发表的其他文章形成对比，展示了你即将发表的文章，带有预览文本和交互式作者简介(这样用户就可以看到你是谁以及你创作了哪些其他文章)。

外观和感觉将取决于你的主题，但是因为每样东西都有它自己的类别，你可以很容易地根据你的需要来设计。

### 获取源代码并将其复制到您的服务器上

让我们从从 SitePoint GitHub 库下载插件的源代码开始。

代码将被包含在一个单独的文件夹中，该文件夹包含 CSS 和 JS 元素的几个子目录。它看起来应该类似于下图。

![Post Preview Structure](img/b938a37c9abe58c3fab93dbe75e6d810.png)

将插件复制到您的`plugins`目录并激活它。一旦你激活了这个，你就可以开始了！

### 看着亮点

为了简单起见，我将在接下来的几节中概述插件中最重要和最有趣的元素。这应该足以帮助你理解它是如何工作的。

如果你感兴趣，你应该浏览所有的东西，并尽可能扩展它以适合你自己的目的。

### 构建管理界面

由于我们想扩展博客文章的工作方式，我们将修改添加/编辑文章管理屏幕，以提供更多的选项。

除了通常显示在屏幕右上角的**‘Publish’**元框之外，你的屏幕看起来应该没有什么变化。

![post preview admin meta box](img/3addc28b28c6148cce7949cf723a1973.png)

就在“选项的**时间表下方，您会看到一个名为**“即将到来的预览”的新部分。**此部分将让您逐个帖子地决定您的预定帖子是否有即将到来的预览。**

如果你想要一个后预览，你只需选择启用选项，你会得到两个额外的选项。

#### 显示带有“更多”标签的内容

使用这个选项意味着当你的预览显示在你网站的前端时，它会在前端显示你所有的内容，直到它找到 WordPress 的**‘阅读更多’**按钮。

![Post Preview Show Up To Excerpt](img/8a8be9184e257c2c1e9ee994a03eedfd.png)

在这个例子中，当显示后预览将显示图像和文本，直到达到“更多”标签。

#### 显示最多 X 个单词的内容

使用此选项，您将能够确定您的帖子内容区域显示多少个**【单词】。这意味着您可以在前端显示任意多或任意少的单词。**

 **这种方法唯一真正的缺点是，由于内容被修剪到一个固定的长度，您使用的任何图像或可视内容都将被删除(只有您的内容会被提取)。

![post preview show x words](img/06cfe18a0b9d56bdab19beee45ca0e06.png)

在这个例子中，当用作预览时，帖子将显示正好 25 个单词，然后以省略号/用户行动呼吁结束。

在`upcoming_posts_preview.php`文件中寻找神奇的`__construct`函数。在这里，我们使用了 [`post_submitbox_misc_actions`](https://codex.wordpress.org/Plugin_API/Action_Reference/post_submitbox_misc_actions) 钩子，用于在每个帖子的`Schedule For`选项之后输出额外的代码。

我们附加了一个名为`add_post_preview_meta_options`的函数，这样我们就可以输出启用/禁用文章预览的选项(以及一些设置，比如选择显示到 read more 链接或通过修剪的单词)。

首先，如果我们在`post` post 类型上，我们只想执行，这就是为什么我们声明了全局`$post_type`。

```
global $post, $post_type;

//determine if we are on a post 
if('post' == $post_type){

} 
```

当我们在一个岗位上，我们将收集我们的主要选项。这些变量将用于确定这篇文章是否启用或禁用文章预览。如果启用，它还决定了将选择的方法类型(显示“更多”按钮之前的内容或显示 X 个单词)。我们使用[三元运算符](https://www.sitepoint.com/using-the-ternary-operator/)，这样我们可以检查我们的帖子是否有任何先前设置的值，如果有，就相应地分配它们(如果没有，我们默认为 false 或空字符串)。

```
 //determine if we have enabled / disabled post previews
    $upcoming_preview_status = ( 
        get_post_meta( $post->ID, 'upcoming_preview_status', true) ? 
            get_post_meta( $post->ID, 'upcoming_preview_status', true) : 
            'false' 
    );

    //determines the type of post preview 
    $upcoming_preview_type = ( 
        get_post_meta( $post->ID, 'upcoming_preview_type', true) ? 
            get_post_meta( $post->ID, 'upcoming_preview_type', true) : 
            'false' 
    );

    //determines the number of words shown per post preview (if we are displaying a trimmed excerpt)
    $upcoming_preview_type_x_words = ( 
        get_post_meta( $post->ID, 'upcoming_preview_x_words_number', true) ? 
            get_post_meta( $post->ID, 'upcoming_preview_x_words_number', true) : 
            '' 
    ); 
```

我们为文章定义单选按钮，让用户启用或禁用即将到来的文章预览。我们像构建任何其他表单元素一样，通过为这些元素提供一个`name`和`id`并确定应该检查哪个按钮(我们之前收集的值确保我们可以默认检查正确的元素)。

![post preview admin simple](img/cf563eb456fbd965ce9c2915f7a1b7cc.png)

在定义了这些基本单选按钮之后，我们定义了一个二级集合，该集合仅在我们选择**启用即将到来的预览**时显示，这将决定我们将要执行的即将到来的帖子预览的**类型**。它将是:

*   显示文章预览到“更多”标签
*   显示 X 个单词

![post preview admin enabled](img/04d6bf1ef6f034cebcc8f78a7e337d15.png)

如果我们选择**显示 X 个单词数**,则会显示另一组信息，这一次会显示一个数字字段，我们可以在其中选择想要显示的单词数。

![post preview admin enabled x words](img/2068d86b5977033ecba471b4098bf111.png)

### 管理界面交互性

为了使插件易于使用，并模仿 WordPress 设计管理界面的方式，插件使用了少量的 jQuery。

我们名为`upcoming_posts_preview_admin_scripts.js`的 JS 脚本被加载到 WordPress 的管理端，用于使与我们插件的交互更加容易。

我们的第一个主要交互是用于切换发布预览的**【启用】** / **【禁用】**状态。我们根据我们的选择上下滑动次级选项的容器。

```
//toggles the main enabled / disabled
function upcoming_post_preview_toggle_display(){

    //get current state, if we have enabled it, slide down else slide up
    chosen_preview_status = preview_status.filter(':checked').val();

    //if we have chosen a post preview
    if(chosen_preview_status == 'true'){
        preview_content_settings.slideDown('fast');
    }
    //else we didn't choose a post preview
    else if(chosen_preview_status == 'false'){
        preview_content_settings.slideUp('fast');
        //reset the X number of characters to display 
        preview_x_number_of_items.val('');
    }   
}
upcoming_post_preview_toggle_display(); 
```

我们在加载时运行这个函数，并把它附加到单选按钮上，这样当我们改变状态时，我们可以更新发生的事情。

```
//when we change status, toggle container accordingly
preview_status.on('click', upcoming_post_preview_toggle_display); 
```

当我们选择启用发布预览时，我们会使用第二个助手功能。如果我们决定**显示 X 个单词**，那么我们需要向下滑动一个额外的字段，这就是将要使用的**‘单词数’**。

```
//Toggling the main settings (display up to more tag or X number of words)
function upcoming_post_preview_setting_display(){

    //get the currently chosen option setting
    var chosen_preview_type = preview_types.filter(':checked').val();

    //if we chose to display content up to the 'more' tag
    if(chosen_preview_type == 'upcoming_preview_more_tag'){
        preview_content_sub_settings.slideUp('fast');
        preview_x_number_of_items.removeAttr('required');
    }
    //if we chose to display X number of words
    else if(chosen_preview_type == 'upcoming_preview_x_words'){
        preview_content_sub_settings.slideDown('fast');
        //set the number of words field to have the 'required' attribute
        preview_x_number_of_items.attr('required','required');
    }

}
upcoming_post_preview_setting_display(); 
```

当我们加载和改变状态类型时，这个函数就会发生。

```
//when we choose our type of upcoming preview that will be displayed, toggle additional options if required
preview_types.on('click', upcoming_post_preview_setting_display); 
```

### 构建您即将发布的预览 HTML

在你的文章预览被显示之前，它首先必须被建立。我们使用一个名为`get_upcoming_post_preview`的函数来做这件事。

该函数接受一组可选的参数，用于获取您最近的预定帖子。

该函数定义了它的默认参数。这些将在我们的主要查询中使用，以便网站知道哪些帖子要拉，有多少。

```
$arguments = array(
        'number_of_previews'    => 1,
        'post_id'               => false
    ); 
```

如果我们传入任何可选参数，我们会检查它们是否存在于我们的参数数组中(我们的默认值)。如果它们确实存在，我们就更新它们(因为我们覆盖了默认功能)。

```
//If we passed in arguments, copy new arguments
if($optional_arguments){
    foreach($optional_arguments as $key => $value){
        if(array_key_exists($key, $arguments)){
            $arguments[$key] = $value;
        }
    }
} 
```

现在我们已经准备好了基本的参数，我们调用`get_posts`函数并传入一个参数列表来决定收集哪些帖子。我们的一些论点将能够决定这个函数是如何运作的。

```
 //main query to find posts
    $post_args = array(
        'post_type'         => 'post',
        'post_status'       => 'future',
        'orderby'           => 'post_date',
        'order'             => 'ASC',
        'posts_per_page'    => $arguments['number_of_previews'],  
        'include'           => $arguments['post_id'], 
        'meta_key'          => 'upcoming_preview_status',
        'meta_value'        => 'true'
    );

    $posts = get_posts($post_args); 
```

重要的是，我们将`meta_key`的值设置为`upcoming_preview_status`，并确保其`meta_value`为`true`。这将限制搜索只有在网站后端的`enabled`的职位。

对于每个即将到来的预览，我们从我们的帖子和帖子作者那里收集大量信息，这样我们就可以设计一个有趣的设计。如果没有信息(例如作者描述或其他作者文章)，这些元素中的许多都不会显示。

如果你想输出特定的信息，可以直接修改插件，或者你可以选择使用`sc_upcoming_post_public_display_start`和`sc_upcoming_post_public_display_end`过滤器来改变你的显示。

当输出完成后，它被返回并最终显示给用户。

### 展示在您的前端(通过动作挂钩)

显示你即将发布的预览的主要方法是使用插件提供的`get_upcoming_post_preview`钩子。

您可以使用其简单形式调用该操作。

```
//display our latest upcoming post previews
do_action('display_upcoming_post_previews'); 
```

或者你可以使用一个更复杂的样式，你可以给动作传递可选的参数(这将允许你定制显示什么样的预览)。

```
//define arguments to pass to our action
$optional_args = array(
    'number_of_previews'    => 2
);

//display our latest upcoming post previews
do_action('display_upcoming_post_previews', $optional_args); 
```

#### 可以传递给我们行动的选项

目前只有两个选项可以设置:

*   `number_of_previews`–确定要显示的预览数量(默认为 1)
*   `post_id`–通过帖子 id 获取单个预览

这种预览显示的方式将取决于你的主题，你可以自由选择你喜欢的风格。下面是分别用在**【二十三】****【二十四】****【二十五】**时的样子。

![enter image description here](img/024810d2632e3f3495bf58ec10311a59.png "post_preview_front_end_theme_comparison.jpg")

### 有用的挂钩和过滤器

在整个插件中，我添加了几个**有用的动作和过滤器**，可以用来扩展插件。你可以挂钩这些元素，**改变默认值**或者**输出你自己的代码来扩展可能的功能。**

这里是你可以使用的钩子

*   `sc_upcoming_post_link_action`
    *   用于更改“阅读更多”按钮将转到的`link`。默认情况下，它会创建一个带有站点管理员电子邮件地址的 mailto:链接。你可以用它来发送所有的动作到一个特定的页面。
*   `sc_upcoming_post_link_text`

    *   用于定义显示在阅读更多按钮上的`text`。默认情况下，它会说“联系我们，我们会在这篇文章发表后通知你！”。您可以将它设置为您想要的任何文本
*   `sc_upcoming_post_admin_form_start`

    *   在发布预览的主要“启用”/“禁用”选项之后显示。一旦您选择“启用”,就会显示此挂钩，并允许您定义您可能想要保存的任何其他附加选项。
*   `sc_upcoming_post_admin_form_end`
    *   在发布预览的“启用”/“禁用”选项后显示。一旦您选择“启用”帖子预览，此钩将显示在表单的末尾。您可以使用它来保存附加信息
*   `sc_upcoming_post_admin_save`

    *   这个动作在我们发布的`save`过程中被调用。一旦我们成功地开始保存，这将被触发，并可用于接收由`sc_upcoming_post_admin_form_start`或`sc_upcoming_post_admin_form_end`钩子添加的新添加的元字段。
*   `sc_upcoming_post_public_display_start`

    *   在即将到来的后期预览输出开始后立即定义。用于在每个帖子预览开始时输出额外的 HTML 或数据(例如添加一些新的信息或样式)
*   `sc_upcoming_post_public_display_end`
    *   在即将到来的预览输出结束时定义。用于在每次发布预览结束时输出额外的 HTML 或数据。
*   `display_upcoming_post_previews`
    *   我们的主要显示挂钩，使用时，从前端调用后预览将被显示。您可以传入一些选项来更改您的输出

### 显示在您的前端(通过一个简单的短代码)

除了使用 action hook，你还可以使用一个方便的 shortcode 来在任何页面上输出你的文章预览。

您可以使用如下的简单形式

```
//displays your upcoming post preview 
[upcoming_post_preview] 
```

shortcode 基本上是我们的主输出函数的包装器，所以它支持与从我们的`display_upcoming_post_previews`动作调用它时相同的属性。您可以传入以下选项:

*   `number_of_previews`–确定要显示的预览数量(默认为 1)
*   `post_id`–通过帖子 id 获取单个预览

如果你想只显示一个即将到来的文章预览，并且知道它的 ID，你可以使用下面的方法在你的页面上输出它。

```
//displays the upcoming post with the ID of 425
[upcoming_post_preview post_id="425"] 
```

![post preview shortcake example](img/85e7260fe452341f3e13a4d56e8de077.png)

## 从这里去哪里？

现在你已经对插件的工作原理有了一个大概的了解，你可以用它来增加你的站点的浏览者的参与度，你可以扩展这个插件并提供额外的功能。

你想做什么取决于你自己，但这里有一些你可能想了解的事情。

*   创建一个简单的 [`Widget`](https://codex.wordpress.org/WordPress_Widgets) ，可以用来在你的工具条中显示你即将发布的文章预览。
*   扩展“启用”发布预览时可用的选项。也许决定什么确切的选项将被显示，如隐藏作者简介，gravatar 图像，链接到其他职位等
*   扩展这个插件的工作方式，使它也能与`pages`或`custom post types`等一起工作

如果你最终使用了这个插件或者以它为基础来构建你自己的插件，请告诉我！** 

## **分享这篇文章**