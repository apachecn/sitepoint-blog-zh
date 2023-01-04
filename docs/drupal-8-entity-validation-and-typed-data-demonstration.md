# Drupal 8 实体验证和类型化数据演示

> 原文：<https://www.sitepoint.com/drupal-8-entity-validation-and-typed-data-demonstration/>

在本系列的前一篇文章中，我们已经开始深入研究实体验证和类型化数据 API。我们已经看到了`DataType`插件如何与数据定义交互，以及如何在多个级别和扩展点向后者添加各种约束。

![Drupal 8 logo](img/231563ef9e7011aefaa17d08645cf05e.png)

在这一部分中，我们将讨论实际的验证和违规处理。此外，我们将编写自己的约束和验证器，以便在数据验证过程中使用自定义行为。

## 验证和违规处理

尽管我们还不知道约束是如何构建的，但我们已经看到了如何将它们添加到类型化数据定义中，包括实体字段。现在让我们看看如何验证实体并处理我们发现的可能的违规。

当谈到类型化数据时，我们已经看到了如何在保存数据定义的`DataType`插件实例上调用`validate()`方法。就实体而言，这可能发生在实体和字段级别。

例如，我们可以使用`validate()`方法验证整个实体:

```
$entity->set('title', 'this is too long of a title');
$violations = $entity->validate(); 
```

在上一篇文章中，我们为节点标题添加了`Length`约束，以防止标题字符串超过 5 个字符。如果这仍然存在，并且我们运行上面的代码，验证显然会失败。然而，`$violations`对象现在是一个`EntityConstraintViolationListInterface`实例，它提供了一些 helper 方法来访问特定于 Drupal 内容实体的违规数据。值得研究一下该接口中所有可用的 helper 方法。

为了获得实体级违规的列表，我们可以使用`getEntityViolations()`方法，但是我们也可以遍历所有的违规。一旦我们有了自己的`ConstraintViolationInterface`实例，我们就可以检查它们出了什么问题。例如，我们可以用`getMessage()`得到错误消息，用`getPropertyPath()`得到失败的属性路径，用`getInvalidValue()`得到无效值，以及其他有用的东西。

对于字段，属性路径的格式如下:`title.0.value`。这包括字段名、列表中单个字段项的键(增量)和实际的属性名。这代表了我们上面违例的属性路径。

除了在整个实体上调用验证(有时这可能是多余的)，我们还可以直接在每个字段上这样做:

```
$entity->set('title', 'this is too long of a title');
$violations = $entity->get('title')->validate(); 
```

在这种情况下，`$violations`又是`ConstraintViolationListInterface`的一个实例，可以循环检查每个违规。但是这一次，属性路径改变为不再包含字段名:`0.value`。

最后，我们甚至可以验证列表中的单个项目:

```
$violations = $entity->get('title')->get(0)->validate(); 
```

正如我们所预料的，现在的区别是属性路径将只在违例中显示`value`,因为我们确切地知道我们正在验证什么:列表中的第一个数据定义。

## 约束和验证器

我们只触及了约束和验证器的方面，但是通过我们自己创建一个来让我们更好地理解它们是如何工作的。我们将创建一个单独的约束和验证器，但是它可以用于节点实体和它们的字段。让约束针对我们想要的数据定义总是更好，但为了简洁起见，我们将在一个约束中完成所有这些，看看如何处理所有这些选项。

我们示例的业务案例是有一个约束，我们可以将它应用于任何基于字符串的内容实体字段，强制字符串包含特定的字母数字代码。后者将作为一个选项传递，以便可以重用。如果应用于节点实体，我们希望确保节点的标题包含代码。所以让我们开始吧。

