# PHP 中的宽查找器

> 原文：<https://www.sitepoint.com/wide-finder-in-errr-php/>

很久没有博客了——最近很忙 [搜索瑞士](http://www.local.ch)，但是首先要向[第二版团队](https://www.sitepoint.com/books/phpant2/)道贺，其次，为蒂姆·布雷令人着迷的 [Wide Finder 项目](http://www.tbray.org/ongoing/When/200x/2007/09/20/Wide-Finder)提交一个最新的 PHP 实现。

蒂姆设置了一个简单但非常现实的挑战；编写一个应用程序，从他的 Apache 访问日志中确定前 10 个最受欢迎的博客。它应该是快速的和可读的，潜台词是说明“语言 X”如何处理并行处理和利用“更广泛的”(许多处理器)系统。

从技术上讲，PHP 几乎没有能力并行执行东西，除了[pcntl _ fork](http://www.php.net/pcntl_fork)—(不要在你的实时 web 服务器上尝试——只有 CLI！)，除非你在数[滴答](http://www.php.net/manual/en/control-structures.declare.php#control-structures.declare.ticks)，我希望你不是。

但是我们不会让小细节阻止我们！因为我们有 [curl_multi_exec()](http://www.php.net/manual/en/function.curl-multi-exec.php) 允许我们与多个*URL*对话，并并行处理它们的响应。想象一下 Apache + mod_php 能够同时处理多少请求。完美的一些[地图和减少](http://en.wikipedia.org/wiki/MapReduce) …

所以首先是一个制图者；

```
 <?php
if ( isset($argv[1]) ) $_GET['f'] = $argv[1];

if ( !isset($_GET['f']) || !is_readable($_GET['f']) ) {
    header("HTTP/1.0 404 Not Found");
	die("Cant read file ".htmlspecialchars($_GET['f']));
}

$s = isset($_GET['s']) && ctype_digit($_GET['s']) ? $_GET['s'] : 0;
$e = isset($_GET['s']) && ctype_digit($_GET['e']) ? $_GET['e'] : filesize($_GET['f']);

$f = fopen($_GET['f'], 'r');
fseek($f, $s);

$counts = array();
while ( !feof($f) && ftell($f) < $e ) {
	$pattern = '#GET /ongoing/When/dddx/(dddd/dd/dd/[^ .]+) #';
	if ( preg_match($pattern, fgets($f), $m) ) {
		isset($counts[$m[1]]) ? $counts[$m[1]]++ : $counts[$m[1]] = 1;
	}

}
fclose($f);

$out = serialize($counts);
header("Content-Length: ".strlen($out));
echo $out; 

```

你把它指向一个它可以通过它的文件系统到达的文件，然后给它一个字节范围(开始和结束)来分析。它返回一个序列化的散列，关键字是博客条目标题，值是出现的次数。调用它的 URL 可能看起来像 http://localhost/~ Harry/wf/wf _ map . PHP？f =/tmp/o10k . AP & s = 257664 & e = 524288。此外，对于基准测试，可以从命令行调用它，像`$ php ./wf_map.php /tmp/o10k.ap`一样在单线程中处理一个完整的文件。

然后是缩减器，它实际上使用 curl 调用映射器；

```
 #!/usr/bin/php
<?php
function wf_reduce($urls) {

	$counts = array();

	$mh = curl_multi_init();

	foreach ( $urls as $url ) {
		$ch = curl_init($url);
		curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
		curl_multi_add_handle($mh, $ch);
	}

	do {
		curl_multi_exec($mh,$running);
		while ( FALSE !==($msg = curl_multi_info_read($mh)) ) {

			if ( "200" != ( $status = curl_getinfo ( $msg['handle'], CURLINFO_HTTP_CODE ) ) ) {
				die("HTTP Status: $status!");
			}

			// Here we can "reduce" as soon as we get a response...
			foreach ( unserialize( curl_multi_getcontent ( $msg['handle'] )  ) as $page => $hits ) {
				isset($counts[$page]) ? $counts[$page] += $hits : $counts[$page] = $hits;
			}

		}
	} while ($running > 0);

	return $counts;

}

$mapurl = "http://localhost/~harry/wf/wf_map.php";
$file = "/tmp/o10k.ap";

if (!file_exists($file) ) {
	print "First run: downloading sample data!";
	file_put_contents($file, file_get_contents("http://www.tbray.org/tmp/o10k.ap"));
	die();
}

$partitions = isset($argv[1]) && ctype_digit($argv[1]) ? $argv[1] : 1;

$end = filesize($file);
$partition = floor($end / $partitions);

$s = 0;
$e = $partition;
$urls = array();

while ( $e <= $end ) {
	$urls[] = sprintf("%s?f=%s&s=%s&e=%s", $mapurl, urlencode($file), $s, $e);
	$s = $e;
	$e += $partition;
}

$counts = wf_reduce($urls);
arsort($counts, SORT_NUMERIC);

$top10 = array_slice($counts, 0, 10, TRUE);
print_r( array_reverse($top10) ); 
```

这纯粹是为了命令行执行，采用一个参数，即应该运行的并行“线程”的数量，例如`$ ./wf_reducer.php 10`将向映射器并行发出 10 个 HTTP 请求，请求它每次解析文件的不同“块”。你需要更换`$mapurl`。

怀疑这将赢得任何奖项[美丽](http://www.oreilly.com/catalog/9780596510046/)和基准与蒂姆的样本文件并不令人印象深刻-它运行单线程更快-也许你会开始获得一个更大的文件？我想知道真相的欲望正在减弱。

但是不管怎样，PHP 也在那里。不知道 Tim 现在是否会在 T5120 上编译它？；)

更新:刚刚发现 Russell Beattie [用单线程解决方案第一个到达那里](http://www.russellbeattie.com/blog/widefinder-in-php)。

## 分享这篇文章