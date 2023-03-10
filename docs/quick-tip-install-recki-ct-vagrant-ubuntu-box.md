# 快速提示:将 Recki-CT 安装到一个浮动的 Ubuntu 盒子中

> 原文：<https://www.sitepoint.com/quick-tip-install-recki-ct-vagrant-ubuntu-box/>

Recki 什么？

如果不知道 Recki-CT 是什么，看 [@ircmaxell 原帖](http://blog.ircmaxell.com/2014/08/introducing-recki-ct.html)或者[回购](https://github.com/google/recki-ct)，这里就不深入了。这个快速提示将仅仅告诉你如何在一个 Homestead 改进的盒子上安装它，就像我们在之前对其他软件[所做的一样。](https://www.sitepoint.com/blog/)

## 步骤 1–家园改善

首先，让一个 [Homestead 改进的](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)实例启动并运行。

给它一个新的虚拟主机，如下所示:

```
- map: test.app
  to: /home/vagrant/Code/recki
```

启动虚拟机并将`vagrant ssh`放入其中。

## Step 2 – JitFu

Recki-CT 需要安装 [JitFu](https://github.com/krakjoe/jitfu) 。

按照说明，在虚拟机中按顺序运行以下命令。

```
sudo apt-get install bison flex texinfo

git clone git://git.sv.gnu.org/libjit.git libijt-fu
cd libjit-fu
./auto_gen.sh
./configure --prefix=/opt
make
sudo make install

git clone https://github.com/krakjoe/jitfu
cd jitfu
phpize
./configure --with-jitfu=/opt
make
sudo make install

sudo su
echo "extension=jitfu.so" >> /etc/php5/fpm/conf.d/20-jitfu.ini
echo "extension=jitfu.so" >> /etc/php5/cli/conf.d/20-jitfu.ini
exit

sudo service nginx restart
sudo service php5-fpm restart
```

要查看我们是否成功安装了它:

```
cd ~/Code
git clone https://github.com/Swader/publicinfo recki
mv recki/public/index.php recki/
touch recki/recki.php
```

打开`recki.php`并在里面粘贴以下内容:

```
<?php
use JITFU\Context;
use JITFU\Type;
use JITFU\Signature;
use JITFU\Func;
use JITFU\Value;

$context = new Context();

$integer   = Type::of(Type::int);

$function = new Func($context, new Signature($integer, [$integer]), function($args) use($integer) {
    $zero     = new Value($this, 0, $integer);
    $one      = new Value($this, 1, $integer);
    $two      = new Value($this, 2, $integer);

    /* if ($arg == 0) return 0; */
    $this->doIf(
        $this->doEq($args[0], $zero),
        function() use ($zero) {
            $this->doReturn($zero);
        }
    );

    /* if ($arg == 1) return 1; */
    $this->doIf(
        $this->doEq($args[0], $one),
        function() use($one) {
            $this->doReturn($one);
        }
    );

    /* return $function($arg-1) + $function($arg-2); */
    $this->doReturn(
        $this->doAdd(
            $this->doCall($this, [$this->doSub($args[0], $one)]),
            $this->doCall($this, [$this->doSub($args[0], $two)]))); 
});

$function->dump("Fibonacci");

var_dump($function(40)); /* __invoke with magicalness */
?>
```

如果您现在转到`test.app:8000`，您应该在 PHPInfo 屏幕中看到 JitFu 支持被启用。如果你去`test.app:8000/recki.php`，你应该很快得到`int 102334155`作为输出。

## 步骤 3–克隆和合成

接下来，我们需要克隆 Recki repo，并使用 Composer 下载依赖项。

```
cd ~/Code
rm -rf recki
git clone https://github.com/google/recki-ct recki
cd recki
composer install
```

## 第 4 步–测试

要查看它是否有效，只需通过命令行运行示例:

```
php examples/01-basic-usage.php
```

或者通过浏览器:

```
test.app:8000/examples/01-basic-usage.php
```

* * *

这就是全部了。现在，您可以集中精力对 PHP 代码的某些部分进行优化，而不必替换应用程序运行的整个 PHP 引擎。

## 分享这篇文章