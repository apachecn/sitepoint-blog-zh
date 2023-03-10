# 用 ApiGen 生成文档

> 原文：<https://www.sitepoint.com/generate-documentation-with-apigen/>

如果你曾经使用过别人的代码，你就会知道缺少(或已有的)文档的痛苦(或快乐)。良好记录的代码是专业软件开发的基石之一，除非你学会如何记录你的代码，使之成为你的第二天性，否则你对自己和他人都是弊大于利。

如果这听起来过于令人沮丧，那是因为它应该如此。如果您正在编写未记录的代码，您应该立即停止。我是认真的。放下一切，保存并退出，专注于改善你工作流程的这个重要部分。

多年来，我不得不忍受可怕的文档(你好，Zend Framework！)并且也浏览了一些相当扎实的东西(谢谢你，斯威夫特·梅勒！).我试着记录我产生的每一个文件，我已经做了足够长的时间，现在它已经成为第二天性。我遵循一个严格的文档标准，一个与 ApiGen(文档生成器)兼容的标准，一个高度可读但又完全兼容现代 ide 的标准。这意味着我可以用一个命令为公众生成我的库文档以及示例、教程、描述和可能的错误。

ApiGen 是一个像 PhpDocumentor 一样的文档块解析器。PhpDocumentor 比 ApiGen 存在的时间要长得多，但不幸的是，它的发展有些迟缓，并且缺乏现代文档和示例。它也有一些令人不快的依赖关系，这可能会导致新 PHP 安装的问题，并经常抛出错误，这些错误在任何地方都没有记录。您可以在之前的 SitePoint 文章中找到关于 PhpDocumentor 的更多信息。至于哪个更好…我认为 ApiGen 是合理的选择，因为 PhpDocumentor 有以下缺点:

*   需要无用的页面级文档块(在命名空间之上)~~；它们从未被正确解析，输入的描述也无处可寻~~。更新，2012 年 8 月 24 日:一位评论者指出，页面级块实际上在模板中使用，而不是默认的。然而，它们仍然不是必需的，并且在 PhpDoc 解析时产生错误，作为记录的错误无限期地留在文档中。
*   在生成的文档中没有搜索。
*   ~~布局错误——长包名破坏了“Api 文档”下拉菜单，生成的 GraphViz 图表的 Z-index 将高于菜单，因此即使在菜单展开时也会出现在菜单上方~~(见下面的注释)
*   ~~由于未知原因，无法识别某些类型提示。~~(这在 alpha8 中是固定的，参见下面的注释)
*   标签描述中没有 html 支持——HTML 标签是可见的，所以据我所知，高级格式是不可能的。(参见下面的注释)

***注意，8 月 24 日:**这些问题大部分已经在我测试的上一个版本——alpha 10 中修复了。然而，缺乏搜索功能对我来说仍然是一个障碍。*

## 文档块和标签

我们将使用我为本教程编写的几个类，它们分布在两个简单名称空间下的几个文件夹中。这些类在现实世界中不会有很大用处，但是它们对本文很有帮助。由于 PSR-2 标准规定了所有的注释和间距，这些类相当大，我已经将完整的代码上传到 GitHub。回购中有多个分支可用；第一个分支“base-code”只包含实际的类，第二个分支“commented-code”包含完全注释的代码，第三个分支“documentation-generated”有一个`docs`目录，您可以在其中找到自动生成的文档。

观察每一节课会花费太多的时间和空间，所以现在让我们只关注一节课。我们将一部分一部分地看一下`User`类，我将解释每个片段的文档块:

```
<?php
/**
 * This block contains a short description of the classes
 * present in the rest of this file.
 *
 * This paragraph contains the long description of the same
 * things.
 *
 * This entire block will be completely ignored by ApiGen and
 * is here only to maintain compatibility with PhpDocumentor.
 * Requiring the presence of this near useless block in every
 * php file is one of PhpDocumentors downsides.
 *
 * @package Some package that only PhpDocumentor sees.
 */
namespace MyLibrary;

use MyLibraryAbstractsSuperType;
use MyLibraryInterfacesUserMapper;
use MyLibraryExceptionsUser as UserException;

/**
 * The User class is a sample class that holds a single
 * user instance.
 *
 * The constructor of the User class takes a Data Mapper which
 * handles the user persistence in the database. In this case,
 * we will provide a fake one.
 *
 * @category  MyLibrary
 * @package   Main
 * @license   http://www.opensource.org/licenses/BSD-3-Clause
 * @example   ../index.php
 * @example <br />
 * 	$oUser = new MyLibraryUser(new MappersUserMapper());<br />
 *  $oUser->setUsername('swader');<br />
 *  $aAllEmails = $oUser->getEmails();<br />
 *  $oUser->addEmail('test@test.com');<br />
 * @version   0.01
 * @since     2012-07-07
 * @author    Bruno Skvorc <bruno@skvorc.me>
 */
class User extends SuperType
{
```

