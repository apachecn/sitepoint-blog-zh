# Drupal 8 第三方设置和伪字段

> 原文：<https://www.sitepoint.com/drupal-8-third-party-settings-and-pseudo-fields/>

在本系列的第一部分中，我们开始了创建一些简单但强大的功能的旅程。我们设定的目标是能够在每个节点页面上加载一个表单，并且能够选择在不同的节点束上应该使用哪种表单类型。

![Drupal 8 logo](img/077a30fb14c973a02eea34dedb6e8ea1.png)

第一步完成了。我们创建了一个名为`ReusableForm`的自定义插件类型，已经有了一个新插件可以扩展的插件基类。此外，我们看到每个插件都将与注释中定义的表单类进行交互。和插件一样，我们也创建了一个新表单可以扩展的基类。

接下来看看我们如何配置核心节点类型以使用站点上定义的插件之一，以及如何在查看节点时呈现相关的表单。但是首先，为了有所作为，让我们创建第一个使用非常简单形式的`ReusableForm`插件。

## 我们的第一个插件

在我们模块的`src/Form`文件夹中，创建一个名为`BasicForm.php`的类(或者您想怎么称呼它都行)。在里面，我们可以有这个简单的表单定义:

```
<?php namespace  Drupal\reusable_forms\Form;  use  Drupal\Core\Form\FormStateInterface;  /**
 * Defines the BasicForm class.
 */  class  BasicForm  extends  ReusableFormBase  {  /**
   * {@inheritdoc}.
   */  public  function getFormId()  {  return  'basic_form';  }  /**
   * {@inheritdoc}.
   */  public  function buildForm(array $form,  FormStateInterface $form_state)  { $form = parent::buildForm($form, $form_state);  return $form;  }  /**
   * {@inheritdoc}
   */  public  function submitForm(array &$form,  FormStateInterface $form_state)  {  // Handle form submission.  }  }
```

