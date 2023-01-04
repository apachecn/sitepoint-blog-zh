# Drupal 8 定制插件类型

> 原文：<https://www.sitepoint.com/drupal-8-custom-plugin-types/>

Drupal 8 以插件系统的形式为后端开发工具包添加了一个很棒的东西。全新的、特定于 Drupal 的、从只服务于少数特定目的发展而来的插件已经成为 Drupal 8 中可重用功能的首选系统。

![Drupal 8 logo](img/44344cd73dc67de517bb8f8b99424ca0.png)

在这个由两部分组成的系列文章中，我们将使用这个系统来构建一个特性，该特性允许将定制表单与节点实体一起使用。完成后，我们将能够执行以下操作:

*   将节点束配置为使用多种表单类型中的一种与节点显示一起显示
*   通过从一个合理的基类扩展，轻松定义新的表单类型

因为这个话题在其他地方已经被很好的讨论过了，所以我不会详细讨论插件是如何工作的。但是，在深入研究这个问题的症结之前，请不要客气，先温习一下这个理论。如果你想看看最终的结果，我们在两篇文章中写的代码可以在这个库的[中找到。](https://github.com/upchuk/d8-demo-modules/tree/master/reusable_forms)

我们将从创建自定义插件类型开始。为此，我们将有 2 个接口和 6 个类。这听起来好像很多，但我向你保证，他们是相当样板和快速设置。然后，在本系列的下一期中，我们将看到如何将它用于附加到节点的可重用表单。

## 插件管理器

负责发现和加载插件，任何插件类型中最重要的部分是*管理器*。然而，创建一个非常简单，因为 Drupal 已经为我们提供了一个合理的默认基础来扩展。所以在我们模块的`/src`文件夹中，我们可以将这个类放在一个`ReusableFormManager.php`文件中(我们插件类型的实际名称变成了`ReusableForm`):

```
<?php namespace  Drupal\reusable_forms;  use  Drupal\Core\Plugin\DefaultPluginManager;  use  Drupal\Core\Cache\CacheBackendInterface;  use  Drupal\Core\Extension\ModuleHandlerInterface;  class  ReusableFormsManager  extends  DefaultPluginManager  {  public  function __construct(\Traversable $namespaces,  CacheBackendInterface $cache_backend,  ModuleHandlerInterface $module_handler)  { parent::__construct('Plugin/ReusableForm', $namespaces, $module_handler,  'Drupal\reusable_forms\ReusableFormPluginInterface',  'Drupal\reusable_forms\Annotation\ReusableForm'); $this->alterInfo('reusable_forms_info'); $this->setCacheBackend($cache_backend,  'reusable_forms');  }  }
```

正如我提到的，我们的管理器扩展了`DefaultPluginManager`类，并覆盖了构造函数，用一些关于插件类型的重要信息来调用父类:

*   `Plugin/ReusableForm`–在任何模块中都可以找到该类型插件的子目录
*   我们每个插件需要实现的接口
*   `Drupal\reusable_forms\Annotation\ReusableForm`–定义插件属性(如 ID、名称等)的注释类。)

