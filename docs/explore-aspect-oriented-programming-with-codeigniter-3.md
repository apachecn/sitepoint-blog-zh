# 用 CodeIgniter 探索面向方面编程，第 3 部分

> 原文：<https://www.sitepoint.com/explore-aspect-oriented-programming-with-codeigniter-3/>

在本系列的前几部分中，我们了解了 AOP 的概念以及在大型项目中使用 AOP 的必要性，我介绍了 CodeIgniter 的钩子，作为从头开始创建 AOP 功能的一种方便的机制。在这一部分中，我将向您展示当专用的 AOP 框架不可用时，如何使用 XML 和基于注释的技术来创建定制的 AOP 功能。

## XML 配置

让我们从 XML 方法开始。首先，我们需要一个带有 AOP 配置细节的定制 XML 文件。我将为 AOP 规则定义特定的标记，如下面的代码所示。

```
<?xml version="1.0" encoding="UTF-8" ?> 
<aopConfig>
 <aopAspect id="LoggingAspect" >
  <aopPointcut expression="execution[*.*]" >
   <aopBefore ref-class="LoggingAspect" method="startLog"/>
   <aopAfter ref-class="LoggingAspect" method="endLog"/>
  </aopPointcut>
 </aopAspect>
 <aopAspect id="ProfilingAspect" >
  <aopPointcut expression="execution[*.*]" >
   <aopBefore ref-class="ProfilingAspect" method="startProfiling"/>
   <aopAfter ref-class="ProfilingAspect" method="endProfiling"/>
  </aopPointcut>
 </aopAspect>
</aopConfig>
```

每个 AOP 框架都有自己独特的标签。在这里，我为每种类型的方面定义了`<aopAspect>`和`<aopPointcut>`标签，以减少连接点的数量来匹配特定的标准。在`<aopPointcut>`中，我使用了表达式属性，它将用于匹配连接点。

我为建议前和建议后定义了两个标签；每当一个方法匹配切入点表达式时，它将寻找之前或之后的建议，并使用`ref-class`属性值调用`Aspect`类中提供的方法。

在上面的代码中，表达式`[*.*]`意味着通知将在所有控制器的所有方法上执行。如果我们想对所有的删除方法应用一些建议。切入点表达式应该是`[*.delete*]`。如果我们想对某些类型的控制器提出建议，那就是`[*Service.*]`。您可以定义任何自定义结构来匹配类和方法。以上只是最基本的做法。

现在让我们看看如何使用这个配置文件在 CodeIgniter 中应用建议。在前一部分中，我解释了如何定义`pre_controller`和`post_controller`钩子来调用定制类。下面显示了这些类的实现。

```
<?php
class AOPCodeigniter
{
    private $CI;

    public function __construct()
        $this->CI = &get_instance();
    }

    public function  applyBeforeAspects() {
        $uriSegments = $this->CI->uri->segment_array();

        $controller = $uriSegments[1];
        $function = $uriSegments[2];

        $doc = new DOMDocument();
        $doc->load("aop.xml");

        $aopAspects = $doc->getElementsByTagName("aopPointcut");

        foreach ($aopAspects as $aspect) {
            $expr = $aspect->getAttribute("expression");
            preg_match('/[(.*?)]/s', $expr, $match);
            if (isset($match[1])) {
                $exprComponents = explode(".", $match[1]);
                $controllerExpr = "/^" . str_replace("*", "[a-zA-Z0-9]+", $exprComponents[0]) . "$/";
                $functionExpr = "/^" . str_replace("*", "[a-zA-Z0-9]+", $exprComponents[1]) . "$/";

            	preg_match($controllerExpr, $controller, $controllerMatch);
            	preg_match($functionExpr, $function, $functionMatch);

            	if (count($controllerMatch) > 0 && count($functionMatch) > 0) {
                    $beforeAspects = $aspect->getElementsByTagName("aopBefore");

                    foreach ($beforeAspects as $beforeAspect) {
                        $refClass = $beforeAspect->getAttribute("ref-class");
                        $refMethod = $beforeAspect->getAttribute("method");

                        $classObject = new $refClass();
                        $classObject->$refMethod();
                    }
                }
            }
        }
    }
}
```

before advices 的所有代码都应用在`applyBeforeAspects()`方法中。

最初，我们使用 CodeIgniter 的`URI`类获得 URI 片段。第一个参数是控制器，下一个参数是当前请求的方法名。然后我们使用`DOMDocument()`加载 AOP 配置文件，并使用`<aopPointcut>`标签获取切入点。

