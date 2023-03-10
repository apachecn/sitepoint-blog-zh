# 使用 phpQuery 的服务器端 HTML 处理

> 原文：<https://www.sitepoint.com/server-side-html-handling-using-phpquery/>

在我们日常的 web 开发任务中，我们有必要同时处理客户端和服务器端的代码。我们使用 PHP 编写业务逻辑，并生成要在用户浏览器中显示的 HTML。然后我们使用 jQuery 或 Prototype 等框架来提供客户端交互性。

现在考虑如何使用服务器端 PHP 代码来更改和操作生成的 HTML。phpQuery 是你能想到的最终解决方案。如果您还没有听说过 phpQuery，您可能会对本文感兴趣，因为我将向您简要介绍 phpQuery，并解释如何在实际项目中使用它。

## 什么是 phpQuery

> phpQuery 是一个基于 jQuery JavaScript 库的服务器端、可链接的、CSS3 选择器驱动的文档对象模型(DOM) API。

这是官方 [phpQuery 项目页面](http://code.google.com/p/phpquery/ "phpquery - phpQuery - jQuery port to PHP - Google Project Hosting")上给出的定义。如果您使用过 jQuery，那么您就会知道它是如何简化许多需要 DOM 操作的任务的。phpQuery 提供了与服务器端 PHP 代码完全相同的功能。您可以使用 echo 语句和类似的方法告别杂乱无章的 HTML 代码生成。

在 phpQuery 中，您将可以使用 jQuery 提供的大部分功能，这些功能可以大致分为下面提到的 4 个任务:

*   创建 DOM 元素
*   选择和操作元素
*   遍历 DOM
*   将输出打印到浏览器

您可以使用 phpQuery 提供的特性来执行任务，这就是所谓的“移植的 jQuery 节”先来看看特性:

*   **选择器**–根据给定条件查找元素。
*   **属性**–处理 DOM 元素的属性。
*   **遍历**–遍历所选元素列表。
*   **操作**–添加和删除所选元素的内容。
*   Ajax–创建服务器端 Ajax 请求。
*   **事件**–在所选元素上绑定 DOM 事件。
*   **实用程序**–支持其他功能的通用功能。

你可以从 code.google.com/p/phpquery 的项目页面下载 phpQuery 库。将文件夹复制到您的 web 服务器，您就可以开始了。安装就是这么简单，您可以执行`demo.php`文件开始安装。

## 如何使用 phpQuery

我将向您展示如何创建一个两列无序列表，其中奇数行和偶数行具有标题和不同的行颜色，如下图所示:

![](img/5876bc1f4452e3c8eaff116ae5cc305b.png)

首先，让我们使用 phpQuery 创建一个 HTML 文档:

```
<?php
require("phpQuery/phpQuery.php");
$doc = phpQuery::newDocument("<div/>");
```

上面的代码将创建一个带有`div`标签的基本 HTML 文档。该库提供了各种创建文档的方法；我用过最简单的一个，但是你可以在`demo.php`和文档中找到其他的。

现在我们需要创建一个无序列表，并将其添加到我们的 HTML 文档中。

```
<?php
...
$doc["div"]->append("<ul><li>Product Name</li><li>Price</li></ul>");
$products = array(
    array("Product 1","$30"),
    array("Product 2","$50"),
    array("Product 3","$20"));

foreach($products as $key=>$product) {
    $doc["div ul"]->append("<li>$product[0]</li><li>$product[1]</li>");
}
print $doc;
```

你可以看到我们现在有了无序列表。但所有元素都在一列中，这是默认设置。我们必须将列表中的偶数元素移到第二列。

```
<?php
...
$doc["div ul"]->attr("style", "width:420px;");
$doc["div ul"]->find("li:even")->attr("style","width:200px; float:left; padding:5px; list-style:none;");
$doc["div ul"]->find("li:odd")->attr("style","width:200px; float:left; padding:5px; list-style:none;");
```

我在这里使用 style 属性来定义我们的示例所需的 CSS 样式，但是不建议使用内联样式，除非真的需要。总是使用 CSS 类来添加样式。

现在，让我们使用 phpQuery 方法突出显示标题行和偶数行。

```
<?php
...
$doc["div ul"]->find("li:nth-child(4n)")->attr("style","background:#EEE; width:200px; float:left; padding:5px; list-style:none;");
$doc["div ul"]->find("li:nth-child(4n-1)")->attr("style","background:#EEE; width:200px; float:left; padding:5px; list-style:none;");
$doc["div ul"]->find("li:lt(1)")->attr("style","background:#CFCFCF; width:200px; float:left; padding:5px; list-style:none;");
```

我们已经完成了简单的例子，现在您应该知道如何使用 phpQuery 来简化服务器端的 HTML 生成。我们所做的一切几乎与用 jQuery 所做的一样，除了我们对`$doc`对象做了所有的动作。

## phpQuery 的重要性

尽管我解释了 phpQuery 的功能，但您一定想知道，当我们在客户端有 jQuery 时，为什么我们还需要这个库。我将使用一个实际的场景来展示 phpQuery 的重要性。

考虑以下情况:假设我们有一个如下所示的表，其中包含所有参加面试的 web 开发人员的信息。

![](img/58edeeb81bfb0c8a37cc1400bc72c73d.png)

下面是我们在这种情况下必须开发的需求列表:

*   考试分数超过 60 分的申请者应该用蓝色突出显示。
*   具有 3 年以上工作经验的申请人应在前面有一个标记为“申请高级软件工程师”的链接，其他申请人应具有“申请软件工程师”的链接。
*   The company has a salary structure based on experience:
    *   1 年——5000 美元
    *   两年——10，000 美元
    *   3 年——2 万美元
    *   3 年以上——50，000 美元

    对于符合标准的申请人,“薪金”列应以绿色突出显示。

输出应该是这样的:

![](img/1d00f6162ecd7172e57f5391a8cd75fe.png)

开发人员可能会提供以下解决方案来满足使用纯 PHP 代码的需求。

```
<?php
// retrieve applicants from database
//...

echo <<<ENDHTML
<div id="main">
 <div class="row_head">
  <div>Name</div>
  <div>Marks</div>
  <div>Experience</div>
  <div>Position</div>
  <div>Expected Salary</div>
 </div>
ENDHTML;
foreach ($applicants as $applicant) {
    echo '<div class="row">';
    echo "<div>" . $applicant["name"] . "</div>";
    echo '<div class="' . marksClass($applicant["marks"]) . '">' . $applicant["marks"] . "</div>";
    echo "<div>" . $applicant["experience"] . "</div>";
    echo "<div>" . positionLink($applicant["experience"]) . "</div>";
    echo '<div class="' . salaryClass($applicant["experience"], $applicant["salary"]) . '">' . $applicant["salary"] . "</div>";
    echo "</div>";
}
echo "</div>";

function marksClass($info) {
    return ($info > 60) ? "pass" : "fail";
}

function positionLink($experience) {
    return ($experience > 3)
        ? '<a href="#">Apply for Senior Software Engineer</a>'
        : '<a href="#">Apply for Software Engineer</a>';
}

function salaryClass($experience, $salary) {
    switch ($experience) {
        case 1:
            return ($salary < 5000) ? "accept" : "reject";
        case 2:
            return ($salary < 10000) ? "accept" : "reject";
        case 3:
            return ($salary < 20000) ? "accept" : "reject";
        default:
            return ($salary < 50000) ? "accept" : "reject";
    }
}
```

现在让我们用 phpQuery 来做，比较一下代码和优点。

```
<?php
require("phpQuery/phpQuery.php");
$doc = phpQuery::newDocument('<div id="main"></div>');
phpQuery::selectDocument($doc);

// retrieve applicants from database
//...

$doc["#main"]->append('
<div id="main">
 <div class="row_head">
  <div>Name</div>
  <div>Marks</div>
  <div>Experience</div>
  <div>Position</div>
  <div>Expected Salary</div>
 </div>');

foreach ($applicants as $key => $applicant) {
    $doc["#main"]->append('<div class="row" id="app_' . $key . '"></div>');
    foreach ($applicant as $field => $info) {
        $doc["#main"]->find("#app_" . $key)->append('<div class="_' . $field . '">' . $info . "</div>");
        if ($field == "experience") {
            $doc["#main"]->find("#app_" . $key)->append('<div style="width:400px" class="_position">-</div>');
        }
    }
}

addMarksClass($doc);
addSalaryClass($doc);
addPositionLink($doc);

print $doc;

function addMarksClass(&$doc) {
    $marks = pq("._marks");
    foreach ($marks as $appMark) {
        if (pq($appMark)->html() > 60) {
            pq($appMark)->addClass("pass");
        }
        else {
            pq($appMark)->addClass("fail");
        }
    }
}

function addSalaryClass(&$doc) {
    $marks = pq("._salary");
    foreach ($marks as $appMark) {
        $experience = pq($appMark)->parent()->find("._experience" )->html();
        $salary = pq($appMark)->html();

        switch ($experience) {
            case 1:
                pq($appMark)->addClass(
                    ($salary < 5000) ? "accept" : "reject"
                );
                break;
            case 2:
                pq($appMark)->addClass(
                    ($salary < 10000) ? "accept" : "reject"
                );
                break;
            case 3:
                pq($appMark)->addClass(
                    ($salary < 20000) ? "accept" : "reject"
                );
                break;
            default:
                pq($appMark)->addClass(
                    ($salary < 50000) ? "accept" : "reject"
                );
        }
    }
}

function addPositionLink(&$doc) {
    $experience = pq("._experience");
    foreach ($experiece as $appExp) {
        if (pq($appExp)->html() > 3) {
            pq($appExp)->parent()->find("._position")->html('<a href="#">Apply for Senior Software Engineer</a>');
        }
        else{
            pq($appExp)->parent()->find("._position")->html('<a href="#">Apply for Software Engineer</a>');
        }
    }
}
```

如果您已经掌握了使用 jQuery 的知识，那么 phpQuery 是很容易的。上面的大部分代码都是不言自明的。不过我想提一下,`pq()`指的是当前的文档。所有其他的都是 jQuery 函数。

尽管两者看起来很相似，但是使用 phpQuery 的代码提供了更好的质量和可扩展性。想想看，如果以后必须添加额外的功能，原始代码会有多脆弱。让我们假设我们想要基于工作经验添加额外的标记验证。在这种情况下，您必须添加另一个方法，并在`foreach`循环中分配返回的结果。这意味着你必须修改已经写好的代码，违反了[开闭原则](http://en.wikipedia.org/wiki/Open/closed_principle "Open/closed principle - Wikipedia, the free encyclopedia"):

> 软件实体(类、模块、函数等。)应该对扩展开放，但对修改关闭。

在第二个使用 phpQuery 的例子中，代码首先在没有任何验证的情况下生成，然后我们将表传递给每个函数，并将更改放入原始表中。每个函数都不影响其他函数，所以我们可以为任何新的需求编写一个新的函数，并在循环之外与其他函数一起使用。我们不修改已经存在的代码，这听起来不错，对吗？这叫做[装饰](http://en.wikipedia.org/wiki/Decorator_pattern "Decorator pattern - Wikipedia, the free encyclopedia"):

> 装饰模式是一种允许将行为动态添加到现有对象的设计模式。

## 摘要

我们通过介绍 phpQuery 特性及其重要性开始了本教程。在通过一个简单的例子学习了如何使用 phpQuery 之后，我们转到了实际例子，在这个例子中，phpQuery 对于提高代码质量变得更加重要。phpQuery 为我们提供了一个在服务器端使用 HTML 的新视角，我希望你能以不同的方式使用 phpQuery，并在下面的评论中分享你的个人经验。

## 分享这篇文章