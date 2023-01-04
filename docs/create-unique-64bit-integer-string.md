# 如何从字符串创建唯一的 64 位整数

> 原文：<https://www.sitepoint.com/create-unique-64bit-integer-string/>

PHP 提供了流行的 [md5()散列函数](http://php.net/manual/en/function.md5.php)，它返回 32 个十六进制字符串。这是为任意长度的字符串生成*指纹*的好方法。但是如果你需要从一个 URL 生成一个*整数指纹*该怎么办呢？

![](img/e86242fa2566aca98b5a7af217c238f2.png)

## 挑战

我们在 [RatingWidget](http://rating-widget.com) 中遇到了这个挑战，当时我们不得不根据网站的加载页面将评级 Widget 绑定到一个唯一的 Int64 IDs。理论上，我们可以只存储 URL 并查询 URL 列，但是 URL 可能很长，为未知长度的文本列创建索引是非常低效的。

因此，如果您正在开发任何类型的动态小部件，需要根据加载数据的 URL 来加载不同的数据，这篇文章将为您节省大量时间。

为了简化问题，让我们把它分成两个子挑战:

1.  URL 规范化
2.  字符串到唯一 Int64 转换

## URL 规范化

在我们的例子中，我们希望为页面分配一个惟一的 Int64，而不是 URL。例如，`http://domain.com?x=1&y=2`和`http://domain.com?y=2&x=1`是不同的 URL，但实际上它们会加载完全相同的页面。因此，我们想给它们分配一个相同的 Int64 ID。因此，通过在将 URL 映射到 Int64 之前将其规范化，我们可以将 URL 转换为统一的表示。

```
function canonizeUrl($url)
    {
        $url = parse_url(strtolower($url));

        $canonic = $url['host'] . $url['path'];

        if (isset($url['query']))
        {
            parse_str($url['query'], $queryString);
            $canonic .= '?' . canonizeQueryString($queryString);
        }

        return $canonic;
    }

    function canonizeQueryString(array $params)
    {
        if (!is_array($params) || 0 === count($params))
            return '';

        // Urlencode both keys and values.
        $keys = urlencode_rfc3986(array_keys($params));
        $values = urlencode_rfc3986(array_values($params));
        $params = array_combine($keys, $values);

        // Parameters are sorted by name, using lexicographical byte value ordering.
        // Ref: Spec: 9.1.1 (1)
        uksort($params, 'strcmp');

        $pairs = array();
        foreach ($params as $parameter => $value) 
        {
            $lower_param = strtolower($parameter);

            if (is_array($value)) 
            {
                // If two or more parameters share the same name, they are sorted by their value
                // Ref: Spec: 9.1.1 (1)
                natsort($value);
                foreach ($value as $duplicate_value)
                    $pairs[] = $lower_param . '=' . $duplicate_value;
            } 
            else 
            {
                $pairs[] = $lower_param . '=' . $value;
            }
        }            

        if (0 === count($pairs))
            return '';

        return implode('&', $pairs);
    }

    function urlencode_rfc3986($input) 
    {
        if (is_array($input))
            return array_map(array(&$this, 'urlencode_rfc3986'), $input);
        else if (is_scalar($input)) 
            return str_replace('+', ' ', str_replace('%7E', '~', rawurlencode($input)));

        return '';
    }
```

基本上，这段代码所做的是按字典顺序重新排列查询字符串参数，并根据 [RFC 3986](http://www.ietf.org/rfc/rfc3986.txt) URI 语法标准稍微调整 URL 编码，以补偿不同浏览器+服务器 URL 编码的不一致性。

**备注:**

1.  在我们的例子中，规范化函数 canonizeUrl 去掉了协议。所以`https://domain.com`和`http://domain.com`都被封为`domain.com`，因为我们希望在 HTTP 和 HTTPS 的对等页面上显示相同的评级小部件。
2.  正如您所注意到的，我们还忽略了 after hashmark 片段中的所有内容。因此，如果您想要为 SPA ( [单页面应用](http://en.wikipedia.org/wiki/Single-page_application))不同的状态(如`http://my-spa.com/#state1`和`http://my-spa.com/#state2`)生成唯一的 id，则必须修改 URL 规范化函数以支持该功能。

## 将字符串转换为 MySql BIGINT 索引列的唯一 Int64 ID

在摆弄了各种位转换函数如`bindec()`、`decbin()`、`base_convert()`之后。我们发现 64 位整数和 PHP 都玩不好。提到的函数都不支持 64 位。在谷歌上挖了一圈后，我们被引导到[一个关于 PHP](http://blog.rac.me.uk/2008/12/09/php-md5-hashes-base_convert-and-32-bit-limitations/) 中 32 位限制的帖子，其中包括使用 [GMP](http://php.net/manual/en/book.gmp.php) 的建议，这是一个非常酷的多精度整数库。使用这个库，我们成功地创建了这个单行散列函数，它从任意长度的字符串中生成一个 64 位整数。

```
function get64BitHash($str)
    {
        return gmp_strval(gmp_init(substr(md5($str), 0, 16), 16), 10);
    }
```

事后，我们本可以实现 [CRC64 算法](http://en.wikipedia.org/wiki/Cyclic_redundancy_check),它生成一个字符串校验和，应该比 MD5 执行得更快。但是我们使用的技术优于 CRC 的地方在于，我们创建了一个单向散列函数，因此我们可以在代码中重用它来实现各种加密目的。

要了解更多关于 GMP 的信息，请参见[此处](https://www.sitepoint.com/arbitrary-precision-big-numbers-php/)。

## 终场演奏

将 URL 规范化与字符串到 Int64 的映射结合起来，最终的解决方案如下所示:

```
function urlTo64BitHash($url)
    {
        return get64BitHash(canonizeUrl($url));
    }
```

## get64BitHash 的碰撞和性能测试

**平台:** Intel i3，Windows 7 64 位，PHP 5.3
迭代: 10，000，000 次生成 get64BitHash
**运行时间:**每 100，000 次生成 460 毫秒
**碰撞:**未发现

## 摘要

我希望这个简单的解决方案能为你的下一个项目节省时间。如果您对该技术的应用有任何意见或其他使用案例，请随时在下面发表评论。

## 分享这篇文章