这个表单类扩展了我们的表单库，并实现了所有需要的方法。要了解更多关于[表单以及它们如何在 Drupal 8](https://www.sitepoint.com/building-drupal-8-module-blocks-forms/) 中工作的信息，你可以查看我以前写的一篇关于 Sitepoint.com 的文章。但是正如您所看到的，我们正在调用父方法`buildForm()`,这样我们在基类中定义的逻辑就会发生。剩下的是实现细节(这里我们不做太多)。但是您可以执行您想要的任何逻辑，并以您需要的任何方式处理提交。

这个表单现在可以和我们这种类型的插件一起使用，它将有 3 个从表单基类继承的表单元素。

接下来，让我们创建我们的第一个插件。在我们模块的`src/Plugin/ReusableForm`文件夹中，我们可以有一个名为`BasicForm.php`的文件，其内容如下:

```
<?php namespace  Drupal\reusable_forms\Plugin\ReusableForm;  use  Drupal\reusable_forms\ReusableFormPluginBase;  /**
 * Provides a basic form.
 *
 * @ReusableForm(
 *   id = "basic_form",
 *   name = @Translation("Basic Form"),
 *   form = "Drupal\reusable_forms\Form\BasicForm"
 * )
 */  class  BasicForm  extends  ReusableFormPluginBase  {}
```

正如你所看到的，我们所需要的都在注释中:id、名称和表单类，用于这个插件。不过，如果你愿意，你可以添加自己的方法，覆盖现有的方法，处理更复杂的逻辑。但是对我们来说这是可以的，因为`ReusableFormPluginBase`类已经处理了表单构建。

仅此而已。这就是现在定义新的`ReusableForm`插件所要做的一切。我们创建一个表单类，包含它需要的所有字段和逻辑，然后在一个简单的插件类中引用它。

## 节点类型配置

现在我们有了一个*可重用表单*插件，我们可以继续配置节点类型实体来使用它。为此，我们需要首先将插件管理器转换成一个服务，这样我们就可以访问它并加载插件。

在我们模块的`reusable_forms.services.yml`文件中，我们可以有:

```
services: plugin.manager.reusable_forms:  class:  Drupal\reusable_forms\ReusableFormsManager
        parent: default_plugin_manager
```

现在我们将能够使用`plugin.manager.reusable_forms`服务 id 访问插件管理器。通过在定义中使用`parent`键，我们指定可以从父元素中查找它的所有依赖项。太酷了！

接下来，让我们转到我们的`.module`文件，在那里做一些事情。首先，我们想改变节点类型编辑表单，并添加一些关于表单插件的额外信息。这是为了当用户编辑节点包时，他们可以选择哪个表单插件应该与该类型的节点一起使用:

```
/**
 * Implements hook_form_BASE_FORM_ID_alter().
 */  function reusable_forms_form_node_type_form_alter(&$form,  FormStateInterface $form_state)  { $form['reusable_forms']  = array(  '#type'  =>  'details',  '#title'  => t('Reusable forms'),  '#group'  =>  'additional_settings',  );  // Load the current node type configuration entity. $node_type = $form_state->getFormObject()->getEntity(); $form['reusable_forms']['reusable_forms_enabled']  = array(  '#type'  =>  'checkbox',  '#title'  => t('Enable reusable forms'),  '#description'  => t('Check this box if you would like a reusable form on this node type.'),  '#default_value'  => $node_type->getThirdPartySetting('reusable_forms',  'enabled',  0),  ); $form_plugins = \Drupal::service('plugin.manager.reusable_forms')->getDefinitions(); $options =  [];  foreach  ($form_plugins as $name => $plugin)  { $options[$name]  = $plugin['name'];  } $form['reusable_forms']['reusable_forms_enabled']  = array(  '#type'  =>  'radios',  '#title'  => t('Available forms'),  '#default_value'  => $node_type->getThirdPartySetting('reusable_forms',  'plugin',  'basic_form'),  '#options'  => $options,  '#description'  => t('The available forms you can choose from for this node type.'),  '#states'  => array(  'visible'  => array(  ':input[name="reusable_forms_enabled"]'  => array('checked'  => TRUE),  ),  ),  ); $form['#entity_builders'][]  =  'reusable_forms_form_node_type_form_builder';  }
```

实现`hook_form_BASE_FORM_ID_alter`将完美地解决这个问题。尽管我们不能忘记在顶部使用`FormStateInterface`类:

```
use  Drupal\Core\Form\FormStateInterface;
```

那么这里发生了什么？我们正在创建一个新的字段集来分组两个与我们的目的相关的表单字段:一个复选框来启用可重用的表单，一个选择列表来从现有的插件中进行选择。作为后者的选择，我们在加载插件管理器并向它请求所有可用的定义后，构建一个包含所有插件名称的数组。并且使用`#states`魔法，我们确保后一个字段只有在启用表单的复选框被选中时才可见。

就在最后，我们向`#entity_builders`组添加了一个额外的回调函数，它将在保存实体时被触发，目的是将值映射到实体。现在让我们来看看这个函数:

```
/**
 * Entity form builder for the node type form to map some values to third party
 * settings
 */  function reusable_forms_form_node_type_form_builder($entity_type,  NodeTypeInterface $type,  &$form,  FormStateInterface $form_state)  {  if  ($form_state->getValue('reusable_forms_enabled')  ===  1)  { $type->setThirdPartySetting('reusable_forms',  'enabled',  1); $type->setThirdPartySetting('reusable_forms',  'plugin', $form_state->getValue('reusable_forms_enabled'));  return;  } $type->unsetThirdPartySetting('reusable_forms',  'enabled'); $type->unsetThirdPartySetting('reusable_forms',  'plugin');  }
```

同样，让我们利用顶部的`NodeTypeInterface`:

```
use  Drupal\node\NodeTypeInterface;
```

在这个函数中，我们做了一些简单但很棒的事情。如果管理员在这个包中启用了可重用表单，我们将利用配置实体的第三方设置空间来存储我们的数据。否则，如果它存在，我们就简单地取消它的设置。

在讨论我们之前实现的表单变更时，我有意忽略了最后一点。现在，您可以看到如何通过向节点类型配置实体上的第三方设置对象发出请求来填充表单元素默认值。

## 配置模式

在实际显示表单之前，我们还需要为存储的新配置添加[模式定义](https://www.drupal.org/node/1905070)。在我们模块的`config/schema/reusable_forms.schema.yml`文件中，我们可以添加以下内容:

```
node.type.*.third_party.reusable_forms: type: mapping
  label:  'Reusable Forms' mapping: reusable_forms_enabled: type:  boolean label:  'Whether to enable the reusable forms on this node type' reusable_forms_enabled: type: sequence
      label:  'Available forms' sequence: type:  string label:  'Plugin name' 
```

## 节点视图

现在我们将用户首选项存储在节点类型配置实体上，让我们看看如何在启用类型的节点页面上呈现我们选择的表单插件。

我们要做的第一件事是定义一个[内容实体伪字段](http://www.webomelette.com/creating-pseudo-fields-drupal-8)，它可以从节点*管理显示*接口进行配置。仍然在我们的`.module`文件中，我们可以有这个:

```
/**
 * Implements hook_entity_extra_field_info().
 */  function reusable_forms_entity_extra_field_info()  { $extra = array(); $bundles =  NodeType::loadMultiple(); $bundles = array_filter($bundles,  function($bundle){  return $bundle->getThirdPartySetting('reusable_forms',  'enabled')  ===  1;  });  foreach  ($bundles as $bundle)  { $extra['node'][$bundle->Id()]['display']['reusable_form']  = array(  'label'  => t('Reusable form'),  'description'  => t('Reusable form'),  'weight'  =>  100,  'visible'  => TRUE,  );  }  return $extra;  }
```

同样，我们必须在顶部使用`NodeType`:

```
use  Drupal\node\Entity\NodeType;
```

这里发生的事情很简单。我们加载所有的节点包，并过滤掉那些没有启用可重用表单的节点包。然后，我们以一种不言自明的方式为每个组件定义了一个额外的`display`组件。

通过清除缓存，将能够在配置页面上看到新的伪字段——尽管它还不会做任何事情。这就把我们带到了拼图的最后一块，呈现相关的表单。

为了使我们刚刚定义的伪字段有用，我们需要实现`hook_entity_view`(或者它的变体)并呈现它的内容:

```
/**
 * Implements hook_ENTITY_TYPE_view().
 */  function reusable_forms_node_view(array &$build,  EntityInterface $entity,  EntityViewDisplayInterface $display, $view_mode, $langcode)  {  if  ($display->getComponent('reusable_form'))  { $plugin_manager = \Drupal::service('plugin.manager.reusable_forms'); $node_type =  NodeType::load($entity->bundle()); $plugin = $node_type->getThirdPartySetting('reusable_forms',  'plugin');  if  (!$plugin)  {  return;  } $build['reusable_form']  = $plugin_manager->createInstance($plugin)->buildForm($entity);  }  }
```

我们不要忘记顶部的使用声明:

```
use  Drupal\Core\Entity\Display\EntityViewDisplayInterface;  use  Drupal\Core\Entity\EntityInterface;
```

首先，我们检查`reusable_form`组件是否存在于这个节点的显示中(它是否在 UI 中可见)。如果是，我们将`reusable_form`键添加到构建这个节点视图的渲染数组中。

我们首先加载插件管理器和当前节点实体的节点类型配置对象。然后我们使用前者实例化一个插件，其 ID 在后者的第三方设置中定义。正如您所记得的，在这样做的时候，我们将当前的实体对象作为参数进行传递，以便正在构建的表单可以知道它所显示的实体(尽管在我们的示例中我们并没有真正利用这一点)。

仅此而已。剩下的就是从头开始安装模块(因为我们添加了配置模式)，编辑一个节点类型并选择我们的默认插件(或者另一个，如果你创建了它)。然后，您可以配置该节点类型的显示，以显示可重用的表单，当查看该类型的节点时，该表单会这样做。干净利落。

## 结论

正如我们在本系列文章中看到的，Drupal 8 为我们提供了一些工具来完成一些功能强大的工作。通过插件系统，我们为可重用的功能建立了基础。然后我们连接到 Drupal 核心的各个点来利用这个逻辑。我们已经看到了实现`ThirdPartySettingInterface`的任何配置实体是如何被扩展以包含新数据的。最后，我们已经在伪字段的帮助下显示了查看内容实体时的相关数据。

但这仅仅是开始。您可以采用我们在这里所做的，并根据您的需求进行扩展。例如，您可以创建一个新的内容实体类型来模拟表单提交。可能性是无限的。

## 分享这篇文章