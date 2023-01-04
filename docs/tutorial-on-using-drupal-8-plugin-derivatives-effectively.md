# 关于有效使用 Drupal 8 插件衍生工具的教程

> 原文：<https://www.sitepoint.com/tutorial-on-using-drupal-8-plugin-derivatives-effectively/>

在早先的教程中，我们研究了 Drupal 8 插件系统以及如何创建我们自己的定制插件类型。我们已经看到，Drupal 7 中通过`_info`钩子声明的许多功能已经被这些插件所取代。我们的用例非常简单，它允许这种功能的每个*实例*通过一个新的插件类和相关表单手动声明。

![Drupal 8 logo](img/077a30fb14c973a02eea34dedb6e8ea1.png)

但是如果我们需要这样的*实例*根据我们的小子系统外部的一些因素动态声明呢？例如，当在 Drupal 7 中声明`_info`钩子时，我们可以获得一个*某物*的列表，循环遍历它，并在返回的数组中为每个单独的*某物*声明一个新项。菜单系统这样做是为了给每个菜单提供一个新的块，要么是 Drupal core 自带的，要么是后来通过 UI 创建的。

那么 Drupal 8 怎么样呢？我们已经看到，对于每一个特定类型的插件，我们需要声明一个不同的 PHP 类。要创建一个新的块，我们需要一个新的类。为了创建**另一个**块，我们需要**另一个**类。那么我们在 Drupal 7 中看到的*循环*会发生在哪里呢？简短的回答是:在一个*插件衍生*中。

