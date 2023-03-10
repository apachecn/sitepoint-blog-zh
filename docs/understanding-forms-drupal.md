# 理解 Drupal 中的表单

> 原文：<https://www.sitepoint.com/understanding-forms-drupal/>

如今，Web 应用程序正被用于各种行业。他们需要接收和处理大量用户数据。数据的获取是通过 HTML 表单进行的，它允许你创建不同类型的输入字段，如文本框、单选按钮等。使用 HTML 标签。如果直接用 HTML 代码编写，这些表单很难维护或更新。Drupal 提供了一种非常好的方法，可以直接在 PHP 代码中定义表单，而表单的生成是由 Drupal 自己完成的。这使您可以轻松地创建和维护表单。在本文中，我们将了解如何在 Drupal 中创建表单。最终的代码可以从 [Github](https://github.com/phpmasterdotcom/drupalforms1) 中克隆。

## drupal_get_form 函数——Drupal 中的表单生成

Drupal 中表单创建的全部魔力都是由函数`drupal_get_form`执行的。这个函数向 Drupal 系统返回一个可呈现的表单数组。它将一个参数作为 form_id:标识表单的惟一标识符，如果存在同名的函数，那么调用该函数来构建表单。你可以通过链接【https://api.drupal.org/api/drupal/includes! 了解更多关于`drupal_get_form`的信息 form.inc/function/drupal_get_form/7

## 在 Drupal 中创建基本表单

让我们首先创建一个小模块，它将创建一个菜单回调函数，然后在这个菜单回调函数上使用`drupal_get_form`函数创建一个表单。要创建一个模块，在 Drupal 安装中添加一个文件夹`sites\all\modules\drupalform`，并添加两个文件:`drupalform.info`和`drupalform.module`，代码如下:

#### drupalform.info

```
name = drupalform
description = This module creates a form using Drupal.
core = 7.x
```

#### drupalform.module

```
<?php
/**
 * @file
 * This is the main module file.
 */

 /**
 * Implements hook_help().
 */
function drupalform_help($path, $arg) {

  if ($path == 'admin/help#rupalform') {
    $output = '<h3>' . t('About') . '</h3>';
    $output .= '<p>' . t('The drupalform module shows how to create forms using Drupal.') . '</p>';
    return $output;
  }
}
```

使用上面的代码，您应该能够在可用模块列表中看到您的模块，并且应该能够启用它，如下所示。如果这些步骤看起来神秘或复杂，请参见我的上一篇教程，以获得好的 [Drupal 模块介绍](https://www.sitepoint.com/building-drupal-7-module-show-latest-nodes/)。

![](img/e038b1ecc9189cdb5e50205969a1a873.png)

一旦我们完成了这些，我们将使用`hook_menu`添加一个菜单回调，并在页面上创建一个表单。其代码如下所示

```
/**
* Implementation of hook_menu().
*/
function drupalform_menu() {
  $items['drupalform/form1'] = array(
        'type' => MENU_CALLBACK,
        'access arguments' => array('access content'),
        'page callback' => 'drupal_get_form',
        'page arguments'=>array('drupalform_form1'));

  return $items;
}

function drupalform_form1() {
    $form = array();

    $form['name']=array(
        '#type'=>'textfield',
        '#title'=>t('Enter your name'),
        '#description'=>t('Your first name goes here')
      );
    $form['last_name']=array(
        '#type'=>'textfield',
        '#title'=>t('Enter your Last name'),
        '#description'=>t('Your Last name goes here')
      );

     $form['email']=array(
        '#type'=>'textfield',
        '#title'=>t('Enter your email'),
        '#description'=>t('Your email goes here')
      );

    $form['country']=array(
        '#type'=>'select',
        '#title'=>t('Select your country'),
        '#options'=>array('USA','UK','France','Japan')
      );

    $form['submit']=array(
        '#type'=>'submit',
        '#value'=>t('Submit')
      );

      return $form;
}
```

在上面实现`hook_menu`的代码中，我们创建了一个菜单项，它的`page_callback`是`drupal_get_form`，它的参数是`form_id`“drupalform _ form 1”。函数`drupalform_form1`返回我们想要创建的表单数组。

在函数`drupalform_form1`中，我们创建了三个文本字段:姓名、姓氏、电子邮件和一个国家选择框。在选择框中，我们用一个数组指定国家的选项。我们还添加了一个提交按钮，因此用户可以实际提交表单。现在您应该能够转到您的菜单 url: `<your drupal base url>/drupalform/form1`并看到如下表单

![](img/4b2c270a4e7fbf472c72afa916d962cf.png)

## 验证 Drupal 表单

Drupal 大量使用命名约定来标识对哪些事件调用哪些函数。完整的 Drupal hook 系统基于命名约定。类似地，被调用来验证表单数据的函数必须遵循命名约定`<form_id>_validate`，并且它将作为变量传递给`form_state`。

因此，对于我们的表单，函数名将是:
`drupalform_form1_validate($form, $form_state)`。

用户输入的值将出现在数组`$form_state['values']`中，其 id 与您在表单中指定的 id 相同。因此，如果我们必须对表单进行验证，函数将是:

```
function drupalform_form1_validate($form, $form_state) {

  if(empty($form_state['values']['name']))
     form_set_error('name','Name cannot be empty');
  else if(empty($form_state['values']['last_name']))
     form_set_error('last_name','Last name cannot be empty');
  else if(filter_var($form_state['values']['email'], FILTER_VALIDATE_EMAIL) == false)
    form_set_error('email','Email is not valid');
}
```

在上面的函数中，我们检查姓名和姓氏是否不为空，以及电子邮件是否有效。如果不是这样，我们使用 Drupal 函数`form_set_error`设置一个表单错误。这将向用户显示错误，并且不会提交表单。

如果您通过输入空名称来提交表单，您应该会在屏幕上看到以下错误:

![](img/79dfcc2f8363f46c5a6c578a9313e081.png)

## 提交 Drupal 表单

与验证函数类似，提交函数也是基于命名约定调用的，函数名应该是`<form_id>_submit`。同样，变量`$form_state`将被传递给它。因此，我们的提交函数将被定义为:

```
function drupalform_form1_submit($form, $form_state) {
   //Depending on the type of form you can add the logic
   //to store the details of the form 
   //by adding it in a Drupal table.
   //or sending a mail to the admin
   //Storing in a file
   //or pass it to some other service
   drupal_set_message("Form has been submitted");
}
```

现在，如果我们通过了所有有效字段，您将在屏幕上看到以下消息。

![](img/51781afbed829aa32c63092c0b744a67.png)

## 在 Drupal 表单中使用字段集来分隔元素。

有时当表单比较大并且有很多字段时，你可能想把它分成小的部分，这样表单对用户来说更有逻辑性。为此，您可以在 Drupal 中创建字段集来创建字段组。

要创建一个名为`basicdetails`的字段集来保存我们上面定义的字段，我们必须如下更新`drupalform_form1`

```
function drupalform_form1() {
    $form = array();

     $form['basicdetails']=array(
        '#type'=>'fieldset',
        '#title'=>t('Enter your Basic details below'),
        '#description'=>t('These are all madatory')
      );

        $form['basicdetails']['name']=array(
            '#type'=>'textfield',
            '#title'=>t('Enter your name'),
            '#description'=>t('Your first name goes here')
          );
        $form['basicdetails']['last_name']=array(
            '#type'=>'textfield',
            '#title'=>t('Enter your Last name'),
            '#description'=>t('Your Last name goes here')
          );

         $form['basicdetails']['email']=array(
            '#type'=>'textfield',
            '#title'=>t('Enter your email'),
            '#description'=>t('Your email goes here')
          );

    $form['submit']=array(
        '#type'=>'submit',
        '#value'=>t('Submit')
      );

      return $form;
}
```

这将创建一个单独的字段集，如下所示

![](img/3fb31fa5ae216e7dadca9c077e322e2c.png)

## 可以在 Drupal 表单中使用的不同表单元素

Drupal 提供了大量不同类型的字段，我们可以在表单中使用它们。我将更新我们的表单，通过为地址详细信息和附加详细信息再创建两个字段集来使用其中的一些内容。
更新后的代码如下:

```
function drupalform_form1() {
    $form = array();

     $form['basicdetails']=array(
        '#type'=>'fieldset',
        '#title'=>t('Enter your Basic details below'),
        '#description'=>t('These are all madatory')
      );

        $form['basicdetails']['name']=array(
            '#type'=>'textfield',
            '#title'=>t('Enter your name'),
            '#description'=>t('Your first name goes here')
          );
        $form['basicdetails']['last_name']=array(
            '#type'=>'textfield',
            '#title'=>t('Enter your Last name'),
            '#description'=>t('Your Last name goes here')
          );

         $form['basicdetails']['email']=array(
            '#type'=>'textfield',
            '#title'=>t('Enter your email'),
            '#description'=>t('Your email goes here')
          );

  $form['addressdetails']=array(
        '#type'=>'fieldset',
        '#title'=>t('Enter your Address details below'),
        '#description'=>t('These are all madatory')
      );

        $form['addressdetails']['country']=array(
            '#type'=>'select',
            '#title'=>t('Select your country'),
            '#options'=>array('USA','UK','France','Japan')
          );
        $form['addressdetails']['city']=array(
            '#type'=>'textfield',
            '#title'=>t('Enter your city'),
            '#description'=>t('Your city name goes here')
          );
        $form['addressdetails']['localaddress']=array(
            '#type'=>'textarea',
            '#title'=>t('Enter address'),
            '#description'=>t('Your Address name goes here')
          );

     $form['additionaldetails']=array(
        '#type'=>'fieldset',
        '#title'=>t('Enter your other details below'),
        '#description'=>t('These are all optional')
      );

        $form['additionaldetails']['gender']=array(
            '#type'=>'radios',
            '#title'=>t('Gender'),
            '#options'=>array('Male','Female')
          );

        $form['additionaldetails']['suscribtion']=array(
            '#type'=>'checkboxes',
            '#title'=>t('I want to subscribe for'),
            '#options'=>array('Email newsletter','Offer vouchers')
          );

        $form['additionaldetails']['birthdate']=array(
            '#type'=>'date',
            '#title'=>t('Birthdate'),
          );

        $form['#attributes']['enctype'] = 'multipart/form-data';

         $form['additionaldetails']['picture']=array(
            '#type'=>'file',
            '#title'=>t('Upload your picture'),
          );

    $form['submit']=array(
        '#type'=>'submit',
        '#value'=>t('Submit')
      );

      return $form;
}
```

这是新字段集的外观:

![](img/bdce239bc4e71443afa933efe11a70ca.png)

## 结论

Drupal 帮助您在模块内部创建和处理表单。Drupal 提供的 API 使得添加或修改表单变得非常简单，因为表单是数组，甚至验证和提交都在不同的函数中进行。这种模块化方法使模块中的表单代码保持整洁，易于维护。如果您使用 Drupal 表单 API，您也不需要为表单的 HTML 细节而烦恼——所有的 HTML 都是自动生成的。祝您在 Drupal 模块中创建下一个表单愉快！

如果您想让我们介绍更具体的使用案例或更详细的信息，或者只是有反馈，请在下面的评论中告诉我们！

## 分享这篇文章