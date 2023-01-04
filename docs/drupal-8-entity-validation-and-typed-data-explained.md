# Drupal 8 实体验证和类型化数据解释

> 原文：<https://www.sitepoint.com/drupal-8-entity-validation-and-typed-data-explained/>

数据验证是任何应用程序中非常重要的一部分。Drupal 7 有一个很棒的表单 API，可以处理提交数据的复杂验证，然后可以将数据转换成实体。然而，表单级验证是有问题的。例如，以编程方式处理实体(数据)验证变得很困难。我们必须要么重新实现验证逻辑，要么用代码模仿表单提交。这使得任何数据交互都依赖于表单系统，这是一个坏主意。

![Drupal 8 logo](img/231563ef9e7011aefaa17d08645cf05e.png)

随着 REST API 等子系统的引入，Drupal 8 需要更好的东西来处理这个问题。选择了 [Symfony 验证组件](http://symfony.com/doc/current/book/validation.html)，Drupal 8 构建在它之上，为[类型数据](https://www.drupal.org/node/1794140)和基于插件的实体系统量身定制。所以现在，表单提交可以验证实体，就像 REST 调用一样，或者任何其他与实体的编程交互都可以。

在本文及其后续文章中，我们将探索 Drupal 8 实体验证 API，了解它是如何工作的，以及如何对它进行扩展。为了更好地理解这一点，我们还将看看支撑 Drupal 8 中实体系统的类型化数据 API。将会有一些已经存在于核心中的代码示例，但是我们也会写一些我们自己的代码，这些代码可以在`demo`模块中的 [git 库](https://github.com/upchuk/d8-demo-modules/tree/master/demo)中找到。

## 类型化数据

[Typed Data](https://api.drupal.org/api/drupal/core%21core.api.php/group/typed_data/8) 是 Drupal 8 API，旨在提供一种与数据或关于数据本身的元数据进行交互的一致方式。为什么这对我们的主题很重要？因为验证是在类型化数据对象上定义和调用的。

这个 API 的两个重要组件非常突出:数据定义和`DataType`插件。前者的作用是定义数据以及交互如何处理数据(包括设置或验证约束)。后者的作用是提供一种从该类型的数据中获取和设置值的方法。当它们被实例化时，数据类型插件利用由插件管理器传递的数据定义实例。后者还可以推断出一个`DataType`插件类型需要使用哪个数据定义。

让我们看一个例子:

```
$definition = DataDefinition::create('string')
    ->addConstraint('Length', array('max' => 20)); 
```

我们创建了一个`string`数据定义，并对其应用了`Length`约束。约束是验证 API 的关键部分，它们定义了将在数据上运行的验证类型。它们与实际执行任务的验证器相结合，但是我们将在本系列的第二部分看到更多关于约束和验证器的内容。

接下来，我们使用`DataType`插件管理器来创建实际的数据类型插件实例:

```
$string_typed_data = \Drupal::typedDataManager()->create($definition, 'my string'); 
```

为了简洁起见，我们在这里静态地加载了管理器服务，但是如果您在类上下文中，您应该在项目中使用依赖注入。`TypedDataManager`上的`create()`方法将数据定义作为第一个参数，将实际值作为第二个参数，并返回一个匹配定义类型的`DataType`插件实例:在我们的例子中是`StringData`。对于复杂的数据，`DataType`插件可以在注释中指定需要使用哪个数据定义类。关于 Drupal 8 中[插件的更多信息，请务必查看我以前的相关文章。](https://www.sitepoint.com/drupal-8-custom-plugin-types/)

这个插件实例中的一个方法是`validate()`，它触发针对所有应用于定义的约束的数据验证，并返回 Symfony 的 [ConstraintViolationList](http://api.symfony.com/2.3/Symfony/Component/Validator/ConstraintViolationList.html) 的一个实例。通过迭代或者使用像`count()`或`get()`这样的方法，我们可以检查数据是否有效，如果无效，哪些约束失败了。

在我们的示例中，违规列表应该没有验证错误，因为我们使用的字符串少于 20 个字符。如果我们在创建插件时使用了一个更长的字符串，我们就会有一个由 Symfony `ConstraintViolationInterface`实例表示的违例，该实例带有一条消息、违规值甚至属性路径。

## 类型化数据和内容实体

现在我们对类型化数据 API 有了一些了解，让我们看看这是如何应用于内容实体的。

Drupal 7 中的实体数据分为实体属性(通常是实体表上的列)和通过 UI 配置的字段 API 字段。在 Drupal 8 中，它们被放在同一个伞下，所以旧属性也变成了字段。但是，仍然存在一个差异，一些字段(主要是旧的实体属性)被定义为基本字段，而其余的是可配置字段。

这些字段的数据定义是`BaseFieldDefinition`和`FieldConfig`，但它们都是同一个`FieldDefinitionInterface`的实现者(它是`DataDefinitionInterface`的复杂扩展——我们前面看到的由`DataDefinition`直接实现的接口)。

每个单独的字段在一个特殊的`FieldItemListInterface`实现中保存数据，并且总是一个单独的`FieldItem`插件列表(即使字段中只有一个实值)。这些插件中的每一个都扩展了一个`DataType`插件，并使用一种类型的`DataDefinitionInterface`实现本身(通常是`FieldItemDataDefinition`)。这一级别的事情相当复杂，因此很有必要仔细研究一下实体字段的构成，以便更好地理解这种架构。

## 添加实体和字段约束

Drupal 8 中的约束也是插件，通常包含少量关于数据实际上是如何被验证的信息，在失败的情况下应该使用什么样的错误消息，以及验证器需要的任何附加选项。验证器类(被约束引用)负责检查数据。我们已经看到了一个例子，`Length`，它实际上是由 Symfony 的`LengthValidator`类直接验证的`LengthConstraint`类。我们将在第二部分看到如何创建我们自己的约束和验证器。但是现在，让我们看看如何向内容实体和字段添加现有的约束。

### 实体级约束

实体级约束被添加到实体类本身的注释中。例如，`Comment`实体就是这样为其名称/作者字段组合定义约束的:

```
...
  constraints = {
    "CommentName" = {}
  }
... 
```

在这个例子中，`CommentName`是约束的插件 ID，当保存一个注释实体时将根据它进行验证。左大括号和右大括号意味着插件没有选项。

如果我们想要添加或删除一个现有实体的约束，我们必须实现`hook_entity_type_alter()`:

```
function demo_entity_type_alter(array &$entity_types) {
  /** @var \Drupal\Core\Entity\ContentEntityType $node */
  $node = $entity_types['node'];
  $node->addConstraint('ConstraintPluginName', ['array', 'of', 'options']);
} 
```

在本例中，我们向节点实体添加了一个虚构的约束，并向它传递了一组选项。

### 字段级约束

根据模块中是否定义了内容实体类型，以及我们所讨论的字段类型是基本字段还是可配置字段，有多种方法可以添加字段级约束。

如果我们定义自己的实体类型，我们必须实现的实际实体类的方法之一是`baseFieldDefinitions()`。这是我们返回一组`BaseFieldDefinition`字段定义的地方，我们可以很容易地在这里添加我们的约束。例如，这是节点 ID 基本字段的定义方式:

```
$fields['nid'] = BaseFieldDefinition::create('integer')
  ->setLabel(t('Node ID'))
  ->setDescription(t('The node ID.'))
  ->setReadOnly(TRUE)
  ->setSetting('unsigned', TRUE); 
```

与我们之前向`DataDefinition`实例添加约束类似，节点实体也可以向节点 ID 字段添加约束，更具体地说:

*   到`BaseFieldDefiniton`本身，正如我们看到的，这是`FieldItemListInterface`实现(列表)的定义
*   到单个的`FieldItemDataDefinition`项，正如我们看到的，它们是`FieldItemInterface`实现(项)的一种复杂数据定义

如果*我们*想要添加一个约束到一个节点基础字段(或者任何其他没有被我们的模块定义的内容实体类型)，我们必须实现`hook_entity_base_field_info_alter()`并在那里添加我们的约束:

```
function demo_entity_base_field_info_alter(&$fields, \Drupal\Core\Entity\EntityTypeInterface $entity_type) {
  if ($entity_type->id() === 'node') {
    /** @var \Drupal\Core\Field\BaseFieldDefinition $title */
    $title = $fields['title'];
    $title->addPropertyConstraints('value', ['Length' => ['max' => 5]]);
  }
} 
```

在上面的示例中，我们向节点标题字段添加了一个约束，以确保任何标题都不能超过 5 个字符。要注意的是，我们使用的是`addPropertyConstraint()`方法，而不是我们之前看到的`addConstraint()`方法。这是因为我们针对的不是项目清单的定义，而是单个项目定义本身(`FieldItemDataDefinition`)。

通过使用`addConstraint()`方法，我们向整个条目列表添加了一个约束。这意味着当约束得到验证时，验证器接收整个列表，而不仅仅是单个项目的值。

如果我们想给一个可配置的字段添加一个约束，过程是非常相似的。不同之处在于，我们需要实现`hook_entity_bundle_field_info_alter()`并使用`FieldConfig`实例而不是`BaseFieldDefinition`。

如果我们想要检查已经在字段上设置的约束，我们可以这样做:

```
$title = $fields['title'];
$constraints = $title->getConstraints();
$property_constraints = $title->getItemDefinition()->getConstraints(); 
```

在这个例子中，`$title`或者是`BaseFieldDefinition`或者是`FieldConfig`的一个实例。正如所料，`$constraints`数组是应用于整个字段项列表的约束列表，而`$property_constraints`是应用于单个字段项本身的约束数组。但是，我们可以注意到，如果我们在应用了`Length`约束后运行这段代码，我们会发现在`$property_constraints`中有一个`ComplexData`约束，它覆盖了应用于字段值的各个约束。这是因为一个字段项可以有多个单独的数据定义，Drupal 默认情况下会这样对它们进行分组。

## 结论

在本文中，我们开始研究 Drupal 8 中的实体验证 API。为此，我们还必须了解作为实体系统基础的类型化数据 API。一旦这一点变得更加清晰，我们已经看到了如何将约束添加到各种类型的数据定义中，包括实体和字段所使用的数据定义。

在下一部分中，我们将看看实际的验证是如何工作的，以及应该如何处理可能发生的违规。此外，我们将创建自己的约束和验证器，并将这部分的知识应用于各种数据定义类型。好玩！

## 分享这篇文章