到目前为止，每个人都应该做的是，我们首先在类文件的开头定义一个名称空间(忽略名称空间之前的整个文档块，这只是为了 PhpDocumentor 兼容性)。然后我们用一些`use`语句来跟进，以便为该类使用的类建立别名。

接下来，我们看到在类声明的前面有一个注释块。这是一个类描述，它用一个简短的 TL 告诉用户/开发者这个类做什么；曼纳博士。主类描述通常由两部分组成——短描述和长描述。短描述是第一段，长描述是从短描述到第一个 docblock 标记的所有内容(甚至是多个段落、行内示例、作者想到的任何内容)。

接下来是几个标记，每个都有自己的目的。

`@category`指定类别所属的类别。类别是包的父类(见下文),形成了类和实用程序的大型超集。类别通常像名称空间一样包罗万象，因此我们的`@category`实际上是名称空间的名称。

定义了一个更小、更精确的类和工具集。它通常是主名称空间中的一个子目录，或者在我们的用户类的情况下，是“main”包，这意味着它在我们的库的根目录下。还有一个可以更深入的标记`@subpackage`。通常不需要它(由于名称空间很好地处理了所有的树结构)，但是一个例子是“@category MyLibrary，@ package User @ subpackage Exceptions”，这意味着该类在 MyLibrary 类别中，并且是用户包中与用户相关的异常。

`@category`和`@package`是任意的，很多开发者都是按照自己认为合适的方式来使用。没有特定的标准，因为正确命名空间的代码取代了对它们的需求——命名空间优先于这些标记。有些开发者甚至喜欢在 MVC 库中将自己的类标记为“@类别控制器”、“类别视图”、“类别模型”。当不使用名称空间时，像 ApiGen 这样的解析器会查看类别和包来组织您的文档——但是他们宁愿忽略它们，而支持名称空间。

指定适用于代码的许可证，通常是一个在线资源的链接。另一个有用的标记是`@copyright`标记，它告诉用户代码是否有版权。版权标记通常包含年份范围和拥有代码版权的实体，无论是个人还是法律实体。

`@example`是一个文件的路径，该文件包含该标记所在类的示例用法。在我们的例子中，我们指出可以在上面一个目录中的`index.php`文件中找到`User`类的示例用法。`@example`标记还可以提供另一个内嵌示例。一个类可以有任意多的示例标签。

`@version`是版本标签，应该随着每次更新而改变。

`@since`标记当前类的创建日期。

`@author`指定创建该类的开发者。它可以只是一个姓名，也可以是格式为“姓名”的电子邮件地址。一个文档块可以包含多个作者，所有作者都将在文档中列出。

继续向前，让我们看看类声明下面的类的属性:

```
/**
 * The user's username. Defaults to contact email.
 * @var string
 */
protected $sUsername;

/**
 * An array of the user's emails. The first element
 * is the main contact email.
 * @var array|null
 */
protected $aEmails = null;

/**
 * A boolean flag on whether or not the user is logged in
 * @var bool
 */
protected $bLoggedIn = false;

/**
 * The mapper is responsible for all data persistence and for
 * fetching existing records from the database when a username
 * is provided.
 * @var UserMapper
 */
protected $oMapper = null;
```

属性文档块只需要包含一个简短的描述和一个提示该属性将包含的值类型的`@var`标记。如果所述属性中可能包含多种类型，则可以使用管道分隔符，表示“或”(例如 array|null)。

最后，让我们看一下方法的文档，在本例中是`setUsername()`方法:

```
/**
 * Sets the username for the given user instance. If the username
 * is already set, it will be overwritten. Throws an invalid
 * argument exception if the provided username is of an invalid
 * format.
 *
 * @param string $sUsername The username string to set
 *
 * @return  User
 * @throws  InvalidArgumentException
 * @todo    Check to make sure the username isn't already taken
 *
 * @since   2012-07-07
 * @author  Bruno Skvorc <bruno@skvorc.me>
 *
 * @edit    2012-07-08<br />
 *          John Doe <john@doe.com><br />
 *          Changed some essential
 *          functionality for the better<br/>
 *          #edit3392
 */
public function setUsername($sUsername)
{
    if (!$this->checkString($sUsername, 5)) {
        throw new InvalidArgumentException(
            "The username needs to be a valid non-empty string of 5 characters or more."
        );
    }

    $this->populate($this->oMapper->findByUnique($sUsername));
    $this->sUsername = $sUsername;

    return $this;
}
```

方法文档块总是从方法的描述开始，描述应该尽可能详细，以避免其他开发人员使用时出现任何问题。

使用一个或多个`@param`标记在描述后列出可接受的参数。每个标记应该有一个预期的类型(字符串、布尔值、整型等。或者用竖线字符 array | int | string 分隔的几种类型的混合)、出现在方法声明中的实际参数名及其描述。