此外，我们创建了一个 alter hook，它可以由各种模块实现，以改变插件定义，并且我们在缓存后端为我们的插件设置了一个键。关于插件管理器的更多信息，它们做什么以及如何设置，你应该查阅 Drupal.org 上的[文档页面](https://www.drupal.org/node/1637730)。

## 插件接口

接下来，让我们创建管理器期望我们所有插件实现的接口。在位于我们模块的`src/`文件夹中的一个名为`ReusableFormPluginInterface.php`的文件中，我们可以有:

```
<?php namespace  Drupal\reusable_forms;  use  Drupal\Core\Entity\EntityInterface;  use  Drupal\Core\Plugin\ContainerFactoryPluginInterface;  use  Drupal\Component\Plugin\PluginInspectionInterface;  interface  ReusableFormPluginInterface  extends  PluginInspectionInterface,  ContainerFactoryPluginInterface  {  /**
   * Return the name of the reusable form plugin.
   *
   * @return string
   */  public  function getName();  /**
   * Builds the associated form.
   *
   * @param $entity EntityInterface.
   *   The entity this plugin is associated with.
   *
   * @return array().
   *   Render array of form that implements \Drupal\reusable_forms\Form\ReusableFormInterface
   */  public  function buildForm($entity);  }
```

这是一个非常简单的接口，只实施了两种方法:`getName()`和`buildForm()`。第一个将返回插件的名称，而第二个将被传递一个实体对象，并返回一个实现`\Drupal\reusable_forms\Form\ReusableFormInterface`(我们将为实际表单设置的接口)的表单定义的呈现数组。您还会注意到，我们正在扩展另外两个接口。这些为我们提供了一些额外的有用方法，并允许我们从容器中注入依赖项。

## 插件注释

如管理器中所定义的，让我们也在`src/Annotation/ReusableForm.php`中设置我们的注释类:

```
<?php namespace  Drupal\reusable_forms\Annotation;  use  Drupal\Component\Annotation\Plugin;  /**
 * Defines a reusable form plugin annotation object.
 *
 * @Annotation
 */  class  ReusableForm  extends  Plugin  {  /**
   * The plugin ID.
   *
   * @var string
   */  public $id;  /**
   * The name of the form plugin.
   *
   * @var \Drupal\Core\Annotation\Translation
   *
   * @ingroup plugin_translatable
   */  public $name;  /**
   * The form class associated with this plugin
   *
   * It must implement \Drupal\reusable_forms\Form\ReusableFormInterface.
   *
   * @var string
   */  public $form;  }
```

这里我们简单地扩展了默认的`Plugin`注释类，并定义了三个属性(id、name 和 form)。这些将是在我们单独插件的注释中找到的三个键(我们将在本系列的第二部分看到一个例子)。

## Plugin base

到目前为止，我们有了插件类型所需的核心:插件管理器，它可以使用我们定义的注释发现新插件，并用默认工厂实例化它们，即*容器工厂*。

现在让我们通过创建一个所有插件能够/应该/将要扩展的基类来为插件本身打下基础。在模块的`src/`文件夹中，我们可以创建一个名为`ReusableFormPluginBase.php`的新文件，其中包含以下抽象类:

```
<?php namespace  Drupal\reusable_forms;  use  Drupal\Component\Plugin\PluginBase;  use  Drupal\Core\Form\FormBuilder;  use  Symfony\Component\DependencyInjection\ContainerInterface;  abstract  class  ReusableFormPluginBase  extends  PluginBase  implements  ReusableFormPluginInterface  {  /**
   * The form builder.
   *
   * @var \Drupal\Core\Form\FormBuilder.
   */  protected $formBuilder;  /**
   * Constructs a ReusableFormPluginBase object.
   *
   * @param array $configuration
   * @param string $plugin_id
   * @param mixed $plugin_definition
   * @param FormBuilder $form_builder
   */  public  function __construct(array $configuration, $plugin_id, $plugin_definition,  FormBuilder $form_builder)  { parent::__construct($configuration, $plugin_id, $plugin_definition); $this->formBuilder = $form_builder;  }  /**
   * {@inheritdoc}
   */  public  static  function create(ContainerInterface $container, array $configuration, $plugin_id, $plugin_definition)  {  return  new  static( $configuration, $plugin_id, $plugin_definition, $container->get('form_builder')  );  }  /**
   * {@inheritdoc}
   */  public  function getName()  {  return $this->pluginDefinition['name'];  }  /**
   * {@inheritdoc}
   */  public  function buildForm($entity)  {  return $this->formBuilder->getForm($this->pluginDefinition['form'], $entity);  }  }
```

这里有几点需要注意。首先，我们扩展了 Drupal 提供的插件基类，从而获得了一些有用的功能(已经用合理的默认值实现了`PluginInspectionInterface`的方法)。其次，我们正在实现我们之前定义的接口。我们所有的插件都需要实现它，所以不妨在这里处理一下。第三，我们使用依赖注入从容器中加载构建表单所需的`form_builder`服务。这是可能的，因为我们的接口是从`ContainerFactoryPluginInterface`扩展而来的。

要了解更多关于 Drupal 8 中的[服务容器和依赖注入的信息，请查阅我以前关于 Sitepoint.com 的一篇文章。](https://www.sitepoint.com/building-drupal-8-module-configuration-management-service-container/)

正如我们的接口所指示的，我们已经在基类中实现了这两个方法。`getName()`方法将简单地返回插件注释中定义的插件名称。另一方面，`buildForm()`方法将使用表单生成器来构建表单。为此，它将使用插件注释的`form`键中提供的类(它需要是实现我们尚未定义的表单接口的类的完全限定名)。在这样做的时候，我们也将`$entity`参数传递给表单(不管是什么，只要它实现了`EntityInterface`)。这是为了让在节点页面上呈现的表单知道它所呈现的节点。

## 表单界面

我们的插件类型已经基本完成了。我们现在可以为这些插件将要使用的表单提供一些合理的默认值。在`src/Form/ReusableFormInterface.php`里面我们可以有这个简单的界面:

```
<?php namespace  Drupal\reusable_forms\Form;  use  Drupal\Core\Form\FormInterface;  interface  ReusableFormInterface  extends  FormInterface  {}
```

除了从默认的 Drupal `FormInterface`扩展之外，我们在这里什么也不做。我们有它，所以我们可以添加我们需要我们的表单实现的任何方法(目前没有)，并且能够识别实现这个接口的表单与我们的插件兼容。

## 表单基础

现在我们有了一个要实现的表单接口，让我们也创建一个表单基类，其余的表单可以扩展它。在`src/Form/ReusableFormBase.php`里面我们可以有下面的抽象类:

```
<?php namespace  Drupal\reusable_forms\Form;  use  Drupal\Core\Entity\EntityInterface;  use  Drupal\Core\Form\FormBase;  use  Drupal\Core\Form\FormStateInterface;  /**
 * Defines the ReusableFormBase abstract class
 */  abstract  class  ReusableFormBase  extends  FormBase  implements  ReusableFormInterface  {  /**
   * @var EntityInterface.
   */  protected $entity;  /**
   * {@inheritdoc}.
   */  public  function buildForm(array $form,  FormStateInterface $form_state)  { $build_info = $form_state->getBuildInfo();  if  ($build_info['args']  && $build_info['args'][0]  instanceof  EntityInterface)  { $this->entity = $build_info['args'][0];  } $form['first_name']  = array(  '#type'  =>  'textfield',  '#title'  => $this->t('First name'),  ); $form['last_name']  = array(  '#type'  =>  'textfield',  '#title'  => $this->t('Last name'),  ); $form['email']  = array(  '#type'  =>  'email',  '#title'  => $this->t('Email'),  ); $form['actions']['#type']  =  'actions'; $form['actions']['submit']  = array(  '#type'  =>  'submit',  '#value'  => $this->t('Submit'),  '#button_type'  =>  'primary',  );  return $form;  }  }
```

正如你所看到的，我们实现了接口，但是我们也扩展了默认的 Drupal `FormBase`类。在这个例子中，我们只有`buildForm`方法返回一个简单的表单，包含三个字段和一个提交按钮。你可以在这里做任何你认为好的基本形式。此外，在这个方法的开始，我们检查在构建表单时是否有一个`EntityInterface`对象作为参数被传递，并将其设置为一个受保护的属性。只要首先调用`parent::buildForm()`方法，扩展它的类将能够利用这个实体。

## 结论

在本系列文章的第一部分中，我们集中于设置我们的自定义插件类型，并准备好使用它。在这样做的时候，我们快速地浏览了这个过程，并且看到了我们的插件是如何与实际的表单类一起工作的。在下一部分中，我们将努力使它们与节点一起显示成为可能。这意味着向节点类型实体添加额外的配置，并使用作为常规内容视图模式的一部分管理的伪字段来显示表单。敬请期待！

## 分享这篇文章