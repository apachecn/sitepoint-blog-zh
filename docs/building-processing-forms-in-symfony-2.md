# 在 Symfony 2 中构建和处理表单

> 原文：<https://www.sitepoint.com/building-processing-forms-in-symfony-2/>

在本教程中，我们将看两个在 Symfony 2 中使用表单的例子。首先，我们将表单元素直接放在视图文件中，然后在控制器中手动处理表单处理*。第二，我们将使用 Symfony 表单系统以面向对象的方式声明表单，并使用 Symfony 处理和保存值。*

 *我们将致力于 Symfony 框架的简单安装。您可能知道，它附带了一个名为`AcmeDemoBundle`的默认包，我们将用它来说明如何使用表单。所有的最终代码都可以在我为本教程建立的库中找到。请随意跟随或看一眼最终的代码。

## 非实体形式

我们要看的第一个例子是如何处理在 Symfony 视图文件中声明为常规 HTML 元素的表单。我们将采取 3 个步骤来说明这一点:

1.  我们将创建一个包含表单 HTML 的视图文件
2.  我们将在名为`form1Action`的`WelcomeController`类中创建一个新的控制器方法，它将处理我们的业务逻辑(呈现视图、处理表单等)。
3.  然后，我们将创建一个简单的 route 条目，将一个 URL 路径映射到这个方法，这样我们就可以在浏览器中看到表单，并能够提交它。

让我们首先创建视图文件，它将在顶部显示我们的表单和提交的值。在位于`src/Acme/DemoBundle/Resources/views/Welcome`文件夹中的一个名为`form1.html.twig`的文件中(默认情况下，`Welcome`控制器类 Symfony 的对应文件夹)我有以下内容:

```
{% extends "AcmeDemoBundle::layout.html.twig" %}

{% block content %}

<h1>Form values</h1>

{% if name is defined %}
<p>Name: {{ name }} </p>
{% endif %}

<div>
    <form name="input" action="" method="post">

        Name:
        <input type="text" name="name"/>

        <input type="submit" name="submit" value="Submit">

    </form>
</div>

{% endblock %}
```

上面，我扩展了`DemoBundle`中的默认布局，这样我就不会看到一个非常白的页面。剩下的也很基本:声明一个简单的 HTML 表单，该表单向自身发送信息，并显示表单上方唯一的文本字段的值(该值将在名为`name`的变量中处理后从控制器传递过来)。

接下来，让我们声明负责显示该视图并处理其中的表单的控制器方法。在`WelcomeController`类文件中，我做了两件事:

1.  我使用 Symfony `Request`类来帮助我们轻松访问提交的值:

    ```
    use Symfony\Component\HttpFoundation\Request; 
    ```

    这超出了类声明的范围。

2.  我有以下方法:

```
public function form1Action()
    {

        $post = Request::createFromGlobals();

        if ($post->request->has('submit')) {
            $name = $post->request->get('name');
        } else {
            $name = 'Not submitted yet';
        }

        return $this->render('AcmeDemoBundle:Welcome:form1.html.twig', array('name' => $name));

    }
```

在这个方法中，我使用在`$request`对象中找到的`createFormGlobals()`方法来收集 PHP 超全局变量(包括`$_POST`值)。然后我检查提交的值是否包含`submit`(基本上是我们的提交按钮)，如果包含，我检索`name`元素的值并将其传递给我们刚刚创建的视图。简单。

最后，让我们定义一个路由规则来匹配这个控制器方法的 URL 路径。在位于`src/Acme/DemoBundle/Resources/config`文件夹的`routing.yml`文件中，我添加了以下规则:

```
_form1:
		pattern:  form1
		defaults: { _controller: AcmeDemoBundle:Welcome:form1 }
```

这将把`form1/`路径映射到新的控制器方法。

就这样，你可以测试一下。需要记住的一点是，在处理实体或任何类型的网站内容时，这并不是一种值得推荐的处理表单的方式。Symfony 附带了一些很好的类，这些类会让你做起来更容易。接下来我们将看到其中一些是如何工作的。

# Symfony 实体和形式

