# PHP 手册 CLI 风格 2.0

> 原文：<https://www.sitepoint.com/php-manual-cli-style-20/>

大约三年前，哈利提到了那个手巧的小 [phpm](http://web.archive.org/web/20041206211514/phpvolcano.com/articles/manual/index.php) 。并且 [Sean Coates](http://blog.phpdoc.info/) 好心地指出如何用 shell 一行程序来代替它。这难道不是一场爱的狂欢吗？

有一点，我错过了这两个中的任何一个，那就是看到整个手工输入的能力。通常，手册实际上包含有用的信息(谁知道呢！)，所以我发现自己经常使用 www.php.net。或者说我曾经有过，直到我决定做点什么。现在，shell 脚本并不是我花费大部分时间研究的内容，所以我很自豪地向您介绍 **phpm two-oh** 。

```
 #!/bin/bash
# phpm
# commandline php-manual interface
# Kudos to Havard Eide and Sean Coates for the original idea
#
# author: Troels Knak-Nielsen <troelskn@gmail.com>
# version: 2007-11-27
#
# dependencies:
#   wget        sudo apt-get install wget
#   sed         sudo apt-get install sed
#   tidy        sudo apt-get install tidy
#   xmlstarlet  sudo apt-get install xmlstarlet
#   konwert     sudo apt-get install konwert
#   html2text   get from http://www.aaronsw.com/2002/html2text/html2text.py
#               symlink to ~/bin/html2text
#   urlencode   get from http://www.shelldorado.de/scripts/cmds/urlencode.txt
#               symlink to ~/bin/urlencode

function print_usage {
  echo "USAGE: phpm <function>"
  echo "To clear cache: phpm --clear"
  exit 0
}

# create cachedir on first run
CACHEDIR=~/.phpm
if [ ! -e $CACHEDIR ]
then
  mkdir $CACHEDIR
fi

if [ $# -gt 0 ]
then
  # parse a few options
  if [ $1 = "--clear" ]
  then
    echo "clearing cache"
    rm -r $CACHEDIR
    exit 0
  fi
  if [ $1 = "--help" ]
  then
    print_usage
  fi
  if [ $1 = "-?" ]
  then
    print_usage
  fi

  URLNAME=$(echo $1 | urlencode)
  CACHE_FILENAME=$CACHEDIR/$URLNAME
  # check cache
  if [ ! -e $CACHE_FILENAME ]
  then
    # fetch from HTTP
    HREF=http://www.php.net/manual-lookup.php?function=$URLNAME
    RESPONSE=$(wget --quiet -O - $HREF)
    if [ $? != 0 ]
    then
      echo "HTTP error" 1>&2
      exit $?
    fi

    # process response
    # test if function has direct match
    if echo $RESPONSE | grep -Eiq '<div([^>]*)class="refentry">'
    then
      # grap and format output
      # the first sed collapses blank lines, the second formats headers
      echo $RESPONSE 
        | tidy -latin1 -asxhtml --input-encoding utf8 --output-xml true --numeric-entities true 2>/dev/null 
        | xmlstarlet select --net --html -t -c "//*[@class='refentry']" 2>/dev/null 
        | tidy -latin1 --input-encoding utf8 -asxhtml 2>/dev/null 
        | konwert utf8-ascii 
        | html2text 2>&1 
        | sed -n 'G; s/n/&&/; /^([ -~]*n).*n1/d; s/n//; h; P' 
        | sed -e '/^# (.*)$/ { s/^# (.*)/1/p ; s/(.{1,1})/=/g }' -e '/^## (.*)$/ { s/^## (.*)/n1/p ; s/(.{1,1})/-/g }' -e '/^### (.*)$/ { s/^### (.*)/n1/p ; s/(.{1,1})/~/g }' 
        > $CACHE_FILENAME
    # test if there are any "best" matches
    elif echo $RESPONSE | grep -Eiq '<a href="/manual/en/function[^>]*><b>'
    then
      echo "Best matches for '$1':" > $CACHE_FILENAME
      echo $RESPONSE 
        | tidy -latin1 -asxhtml --input-encoding utf8 --output-xml true --numeric-entities true --wrap 0 2>/dev/null 
        | sed -n 's/.*<a href="/manual/en/function[^>]*><b>([^<]{1,})<.*/1/p' 
        >> $CACHE_FILENAME
    # test if there are any "weak" matches
    elif echo $RESPONSE | grep -Eiq '<a href="/manual/en/function[^>]*>[^<]+'
    then
      echo "Possible matches for '$1':" > $CACHE_FILENAME
      echo $RESPONSE 
        | tidy -latin1 -asxhtml --input-encoding utf8 --output-xml true --numeric-entities true --wrap 0 2>/dev/null 
        | sed -n 's/.*<a href="/manual/en/function[^>]*>[^<]{1,}([^<]{1,})<.*/1/p' 
        >> $CACHE_FILENAME
    fi
  fi

  if [ -e $CACHE_FILENAME ]
  then
    cat $CACHE_FILENAME
  else
    echo "No matches found for '$1'"
    exit -1
  fi
else
  print_usage
fi 
```

## 正在安装

显然，您需要一个 bash 环境来运行它(我想 [cygwin](http://www.cygwin.com/) 就可以了)。除此之外，您还需要大量的 cli 工具和实用程序。如果您使用的是基于 debian 的系统，下面的代码应该可以让您运行起来:

`sudo apt-get install wget sed tidy xmlstarlet konwert`

然后得到以下两个脚本:

*   [html 2 文本](http://www.aaronsw.com/2002/html2text/html2text.py)
*   [URL 编码](http://www.shelldorado.de/scripts/cmds/urlencode.txt)

将它们(没有文件扩展名)保存在`~/bin`中，并使它们可执行:

`chmod +x ~/bin/html2text`


(或者你可以把它们放在某个地方，比如在`~/scripts`中，然后用符号链接它们)

最后，像其他两个脚本一样，将上面的脚本保存为`~/bin/phpm`并 chmod 它。

## 使用

现在，您应该能够在 PHP 手册中查找一个简单的函数，如下所示:

`phpm substr`

作为奖励，你会得到一个不匹配的建议列表。例如:

`$ phpm substring`
`Best matches for 'substring':`
`is_string`

非常方便，当你只记得函数名的一部分时。

该脚本在 php 网站上查询文档，因此它总是最新的。为了提高性能，结果被缓存在`~/.phpm`中。您总是可以通过调用 phpm 来清除缓存，如下所示:

`phpm --clear`

## Emacs 奖金

作为对 Emacs 用户的最后一点奖励，这里有一个将当前单词 F4 绑定到 phpm 的片段:

```
 (defun php-manual-lookup ()
  "Shows short documentation for the word at the point."
  (interactive)
  (let ((word (current-word t))
        (buffername "*phpm*"))
    (when (get-buffer buffername)
      (kill-buffer buffername))
    (save-excursion
      (pop-to-buffer buffername)
      (shell-command (format "phpm %s"
                             (shell-quote-argument word)) buffername)
      (other-window 1))))
(global-set-key '[f4] 'php-manual-lookup) 
```

## 分享这篇文章