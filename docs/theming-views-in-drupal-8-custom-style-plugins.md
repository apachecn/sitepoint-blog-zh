# Drupal 8 中的主题视图——定制风格插件

> 原文：<https://www.sitepoint.com/theming-views-in-drupal-8-custom-style-plugins/>

![Drupal 8 logo](img/86e2dfe837ba6b0ca72e19e81899e0a5.png)

[浏览量](https://www.drupal.org/node/1912118)在 Drupal 8 核心。我们现在都知道了。 [Twig](https://www.drupal.org/theme-guide/8/twig) 是 Drupal 8 中新的模板引擎。这个我们也知道。但是我们知道如何以编程方式与第一个交互，以便使用第二个主题化视图吗？除了像任何其他子系统一样覆盖视图模板，我们还提供了一个更强大的视图插件(显示、样式、行和[字段](http://www.webomelette.com/creating-custom-views-field-drupal-8))。

在本文中，我们将看看如何在 Drupal 8 中为视图创建一个定制风格的插件。我们将使用[引导标签标记](http://getbootstrap.com/javascript/#tabs)作为目标，并为我们的视图结果实现一个标签输出。在视图配置中，样式设置将允许我们指定哪个字段将用作选项卡导航副本，其余字段显示在各自的选项卡窗格中。基本上，每个视图结果都代表一个选项卡，所以这个例子不适合有多个结果的视图。主要目标是说明如何在 Drupal 8 中创建我们自己的视图风格插件。

我们不会详细介绍如何在项目中使用 Bootstrap。然而，你可以查看关于资产的[文档页面](https://www.drupal.org/theme-guide/8/assets)甚至[这篇文章](http://www.webomelette.com/drupal-8-core-javascript-files-anonymous-users)如何确保匿名用户可以从 jQuery 加载到页面中获益。如果你想提前看到我们写的代码，你可以在演示模块的[资源库](https://github.com/upchuk/d8-demo-modules/tree/master/demo)中找到。

## 什么是风格插件？

视图样式插件是负责呈现清单的插件。核心风格插件的显著例子是*无格式列表*、 *HTML 列表*、*表格*或*网格*。它们由显示插件使用，它们反过来使用行插件来表示列表中的一个项目。

在 Drupal 8 中，所有视图插件类型都是使用[新插件系统](https://www.sitepoint.com/drupal-8-custom-plugin-types/)构建的，并且共享一些公共功能(它们总是从相同的视图`PluginBase`扩展而来)。

现在让我们创建我们自己的这样的样式插件，它可以被大多数显示类型(页面、块等)使用，并且使用字段行插件。

## 引导标签样式插件

第一步是在模块的`Plugin/views/style`文件夹中创建插件类:

```
namespace Drupal\demo\Plugin\views\style;
use Drupal\Core\Form\FormStateInterface;
use Drupal\views\Plugin\views\style\StylePluginBase;

/**
 * A Views style that renders markup for Bootstrap tabs.
 *
 * @ingroup views_style_plugins
 *
 * @ViewsStyle(
 *   id = "bootstrap_tabs",
 *   title = @Translation("Bootstrap Tabs"),
 *   help = @Translation("Uses the Bootstrap Tabs component."),
 *   theme = "demo_bootstrap_tabs",
 *   display_types = {"normal"}
 * )
 */
class BootstrapTabs extends StylePluginBase {

  /**
   * Does this Style plugin allow Row plugins?
   *
   * @var bool
   */
  protected $usesRowPlugin = TRUE;

  /**
   * Does the Style plugin support grouping of rows?
   *
   * @var bool
   */
  protected $usesGrouping = FALSE;

  /**
   * {@inheritdoc}
   */
  protected function defineOptions() {
    $options = parent::defineOptions();
    $options['tab_nav_field'] = array('default' => '');
    return $options;
  }

  /**
   * {@inheritdoc}
   */
  public function buildOptionsForm(&$form, FormStateInterface $form_state) {
    parent::buildOptionsForm($form, $form_state);
    $options = $this->displayHandler->getFieldLabels(TRUE);
    $form['tab_nav_field'] = array(
      '#title' => $this->t('The tab navigation field'),
      '#description' => $this->t('Select the field that will be used as the tab navigation. The rest of the fields will show up in the tab content.'),
      '#type' => 'select',
      '#default_value' => $this->options['tab_nav_field'],
      '#options' => $options,
    );
  }
} 
```

我们正在创建实例的 Drupal 插件类型是`ViewsStyle`，在注释中传递了一些基本配置。抛开那些显而易见的，我们有值得一提的`theme`和`display_types`键。第一个声明这个样式插件将使用哪个主题函数来呈现它的数据，而第二个声明这个样式可以被哪种类型的显示插件使用(在我们的例子中，所有显示类型都没有指定一个定制类型:normal)。有关该插件类型的所有可用注释配置的更多信息，请查看`Drupal\views\Annotation\ViewsStyle`注释类。

使用这两个类属性，我们声明我们的样式使用行插件，但是不允许分组。请务必查看父类，以了解更多有关可以像这样指定的其他选项的信息。例如，我们正在扩展的类已经声明视图字段可以与样式插件一起使用。

如前所述，使用这两种方法，我们创建了一个插件选项和表单元素，能够指定哪个字段应该作为选项卡导航。使用当前显示处理器(`$this->displayHandler`)，我们可以加载站点构建器添加到其中的所有可用视图字段。这个新的表单元素将出现在样式设置表单中:

![Drupal 8 Style Plugins](img/82df0ba7dde9195dd66a35b9be85af0a.png)

因为我们是从`StylePluginBase`类扩展而来，所以我们不再需要做什么了。对于标记输出，我们可以依赖于从执行的视图接收相关变量的`demo_bootstrap_tabs`主题。如果我们愿意，我们可以覆盖任何渲染方法，添加更多的变量，改变主题，或者我们需要的任何东西。我们对默认值很满意，特别是因为我们将实现一个预处理程序来处理模板接收的变量。

## 主题

是时候像平常一样定义`demo_bootstrap_tabs`主题了(在我们的`.module`文件中):

```
/**
 * Implements hook_theme().
 */
function demo_theme($existing, $type, $theme, $path) {
  return array(
    'demo_bootstrap_tabs' => array(
      'variables' => array('view' => NULL, 'rows' => NULL),
      'path' => drupal_get_path('module', 'demo') . '/templates',
    ),
  );
} 
```

默认情况下，样式插件将`$view`对象和结果`$rows`传递给模板。在将变量发送到模板之前，由预处理器对这些变量进行一些处理(如果需要的话):

```
/**
 * Prepares variables for views demo_bootstrap_tabs template.
 *
 * Template: demo-bootstrap-tabs.html.twig.
 *
 * @param array $variables
 *   An associative array containing:
 *   - view: The view object.
 *   - rows: An array of row items. Each row is an array of content.
 */
function template_preprocess_demo_bootstrap_tabs(&$variables) {
  $view = $variables['view'];
  $rows = $variables['rows'];
  $variables['nav'] = array();

  // Prepare the tab navigation.
  $field = $view->style_plugin->options['tab_nav_field'];
  if (!$field || !isset($view->field[$field])) {
    template_preprocess_views_view_unformatted($variables);
    return;
  }

  $nav = array();
  foreach ($rows as $id => $row) {
    $nav[$id] = array(
      '#theme' => 'views_view_field',
      '#view' => $view,
      '#field' => $view->field[$field],
      '#row' => $row['#row'],
    );
  }

  template_preprocess_views_view_unformatted($variables);
  $variables['nav'] = $nav;
} 
```

这里发生了什么？首先，我们检查将要使用的字段名称的样式插件选项(在配置视图时选择的选项)。如果没有，我们返回，但是在做一些默认的预处理之前,`template_preprocess_views_view_unformatted`函数已经做得很好了。所以我们委托给它。然后，我们遍历视图结果，为选项卡导航构建一个内容数组。为此，我们使用默认的视图`views_view_field`主题函数来呈现选中的字段。最后，我们将这个数组传递给模板，并运行无格式列表样式的默认预处理程序。

## 模板

在 Drupal 8 中不再有主题函数，所有的事情都在 Twig 模板中处理。所以让我们看看`demo-bootstrap-tabs.html.twig`文件在我们模块的`templates`文件夹中是什么样子的:

```
<div>
    <!-- Nav tabs -->
    <ul class="nav nav-tabs" role="tablist">
        {% for tab in nav %}
            {% set active = '' %}
            {% if loop.index0 == 0 %}
                {% set active = 'active' %}
            {% endif %}
            <li role="presentation" class="{{ active }}"><a href="#tab-{{ loop.index0 }}" aria-controls="profile" role="tab" data-toggle="tab">{{ tab }}</a></li>
        {% endfor %}
    </ul>

    <!-- Tab panes -->
    <div class="tab-content">
        {% for row in rows %}
            {% set active = '' %}
            {% if loop.index0 == 0 %}
                {% set active = 'active' %}
            {% endif %}
            <div role="tabpanel" class="tab-pane {{ active }}" id="tab-{{ loop.index0 }}">{{ row.content }}</div>
        {% endfor %}
    </div>
</div> 
```

如您所见，这是引导选项卡的必要标记。当然，如果不首先确保相关的引导样式和脚本被加载到你的主题中，这是行不通的。

我们首先呈现的是选项卡导航项(来自我们的`nav`变量)。在遍历这个数组时，我们还利用了循环索引值，以便将第一个项目默认为活动的，并且能够使用惟一的 id 定位下面的选项卡内容窗格。对于项目的实际值，我们只打印我们在预处理器中创建的渲染数组，Drupal 负责渲染。也就是说，确保您在这里使用的字段相对较短，没有链接和普通标记，这可能是一个好主意。标题可能会工作得很好。但是这是一个相应地配置视图的问题。

在导航下方，我们打印实际的视图行，使用相同的循环索引将第一行默认为活动选项卡窗格，并唯一地标识它们，以便上面的导航可以控制它们的可见性。至于内容，我们打印整个`row.content`变量(准备在`template_preprocess_views_view_unformatted`中),它包含我们视图中的所有字段。如果我们希望*而不是*包含我们用于导航的字段，我们可以在视图配置中不显示该字段。它仍然会出现在导航中(因为我们在那里显式地打印了它)，但不会出现在主选项卡窗格中。

## 结论

现在我们有了。一个视图风格插件，以引导标签的形式输出视图结果。我们现在需要做的就是确保引导资产被加载，并简单地配置我们的视图来使用新的样式插件。请记住，这并不意味着有很多结果的视图，它只是作为一个例子来演示如何创建样式插件。

如果您有问题、意见或建议，请在下面留下！

## 分享这篇文章