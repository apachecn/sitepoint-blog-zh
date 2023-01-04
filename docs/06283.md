# 使用 Symfony 2 构建 Web 应用程序:最终完成

> 原文：<https://www.sitepoint.com/building-web-app-symfony-2-finalizing/>

在本系列的第 1 部分和第 2 部分的[中，我已经介绍了使用 Symfony 2 开发一个正常运行的网站的基础知识。](https://www.sitepoint.com/building-a-web-app-with-symfony-2-bootstrapping/)

在教程的这一部分，我将介绍一些更高级的技术，并用分页、图像水印和 NativeQuery 来完成这个项目。

我们将使用的代码与第 2 部分的代码相同——特性已经存在，只是没有讨论。

### 图像水印

鉴于 SitePoint 上已经有大量通过 Imagick 教程进行的图像操作和水印，并且由于我们在这个特定的项目中不做任何高级图像操作，我们将坚持使用 PHP 的默认图像维护库——GD。

一般来说，当有“图像处理”时，我们谈论的是在我们最终使用`<img>`标签显示图像之前应用于图像的一个或多个动作。在我的应用程序中，这分两步完成:

*   创建一个路由来捕获“图像显示请求”并将其映射到控制器中的一个动作。
*   在动作中实现处理。

#### 路线配置

处理后显示图像的路线很简单:

```
 cover: 
        pattern: /books/cover/{id}_{title}_{author}_{width}.png
        defaults: {_controller: trrsywxBundle:Default:cover, width: 300}
```

在模板中，我们可以通过以下方式调用显示处理过的图像的调用:

```
 <img src="{{path("cover", {'id':book.id, 'author':author, 'title':book.title}) }}" alt="{{book.title}}'s cover" title="{{book.title}}'s cover"/>
```

所以每次遇到这个`<img>`标签，就会显示一个处理过的图像。

#### 图像处理

在这个应用程序中，我们在显示图像之前做了两件事:

1.  根据书籍封面图像是否存在，显示它，或显示默认封面图像；
2.  添加水印并调整大小(图书详细视图中 300px 宽，阅读列表视图中 200px 宽)。

完整的代码在函数`coverAction`的`src/tr/rsywxBundle/Controller/DefaultController.php`中。代码简单明了，我将向您展示真实书籍封面和默认封面的详细视图输出:

![](img/2bbed9c8d966ef0b0974c07edc856c51.png)

![](img/2f03b176c68bd9ac1330b2b9e7b02498.png)

请注意，我在“web”下创建了一个“cover”文件夹来保存默认封面和扫描的书籍封面。我还使用了中文 TTF 来显示水印文本。请随意使用您自己的字体(并将该字体复制到“封面”文件夹)。

在一个高流量的网站上，正确的做法应该是缓存自动生成的图像，就像 Lukas White 在他的文章中所做的那样，但是我会让你自己去处理。

### 页码

也有很多关于大数据集分页的文章。在本教程中，我将向您展示我是如何在这个应用程序中做到这一点的，我们将对它进行测试。

该类的源代码在`src/tr/rsywxBundle/Utility/Paginator.php`中。

代码本身很容易阅读，不涉及任何特别高级的东西，所以我将只讨论过程。

分页类中有两个基本值:

1.  一共多少条记录，一共多少页？
2.  什么是当前页面，如何构建一个易于访问的页面列表以便进一步处理？

许多分页类也经常处理数据检索，但这不是好的做法:分页应该只处理与分页相关的事情，而不是数据本身。数据是实体/存储库的领域。

如果我们回到获取符合特定标准的书籍的实现，您会注意到这两个步骤(获取数据和分页)在控制器中是如何分开的:

`File location: src/tr/rsywxBundle/Controller/BookController.php`

```
 public function listAction($page, $key)
    {
        $em = $this->getDoctrine()->getManager();
        $rpp = $this->container->getParameter('books_per_page');

        $repo = $em->getRepository('trrsywxBundle:BookBook');

        list($res, $totalcount) = $repo->getResultAndCount($page, $rpp, $key);
        //Above to retrieve data and counts
        //Below to instantiate the paginator

        $paginator = new \tr\rsywxBundle\Utility\Paginator($page, $totalcount, $rpp);
        $pagelist = $paginator->getPagesList();

        return $this->render('trrsywxBundle:Books:List.html.twig', array('res' => $res, 'paginator' => $pagelist, 'cur' => $page, 'total' => $paginator->getTotalPages(), 'key'=>$key));
    }
```

我的分页器的构造函数有 3 个参数:

*   页面:显示当前页面。这将用于返回在模板中显示为可单击链接的页面列表；
*   totalcount:告知结果的计数。这将与 rpp 参数一起用于计算总页数；
*   每页记录数的缩写。

在我当前的实现中，我使用了一个简单的分页版本，只显示“第一个”、“上一个”、“下一个”和“最后一个”页面链接，但是您可以通过使用`getPagesList`函数尝试不同的类型。

以这样一个页面列表为例:

```
 1 2 3 4 5
```

这里的关键是在`getPagesList`函数中，它确保当前页面总是在中间，或者如果没有足够的页面，它确保它在正确的位置。

```
 public function getPagesList()
    {
        $pageCount = 5;
        if ($this->totalPages <= $pageCount) //Less than total 5 pages
            return array(1, 2, 3, 4, 5);

        if($this->page <=3)
            return array(1,2,3,4,5);

        $i = $pageCount;
        $r=array();
        $half = floor($pageCount / 2);
        if ($this->page + $half > $this->totalPages) // Close to end
        {
            while ($i >= 1)
            {
                $r[] = $this->totalPages - $i + 1;
                $i--;
            }
            return $r;
        } else
        {
            while ($i >= 1)
            {
                $r[] = $this->page - $i + $half + 1;
                $i--;
            }
            return $r;
        }
    }
```

为了确保这个功能确实有效，我们必须在使用它之前进行测试。我们将使用 [PHPUnit](https://github.com/sebastianbergmann/phpunit/) 作为测试平台。关于如何安装的详细说明，请参考[的官方网站。我使用 phpunit.phar 方式下载包，并将其放在我的项目根文件夹中。](http://phpunit.de/manual/current/en/installation.html)

为了测试我们刚刚创建的 Paginator 类，首先我们需要在`src/tr/rsywxBundle/Tests`下创建一个文件夹`Utility`。Symfony 中的所有测试都应该放在`src/tr/rsywxBundle/Tests`下。在`Utility`文件夹中，创建一个名为`PaginatorTest.php`的 PHP 文件:

```
 namespace tr\rsywxBundle\Tests\Utility;

    use tr\rsywxBundle\Utility\Paginator;

    class PaginatorTest extends \PHPUnit_Framework_TestCase
    {
        public function testgetPageList()
        {
            $paginator=new Paginator(2, 101, 10);
            $pages=$paginator->getTotalPages();
            $this->assertEquals($pages, 11);
            $list=$paginator->getPagesList();
            $this->assertEquals($list, array(1,2,3,4,5));

            $paginator=new Paginator(7, 101, 10);
            $list=$paginator->getPagesList();
            $this->assertEquals($list, array(5,6,7,8,9));

            $paginator=new Paginator(10, 101, 10);
            $list=$paginator->getPagesList();
            $this->assertEquals($list, array(7,8,9,10,11));
        }
    }
```

这种测试叫做[单元测试](http://en.wikipedia.org/wiki/Unit_testing)。它测试程序的一个特定单元。

在`testgetPageList`函数中，我们基本上用我们能想到的任何参数组合来实例化我们想要测试的对象(一个分页器)。然后，我们调用该对象的一些方法，并通过使用断言来测试结果的有效性。这里我们只用方法`assertEquals`。

在我们创建的`$paginator`对象的示例`$this->assertEquals($list, array(7,8,9,10,11))`中，我们知道总共应该有 11 页(总共有 101 条记录，每页 10 条记录)，作为当前页的第 10 页将返回一个页列表`7,8,9,10,11`，因为第 10 页非常接近末尾。我们断言这一点，如果断言失败，函数逻辑中一定有问题。

在我们的命令行/终端中，我们运行以下命令:

`php phpunit.phar -c app/`

这将从文件夹`app/`中读取 PHPUnit 的配置文件(`phpunit.xml.dist`由 Symfony 安装生成)。不要改！)

注意:请删除 Symfony 自动生成的所有其他测试文件(如`Tests`下的`Controller`文件夹)。否则，您至少会看到一个错误。

上面的命令将解析`Tests`下的所有测试文件，并确保所有断言都通过。在上面的例子中，你会看到一个类似于`OK, 1 test, 4 assertions`的提示。这意味着我们创建的所有测试都已经通过，从而证明了函数行为正常。如果不是，一定是代码有问题(在实现中或者在测试中)。

请随意扩展 Paginator 类的测试文件。

在程序中使用自制模块之前，对其进行测试总是一个好的做法。

要更深入地了解 PHPUnit 和 PHP 中的测试，请参阅 SitePoint 的众多 PHPUnit 文章中的任何一篇。

### 本地查询

我们的数据库有一个名为`book_visit`的表，我们使用 timestamp 作为数据类型来记录访问图书详细页面的时间。我们需要对访问做一些统计汇总，其中之一是按天获取总访问计数(我的“天”在+8 小时时区)。

在 SQL 中，这很简单:

```
 select count(v.bid) vc, date(from_unixtime(v.visitwhen+15*60*60)) vd from book_visit v group by vd order by vd
```

在上面，`15*60*60`是用来调整我的服务器时间到我的时区的。

但是，如果您尝试在 Symonfy 中使用类似的语法(当然，将表名改为它的 FQN)，一个错误提示会告诉您类似于`date function is not supported`的内容。要解决这个问题，一种方法是使用纯 SQL:

```
 $q = $em->getConnection()->prepare('select count(v.bid) vc, date(from_unixtime(v.visitwhen+8*60*60)) vd from book_visit v group by vd order by vd');
    $q->execute();
    return $q->fetchAll();
```

或者像 Symfony 和[主义](https://www.google.com.hk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&ved=0CDAQFjAB&url=%68%74%74%70%3a%2f%2f%77%77%77%2e%64%6f%63%74%72%69%6e%65%2d%70%72%6f%6a%65%63%74%2e%6f%72%67%2f&ei=Z7xlUrTPB5DbkgW_jYCQCA&usg=AFQjCNFzYTSg-MM7hwfV0GE-4889XiCyFg)推荐的那样，我们可以(也应该)使用`createNativeQuery`和`ResultSetMapping`。

```
 public function getVisitCountByDay()
    {
        $em = $this->getEntityManager();

        $rsm=new \Doctrine\ORM\Query\ResultSetMapping;

        $rsm->addScalarResult('vc', 'vc');
        $rsm->addScalarResult('vd', 'vd');

        $q=$em->createNativeQuery('select count(v.bid) vc, date(from_unixtime(v.visitwhen+15*60*60)) vd from book_visit v group by vd order by vd', $rsm);

        $res=$q->getResult();

        return $res;

    }
```

在上面的示例中，最关键的语句是创建一个 ResultSetMapping 并将结果添加到该映射中。

`vc`(访问次数)和`vd`(访问日期)都在`addScalarResult`调用中出现了两次。第一个是查询将返回的列名，第二个是该列的别名。为了避免创建更多名称的复杂性，我们只使用相同的名称。

标量结果描述 SQL 结果集中的单个列到教条结果中的标量值的映射。标量结果通常用于聚合值，但是 SQL 结果集中的任何列都可以映射为标量值。

上述功能在最终代码中没有实现。把它当作家庭作业。

### 结论

这远不是 Symfony 的完整教程。还有很多内容没有涉及到(表单、安全性、功能测试、i18n 等)，很容易再花 10-12 个部分。我强烈推荐你阅读 Symfony 提供的完整官方文档，可以在这里下载。

这是我第一次用 PHP 和 Sitepoint 写系列文章，如果你能给我一些建设性的批评和一般性的反馈，我会非常感激。

## 分享这篇文章