`@return`描述方法的返回值。如果该方法没有返回值，标记应该是“@return void”，否则，应该声明一个特定的类型(例如，“@return int”，或者在我们的例子中是“@return User”，因为该方法返回它所执行的对象的实例)。数据的描述是可选的，但当返回值可能变得复杂时(例如关联数组)，这是必需的。

`@throws`标记让开发人员知道在出错的情况下会出现哪些异常。可以有多个`@throws`标记，它们可以(但通常没有)有描述。相反，抛出异常的情况通常在方法描述中描述。

如果一个方法/类还没有完成，还有一些功能需要添加，可以使用`@todo`标记。它不仅被 ApiGen 识别，并在文档生成时转化为任务列表，它还出现在许多现代 ide 的任务列表中，使您能够轻松地跟踪未完成的工作。文档块可以有任意多的 todo 标记——它们甚至可以内联添加(在方法中间),并且仍然会被 ApiGen 识别并添加到任务列表中。

在方法级别使用`@since`和`@author`并不常见，因为将作者和日期放入类级别的文档块中通常就足够了，但是知道哪个开发人员将一个神秘的方法添加到了一个原本属于您的类中，这在大型团队中是非常宝贵的，在这些团队中，多个开发人员有时会处理同一个类。这完全是可选的，需要更多的时间和按键，但是我发现在大型项目中这样做的好处远远大于付出的努力。

`@edit`标签不是官方支持的标签。它在官方的 docblock 文档中没有任何意义或价值，并且我所知道的任何文档生成器都不期望它。我之所以强制使用它，只是因为它对于通过版本控制之外的方式跟踪代码变更是不可或缺的。与 Git 或 SVN 一起，`@edit`标签可以很容易地让你知道谁编辑了哪个文件，什么时候编辑的，为什么编辑——最后的 hashtag 将与实际版本控制系统中编辑的提交消息中使用的 hashtag 相同，因此在需要时可以很容易地找到它。适当的版本控制和团队合作超出了本文的范围，但是在处理其他人的代码时，您最好采用@edit 标记——当您几个月后回顾您的编辑时，它提供的清晰性可以为您节省无数个小时的沮丧。

有了这种文档，我们就能确保将来打开我们文件的每个开发人员都确切地知道每个类是做什么的，并且不管他们使用哪种 IDE，都有良好的自动完成功能。现在让我们把我们的文档变成更漂亮的东西吧！

## 安装和使用 ApiGen

安装 ApiGen 就像他们网站上说的那么简单。要安装 ApiGen，运行以下命令(您可能必须“sudo”它们):

```
$ pear config-set auto_discover 1
$ pear install pear.apigen.org/apigen
```

就是这样，现在安装了 ApiGen。如果这不起作用，试着遵循他们网站上详述的一些[替代说明](http://apigen.org/##installation "ApiGen | API documentation generator for PHP 5.3+")。

现在是时候用一些有用的标志运行 ApiGen 了(除了源和目的地之外的一切都是可选的):

```
$ apigen --source . --destination docs/apigen --todo yes 
 --title "My Library Documentation" --php no --download yes
```

`--destination`标志指示 ApiGen 将文档放在`docs/apigen`目录中。`--todo`标志从它找到的任何`@todo`标记生成一个任务列表。`--title`设置文档项目的标题，`--php`标志告诉 ApiGen 忽略核心 PHP 文件(比如某些异常，如果使用的话),`--download`标志告诉它也生成我们源代码的可下载 ZIP 存档。

通过打开文档文件夹中的`index.html`,或者通过在浏览器中访问适当的虚拟主机 URL，查看生成的文档。ApiGen 在那里生成了一组令人印象深刻的 HTML 文件，当这些文件一起使用时，可以形成一个漂亮的静态网站，展示出项目的整个结构。您可以浏览名称空间、子文件夹，甚至可以使用右上角的快速自动完成搜索功能，下载整个压缩源代码。我们在项目中定义的每个属性、方法和类现在都可以在屏幕上以结构化表格和/或树的形式看到——甚至是“todo”注释(单击屏幕顶部的“todo ”)!

为了向公众展示这些文档，你所要做的就是把它上传到你的服务器上，并指向它的一个域或子域——你在 HTML 文件中看到的一切也将被访问者看到。

ApiGen 还有许多其他有用的标志(Google Analytics 的实现、自定义根 URL、自定义配置指令、样式等。)但是这些超出了本文的范围，可能会在后面的文章中详细讨论。

## 最后

无论您是从事较小项目的单个开发人员，还是从事共同工作的较大团队的一员，注释对于您的工作流程都是必不可少的。如果做得恰当，并考虑到某些项目范围或团队范围的标准，它将帮助您和您的同事在以后重新访问旧代码时避免数小时的辛苦工作，并将允许您的代码的用户有一个更顺畅的体验进入它并逗留。糟糕的文档是复杂库的新用户放弃它们的主要原因之一——所以为了避免拒绝你的用户和同事，今天就采用这些最佳实践。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章