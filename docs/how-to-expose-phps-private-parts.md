# 如何暴露 PHP 的私有部分

> 原文：<https://www.sitepoint.com/how-to-expose-phps-private-parts/>

我一直在修补 PHP 对象的转储，并且发现自己经常碰壁。从`[print_r](http://docs.php.net/manual/en/function.print-r.php)`和 friends 得到的输出在某些情况下是不错的，但是对于更大的结构来说，最好将输出整理一下，并将其封装在一些 HTML 中。

但是，这些函数有一定的特权，因为它们可以访问私有/受保护的变量。这在普通的 PHP 中是无法回避的(除了通过一些外来的扩展，我宁愿不依赖它们)。似乎 5.3.0 可能[在反射 API 中引入一个功能](http://docs.php.net/manual/en/language.oop5.reflection.php#language.oop5.reflection.reflectionproperty)，但是没人知道 5.3.0 什么时候会出来。

所以我意识到可以将任何对象序列化为字符串，包括私有和受保护的变量。然后，我所要做的就是解析序列化的字符串，我将获得那种神秘的洞察力。人们可能会认为这样的解析器已经存在。也许有，但是我找不到它(虽然我找到了一个 [Java 实现](http://code.google.com/p/serialized-php-parser/))。不过格式相当简单，所以我花了半个周日下午的时间。与`print_r`相比，一个非常好的好处是这种格式处理递归，这对于以可展示的形式转储输出非常方便
。

So, without further ado, here’s the code:

```
< ?php
/**
* Exports variable information, including private/protected variables
and with recursion-protection.
* Since this is built upon PHP serialization functionality,
unserializable objects may cause trouble.
*/
class XrayVision {
  protected $id;
  function export($object) {
    $this->id = 1;
    list($value, $input) = $this->parse(serialize($object));
    return $value;
  }
  protected function parse($input) {
    if (substr($input, 0, 2) === 'N;') {
      return array(array('type' => 'null', 'id' => $this->id++,
'value' => null), substr($input, 2));
    }
    $pos = strpos($input, ':');
    $type = substr($input, 0, $pos);
    $input = substr($input, $pos + 1);
    switch ($type) {
    case 's':
      return $this->s($input);
    case 'i':
      return $this->i($input);
    case 'd':
      return $this->d($input);
    case 'b':
      return $this->b($input);
    case 'O':
      return $this->o($input);
    case 'a':
      return $this->a($input);
    case 'r':
      return $this->r($input);
    }
    throw new Exception("Unhandled type '$type'");
  }
  protected function s($input) {
    $pos = strpos($input, ':');
    $length = substr($input, 0, $pos);
    $input = substr($input, $pos + 1);
    $value = substr($input, 1, $length);
    return array(array('type' => 'string', 'id' => $this->id++,
'value' => $value), substr($input, $length + 3));
  }
  protected function i($input) {
    $pos = strpos($input, ';');
    $value = (integer) substr($input, 0, $pos);
    return array(array('type' => 'integer', 'id' => $this->id++,
'value' => $value), substr($input, $pos + 1));
  }
  protected function d($input) {
    $pos = strpos($input, ';');
    $value = (float) substr($input, 0, $pos);
    return array(array('type' => 'float', 'id' => $this->id++, 'value'
=> $value), substr($input, $pos + 1));
  }
  protected function b($input) {
    $pos = strpos($input, ';');
    $value = substr($input, 0, $pos) === '1';
    return array(array('type' => 'boolean', 'id' => $this->id++,
'value' => $value), substr($input, $pos + 1));
  }
  protected function r($input) {
    $pos = strpos($input, ';');
    $value = (integer) substr($input, 0, $pos);
    return array(array('type' => 'recursion', 'id' => $this->id++,
'value' => $value), substr($input, $pos + 1));
  }
  protected function o($input) {
    $id = $this->id++;
    $pos = strpos($input, ':');
    $name_length = substr($input, 0, $pos);
    $input = substr($input, $pos + 1);
    $name = substr($input, 1, $name_length);
    $input = substr($input, $name_length + 3);
    $pos = strpos($input, ':');
    $length = (int) substr($input, 0, $pos);
    $input = substr($input, $pos + 2);
    $values = array();
    for ($ii=0; $ii < $length; $ii++) {
      list($key, $input) = $this->parse($input);
      $this->id--;
      list($value, $input) = $this->parse($input);
      if (substr($key['value'], 0, 3) === "00*00") {
        $values['protected:' . substr($key['value'], 3)] = $value;
      } elseif ($pos = strrpos($key['value'], "00")) {
        $values['private:' . substr($key['value'], $pos + 1)] = $value;
      } else {
        $values[str_replace("00", ':', $key['value'])] = $value;
      }
    }
    return array(
      array('type' => 'object', 'id' => $id, 'class' => $name, 'value'
=> $values),
      substr($input, 1));
  }
  protected function a($input) {
    $id = $this->id++;
    $pos = strpos($input, ':');
    $length = (int) substr($input, 0, $pos);
    $input = substr($input, $pos + 2);
    $values = array();
    for ($ii=0; $ii < $length; $ii++) {
      list($key, $input) = $this->parse($input);
      $this->id--;
      list($value, $input) = $this->parse($input);
      $values[$key['value']] = $value;
    }
    return array(
      array('type' => 'array', 'id' => $id, 'value' => $values),
      substr($input, 1));
  }
}
?>
```

这种技术至少有两个已知的问题。第一个是资源被序列化为整数。对于转储，这并不重要，因为资源在运行的进程之外是没有意义的。另一个问题是关于实现`[__sleep](http://docs.php.net/manual/en/language.oop.magic-functions.php)`的对象。因为这个函数可能有副作用，所以对于使用这个特性的对象，你可能会弄乱你的程序。以我的经验来看，这是一个很少使用的功能，所以它并没有真正困扰我。

## 分享这篇文章