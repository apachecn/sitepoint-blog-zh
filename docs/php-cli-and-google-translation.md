# PHP 命令行界面和谷歌翻译

> 原文：<https://www.sitepoint.com/php-cli-and-google-translation/>

刚刚做了一些从德语到英语的翻译。这导致编写一个简单的命令行工具来通过 [Google](http://www.google.com/language_tools?hl=en) 进行翻译——换句话说就是浪费时间来节省时间。从头开始制作一个原型大约需要 15 分钟(我猜这有助于发现博客垃圾)。我把它清理了一下，放在这里，以防有人会用它。

仍然需要做的两件大事；

–验证语言对 CLI 选项，以保持用户理智

–[字符集转换](http://ww.php.net/iconv)–现在正在从谷歌获取 UFT8，这样 [XML_HTMLSax](http://pear.php.net/XML_HTMLSax3) 可以正确解析(它依赖于 str_ functions)，但之后不会做任何聪明的事情。

…可能还有一个或三个错误。

 `#!/usr/local/bin/php
http://www.php.net/license/3_0.txt
*/
//------------------------------------------------------------------------------
require_once 'Console/Getopt.php';
require_once 'HTTP/Request.php';
require_once 'XML/HTMLSax3.php';`

 `///-
类 Google_Translate {

var $ langpair
var $ proxy = null；

函数 Google_Translate($langpair，$proxy = null) {

$ this-> lang pair = $ lang pair；
$this- >代理= $ proxy

}

函数查询($text) {
$R = &新建 HTTP _ Request(' HTTP://translate . Google . com/translate _ t ')；
$ R->set method(' POST ')；

如果(！is _ null($ this-> proxy)){
$ pxy = parse _ URL($ this->proxy)；
$R- > setProxy($pxy['主机']，$pxy['端口']，$pxy['用户']，$pxy['传递'])；
}

$ r-> add ostdata(' text '，utf8 _ encode($ text))；
【r->add ostdata(' lang pair '，$ this->lang pair)；
$ r->add ostdata(' ie '，' utf8 ')；
$ r->add ostdata(' OE '，' utf8 ')；

$res = $R->sendRequest();

if(PEAR::isError($ RES)){
fwrite(STDERR，"连接问题:"。$res- > toString()。“n”)；
退出(1)；
}

if ($R->getResponseCode()！= '200') {
fwrite(STDOUT，"无效的 HTTP 状态:"。$R- > getResponseCode()。“n”)；
退出(1)；
}

return $ R-> getResponseBody()；
}

函数 translate($text) {
$P = &新增 Google _ Translate _ Parser()；
return $ P->parse($ this->query($ text))；
}
}

///-
类 Google_Translate_Parser {

var $ inResult = FALSE
var $ result =“”；

函数 open($p，$tag，$ attrs){
if($ tag = = ' textarea '&&isset($ attrs[' name '])&&$ attrs[' name ']= ' q '){
$ this->in result = TRUE；
}
}

函数 close($p，$ tag){
if($ this->in result&&$ tag = = ' textarea '){
$ this->in result = FALSE；
}
}

函数数据($p，$ data){
if($ this->in result){
$ this->结果。= $ data
}
}

函数解析($html) {

$ P = & new XML _ html sax 3()；
$ P->set _ object($ this)；
$ P->set _ element _ handler(' open '，' close ')；
$ P->set _ data _ handler(' data ')；
$P- >解析($ html)；

返回 utf8 _ decode($ this-> result)；
}
}

///-
函数用法(){
$用法= < < <eod usage:="" .="" gtrans.php="" options="" text="" to="" tranlate="" translates="" input="" string="" via="" google="" language="" tools.="" to-from="" e.g.="" de-en="" proxy="" server="" url="" display="" usage="" eod="" fwrite="" exit="" if="" pear::iserror="">getMessage()。“n”)；
退出(1)；
}</eod>

if(realpath($ _ SERVER[' argv '][0])= = _ _ FILE _ _){
$ options = Console _ Getopt::Getopt($ args，' HL:p:')；
} else {
$ options = Console _ Getopt::Getopt 2($ args，' HL:p:')；
}

if(PEAR::isError($ options)){
fwrite(STDERR，$options- > getMessage()。“n”)；
用法()；
}

$proxy = null;

foreach($ options[0]as $ option){
switch($ option[0]){
case ' h ':
用法()；
破；

case ' l ':
$ lang = str _ replace('-'，' | '，$ option[1])；
破；

案例“p”:
$ proxy = $ option[1]；
破；
}
}` 

`$G = & new Google_Translate($lang, $proxy);
echo $G->translate($options[1][0])."n";
exit(0);`

## 分享这篇文章