在这篇文章中，我们将探索这个问题的答案，并了解什么是导数，以及我们如何使用它们。对于后者，我们将在`demo`模块中构建一个例子，这个例子可以在[这个 git 库](https://github.com/upchuk/d8-demo-modules/tree/master/demo)中找到，希望它能帮助我们更好地理解发生了什么。举一个稍微复杂一点的例子，菜单系统很棒，因为它为每个菜单提供了一个单独的块(类似于 Drupal 7，但是使用了插件)。

我们要做的事情其实很简单。我们将实现基本的节点块功能，对于我们网站上的所有文章节点，我们将有一个块。可笑？当然可以。我们应该对我们站点上的所有节点都这样做吗？肯定不是！但这是一个非常基本的实现，意在保持简洁并演示插件衍生工具的使用。

## 插件衍生品

插件派生是一种方式，通过这种方式，某种类型的插件可以在系统中表示为其自身的多个实例。换句话说，一个插件可以引用一个 deriver 类，该类负责提供一个插件定义列表，这些插件定义基于初始插件(从相同的基本定义开始)，但是具有稍微不同的配置或定义数据。我们上面提到的`SystemMenuBlock`就是一个很好的例子。这是一个插件，它的衍生工具和网站上的菜单一样多。

更深入地说，当请求某一类型的所有插件的列表时，插件管理器使用它的发现机制来加载该类型的所有插件。如果这个机制用`DerivativeDiscoveryDecorator`修饰，经理也将能够检索衍生产品。为了做到这一点，派生发现在每个插件上寻找一个派生类，如果找到一个，就要求它提供这个列表。

扩展了`DefaultPluginManager`基类的插件类型管理器通常应该有修饰默认发现(注释)的派生发现机制。这是 Drupal 核心插件系统中最常见的模式:由衍生产品包装的带注释的发现。

## 衍生类

现在我们知道了插件派生的作用，让我们创建我们的第一个 deriver 类，它将被我们的 block 插件使用(我们将在一分钟内创建)。

在`demo`模块的`src/Plugin/Derivative/NodeBlock.php`中，我们有以下内容:

```
<?php

/**
 * @file
 * Contains \Drupal\demo\Plugin\Derivative\NodeBlock.
 */

namespace Drupal\demo\Plugin\Derivative;

use Drupal\Component\Plugin\Derivative\DeriverBase;
use Drupal\Core\Entity\EntityStorageInterface;
use Drupal\Core\Plugin\Discovery\ContainerDeriverInterface;
use Symfony\Component\DependencyInjection\ContainerInterface;

/**
 * Provides block plugin definitions for nodes.
 *
 * @see \Drupal\demo\Plugin\Block\NodeBlock
 */
class NodeBlock extends DeriverBase implements ContainerDeriverInterface {

  /**
   * The node storage.
   *
   * @var \Drupal\Core\Entity\EntityStorageInterface
   */
  protected $nodeStorage;

  /**
   * Constructs new NodeBlock.
   *
   * @param \Drupal\Core\Entity\EntityStorageInterface $node_storage
   *   The node storage.
   */
  public function __construct(EntityStorageInterface $node_storage) {
    $this->nodeStorage = $node_storage;
  }

  /**
   * {@inheritdoc}
   */
  public static function create(ContainerInterface $container, $base_plugin_id) {
    return new static(
      $container->get('entity.manager')->getStorage('node')
    );
  }

  /**
   * {@inheritdoc}
   */
  public function getDerivativeDefinitions($base_plugin_definition) {
    $nodes = $this->nodeStorage->loadByProperties(['type' => 'article']);
    foreach ($nodes as $node) {
      $this->derivatives[$node->id()] = $base_plugin_definition;
      $this->derivatives[$node->id()]['admin_label'] = t('Node block: ') . $node->label();
    }
    return $this->derivatives;
  }
} 
```

我们的类只需要实现`DeriverInterface`并实现它的两个方法。我们使用`ContainerDeriverInterface`来代替，因为我们想让我们的驱动容器知道。为什么？因为我们使用依赖注入来加载 Drupal 的实体管理器，这样我们就可以访问节点存储(这是构造函数和`create()`方法所做的)。此外，我们的 deriver 类从`DeriverBase`类扩展而来，因为它已经处理了一个必需的方法(`getDerivativeDefinition()`)。

最后，`getDerivativeDefinitions()`是负责提供插件定义数组的方法，这些插件定义是从使用这个类的插件中派生出来的。它接收`$base_plugin_definition`作为参数(使用这个衍生工具的实际插件的定义),我们用它来建立我们的衍生工具定义。在我们的例子中，我们不加选择地加载所有的文章节点，并为它们中的每一个创建一个单独的定义，不同之处仅在于具有不同的`admin_label`(这是`Drupal\Core\Block\Annotation\Block`注释类的一个属性)。导数的数组是由导数的 ID(在我们的例子中是节点 ID，我们将在后面使用)决定的。

这里我们需要指出的非常重要的一点是，加载所有的节点并从中创建插件从来都不是一个好主意。可能有趣的是实现这样的功能，通过它，单个节点可以通过复选框或类似的东西作为块公开。

## 阻止插件

现在我们有了 deriver 类，让我们创建一个简单的块插件，用它来生成自身的多个实例(每个文章节点一个)。

内部`src/Plugin/Block/NodeBlock.php`:

```
<?php

namespace Drupal\demo\Plugin\Block;

use Drupal\Core\Block\BlockBase;
use Drupal\Core\Entity\EntityManagerInterface;
use Drupal\Core\Entity\EntityViewBuilderInterface;
use Drupal\Core\Plugin\ContainerFactoryPluginInterface;
use Drupal\Core\Session\AccountInterface;
use Drupal\node\NodeInterface;
use Symfony\Component\DependencyInjection\ContainerInterface;

/**
 * Provides a 'NodeBlock' block plugin.
 *
 * @Block(
 *   id = "node_block",
 *   admin_label = @Translation("Node block"),
 *   deriver = "Drupal\demo\Plugin\Derivative\NodeBlock"
 * )
 */

class NodeBlock extends BlockBase implements ContainerFactoryPluginInterface {

  /**
   * @var EntityViewBuilderInterface.
   */
  private $viewBuilder;

  /**
   * @var NodeInterface.
   */
  private $node;

  /**
   * Creates a NodeBlock instance.
   *
   * @param array $configuration
   * @param string $plugin_id
   * @param array $plugin_definition
   * @param EntityManagerInterface $entity_manager
   */
  public function __construct(array $configuration, $plugin_id, $plugin_definition, EntityManagerInterface $entity_manager) {
    parent::__construct($configuration, $plugin_id, $plugin_definition);
    $this->viewBuilder = $entity_manager->getViewBuilder('node');
    $this->nodeStorage = $entity_manager->getStorage('node');
    $this->node = $entity_manager->getStorage('node')->load($this->getDerivativeId());
  }

  /**
   * {@inheritdoc}
   */
  public static function create(ContainerInterface $container, array $configuration, $plugin_id, $plugin_definition) {
    return new static(
      $configuration,
      $plugin_id,
      $plugin_definition,
      $container->get('entity.manager')
    );
  }

  /**
   * {@inheritdoc}
   */
  public function build() {
    if (!$this->node instanceof NodeInterface) {
      return;
    }
    $build = $this->viewBuilder->view($this->node, 'full');
    return $build;
  }

  /**
   * {@inheritdoc}
   */
  public function blockAccess(AccountInterface $account, $return_as_object = FALSE) {
    return $this->node->access('view', NULL, TRUE);
  }
} 
```

在这个插件的注释中，我们注意到的第一件事是指向我们之前创建的类的`deriver`键。这基本上就是我们把两者结合起来所需要的。衍生发现装饰器处理繁重的工作。

剩下的大部分是基本的积木搭建[我们应该熟悉](https://www.sitepoint.com/building-drupal-8-module-blocks-forms/)。有趣的是，我们可以使用`getDerivativeId()`方法来检索节点 ID，我们也将该节点 ID 用作所显示的导数的 ID，使用它，我们加载节点对象并构建作为实际节点输出的块。最后，在`blockAccess()`方法中，我们确保这个块与实际节点本身具有相同的访问检查。因此，如果当前用户无权查看当前节点，该块甚至不会显示。

现在，如果我们清除缓存并导航到块布局界面，我们应该会看到一些名为`Node Block: [Node title]`的块。您可以将它们放在您想要的位置，它们将呈现相关的节点。

## 结论

在本文中，我们研究了插件衍生工具，并看到了它们如何工作的一个简单例子。这个话题的关键是插件派生是我们动态声明同一个插件的多个实例的方式。它们通常帮助我们将用户配置的功能(例如菜单)转换成插件(例如菜单块)。

为了说明导数的使用，我们看到了一个非常简单的技术，它允许我们将文章节点呈现为块。我们应该记住，不要在一个有很多文章节点的网站上尝试，而是实现额外的功能来限制暴露的节点数量。你知道，这样我们的网站就不会崩溃。

有问题吗？评论？你想进一步解释什么吗？让我们知道！

## 分享这篇文章