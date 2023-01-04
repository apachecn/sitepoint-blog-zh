# Drupal 应用程序中的 AngularJS

> 原文：<https://www.sitepoint.com/angularjs-drupal-apps/>

Angular.js 是目前在客户端设计应用程序的热门新事物。好吧，它不再那么新了，但肯定仍然很热，特别是现在它被谷歌使用和支持。它将 JavaScript 框架的概念提升到了一个全新的水平，并为开发可以在浏览器中运行或作为混合移动应用程序运行的丰富的动态应用程序提供了一个很好的基础。

![logo_drupal](img/53c06f5d4189f9b7150b0336971f5f32.png)

在本文中，我将向您展示在 Drupal 7 站点中使用它的一些魔力的一种简洁的小方法。这是一个简单的功能，但足以展示 Angular.js 有多强大，以及在 Drupal 等大型服务器端 PHP 框架中的潜在用例。那我们在做什么？

我们将创建一个列出一些节点标题的块。了不起。但是，这些节点标题将使用 Angular.js 异步加载，并且它们上面将有一个 textfield 来过滤/搜索节点(也是异步完成的)。作为奖励，我们还将使用一个小型开源 Angular.js 模块，当我们点击标题时，它将允许我们在一个对话框中查看一些节点信息。

所以让我们开始吧。像往常一样，我们在教程中写的所有代码都可以在[这个资源库](https://github.com/sitepoint-examples/d7-angular)中找到。

## 佐料

为了模拟这种情况，我们需要以下内容:

*   自定义 Drupal 模块
*   为查询节点创建端点的 Drupal `hook_menu()`实现
*   一个 Drupal 主题函数，使用一个模板文件来呈现我们的标记
*   一个自定义的 Drupal 块，用于调用主题函数并将标记放在我们想要的位置
*   一个小 Angular.js app
*   对于额外部分，ngDialog 角度模块

## 该模块

让我们开始创建一个名为`Ang`的定制模块。像往常一样，在`modules/custom`文件夹中创建一个`ang.info`文件:

```
name = Ang
description = Angular.js example on a Drupal 7 site.
core = 7.x
```

…和一个包含我们大多数 Drupal 相关代码的`ang.module`文件。在这个文件内部(不要忘记开始的`<?php`标签)，我们可以从[的 hook_menu()](https://api.drupal.org/api/drupal/modules%21system%21system.api.php/function/hook_menu/7) 实现开始:

```
/**
 * Implements hook_menu().
 */
function ang_menu() {
  $items = array();

  $items['api/node'] = array(
    'access arguments' => array('access content'),
    'page callback'     => 'ang_node_api',
    'page arguments' => array(2),
    'delivery callback' => 'drupal_json_output'
  );

  return $items;
}
/**
 * API callback to return nodes in JSON format
 *
 * @param $param
 * @return array
 */
function ang_node_api($param) {

  // If passed param is node id
  if ($param && is_numeric($param)) {
    $node = node_load($param);
    return array(
      'nid' => $param,
      'uid' => $node->uid,
      'title' => check_plain($node->title),
      'body' => $node->body[LANGUAGE_NONE][0]['value'],
    );
  }
  // If passed param is text value
  elseif ($param && !is_numeric($param)) {
    $nodes = db_query("SELECT nid, uid, title FROM {node} n JOIN {field_data_body} b ON n.nid = b.entity_id WHERE n.title LIKE :pattern ORDER BY n.created DESC LIMIT 5", array(':pattern' => '%' . db_like($param) . '%'))->fetchAll();
    return $nodes;
  }
  // If there is no passed param
  else {
    $nodes = db_query("SELECT nid, uid, title FROM {node} n JOIN {field_data_body} b ON n.nid = b.entity_id ORDER BY n.created DESC LIMIT 10")->fetchAll();
    return $nodes;
  }
}
```

在`hook_menu()`中，我们声明了一个路径(`api/node`)，任何有权限查看内容的人都可以访问它，它将返回在回调函数`ang_node_api()`中创建的 JSON 输出。后者传递了一个参数，即在 URL 中我们声明的路径之后的内容:`api/node/[some-extra-param]`。我们需要这个论点，因为我们希望通过这个端点实现三件事:

1.  返回最近 10 个节点的列表
2.  返回具有特定 id 的节点(例如`api/node/5`)
3.  返回标题中有传递参数的所有节点(例如，`api/node/chocolate`，其中`chocolate`是一个或多个节点标题的一部分)

这是在第二个函数中发生的。针对三种情况检查参数:

*   如果它存在并且是数字的，我们加载相应的节点并从该节点返回一个包含一些基本信息的数组(记住，这将是 JSON 格式)
*   如果它存在但不是数字，我们执行数据库查询并返回所有标题包含该值的节点
*   在任何其他情况下(本质上意味着缺少参数)，我们查询 db 并返回最新的 10 个节点(仅作为示例)

显然，这个回调可以进一步改进和巩固(错误处理等)，但出于演示的目的，它将工作得很好。现在让我们创建一个主题，它使用一个模板文件和一个将呈现它的自定义块:

```
/**
 * Implements hook_theme().
 */
function ang_theme($existing, $type, $theme, $path) {
  return array(
    'angular_listing' => array(
      'template' => 'angular-listing',
      'variables' => array()
    ),
  );
}

/**
 * Implements hook_block_info().
 */
function ang_block_info() {

  $blocks['angular_nodes'] = array(
    'info' => t('Node listing'),
  );

  return $blocks;
}

/**
 * Implements hook_block_view().
 */
function ang_block_view($delta = '') {

  $block = array();

  switch ($delta) {
    case 'angular_nodes':
      $block['subject'] = t('Latest nodes');
      $block['content'] = array(
        '#theme' => 'angular_listing',
        '#attached' => array(
          'js' => array(
            'https://ajax.googleapis.com/ajax/libs/angularjs/1.2.26/angular.min.js',
            'https://ajax.googleapis.com/ajax/libs/angularjs/1.2.26/angular-resource.js',
            drupal_get_path('module', 'ang') . '/lib/ngDialog/ngDialog.min.js',
            drupal_get_path('module', 'ang') . '/ang.js',
          ),
          'css' => array(
            drupal_get_path('module', 'ang') . '/lib/ngDialog/ngDialog.min.css',
            drupal_get_path('module', 'ang') . '/lib/ngDialog/ngDialog-theme-default.min.css',
          ),
        ),
      );
      break;
  }

  return $block;
}

/**
 * Implements template_preprocess_angular_listing().
 */
function ang_preprocess_angular_listing(&$vars) {
  // Can stay empty for now.
}
```

这里有四个简单的函数:

1.  使用 [hook_theme()](https://api.drupal.org/api/drupal/modules%21system%21system.api.php/function/hook_theme/7) 我们创建了我们的`angular_listing`主题，它使用了我们不久将创建的`angular-listing.tpl.php`模板文件。
2.  在 [hook_block_info()](https://api.drupal.org/api/drupal/modules%21block%21block.api.php/function/hook_block_info/7) 中，我们定义了新的块，它的显示由下一个函数控制。
3.  使用 [hook_block_view()](https://api.drupal.org/api/drupal/modules%21block%21block.api.php/function/hook_block_view/7) 我们定义了我们的块的输出:一个使用`angular_listing`主题的可呈现数组，并且附加了各自的 javascript 和 css 文件。我们从 Google CDN 加载 Angular.js 库文件，在`ang.js`中我们将编写 JavaScript 逻辑，在`/lib/ngDialog`文件夹中我们有用于创建对话框的库。您可以下载后者，并按照所描述的结构将其放入模块中。你可以在[库](https://github.com/sitepoint-examples/d7-angular)或者图书馆网站上找到这些文件。
4.  最后一个函数是模板的模板预处理器，以确保变量被传递给它(即使我们实际上没有使用任何变量)。

如您所见，这是标准的样板 Drupal 7 代码。在启用模块或试用这段代码之前，让我们快速创建模板文件，这样 Drupal 就不会出错。在名为`angular-listing.tpl.php`的文件中，添加以下内容:

```
<div ng-app="nodeListing">

	   <div ng-controller="ListController">

	     <h3>Filter</h3>
	     <input ng-model="search" ng-change="doSearch()">

	      <ul>
	        <li ng-repeat="node in nodes"><button ng-click="open(node.nid)">Open</button> {{ node.title }}</li>
	      </ul>

	     <script type="text/ng-template" id="loadedNodeTemplate">
	     <h3>{{ loadedNode.title }}</h3>
	     {{ loadedNode.body }}
	     </script>

	    </div>

	</div>
```

这里我们有一些用 Angular.js 指令和表达式填充的简单 HTML。此外，我们有一个由`ngDialog`模块使用的`<script>`标签作为对话框的模板。在试图解释这一点之前，让我们也创建我们的`ang.js`文件，并添加我们的 javascript 到其中(因为这两者是如此的联系在一起):

```
angular.module('nodeListing', ['ngResource', 'ngDialog'])

  // Factory for the ngResource service.
  .factory('Node', function($resource) {
    return $resource(Drupal.settings.basePath + 'api/node/:param', {}, {
      'search' : {method : 'GET', isArray : true}
    });
  })

  .controller('ListController', ['$scope', 'Node', 'ngDialog', function($scope, Node, ngDialog) {
    // Initial list of nodes.
    $scope.nodes = Node.query();

    // Callback for performing the search using a param from the textfield.
    $scope.doSearch = function() {
      $scope.nodes = Node.search({param: $scope.search});
    };

    // Callback to load the node info in the modal
    $scope.open = function(nid) {
      $scope.loadedNode = Node.get({param: nid});
      ngDialog.open({
        template: 'loadedNodeTemplate',
        scope: $scope
      });
    };

}]);
```

好吧。现在我们有了所有的东西(确保你也按照我们上面写的可渲染数组的`#attached`键的要求添加了`ngDialog`文件)。您可以启用该模块，并将该模块放在您可以看到的显著位置。如果一切顺利，你应该得到 10 个节点标题(如果你有这么多)和上面的搜索框。搜索将对我们的端点服务器进行 AJAX 调用，并返回其他节点标题。单击它们将打开一个对话框，上面有节点标题和主体。太好了。

但是让我解释一下 Angular.js 方面发生了什么。首先，我们定义一个名为`nodeListing`的 Angular.js 应用，以`ngResource`(angular . js 服务负责与服务器通信)和`ngDialog`为依赖。这个模块也在我们的模板文件中被声明为主应用程序，使用的是`ng-app`指令。

在这个模块中，我们为一个名为`Node`的新服务创建了一个工厂，它返回一个`$resource`。后者实际上是到服务器上数据的连接(通过我们的端点访问 Drupal 后端)。除了它的默认方法之外，我们定义了另一个名为`.search()`的方法，它将发出一个 GET 请求并返回一个结果数组(我们需要一个新的方法，因为默认的`.get()`不接受一个结果数组)。

在这个工厂下面，我们定义了一个名为`ListController`的控制器(也是使用`ng-controller`指令在模板文件中声明的)。这是我们唯一的控制器，它的范围将适用于所有的模板。我们在控制器内部做了一些事情:

1.  我们使用`query()`方法从资源中加载节点。我们不传递参数，所以我们将获得站点上最新的 10 个节点(如果您记得我们的端点回调，请求将被发送到`/api/node`)。我们将结果附加到一个名为`nodes`的变量中。在我们的模板中，我们使用`ng-repeat`指令遍历这个数组，并列出节点标题。此外，我们为每个按钮创建了一个触发回调的`ng-click`指令`open(node.nid)`(在第 3 点有更多相关内容)。
2.  仍然看一下模板，在这个清单上面，我们有一个输入元素，它的值将使用`ng-model`指令绑定到作用域。但是使用`ng-change`指令，每当用户在文本字段中输入或删除内容时，我们就调用作用域上的函数(`doSearch()`)。这个函数在控制器中定义，负责使用用户在文本字段中输入的参数(变量`search`)在我们的端点上执行搜索。随着搜索的进行，结果会自动填充到模板中。
3.  最后，对于额外的部分，我们定义了`open()`方法，它将一个节点 id 作为参数，从我们的端点请求节点。按下按钮，这个回调函数打开对话框，该对话框使用 id 为`loadedNodeTemplate`的`<script>`标签中定义的模板，并将控制器的当前范围传递给它。如果我们转向模板文件，我们会看到对话框模板只是输出节点的标题和主体。

## 结论

您可以自己看到我们为完成这个简洁的功能而编写的代码量。大部分其实是样板文件。一个非常快速的节点查询块，它异步地提供结果，并具有所有的优点。如果你知道 Angular.js，你可以想象进一步增强 Drupal 体验的可能性。

现在，你有兴趣了解 Angular.js 和 Drupal 之间的爱情吗？你会做任何不同的事情吗？请在下面的评论中告诉我们！

## 分享这篇文章