首先，在我们的`demo`模块中，可以在[这个 git 存储库](https://github.com/upchuk/d8-demo-modules/tree/master/demo)中找到，我们需要在我们的名称空间的`Plugin`文件夹中创建`Validation`文件夹(`src/`目录)。在里面，我们需要`Constraint`文件夹。这是因为约束是期望在那里定义的插件。

其次，在`Constraint/`中，我们可以创建我们的约束类:

```
<?php

/**
 * @file
 * Contains \Drupal\demo\Plugin\Validation\Constraint\HasCodeConstraint.
 */

namespace Drupal\demo\Plugin\Validation\Constraint;

use Symfony\Component\Validator\Constraint;
use Symfony\Component\Validator\Exception\MissingOptionsException;

/**
 * Constraint for checking if a string contains a certain alphanumerical code.
 *
 * @Constraint(
 *   id = "HasCode",
 *   label = @Translation("Has code", context = "Validation"),
 *   type = { "string", "entity:node" }
 * )
 */
class HasCodeConstraint extends Constraint {

  /**
   * Message shown when the code is missing.
   *
   * @var string
   */
  public $messageNoCode = 'The string <em>%string</em> does not contain the necessary code: %code.';

  /**
   * The code this constraint is checking for.
   *
   * @var string
   */
  public $code;

  /**
   * Constructs a HasCodeConstraint instance.
   *
   * @param null $options
   */
  public function __construct($options = NULL) {
    if ($options !== NULL && is_string($options)) {
      parent::__construct(['code' => $options]);
    }

    if ($options !== NULL && is_array($options) && isset($options['code'])) {
      parent::__construct($options);
    }

    if ($this->code === NULL) {
      throw new MissingOptionsException('The code option is required', __CLASS__);
    }
  }
} 
```

正如我们前面提到的，约束插件类相对简单。它具有预期的注释，在样板元数据中，还指定了该约束可以应用于什么类型的数据。我们选择了简单的字符串和节点实体类型。然后我们声明两个公共属性:约束失败时使用的消息(占位符填充在验证器中)和要检查的实际代码(由父类`Constraint`的构造函数填充)。在我们的构造函数中，我们检查传递的选项是字符串还是数组(只是为了灵活一点),如果代码参数没有以任何形状或形式作为选项传递，就抛出一个异常。

父类`Constraint`的任务之一是指定哪个类将用于验证该约束。默认情况下，它是一个像约束本身一样命名的类，但是在末尾附加了`Validate`(`HasCodeConstraintValidator`)。如果我们想要创建一个不同命名和/或命名空间的类，我们必须覆盖`validatedBy()`方法并返回我们想要的类的完全限定名。

现在让我们来看看`HasCodeConstraintValidator`类，因为对我们来说缺省值就可以了:

```
<?php

/**
 * @file
 * Contains \Drupal\demo\Plugin\Validation\Constraint\HasCodeConstraintValidator.
 */

namespace Drupal\demo\Plugin\Validation\Constraint;

use Drupal\Core\Field\FieldItemListInterface;
use Drupal\node\NodeInterface;
use Symfony\Component\Validator\Constraint;
use Symfony\Component\Validator\ConstraintValidator;
use Symfony\Component\Validator\Exception\UnexpectedTypeException;
use Symfony\Component\Validator\Violation\ConstraintViolationBuilderInterface;

/**
 * Validates the HasCodeConstraint.
 */
class HasCodeConstraintValidator extends ConstraintValidator {

  /**
   * Validator 2.5 and upwards compatible execution context.
   *
   * @var \Symfony\Component\Validator\Context\ExecutionContextInterface
   */
  protected $context;

  /**
   * {@inheritdoc}
   */
  public function validate($data, Constraint $constraint) {
    if (!$constraint instanceof HasCodeConstraint) {
      throw new UnexpectedTypeException($constraint, __NAMESPACE__.'\HasCodeConstraint');
    }

    // Node entity
    if ($data instanceof NodeInterface) {
      $this->validateNodeEntity($data, $constraint);
      return;
    }

    // FieldItemList
    if ($data instanceof FieldItemListInterface) {
      foreach ($data as $key => $item) {
        $violation = $this->validateString($item->value, $constraint);
        if ($violation instanceof ConstraintViolationBuilderInterface) {
          $violation->atPath('title')->addViolation();
        }
      }
      return;
    }

    // Primitive data
    if (is_string($data)) {
      $violation = $this->validateString($data, $constraint);
      if ($violation instanceof ConstraintViolationBuilderInterface) {
        $violation->addViolation();
        return;
      }
    }
  }

  /**
   * Handles validation of the title field of the Node entity.
   *
   * @param NodeInterface $node
   * @param HasCodeConstraint $constraint
   */
  protected function validateNodeEntity($node, $constraint) {
    foreach ($node->title as $item) {
      $violation = $this->validateString($item->value, $constraint);
      if ($violation instanceof ConstraintViolationBuilderInterface) {
        $violation->atPath('title')
          ->addViolation();
      }
    }
  }

  /**
   * Handles validation of a string
   *
   * @param $string
   * @param $constraint
   *
   * @return \Symfony\Component\Validator\Violation\ConstraintViolationBuilderInterface
   */
  protected function validateString($string, $constraint) {
    if (strpos($string, $constraint->code) === FALSE) {
      return $this->context->buildViolation($constraint->messageNoCode, array('%string' => $string, '%code' => $constraint->code));
    }
  }

} 
```

这个类的主要工作是实现`validate()`方法，如果传递给它的数据无效，就在当前执行上下文中构建违例。后者可以是多种类型的数据，这取决于约束应用于什么。

在我们的例子中，我们也对实体、字段项列表和原始数据使用约束。这只是为了给我们节省一些空间。但是*逻辑*是，如果一个节点实体被传递，代码在其标题中被检查，而对于字段项，执行迭代来检查字段的值。当然，约束也可以添加到单个字段项中，在这种情况下,`$data`变量将是数据定义的值。

`$context`属性有一个方便的`buildViolation()`方法，它允许我们指定许多与实际失败相关的事情(路径、消息等)。

因此，如果我们想利用这个约束，我们可以应用我们之前学到的知识，做以下三件事之一:

内部`hook_entity_type_alter()`:

```
$node = $entity_types['node'];
$node->addConstraint('HasCode', ['code' => 'UPK']); 
```

这为节点实体添加了约束，因此所有节点标题现在都必须包含代码`UPK`。

在`hook_entity_base_field_info_alter()`或`hook_entity_bundle_field_info_alter()`内，以下两行之一:

```
$title->addConstraint('HasCode', ['code' => 'UPK']);
$title->addPropertyConstraints('value', ['HasCode' => ['code' => 'UPK']]); 
```

其中`$title`是一个字段定义，第一种情况将约束添加到整个项目列表，而后者直接将其添加到单个项目。

这三种可能性涵盖了我们示例中约束验证器处理的三种情况。差不多就是这样。不太难。

# 结论

在本文中，我们继续深入研究实体验证 API，看两件事:验证和违规处理，以及自定义约束的创建。我们已经看到，一旦应用到实体或字段数据定义上，就可以非常容易地对约束进行验证和违规检查。这个 API 借鉴了 Symfony 的很多东西，使得从表单 API 中分离验证变得轻而易举。

正如我们所看到的，扩展现有的验证标准也很容易。这是通过约束插件完成的，所有的插件都有自己的验证器，并且可以用非常可重用的方式编写。摆弄这些东西并观察所有部件如何相互作用是非常有趣的。这也是一次很好的学习经历。

## 分享这篇文章