接下来，我们将看看如何显示和处理一个名为`Article`的 Symfony 实体的表单(我已经定义了它，您可以在存储库中找到它)。对于实体如何工作的复习，请查看我们关于 Symfony 框架系列的第二部分[。](https://www.sitepoint.com/building-web-app-symfony-2-development/)

我将展示一个非常简单的例子，展示如何显示一个表单，然后使用 *Symfony 方式*将新的`Article`实体保存到数据库中。我们将主要处理 5 个文件:controller 类文件，我们在其中添加了两个新方法来显示表单和显示一个简单的确认页面；因为我们需要映射 URLs 显示表单和确认的两个视图文件；和一个用于构建表单的`ArticleType.php`文件。

我们先从后者说起。虽然您也可以直接在控制器内部构建表单，但是使用一个单独的*类型的*文件会使它更加可重用，所以我们将这样做。在我们的`src/Acme/DemoBundle/Form`文件夹中，我有一个名为`ArticleType`的文件，内容如下:

```
<?php

namespace Acme\DemoBundle\Form;

use Symfony\Component\Form\AbstractType;
use Symfony\Component\Form\FormBuilderInterface;
use Symfony\Component\OptionsResolver\OptionsResolverInterface;

class ArticleType extends AbstractType
{

    public function buildForm(FormBuilderInterface $builder, array $options)
    {
        $builder->add('title', 'text', array('label' => 'Title'))
            ->add('body', 'textarea')
            ->add('save', 'submit');
    }

    public function getName()
    {
        return 'article';
    }

    public function setDefaultOptions(OptionsResolverInterface $resolver)
    {
        $resolver->setDefaults(array(
            'data_class' => 'Acme\DemoBundle\Entity\Article',
        ));
    }

}
```

上面，我扩展了默认的 Symfony form builder 类来定义我自己的表单。`buildForm()`方法负责实际构建表单。在内部，我向`$builder`对象添加了各种表单元素，这些元素将适合我们的文章实体，并以实体属性命名。有关可用表单元素的更多信息，您可以[查阅文档](http://symfony.com/doc/current/book/forms.html#built-in-field-types)。

`getName()`方法只返回表单名称*类型*本身，而使用`setDefaultOptions()`方法我们指定使用该表单的实体类(作为`data_class`键的值)。就这样，我们有了表单定义，让我们在控制器中构建它，并在页面上显示它。

回到`WelcomeController`类，我已经在顶部包含了另外两个类引用(对于我们刚刚创建的文章实体和表单类型):

```
use Acme\DemoBundle\Entity\Article;
use Acme\DemoBundle\Form\ArticleType;
```

接下来，我添加了以下两个方法:

```
public function form2Action(Request $request)
    {

        $article = new Article();

        $form = $this->createForm(new ArticleType(), $article);

        $form->handleRequest($request);

        if ($form->isValid()) {
            $em = $this->getDoctrine()->getManager();
            $em->persist($article);
            $em->flush();

            $session = $this->getRequest()->getSession();
            $session->getFlashBag()->add('message', 'Article saved!');

            return $this->redirect($this->generateUrl('_form2saved'));
        }

        return $this->render('AcmeDemoBundle:Welcome:form2.html.twig', array('form' => $form->createView()));

    }

    public function form2savedAction()
    {

        return $this->render('AcmeDemoBundle:Welcome:form2saved.html.twig');

    }
```

`form2Action()`方法负责显示和处理表单以创建新的`Article`实体。首先，它实例化该类的一个新的空对象。然后，它使用我们之前定义的表单类型为它创建一个新的表单。接下来，如果表单已经提交，它将处理表单，如果没有，它将呈现`form2.html.twig`视图文件，并传递呈现的 HTML 表单以供显示。让我们现在创建该文件，然后我们再回来看看表单处理会发生什么。

就在我们创建`form1.html.twig`的地方旁边，我有包含以下内容的`form2.html.twig`:

```
{% extends "AcmeDemoBundle::layout.html.twig" %}

{% block content %}

{{ form(form) }}

{% endblock %}
```

再简单不过了。它只是呈现传递给它的表单。让我们快速地将下面的路线添加到我们的`routing.yml`文件中，这样我们就可以在浏览器中看到它:

```
_form2:
	    pattern:  form2
	    defaults: { _controller: AcmeDemoBundle:Welcome:form2 }
```

现在我们可以将浏览器指向`form2/`路径，看到一个简单(但相当难看)的表单。现在进行加工。

正如我们前面看到的，`form2Action()`方法有一个传递给它的参数:`$request`对象。所以当我们提交这个表单时，它使用我们构建的`$form`对象的`handleRequest()`方法来查看表单是否已经提交(如果它的值存在于`$request`超全局变量中)。如果已经提交，它将对其进行标准验证，并将新对象保存到数据库中(我们最初实例化的对象已经自动填充了表单值)。最后，它保存一个 *one request only* flash 消息并重定向到另一个页面，该路径是基于另一个路由构建的:

```
_form2saved:
	    pattern:  form2saved
	    defaults: { _controller: AcmeDemoBundle:Welcome:form2saved }
```

路线触发了我们在`WelcomeController`类中声明的`form2savedAction()`方法，该方法呈现了`form2saved.html.twig`视图。在这个视图中，我现在所做的就是查看会话 *flashBag* 中是否有消息，并将其打印出来:

```
{% extends "AcmeDemoBundle::layout.html.twig" %}

	{% block content %}

		{% for flashMessage in app.session.flashbag.get('message') %}
		    <p>{{ flashMessage }}</p>
		{% endfor %}

	{% endblock %}
```

仅此而已。现在您可以刷新表单并提交它。您应该被重定向到一个显示确认消息的简单页面。如果您检查数据库，应该已经保存了一条新的文章记录。

## 结论

在本文中，我们已经了解了 Symfony 2 表单系统非常简单的用法。首先，我们看到了如何使用视图中打印的常规 HTML 表单元素来处理表单提交。然后，我们看到了如何利用 Symfony 表单系统的能力在代码中定义表单，以便将值作为一个实体进行处理和持久化。

每种技术都有其使用案例。如果您正在对数据或内容执行 CRUD 操作，最好使用第二个选项。Symfony 在提取数据方面非常强大，这使得表单构建非常容易。但是，如果您需要对不保存任何内容的表单进行一些“动态”处理，您可以将表单直接构建到视图中，然后在控制器中捕获提交的值。

有问题吗？评论？你想知道更多吗？让我们知道！

## 分享这篇文章*