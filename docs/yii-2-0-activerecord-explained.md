# Yii 2.0 ActiveRecord 解释

> 原文：<https://www.sitepoint.com/yii-2-0-activerecord-explained/>

Yii 中的 ActiveRecord 类提供了一个面向对象的接口(aka ORM ),用于访问数据库存储的数据。类似的结构可以在大多数现代框架中找到，如 Laravel、CodeIgniter、Smyfony 和 Ruby。今天，我们将讨论 Yii 2.0 中的实现，我将向您展示它的一些更高级的特性。

![Database and networking concept](img/d6f4718563a4a0f82cae66af965e3cbf.png)

## 模型课程简介

Yii ActiveRecord 是 base `yii\base\Model`的高级版本，它是模型-视图-控制器架构的基础。我将快速解释 ActiveRecord 从 Model 类继承的最重要的功能:

### 属性

业务数据保存在属性中。这些是模型实例的公共可用属性。通过将任意数组分配给模型的`attributes`属性，可以方便地将所有属性[大量分配给](http://www.yiiframework.com/doc-2.0/guide-structure-models.html#massive-assignment)。这是可行的，因为基本组件类(Yii 2.0 中几乎所有组件的基础)实现了`__set()`方法，该方法又调用了模型类中的`setAttributes()`方法。检索也是如此；通过获取`attributes`属性可以检索所有属性。同样，构建在组件类之上，组件类实现了调用模型类中的`getAttributes()`的`__get()`。
模型还提供用于显示目的的属性标签，这使得在页面上的表单中使用它们更加容易。

### 确认

应该检查从用户输入传入模型的数据，看它们是否满足您的业务逻辑。这是通过指定`rules()`来完成的，它通常为每个属性保存一个或多个验证器。
默认情况下，只有被视为“安全”的属性，即定义了验证规则的属性，才能被大量分配。

### 情节

这些场景允许您定义不同的“场景”,在这些场景中，您将使用一个允许您更改其验证和处理数据的方式的模型。文档中的[示例描述了如何在 FormModel(也扩展了 Model 类)中使用它，只需在一个模型中定义两个不同的场景，就可以为用户注册和登录指定不同的验证规则。](http://www.yiiframework.com/doc-2.0/guide-structure-models.html#scenarios)

## 创建活动记录模型

ActiveRecord 实例表示数据库表中的一行，因此我们需要一个数据库。在本文中，我将使用下图中的数据库设计作为例子。这是博客文章的简单结构。作者可以有多篇文章，这些文章可以有多个标签。文章通过 N:M 关系与标签相关，因为我们希望能够根据标签显示相关文章。“articles”表将得到我们的关注，因为它有最有趣的一组关系。

![db_structure.PNG](img/250f67996855610070f7695243aec032.png)

我使用 Gii 扩展来生成基于表及其关系的模型。下面是通过单击几个按钮从数据库结构中为 articles 表生成的内容:

```
namespace app\models;

use Yii;

/**
 * This is the model class for table "articles".
 *
 * @property integer $ID
 * @property integer $AuthorsID
 * @property string $LastEdited
 * @property string $Published
 * @property string $Title
 * @property string $Description
 * @property string $Content
 * @property string $Format
 *
 * @property Authors $authors
 * @property Articlestags[] $articlestags
 */
class Articles extends \yii\db\ActiveRecord
{
    /**
     * @inheritdoc
     */
    public static function tableName()
    {
        return 'articles';
    }

    /**
     * @inheritdoc
     */
    public function rules()
    {
        return [
            [['AuthorsID'], 'integer'],
            [['LastEdited', 'Title', 'Description', 'Content'], 'required'],
            [['LastEdited', 'Published'], 'safe'],
            [['Description', 'Content'], 'string'],
            [['Format'], 'in', 'range' => ['MD', 'HTML']],
            [['Title'], 'string', 'max' => 250],
        ];
    }

    /**
     * @inheritdoc
     */
    public function attributeLabels()
    {
        return [
            'ID' => Yii::t('app', 'ID'),
            'AuthorsID' => Yii::t('app', 'Authors ID'),
            'LastEdited' => Yii::t('app', 'Last Edited'),
            'Published' => Yii::t('app', 'Published'),
            'Title' => Yii::t('app', 'Title'),
            'Description' => Yii::t('app', 'Description'),
            'Content' => Yii::t('app', 'Content'),
            'Format' => Yii::t('app', 'Format'),
        ];
    }

    /**
     * @return \yii\db\ActiveQuery
     */
    public function getAuthors()
    {
        return $this->hasOne(Authors::className(), ['ID' => 'AuthorsID']);
    }

    /**
     * @return \yii\db\ActiveQuery
     */
    public function getArticlestags()
    {
        return $this->hasMany(Articlestags::className(), ['ArticlesID' => 'ID']);
    }
}
```

类定义前的注释中列出的属性显示了每个实例上可用的属性。值得注意的是，由于关系定义(在该类中定义)，您还可以获得相关数据的属性；一个`Authors $authors`和多个`Articlestags[] $articlestags`。

`tableName()`函数定义了哪个数据库表与这个模型相关。这允许类名与实际的表名分离。

`rules()`定义了模型的验证规则。没有定义方案，因此只有一组规则。挺有可读性的；显示哪些字段是必需的，哪些字段需要整数或字符串。有相当多的[核心验证器](http://www.yiiframework.com/doc-2.0/guide-tutorial-core-validators.html)可以满足大多数人的需求。

如果在视图中使用属性，函数`attributeLabels()`为每个属性提供标签。我选择让我的 i18n 与 Gii 兼容，Gii 增加了对`Yii::t()`的所有调用。这基本上意味着，如果我们需要的话，最终呈现在页面中的标签的翻译将变得更加容易。
最后，`getAuthors()`和`getArticlestags()`函数定义了这个表与其他表的关系。

> **注意**:我很惊讶地发现属性、验证器和标签中完全没有“格式”属性。原来 Gii 不支持枚举。除了 MySQL(及其分支)之外，只有 PostgreSQL 支持它，因此它没有被实现。我不得不手动添加它们。

## 完成模型

您可能会看到生成的 Articles 类只具有为外键表定义的关系。从文章到标签的 N:M 关系不会自动生成，所以我们必须手动定义。

这些关系都作为 [yii\db\ActiveQuery](http://www.yiiframework.com/doc-2.0/yii-db-activequery.html) 的实例返回。为了定义文章和标签之间的关系，我们需要使用文章标签作为连接表。在 ActiveQuery 中，这是通过表定义一个*来完成的。ActiveQuery 有两种方法可用于此:*

*   `via()`允许您使用类中已定义的关系来定义关系。
*   `viaTable()`或者允许您定义一个用于关系的表格。

`via()`方法允许您通过表使用已经定义的关系作为*。然而，在我们的例子中，ArticlesTags 表没有保存我们关心的信息，所以我将使用`viaTable()`方法。*

```
/**
 * @return \yii\db\ActiveQuery
 */
public function getTags()
{
    return $this->hasMany(Tags::className(), ['ID' => 'TagsID'])
			    ->viaTable(Articlestags::tableName(), ['ArticlesID' => 'ID']);
}
```

现在我们已经定义了所有的关系，我们可以开始使用这个模型了。

## 使用模型

我将手动快速创建一些数据库内容。

```
$phpTag = new \app\models\Tags();		//Create a new tag 'PHP'
$phpTag->Tag = 'PHP';
$phpTag->save();

$yiiTag = new \app\models\Tags();		//Create a new tag 'Yii'
$yiiTag->Tag = 'Yii';
$yiiTag->save();

$author = new \app\models\Authors();	//Create a new author
$author->Name = 'Arno Slatius';
$author->save();

$article = new \app\models\Articles();	//Create an article and link it to the author
$article->AuthorsID = $author->ID;
$article->Title = 'Yii 2.0 ActiveRecord';
$article->Description = 'Arno Slatius dives into the Yii ActiveRecord class';
$article->Content = '... the article ...';
$article->LastEdited = new \yii\db\Expression('NOW()');
$article->save();

$tagArticle = new \app\models\ArticlesTags();	//Link the 'PHP' tag to the article
$tagArticle->ArticlesID = $article->ID;
$tagArticle->TagsID = $phpTag->ID;
$tagArticle->save();

$tagArticle = new \app\models\ArticlesTags();	//Link the 'Yii' tag to the article
$tagArticle->ArticlesID = $article->ID;
$tagArticle->TagsID = $yiiTag->ID;
$tagArticle->save();
```

应该注意的是，上面的代码有一些假设，我会解释；

*   不计算布尔值`save()`的结果。这通常是不明智的，因为 Yii 实际上会在将类保存到数据库之前调用这个类的`validate()`。如果任何验证规则失败，数据库`INSERT`将不会被执行。
*   您可能会注意到，各种实例的 ID 属性在未设置时会被使用。这可以安全地完成，因为`save()`调用将`INSERT`数据，并从数据库中获得分配的主键，使 ID 属性值有效。
*   `$article->LastEdited`是数据库中的日期时间值。我想通过调用当前日期时间上的`NOW()` SQL 函数来插入当前日期时间。您可以通过使用 Expression 类来实现这一点，该类允许对 ActiveRecord 实例使用各种 SQL 表达式。

然后，您可以再次从数据库中检索文章；

```
//Look up our latest article
$article = \app\models\Articles::findOne(['Title'=>'Yii 2.0 ActiveRecord']);

//Show the title
echo $article->Title;

//The related author, there is none or one because of the hasOne relation
if (isset($article->authors)) {
    echo $article->authors->name
}

//The related tags, always an array because of the hasMany relations
if (isset($article->tags)) {
    foreach($article->tags as $tag) {
        echo $tag->Tag;
    }
}
```

## 新的高级用法

到目前为止，我所描述的 Yii ActiveRecord 非常简单。让我们把它变得有趣一点，深入了解 Yii 2.0 中新的和改变的功能。

### 脏属性

Yii 2.0 引入了检测属性变化的能力。对于 ActiveRecord，这些被称为*脏*属性，因为它们需要数据库更新。默认情况下，这种能力允许您查看模型中哪些属性发生了变化，并对其进行操作。例如，当您已经从表单帖子中大量分配了所有属性时，您可能只想获得更改后的属性:

```
//Get a attribute => value array for all changed values
$changedAttributes = $model->getDirtyAttributes();

//Boolean whether the specific attribute changed
$model->isAttributeChanged('someAttribute');

//Force an attribute to be marked as dirty to make sure the record is 
// updated in the database
$model->markAttributeDirty('someAttribute');

//Get on or all old attributes
$model->getOldAttribute('someAttribute');
$model->getOldAttributes();
```

### 可排列的

ActiveRecord 从`Model`扩展而来，现在用它的`toArray()`方法实现了`\yii\base\Arrayable`特征。这允许您将带有属性的模型快速转换为数组。它还允许一些很好的补充。

通常对`toArray()`的调用会调用`fields()`函数并将它们转换成数组。可选的扩展参数`toArray()`将额外调用`extraFields()`，它指示哪些字段也将被包含。

这两个字段方法由`BaseActiveRecord`实现，您可以在自己的模型中实现它们来定制`toArray()`调用的输出。

在我的例子中，我希望扩展后的数组包含一篇文章的所有标签，并在数组输出中以逗号分隔的字符串形式出现；

```
public function extraFields()
{
	return [
		'tags'=>function() {
			if (isset($this->tags)) {
				$tags = [];
				foreach($this->tags as $articletag) {
					$tags[] = $articletag->Tag;
				}
				return implode(', ', $tags);
			}
		}
	];
}
```

然后从模型中获取所有字段和这个额外字段的数组；

```
//Returns all the attributes and the extra tags field
$article->toArray([], ['tags']);
```

### 事件

Yii 1.1 已经在 [CActiveRecord](http://www.yiiframework.com/doc/api/1.1/CActiveRecord) 上实现了各种事件，它们在 Yii 2.0 中仍然存在。Yii 2.0 指南中的 [ActiveRecord 生命周期](http://www.yiiframework.com/doc-2.0/guide-db-active-record.html#active-record-life-cycles)非常好地展示了在使用 ActiveRecord 时所有这些事件是如何触发的。所有事件都是围绕 ActiveRecord 实例的正常操作触发的。事件的命名非常明显，因此您应该能够知道它们是何时被触发的；`afterFind()`、`beforeValidate()`、`afterValidate()`、`beforeSave()`、`afterSave()`、`beforeDelete()`、`afterDelete()`。

在我的例子中，`LastEdited`属性是演示事件用法的好方法。我想确保`LastEdited`总是反映文章最后一次被编辑的时间。我可以把它设定在两个事件上。`beforeSave()`和`beforeValidate()`。我的模型规则将`LastEdited`定义为必需属性，因此我们需要使用`beforeValidate()`事件来确保它也设置在模型的新实例上；

```
public function beforeValidate($insert)
{
    if (parent::beforeValidate($insert)) {
		$this->LastEdited = new \yii\db\Expression('NOW()');
        return true;
    }
    return false;
}
```

请注意，对于所有这些事件，您应该调用父事件处理程序。返回 false(或者什么都没有！)从这些函数中的 before 事件停止动作的发生。

### 行为

行为可用于增强现有组件的功能，而无需修改其代码。它还可以响应它所附加到的组件中的事件。它们的行为类似于 PHP 5.4 中引入的[特征](http://php.net/manual/en/language.oop5.traits.php)。
Yii 2.0 提供了许多可用的行为；

*   [`yii\behaviors\AttributeBehavior`](http://www.yiiframework.com/doc-2.0/yii-behaviors-attributebehavior.html) 允许您指定需要在指定事件上更新的属性。例如，您可以基于`BEFORE_INSERT`事件上的未命名函数将属性设置为一个值。

*   [`yii\behaviors\BlameableBehavior`](http://www.yiiframework.com/doc-2.0/yii-behaviors-blameablebehavior.html) 果然不出所料；责怪某人。您可以设置两个属性；一个`createdByAttribute`和`updatedByAttribute`，当对象被创建或更新时，它们将被设置为当前用户 ID。

*   [`yii\behaviors\SluggableBehavior`](http://www.yiiframework.com/doc-2.0/yii-behaviors-sluggablebehavior.html) 允许你根据模型中的一个属性到另一个属性自动创建一个 URL slug。

*   [`yii\behaviors\TimestampBehavior`](http://www.yiiframework.com/doc-2.0/yii-behaviors-timestampbehavior.html) 将允许你在你的模型中自动创建和更新`createdAtAttribute`和`updatedAtAttribute`中的时间戳。

在我的例子中，你可能也看到了一些实际应用。假设当前登录到应用程序的人是一篇文章的实际作者，我可以使用`BlameableBehavior`让他们成为作者，我还可以使用`TimestampBehaviour`确保`LastEdited`属性保持最新。这将取代我之前在模型中实现的`beforeValidate()`事件。这是我如何将行为附加到我的文章模型的:

```
public function behaviors()
{
    return [
	    [
            'class' => \yii\behaviors\BlameableBehavior::className(),
            'createdByAttribute' => 'AuthorID',
            'updatedByAttribute' => 'AuthorID',
        ],
        [
            'class' => \yii\behaviors\TimestampBehavior::className(),
            'createdAtAttribute' => false,    //or 'LastEdited'
            'updatedAtAttribute' => 'LastEdited',
            'value' => new \yii\db\Expression\Expression('NOW()'),
        ],
    ];
}
```

当然，我在这里假设文章的作者和编辑是同一个人。因为我没有创建时间戳字段，所以我选择不使用它，将`createdAtAttribute`设置为`false`。当然，我也可以将它设置为`'LastEdited'`。

### 交易操作

我想介绍的最后一个特性是在模型中自动强制使用事务的可能性。随着外键的实施，数据库查询也有可能因此而失败。这可以通过将它们包装在一个事务中来更好地处理。Yii 允许您通过在您的模型中实现一个`transactions()`函数来指定应该是事务性的操作，该函数指定应该将哪些场景中的哪些操作包含在一个事务中。请注意，如果您希望在操作中默认这样做，您应该为`SCENARIO_DEFAULT`返回一个规则。

```
public function transactions()
{
	return [
		//always enclose updates in a transaction
	    \yii\base\Model::SCENARIO_DEFAULT => self::OP_UPDATE,	    
	    //include all operations in a transaction for the 'editor' scenario
	    'editor' => self::OP_INSERT | self::OP_UPDATE | self::OP_DELETE,
	];
}
```

## 结论

Yii ActiveRecord 类已经使 ORM 处理变得非常简单，Yii 2.0 建立在这个伟大的基础上，并进一步扩展了它。由于可以定义不同的使用场景、附加行为和使用事件，因此灵活性非常大。

这些是 ActiveRecord 中的一些特性，随着 Yii 2.0 的到来，我发现它们是最有用的，也是最受欢迎的。你是否错过了 ActiveRecord 的一个特性，或者可能觉得 Yii ActiveRecord 缺少了另一个框架的一个很棒的特性？请在评论中告诉我们！

## 分享这篇文章