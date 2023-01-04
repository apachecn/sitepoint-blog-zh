# CodeIgniter 中的多语言支持

> 原文：<https://www.sitepoint.com/multi-language-support-in-codeigniter/>

多语言支持，也称为国际化，是现代 web 应用程序的一个关键特性。大多数全栈 PHP 框架都有多语言支持，这使我们能够以不同的语言动态地呈现我们的应用程序界面，而无需为每种语言复制现有的源代码。今天我们将讨论如何使用 CodeIgniter 支持多种语言，以及一些定制核心功能的技巧。

## 配置多语言支持

首先，我们需要配置必要的文件，然后才能开始使用语言支持。位于`application/config`目录中的 CodeIgniter 配置文件包含一个名为 language 的选项，它定义了应用程序的默认语言。

```
<?php
$config['language'] = 'english';
```

我们还需要创建包含不同语言消息的实际文件。这些文件需要放在`application/language`目录中，每种语言有一个单独的目录。例如，英语语言文件应该位于`application/language/english`目录，法语语言文件应该位于`application/language/french`。

让我们为一个示例应用程序创建一些包含错误消息的语言文件。创建文件`english/message_lang.php`(重要的是所有语言文件都有后缀`_lang.php`)。以下代码包含我们的语言文件内容的一些示例条目:

```
<?php
$lang["msg_first_name"] = "First Name";
$lang["msg_last_name"] = "Last Name";
$lang["msg_dob"] = "Date of Birth";
$lang["msg_address"] = "Address";
```

当然，在一个语言目录中可以有多个语言文件。建议根据上下文和目的将您的消息分组到不同的文件中，并为一致性在您的消息关键字前加上特定于文件的关键字。

另一种方法是为每个控制器创建单独的消息文件。这种技术的优点是只加载所需的消息，而不是加载整个语言文件，这会产生一定程度的性能开销。

## 加载语言文件

尽管我们创建了语言文件，但只有在我们将它们加载到控制器中时，它们才有效。以下代码显示了如何在控制器中加载文件:

```
<?php
class TestLanguage extends CI_Controller
{
    public function __construct() {
        parent::__construct();   	 
        $this->lang->load("message","english");
    }

    function index() {
        $data["language_msg"] = $this->lang->line("msg_hello_english");
        $this->load->view('language_view', $data);
    }
}
```

我们通常在控制器和视图中使用语言文件(在模型中使用语言文件不是一件好事)。这里我们使用了一个控制器的构造函数来加载语言文件，这样它就可以在整个类中使用，然后我们在类的'`index()`方法中引用它。

lang->load()方法的第一个参数是不带 _lang 后缀的语言文件名。第二个参数是可选的，是语言目录。如果这里没有提供默认语言，它将指向你的配置中的默认语言。

我们可以使用`lang->line()`方法直接引用语言文件的条目，并将它的返回赋给传递给视图模板的数据。在视图内部，我们可以使用上面的语言消息作为`$language_msg`。

有时我们也需要直接从视图中加载语言文件。例如，将语言项用于表单标签可能被认为是直接在视图中加载和访问消息的一个好理由。可以在视图中使用与控制器中相同的访问方法来访问这些文件。

```
<?php
$this->lang->line("msg_hello_english");
```

虽然它工作得很完美，但是当我们的视图模板代码不是一个实际的类时，使用`$this`可能会令人困惑。我们还可以在 language helper 的支持下使用下面的代码在视图中加载语言条目，这给了我们更简洁的代码。

```
<?php
lang("msg_view_english");
```

这基本上就是开始使用 CodeIgniter 语言文件所需了解的全部内容。但是即使这足够简单，在每个控制器中加载必要的语言文件也是不必要的，而且是重复的工作，特别是如果您的项目包含数百个类的话。幸运的是，我们可以使用 CodeIgniter 挂钩来构建一个快速有效的解决方案，为每个控制器自动加载语言文件。

## 将语言加载职责分配给挂钩

CodeIgniter 调用一些内置的钩子作为其执行过程的一部分。你可以在用户指南中找到钩子的完整列表。我们将使用`post_controller_constructor`钩子，它在我们的控制器被实例化之后，任何其他方法调用之前被立即调用。

我们通过在主配置文件中设置参数`enable_hooks`来启用应用程序中的钩子。

```
<?php
$config['enable_hooks'] = TRUE;
```

然后我们可以打开`config`目录中的`hooks.php`文件，并创建一个自定义钩子，如下面的代码所示:

```
<?php
$hook['post_controller_constructor'] = array(
    'class' => 'LanguageLoader',
    'function' => 'initialize',
    'filename' => 'LanguageLoader.php',
    'filepath' => 'hooks'
);
```

这定义了钩子，并提供了执行它所必需的信息。实际的实现将在`application/hooks`目录中的自定义类中创建。

```
<?php
class LanguageLoader
{
    function initialize() {
        $ci =& get_instance();
        $ci->load->helper('language');
        $ci->lang->load('message','english');
    }
}
```

在这里，我们不能使用`$this->lang`访问语言库，所以我们需要使用`get_instance()`函数获取 CI 对象实例，然后像前面一样加载语言。现在，语言文件将可用于我们应用程序的每个控制器，而无需手动将其加载到控制器中。

## 在不同语言之间切换

一旦我们建立了对多种语言的支持，就可以为用户提供每种语言的链接，通常是在我们应用程序的一个菜单中，用户可以单击该菜单并切换语言。会话或 cookie 值可用于跟踪活动语言。

让我们看看如何使用我们之前生成的 hooks 类来管理语言切换。首先我们需要创建一个类来切换语言；我们将为此使用一个单独的控制器，如下所示:

```
<?php
class LangSwitch extends CI_Controller
{
    public function __construct() {
        parent::__construct();
        $this->load->helper('url');
    }

    function switchLanguage($language = "") {
        $language = ($language != "") ? $language : "english";
        $this->session->set_userdata('site_lang', $language);
        redirect(base_url());
    }
}
```

然后我们需要定义链接来切换每种可用的语言。

```
<a href='<?php echo $base_url; ?>langswitch/switchLanguage/english'>English</a>
<a href='<?php echo $base_url; ?>langswitch/switchLanguage/french'>French</a>
```

每当用户选择一种特定的语言时，`LangSwitch`类的`switchLanguage()`方法会将选择的语言分配给会话，并将用户重定向到主页。

现在，活动语言将在会话中被更改，但它不会受到影响，直到我们加载活动语言的特定语言文件。我们还需要修改 hooks 类，从会话中动态加载语言。

```
<?php
class LanguageLoader
{
    function initialize() {
        $ci =& get_instance();
        $ci->load->helper('language');

        $site_lang = $ci->session->userdata('site_lang');
        if ($site_lang) {
            $ci->lang->load('message',$ci->session->userdata('site_lang'));
        } else {
            $ci->lang->load('message','english');
        }
    }
}
```

在`LanguageLoader`类中，我们获取活动语言并加载必要的语言文件，或者如果没有会话密钥，我们加载默认语言。我们可以在这个类中加载单一语言的多个语言文件。

## 结论

大多数全栈 PHP 框架都有多语言支持，这使我们能够轻松地用不同的语言呈现我们的应用程序界面。在本文中，我们看到了如何使用 CodeIgniter 提供多种语言。当然，还有其他构建多语言解决方案的方法，所以请随意讨论您在 CodeIgniter 甚至其他框架中实现多语言支持的最佳实践和经验。我期待着收到你的来信！

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章