在遍历每个切入点时，我们得到了`expression`属性。然后，我们使用 regex 获取括号内的表达式，并通过分解匹配的字符串来准备控制器名称和方法名称。我使用了一个基本的正则表达式来匹配控制器和方法，但是在真正的 AOP 框架中，这要复杂得多。

然后，我们尝试将控制器名和方法名与我们创建的正则表达式进行匹配。如果方法和控制器都匹配，我们就有了一个需要应用建议的点。因为我们在这个方法中应用了 before advice，所以我们得到了当前切入点的`<aopBefore>`标签。在遍历每个 before 标签时，我们通过调用`ref-class`的方法来应用通知。

这是用 CodeIgniter 创建 AOP 功能的基本方法。同样的过程也适用于事后建议。

我们的`LoggingAspect`类如下所示:

```
<?php
class LoggingAspect
{
	function startLog() {
		echo "Started Logging";
	}

	function endLog(){
	}
}
```

现在我们知道了 XML 方法是如何工作的，所以让我们继续讨论基于注释的技术。

## 基于文档注释的配置

在这种技术中，我们不像使用 XML 方法那样有任何配置文件。相反，所有的建议都在方面类中。我们根据预定义的结构定义注释，然后我们可以使用 PHP 反射类操作注释来应用建议。

```
<?php
/**
 * 
 * @Aspect
 */
class LoggingAspect
{
    /**
     * @Before:execution[*.*]
     *
     */
    function startLog() {
        echo "Started Logging";
    }

    /**
     * @After:execution[*.*]
     *
     */
    function endLog() {
    }
}
```

`LoggingAspect`类已经改变，现在是一个使用`@Aspect`注释的`Aspect`类。在运行时，AOP 框架会找到方面类，并寻找与特定切入点匹配的建议。使用`@Before`和`@After`以及指定的执行参数定义前后建议。

让我们转到这个方法的`applyBeforeAspects()`函数的实现上。

```
<?php
public function applyBeforeAspects() {
    $uriSegments = $this->CI->uri->segment_array();

    $controller = $uriSegments[1];
    $function = $uriSegments[2];

    $aspectClasses = array("LoggingAspect");

    foreach ($aspectClasses as $aspectClass) {
        $ref = new ReflectionClass($aspectClass);
        $methods = $ref->getMethods();

        foreach ($methods as $method) {

            $methodName = $method->name;
            $methodClass = $method->class;
            $reflectionMethod = new ReflectionMethod($methodClass, $methodName);
            $refMethodComment = $reflectionMethod->getDocComment();

            preg_match('/@Before:execution[(.*?)]/s', $refMethodComment, $match);

            if (isset($match[1])) {
                $exprComponents = explode(".", $match[1]);

                $controllerExpr = "/^" . str_replace("*", "[a-zA-Z0-9]+", $exprComponents[0]) . "$/";
                $functionExpr = "/^" . str_replace("*", "[a-zA-Z0-9]+", $exprComponents[1]) . "$/";

                preg_match($controllerExpr, $controller, $controllerMatch);
                preg_match($functionExpr, $function, $functionMatch);

                if (count($controllerMatch) > 0 && count($functionMatch) > 0) {
                    $classObject = new $methodClass();
                    $classObject->$methodName();
                }
            }
        }
    }
}
```

为了简单起见，我将方面类硬编码在一个数组中，但是理想情况下，AOP 框架将在运行时检索所有方面类。

我们使用反射获得每个方面类的方法。然后，在遍历每个方法时，我们检索在该方法之前定义的文档注释。我们通过将注释与特定的正则表达式进行匹配来提取切入点表达式。然后，我们对剩余的逻辑继续与 XML 方法相同的过程。

## 摘要

在本系列中，您了解了 AOP 的概念，以及如何识别应该应用 AOP 的情况。虽然我希望您现在对 AOP 如何工作有了更好的理解，但是请记住 CodeIgniter 不是一个 AOP 框架，所以使用 hooks 并不是在您的项目中应用 AOP 的最佳方式。相反，CodeIgniter 在整个系列中充当了一个媒介，从头开始提供关于 AOP 的知识，以便您可以轻松地适应任何新的 AOP 框架。我建议您对大型项目使用众所周知的 AOP 框架，因为如果使用不当，它可能会使您的应用程序更加复杂。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章