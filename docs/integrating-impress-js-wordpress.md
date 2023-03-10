# 将 impress.js 集成到 WordPress

> 原文：<https://www.sitepoint.com/integrating-impress-js-wordpress/>

在线演示工具比桌面演示工具更受欢迎。Impress.js 是最受欢迎的开源库之一，用于使用 CSS 过渡和转换创建令人惊叹的演示。尽管它是非常受欢迎的库，但没有多少工具可以用来生成包含动态内容的演示文稿。所以在这里，我们将 impress.js 集成到 WordPress 中，以自动创建演示文稿。在这两部分教程中，我们将开发高度可定制的插件，同时构建 impress.js 演示。

让我们开始吧。

## 牛逼 Impress.js 演示

在我们深入研究插件实现之前，您可能想看看用这个强大的库创建的一些令人惊叹的演示。让我们看看 impress.js 的一些创造性用法。

[长城迪斯](http://zovi.com/wall-t)
[用 Impress.js](http://www.innovativephp.com/demo/impress-js-demos/chapter5/#/about)
[jgog.in 网站/作品集](http://www.jgog.in)
[Pongoweb.it 来自 PongoCMS 的创作者](http://www.pongoweb.it/#/start)

这些演示和网站可能会启发您更多地了解这个神奇的工具。因此，在深入研究与 WordPress 的集成之前，我想推荐一些关于 impress.js 基础的教程。

[Impress.js source](https://github.com/bartaz/impress.js/blob/master/index.html)
[使用 Impress.js 创建令人惊叹的可视化效果](http://www.1stwebdesigner.com/css/creating-stunning-visualizations-with-impress-js/)
[使用 Impress.js 构建令人印象深刻的演示文稿](http://www.packtpub.com/building-impressive-presentations-with-impressjs/book)

完成了获取基础知识的文章后，我们现在可以进入与 WordPress 的实际集成了。

## 规划插件

首先，在进入插件开发之前，我们需要确定表示创建的主要需求。让我们根据一般演示文稿的特点列出任务。

*   我们应该能够创建具有特定设计的幻灯片。
*   每张幻灯片需要有过渡效果。
*   幻灯片需要组合在一起以创建演示文稿。
*   演示文稿应该有可定制的设置。

在这一部分，我们将实现列表中提到的前三项任务。演示定制设置和插件使用技术将在第二部分也是最后一部分讨论。所以让我们开始吧。

## 创建插件

我们可以通过在 wp-content/plugins 目录下创建一个名为 wimpress 的插件文件夹来开始实现。所有插件都有一个主文件，它使用代码注释定义了插件的信息。让我们将主插件文件创建为 wimpress.php，以包含以下代码。

```
<?php

/*
  Plugin Name: WImpress
  Plugin URI:
  Description: WordPress integration of impress.js for online presentations.
  Author: Rakhitha Nimesh
  Version: 1.0
  Author URI: http://www.innovativephp.com/
 */

class WImpress {

}
?>
```

这个插件将使用面向对象的方法，因此我们在主文件中有一个主插件类。出于演示的目的，所有插件相关功能的实现都驻留在这个类中。

## 正在初始化插件

尽管 WordPress 在内核中使用了程序风格的功能，但是大多数开发者更喜欢使用 OOP 风格来创建插件。在这项技术中，我们有一个提供大部分功能的主类。考虑下面这些插件的初始实现代码。

```
class WImpress {
public function __construct() {

	}
}

$wimpress = new WImpress();
```

定义之后，我们初始化主类的一个对象。WordPress 使用动作和过滤器来提供它的大部分核心功能。因此，我们使用 WImpress 类构造函数来定义 WordPress 的必要操作。在大多数情况下，这些动作和过滤器的实现函数将在同一个类中定义。让我们通过为 WImpress 插件创建第一个动作来确定动作定义。

## 定义演示文稿和幻灯片

演示文稿和包含在这些演示文稿中的幻灯片是这个插件的两个最重要的组件。WordPress 提供自定义的文章类型，用于向网站添加各种内容类型。这里，我们将使用两种自定义帖子类型来实现演示和幻灯片。让我们看看自定义帖子是如何在插件中定义的。

我们可以通过更新类的构造函数来开始这个过程，以包含自定义 post 类型定义的必要操作。

```
class WImpress {
private $presentation_type;
	private $step_type;

	public function __construct() {
    		$this->presentation_type = "wpresentations";
    		$this->step_type = "wsteps";

    		add_action('init', array($this, 'wimpr_generate_post_types'));

    	}
}
```

我们使用了两个实例变量来定义定制的帖子名称。这应该比硬编码自定义帖子名称更可取，以保持在稍后阶段无需太多努力就可以更改名称的可能性。自定义 post 类型的名称通过构造函数分配给实例变量。

在 impress.js 中，单个幻灯片被命名为一个步骤，因此我们将从这里开始使用步骤来引用幻灯片。

这里，我们使用`wpresentations`作为演示文章类型，使用`wsteps`作为步骤文章类型。最后，我们在 init action 上添加了一个名为`wimpr_generate_post_types`的函数来定义定制的 post 类型，如下面的代码所示。

```
public function wimpr_generate_post_types() {

    	// Register custom post type for creating impress presentations
    	$labels = array(
        	'name' => _x('Presentations', $this->presentation_type),
        	'singular_name' => _x('Presentation', $this->presentation_type),
        	'add_new' => _x('Add New', $this->presentation_type),
        	'add_new_item' => __('Add New Presentation'),
        	'edit_item' => __('Edit Presentation'),
        	'new_item' => __('New Presentation'),
        	'all_items' => __('All Presentations'),
        	'view_item' => __('View Presentation'),
        	'search_items' => __('Search Presentations'),
        	'not_found' => __('No Presentations found'),
        	'not_found_in_trash' => __('No Presentation found in the Trash'),
        	'parent_item_colon' => '',
        	'menu_name' => 'Impress Presentations'
    	);

    	$args = array(
        	'labels' => $labels,
        	'hierarchical' => true,
        	'description' => 'Presentation',
        	'supports' => array('title', 'editor'),
        	'public' => true,
        	'show_ui' => true,
        	'show_in_menu' => true,
        	'show_in_nav_menus' => true,
        	'publicly_queryable' => true,
        	'exclude_from_search' => false,
        	'has_archive' => true,
        	'query_var' => true,
        	'can_export' => true,
        	'rewrite' => true,
        	'capability_type' => 'post',
    	);

    	register_post_type($this->presentation_type, $args);

    	// Register custom post type for adding steps to impress presentations
    	$labels = array(
        	'name' => _x('Steps', $this->step_type),
        	'singular_name' => _x('Step', $this->step_type),
        	'add_new' => _x('Add New', $this->step_type),
        	'add_new_item' => __('Add New Step'),
        	'edit_item' => __('Edit Step'),
        	'new_item' => __('New Step'),
        	'all_items' => __('All Steps'),
        	'view_item' => __('View Step'),
        	'search_items' => __('Search Steps'),
        	'not_found' => __('No Steps found'),
        	'not_found_in_trash' => __('No Step found in the Trash'),
        	'parent_item_colon' => '',
        	'menu_name' => 'Impress Steps'
    	);

    	$args = array(
        	'labels' => $labels,
        	'hierarchical' => true,
        	'description' => 'Steps',
        	'supports' => array('title', 'editor'),
        	'public' => true,
        	'show_ui' => true,
        	'show_in_menu' => true,
        	'show_in_nav_menus' => true,
        	'publicly_queryable' => true,
        	'exclude_from_search' => false,
        	'has_archive' => true,
        	'query_var' => true,
        	'can_export' => true,
        	'rewrite' => true,
        	'capability_type' => 'post',
    	);

    	register_post_type($this->step_type, $args);
}
```

这里，我们使用带有必要参数的`register_post_type`函数为演示和步骤定义了两种定制的 post 类型。这是 WordPress 中实现得很差的功能之一，我们不得不定义大量不必要的代码来定义一个自定义的文章类型。

注意在参数中使用实例变量来定义定制的文章名称。万一我们决定改名字，我们只有一个地方可以改。前面的大部分代码都包含定义定制文章类型所需的默认参数和标签。

现在，你应该在左边的菜单中有两个自定义的文章类型菜单，如下图所示。

![steps screen](img/5f8af828580b13cd1331ad5eeabf5c51.png)

## 创建演示步骤

演示文稿包含多个步骤，每个步骤都可以包含任何类型的 HTML 内容。在上一节中，我们为`wsteps`帖子类型的`supports`参数分配了标题和编辑器。标题字段将用于唯一标识帖子列表中的步骤，而编辑器字段将用于插入步骤的实际内容和设计。

impress.js 的强大之处在于它的 CSS 过渡和转换。因此，我们需要有每个步骤的过渡设置部分作为元框内的自定义字段。让我们看看如何为步骤定义定制字段。首先，我们必须更新类构造函数，以包含在 post 屏幕上创建自定义元框的`add_meta_boxes`动作。

```
add_action('add_meta_boxes', array($this, 'wimpr_generate_step_options'));
```

现在让我们继续讨论`wimpr_generate_step_options`函数的实现。

```
public function wimpr_generate_step_options() {
    	add_meta_box("wimpr-step-options", "Impress Step Options", array($this, 'wimpr_step_options_box'), $this->step_type, "normal");
}
```

使用`add_meta_box`函数定义元框。该函数的参数包括唯一键、显示名称、显示函数、文章类型和上下文。这里我们也使用了`step_type`变量来代替硬编码值。

上下文可以定义为三个值之一:正常、侧面或高级。值为 normal 将元框放在编辑器字段下，而 side 将元框放在右侧，包含类别、标签等。

定义了元框之后，我们可以开始实现`wimpr_step_options_box`函数中的自定义字段。因为这个函数包含很长的代码，所以我将在三个不同的部分提供解释。在阅读解释的同时使用源代码是最理想的。

```
public function wimpr_step_options_box() {
    	global $post;

    	$step_data = json_decode(get_post_meta($post->ID, "_step_data", true));
    	$presentation_id = get_post_meta($post->ID, "_step_presentation", true);

    	$query = new WP_Query(array('post_type' => 'wpresentations', 'post_status' => 'publish'));

	// Section 2 code
	// Section 3 code

}
```

首先，我们必须从数据库中获取现有的步骤数据。由于我们正在创建过程中，默认情况下我们不会有任何数据。一旦保存并查看了该步骤，这些变量将被现有数据填充。

我们使用`post_meta`表来保存关于步骤的必要细节。每个步骤都应该在演示文稿中。所以我们使用一个名为`_step_presentation`的键来保存该步骤的表示 ID。所有的步进效果都作为 json 编码的字符串存储在一个名为`_step_data`的键中。

这两个键都带有下划线前缀。这允许我们从自定义字段部分隐藏这些字段。如果没有下划线，这些字段将同时出现在 meta 框和 custom fields 部分，这使得管理起来很困难。

最后，我们将数据库中所有现有的演示文稿显示在下拉字段中。然后，我们必须在下拉框中显示检索到的呈现列表以供选择，如前面函数的第 2 部分所示。

```
public function wimpr_step_options_box() {
    	// Section 1

	$html .= "
      	<tr>
        	<th style=''><label for='Upload Images'>Select Presentation : </label></th>
        	<td><select class='widefat' name='wimpr_presentation' id='wimpr_presentation' >
                <option value='0' >Select</option>";
    	        foreach ($query->posts as $presentation) {

        	if ($presentation_id == $presentation->ID) {
            	$select = "selected";
        	}
        	$html .= "<option $select value='$presentation->ID' >$presentation->post_title</option>";
    	        }

    	$html .= "</select>
        	</td>
      	</tr>";

    	$html .= "<tr>
        		<th style=''><label >CSS Classes : </label></th>
        		<td><input name='wimpr_step_class' id='wimpr_step_class' class='widefat impress_text' type='text' value='" . $step_data->wimpr_step_class . "'  />
        		</td>
      		</tr>";

	// Section 3 code

}
```

前面的代码在检查为编辑屏幕分配的演示时将演示填充到下拉字段中。我们使用`$query->posts`数组来获取演示文稿，这些演示文稿作为自定义的 post 类型存储在`wp_posts`表中。接下来，我们可以进入代码的最后一部分。

我假设您使用本文开头提供的资源花时间阅读了 impress.js 的基础知识。如果没有，我建议您浏览一下代码，等到本文的第二部分，我将在构建演示文稿时解释基本概念。

Impress.js 主要建立在三个方向 x、y、z 上的三种效果上，分别称为过渡、缩放和旋转。因此，我们需要 3*3 = 9 个选项来配置每一步的效果。让我们来看看前面的函数的第三部分，为 impress 效果创建字段。

```
public function wimpr_step_options_box() {
    	// Section 1

	// Section 2

	$html .= "<tr>
        	<th style=''><label for='Upload Images'>Transition Settings : </label></th>
        	<td>
        	</td>
      	</tr>
      	<tr>
        	<td colspan='2'>
            	x:<input name='transition_x' id='transition_x' class='impress_text' type='text' value='" . $step_data->transition_x . "'  />
            	y:<input name='transition_y' id='transition_y' class='impress_text' type='text' value='" . $step_data->transition_y . "'  />
            	z:<input name='transition_z' id='transition_z' class='impress_text' type='text' value='" . $step_data->transition_z . "'  />
        	</td>
      	</tr>
      	<tr>
        	<th style=''><label for='Upload Images'>Rotation Settings : </label></th>       	 
      	</tr>
      	<tr>
         	<td colspan='2'>
            	x:<input name='rotation_x' id='rotation_x' class='impress_text' type='text' value='" . $step_data->rotation_x . "'  />
            	y:<input name='rotation_y' id='rotation_y' class='impress_text' type='text' value='" . $step_data->rotation_y . "'  />
            	z:<input name='rotation_z' id='rotation_z' class='impress_text' type='text' value='" . $step_data->rotation_z . "'  />
        	</td>
      	</tr>
      	<tr>
        	<th style=''><label>Scaling Settings : </label></th>       	 
        </tr>
        <tr>
        	<td colspan='2'>
            	<input name='scale' id='scale' class='impress_text' type='text' value='" . $step_data->scale . "'  />
                </td>
        </tr>

    	</table>";

    	echo $html;

}
```

这里，我们有 9 个文本字段来配置 x、y、z 方向的旋转、过渡和缩放效果。在第 1 节中，我们使用一个名为`_step_data`的键从数据库中检索值。这个 postmeta 表键用于将所有 9 个效果值存储为一个 json 编码的字符串。因此，我们使用从 json 字符串解码的`$step_data`数组将现有值分配给文本字段。接下来，我们需要在用户发布演示步骤时保存这些值。

## 保存步骤数据

点击发布按钮后，编辑器的步骤标题和内容将自动保存到数据库中。但是 meta box 值需要使用 action 钩子手动保存。所以我们必须使用 WordPress `save_post`动作来调用一个名为`wimpr_save_step_options`的自定义函数。此操作在每次 post 插入或更新时执行。让我们使用下面的代码遍历一下`wimpr_save_step_options`函数。

```
public function wimpr_save_step_options() {
    	global $post;

    	if (!wp_verify_nonce($_POST['wipmr_box_nonce'], "wipmr-step-meta")) {
        		return $post->ID;
    	}

    	if (defined('DOING_AUTOSAVE') && DOING_AUTOSAVE) {
        		return $post->ID;
    	}

    	if ($this->step_type == $_POST['post_type'] && current_user_can('edit_post', $post->ID)) {

        	$step_data = array();
        	// Retrive the transition data through form submission
        	$step_data['transition_x'] = (isset($_POST['transition_x']) ? $_POST['transition_x'] : '');
        	$step_data['transition_y'] = (isset($_POST['transition_y']) ? $_POST['transition_y'] : '');
        	$step_data['transition_z'] = (isset($_POST['transition_z']) ? $_POST['transition_z'] : '');

// Retrieve the rotation data through form submission
        	$step_data['rotation_x'] = (isset($_POST['rotation_x']) ? $_POST['rotation_x'] : '');
        	$step_data['rotation_y'] = (isset($_POST['rotation_y']) ? $_POST['rotation_y'] : '');
        	$step_data['rotation_z'] = (isset($_POST['rotation_z']) ? $_POST['rotation_z'] : '');

// Retrieve the scale data through form submission
                $step_data['scale'] = (isset($_POST['scale']) ? $_POST['scale'] : '');

        	$presentation = (isset($_POST['wimpr_presentation']) ? $_POST['wimpr_presentation'] : '');
        	update_post_meta($post->ID, "_step_presentation", $presentation);

        	// Update the custom field values upon successfull validation
        	update_post_meta($post->ID, "_step_data", json_encode($step_data));
    	} else {
        		return $post->ID;
    	}
}
```

首先，我们必须在将数据保存到数据库之前验证 nonce。Nonce 是表示使用过一次的数字的缩写。我们必须在每个提交表单上生成一个新的随机数，作为来自第三方表单提交的安全预防措施。在前面的函数中，我们使用`wp_create_nonce`函数创建了 nonce 值，现在我们根据从`$_POST`数组提交的值检查它。如果验证失败，我们通过返回文章 ID 来中断这个过程。

一旦 none 检查成功，我们再次检查它是否是自动保存，以中断流程并返回帖子 ID。最后，在保存数据之前，我们检查必要的文章类型和用户权限。

省略发布类型检查将对每个发布类型执行保存发布操作，从而可能产生不一致的数据。

所有检查完成后，我们从`$_POST`数组中获得所有九个自定义字段值，以配置 impress.js 步骤的效果。最后，我们使用 update_meta 函数将效果值保存为 JSON 编码的字符串。现在，我们已经完成了创建必要的演示文稿、步骤和演示文稿内容的过程。接下来，我们需要在 WordPress 应用程序的前端显示演示文稿。

## 创建演示模板

我们在本文开头看到了一些令人惊叹的 impress.js 演示文稿。大多数 impress 演示文稿，包括我们展示的那些，都占据了浏览器窗口的全屏尺寸。因此，我们将设计一个全屏模板来显示由 WImpress 插件生成的演示文稿。大多数现存的 WordPress 主题模板包含了普通的页眉和页脚。这里，我们需要全屏显示，因此我们必须使用一个没有共同的页眉，页脚和任何其他共同的组件模板。

所以我们将通过截取默认的 WordPress 路由过程来加载一个独特的定制模板。WordPress 提供了一个名为`template_redirect`的动作来加载定制模板，而不是默认模板。让我们将下面的代码添加到插件的构造函数中。

```
add_action("template_redirect", array($this, 'wimpr_template_redirect'));
```

现在我们可以看看加载定制模板的`wimpr_template_redirect`函数的实现。

```
public function wimpr_template_redirect() {
    	global $wp;

    	if ($wp->query_vars["post_type"] == "wpresentations") {
        		if (is_single ()) {
            		include(plugin_dir_path(__FILE__) . '/single-wpresentations.php');
            		exit;
        		}
    	}
}
```

首先，我们检查演示的 post 类型，以及我们是否在单一演示显示屏上。然后，我们使用 PHP include 语句加载名为 single-wpresentations.php 的定制模板。现在我们必须用 HTML 代码在插件文件夹中创建 single-wpresentations.php 文件来显示演示文稿。

```
<?php
global $post;

$presentation_id = $post->ID;
$query = new WP_Query(array('post_type' => 'wsteps',
        	'post_status' => 'publish',
        	'meta_query' => array(
            	array(
                	'key' => '_step_presentation',
                	'value' => $presentation_id
            	)
        	)
    	));
?>
```

我们通过在模板文件的开头使用全局`$post`对象来获得演示的 ID。然后我们查询数据库来检索当前演示的步骤。然后我们遍历结果集中的每个步骤，生成 impress.js 步骤，如下面的代码所示。

```
<script src="https://code.jquery.com/jquery-1.10.1.min.js"></script>
<div id="impress">

	<?php
	foreach ($query->posts as $step) {
    	$step_data = json_decode(get_post_meta($step->ID, "_step_data", true));

    	$transition_x = (isset($step_data->transition_x) && !empty($step_data->transition_x)) ? "data-x='" . $step_data->transition_x . "'" : '';
    	$transition_y = (isset($step_data->transition_y) && !empty($step_data->transition_y)) ? "data-y='" . $step_data->transition_y . "'" : '';
    	$transition_z = (isset($step_data->transition_z) && !empty($step_data->transition_z)) ? "data-z='" . $step_data->transition_z . "'" : '';

    	$rotation_x = (isset($step_data->rotation_x) && !empty($step_data->rotation_x)) ? "data-rotate='" . $step_data->rotation_x . "'" : '';
    	$rotation_y = (isset($step_data->rotation_y) && !empty($step_data->rotation_y)) ? "data-rotate='" . $step_data->rotation_y . "'" : '';
    	$rotation_z = (isset($step_data->rotation_z) && !empty($step_data->rotation_z)) ? "data-rotate='" . $step_data->rotation_z . "'" : '';

        $scale = (isset($step_data->scale) && !empty($step_data->scale)) ? "data-scale='" . $step_data->scale . "'" : '';

        $css_class = (isset($step_data->wimpr_step_class) && !empty($step_data->wimpr_step_class)) ? $step_data->wimpr_step_class : '';

        echo '<div id="' . $step->ID . '" class="step slide ' . $css_class . '" ' . $transition_x . ' ' .        
           $transition_y . ' ' . $transition_z . '
           ' . $scale . '        ' . $rotation_x . ' ' . $rotation_y . ' ' . $rotation_z . ' >
           ' . $step->post_content . '
        </div>';

	}
	?>

</div>
```

我们在循环结果集时使用`get_post_meta`函数获得步骤效果。最后，我们用配置好的效果和值生成 DIV 元素，用作 impress.js 步骤。具有空值的效果不会被分配给 DIV，因此将使用 impress.js 的默认值。最后，我们使用以下代码加载必要的 CSS 并初始化位于 WImpress 文件夹中的 impress.js 库。

```
<link rel="stylesheet" href="<?php echo plugins_url('css/impress-demo.css', __FILE__); ?>" />
<style>

	body{
    		background : #FFF;
    		color:#000;
    		font-size:35px;
}
</style>
<script type="text/javascript" src="<?php echo plugins_url('js/impress.js', __FILE__); ?>" ></script>
<script type="text/javascript">impress().init();</script>
```

现在我们已经完成了将 impress.js 演示文稿集成到 WordPress 的过程。您可以使用管理仪表板来创建动态演示。让我们来看看使用这个插件创建演示文稿的过程。

**步骤 1**–上传并激活插件，查看左侧菜单中名为 Impress 步骤和 Impress 演示的选项卡。

**第 2 步**–使用 Impress 演示菜单创建演示。

**步骤 3**–通过改变效果值创建多个步骤。

**步骤 4**–使用下拉框将每个步骤分配到演示中。

**第 5 步**–发布演示文稿，点击查看演示文稿按钮加载演示文稿。

您可以使用以下 URL 访问用该插件创建的演示文稿。

[http://www.innovativephp.com/demo/wimpress-demo/](http://www.innovativephp.com/demo/wimpress-demo/)

## 包裹

在本教程中，我们将 impress.js 集成到 WordPress 中，以使用自定义帖子类型功能管理动态演示。贯穿本教程创建的插件足以处理基本的演示。演示应该有引人注目的设计和内容来吸引观众。

所以在下一部分，我们将通过添加设置面板来配置背景图片和 CSS 样式来增强 WImpress 插件的功能。我们还将学习 impress.js 效果的基础知识，同时使用这个插件创建一个演示文稿。

希望你喜欢这个教程，期待你的评论和建议。请继续关注本教程的第二部分，也是最后一部分，以增强 impress.js 演示文稿的功能。

## 分享这篇文章