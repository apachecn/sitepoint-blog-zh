# 懒惰 PHP:第 3 部分

> 原文：<https://www.sitepoint.com/lazy-php-part-3/>

继[上一篇关于 PHP 懒惰的博客](https://www.sitepoint.com/blog/)，
之后，一些更懒惰的初始化，这次应用于类加载。

在大多数编程语言中，对象在性能方面是“昂贵的”。在 PHP 中，由于 PHP 在页面请求之间放弃了内存(见[第一部分](https://www.sitepoint.com/blog/))以及其他一些东西，所以“对象的代价”更加明显。

为了演示这一点，这里有一个简单的表单控制类(大致基于 [WACT](http://wact.sf.net) 中的表单控制器)；

```
 class FormController {
    var $initial_action;
    var $invalid_action;
    var $valid_action;
    var $validator;
    /**
    * This action will be performed if the form has not yet
    * been posted
    */
    function registerInitialAction(& $action) {
        $this->initial_action = & $action;
    }
    /**
    * This action is performed if the form IS posted by
    * invalid data was submitted
    */
    function registerInvalidAction(& $action) {
        $this->invalid_action = & $action;
    }
    /**
    * This action is performed if the form IS posted and
    * the submitted data was validated
    */
    function registerValidAction(& $action) {
        $this->valid_action = & $action;
    }
    /**
    * Registers the object for validation
    */
    function registerValidator(& $validator) {
        $this->validator = & $validator;
    }
    /**
    * Run the form controller
    */
    function run() {
        # Has the form been posted?
        if ( !strcasecmp($_SERVER['REQUEST_METHOD'], 'POST') ) {

            # No - perform the initial action
            $this->initial_action->perform();

        } else {

            # Yes - need to validate
            if ( ! $this->validator->isValid() ) {

                # Invalid data - perform the invalid action
                $this->invalid_action->perform();

            } else {

                # All OK - perform the valid action
                $this->valid_action->perform();
            }
        }
    }
}

```

为了使用这个控制器，我定义了自己的“action”类，其中包含了 perform 方法。

```
 class UserUpdateInitial {
    function perform() {
        // display the HTML form
    }
} 

```

和

```
 class UserUpdateValid {
    function perform() {
        // pull data from $_POST and update database
        // display a some HTML saying "success"
    }
} 

```

然后我可以使用表单控制器，比如:

```
 < ?php
// Load all the necessary classes
require_once 'lib/controller/formcontroller.php';
require_once 'lib/action/userupdateinitial.php';
require_once 'lib/action/userupdateinvalid.php';
require_once 'lib/action/userupdatevalid.php';
require_once 'lib/validator/validator.php';

// Create the FormController
$F = & new FormController();

// Register the actions
$F->registerInitialAction(new UserUpdateInitial());
$F->registerInvalidAction(new UserUpdateInvalid());
$F->registerInvalidAction(new UserUpdateValid());

// Validation
$V = & new Validator();
$V->addRule(new SizeRule('username',1,15));
$V->addRule(new MatchRule('password','confirmpassword'));
$F->registerValidator($V);

// Run the form controller
$F->run();
?> 

```

如果这超出了您的理解范围，请花点时间检查一下上面的 FormController::run()方法，您应该会看到这两者是如何结合在一起的。

验证器类和“规则”我将作为虚构的代码留下来，以保持重点。

到目前为止一切顺利。表单控件和验证类现在是我可以在任何(简单)表单中重用的代码，允许我专注于开发特定于该表单的“动作”。

但是说到效率，就有问题了。如果你看一下`FormController::run()`，你会注意到一些事情；

*   Validator 类仅在表单被实际提交时使用。在表单的初始状态(显示空表单)，我不需要验证器(还没有任何东西需要验证)。
*   –对于单个页面请求，只执行一个操作。但是对于每个页面请求，我创建了三个“动作对象”，其中两个没有使用。
*   也许对于这个简单的例子，我可以不去管它，但是对于一个复杂的表单，它有多个页面和许多要验证的字段，还结合了各种其他的“基础设施”类(db access 等等)。)，它很快就变得太贵了。

所以我能改进吗？要回答这个问题，请输入[杰夫](http://www.procata.com/blog/)，他不久前在[代理对象合成](https://www.sitepoint.com/forums/showthread.php?t=150502)中提出了这个主题。讨论结果可以在 [WACT](http://wact.sf.net) 中以 ResolveHandle 函数的形式找到(在 WACT:framework/util/handle . Inc . PHP 中找到)；

```
 < ?php
//--------------------------------------------------------------------------------
// Copyright 2003 Procata, Inc.
// Released under the LGPL license ([http://www.gnu.org/copyleft/lesser.html)
//--------------------------------------------------------------------------------
/**
* @package WACT_UTIL
* @version $Id: handle.inc.php,v 1.1 2004/03/10 01:53:49 jeffmoore Exp $
*/
/**
* Takes a "handle" to an object and modifies it to convert it to an instance
* of the class. Allows for "lazy loading" of objects on demand.
* @see](http://www.gnu.org/copyleft/lesser.html) [http://wact.sourceforge.net/index.php/ResolveHandle](http://wact.sourceforge.net/index.php/ResolveHandle)
* @param mixed
* @return void
* @access public
* @package WACT
*/
function ResolveHandle(&$Handle) {
    if (!is_object($Handle) && !is_null($Handle)) {
        if (is_array($Handle)) {
            $Class = array_shift($Handle);
            $ConstructionArgs = $Handle;
        } else {
            $ConstructionArgs = array();
            $Class = $Handle;
        }
        if (is_integer($Pos = strpos($Class, '|'))) {
            $File = substr($Class, 0, $Pos);
            $Class = substr($Class, $Pos + 1);
            require_once $File;
        }
        switch (count($ConstructionArgs)) {
        case 0:
            $Handle = new $Class();  // =& doesn't work here.  Why?
            break;
        case 1:
            $Handle = new $Class(array_shift($ConstructionArgs));
            break;
        case 2:
            $Handle = new $Class(
                array_shift($ConstructionArgs), 
                array_shift($ConstructionArgs));
            break;
        case 3:
            $Handle = new $Class(
                array_shift($ConstructionArgs), 
                array_shift($ConstructionArgs), 
                array_shift($ConstructionArgs));
            break;
        default:
            // Too many arguments for this cobbled together implemenentation.  :(
            die();
        }
    }
}
?> 

```

这个看起来神秘的函数除了使用普通的 PHP 变量赋值之外，还为引用对象提供了额外的“协议”。换句话说，而不是这样；

```
 require_once 'lib/action/userupdateinitial.php';
//...
$F->registerInitialAction(new UserUpdateInitial()); 

```

我现在可以做到这一点；

```
 $F->registerInitialAction('lib/action/userupdateinitial.php|UserUpdateInitial'); 

```

我注册的不是对象本身，而是一个包含文件名和类名的字符串，以后需要这个对象时可以“解析”它。

我需要更新 FormController::run()方法来利用 resolve handle()；

(更新):更改以下脚本 ResolveHandle 的原始用法不正确

```
 /**
    * Run the form controller
    */
    function run() {
        if ( !strcasecmp($_SERVER['REQUEST_METHOD'], 'POST') ) {

            // Resolve the InitialAction
            ResolveHandle($this->initial_action);
            $this->initial_action->peform();

        } else {
            // Resolve the Validator
            ResolveHandle($this->validator);

            if ( ! $this->validator->isValid() ) {

                // Resolve the InvalidAction
                ResolveHandle($this->invalid_action);
                $this->invalid_action->perform();

            } else {

                // Resolve the ValidAction
                ResolveHandle($this->valid_action);
                $this->valid_action->perform();
            }
        }
    } 

```

使用`ResolveHandle()`函数，只在需要时创建对象，将性能开销减少到只处理当前请求所需的那些对象。

注意，对于这个特殊的例子，我可以简单地“注册”类名和文件名，但是 Jeff 的`ResolveHandle()`更加灵活，因为它支持许多不同类型的“引用”(包括对真实对象的普通引用)以及将对象传递给构造函数。更多的例子可以在 http://wact.sourceforge.net/index.php/ResolveHandle 找到。

虽然我在这个例子中关注的是表单处理，但是一般原则可以扩展到 PHP 应用程序中的其他问题领域，从对象关系映射到构建领域模型。改天再谈这些。

## 分享这篇文章