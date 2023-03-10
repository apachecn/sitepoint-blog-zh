# 使用自定义本地化构建多语言 Android 应用程序

> 原文：<https://www.sitepoint.com/build-a-multilingual-survey-app-in-android-os/>

在当今精通技术的世界中，公司渴望从客户那里获得可操作的反馈，通常以调查和其他客户反馈机制的形式。这已经成为一种常见、高效的做法，为公司提供有组织的数据，并为客户自己提供简单、直接的流程。只需轻轻一点，就可以将数据保存到公共服务器上，免去了纸质调查表的麻烦。多语言调查应用程序甚至更加有用和强大，因为它们可以帮助调查人员从一个系统中收集特定区域的数据并进行国际调查。

Android SDK 中自定义本地化的概念在为多语言交互性提供强大支持方面发挥了重要作用。自定义本地化意味着用户应该能够使用应用程序设置来更改应用程序语言，而不必更改整个移动设备的语言。其中一个限制是，对印度语言的多语言支持不是 Android SDK 的一部分，这对在 Android OS 上执行自定义本地化的开发人员来说是一个重大挑战。

使用 Android SDK 平台，可以轻松处理本地化的概念，但是主要的挑战是对印度本地语言的本地化支持不足。对于任何外语，设备区域设置都可以设置为语言名称。例如，来自法国的用户可以将他们设备的语言更改为法语，所有的书写内容都将转换为法语。以下基于调查的应用程序的优势之一是它支持印度的地区语言，如泰米尔语、马拉地语、印地语等等。

### 描述

Android SDK 提供了一个 API 函数，允许使用外部字体。这些字体允许呈现不同语言使用的字符和符号。为了使用这种方法，每个组件都在一个 activity 类中初始化，并且设置其字体类型以确保呈现正确的字体。这种方法解决了区域语言支持的问题，但是它不能被认为是完美的解决方案，因为它在 activity 类中创建了大量不必要的对象和引用。

开发多语言支持应用程序时，应注意以下几点:

Android SDK 提供了一种资源类型来为视图创建定制属性。自定义属性可以创建为“langtext”为清楚起见，请参考以下代码片段:

```
&amp;lt;!--?xml version=&amp;quot;1.0&amp;quot; encoding=&amp;quot;UTF-8&amp;quot;?--&amp;gt;
```

在 strings.xml 文件中，字符串值是以键值对的方式创建的。下面是一个演示。

```
&amp;lt;/pre&amp;gt;
error

Error // text in english

गलती //text in hindi

తప్పు //text in telugu
&amp;lt;pre&amp;gt;
```

创建这些字符串的方式是，通过在 key_ <somevalue>的值中添加语言类型(例如:en、hi、tu ),它将返回所选语言的实际字符串值。</somevalue>

可以创建自定义视图，扩展了 Android 的 TextView 类。在此类中，重写 setTypeface。使用这种方法，将所需的字体文件设置到 textview。请参考以下代码片段:

[sourcecode language="java"]
打包 com . WB . mobile . Android . ot . ui . custom views；

导入 Android . content . context；
导入 Android . content . RES . typed array；
导入 Android . graphics . typeface；
导入 Android . util . attributeset；
导入 Android . widget . textview；

导入 com . sample . mobile . Android . ot . r；
导入 com . sample . mobile . Android . ot . ui . activities . splash screen；
导入 com . sample . mobile . Android . ot . utils . constants；
导入 com . sample . mobile . Android . ot . utils . helper；

/**
*自定义实现支持多种语言的 textview。
*
* @ author Magar wal
*
*/
公共类 MultiLingualTextView 扩展 TextView {

私有字符串 mLangText

公共字符串 get langtext(){
返回 mlangtext

public MultiLingualTextView(Context Context){
super(Context)；
}

public MultiLingualTextView(Context Context，AttributeSet attrs){
super(Context，attrs)；

typed array typed array = context . observe styledattributes(attrs，
r . styleable . custom textview，0，0)；
String lang = splash screen . get user settings()。getString(
常量。LANG_CONSTANST_STR，常量。LANG _ CONSTANST _ EN)；
String langText = typed array
。getString(r . styleable . custom text view _ lang text)；
mLangText = lang text；
if(helper . checkstring(lang text)){
this . settext(helper . getstringresource(get context()，lang+&amp；quot_&amp；quot
+lang text，&amp；quot 字符串&amp；quot));
}
}

public multilingual textview(Context Context，AttributeSet attrs，
int def style){
super(Context，attrs，def style)；
}

@ override
public void settype face(type face TF){
string lang = splash screen . getusersettings()。getString(
常数。lang _ constanst _ str 常量。lang _ constanst _ en：
if(lang . equal miss(常量)。lang _ Constance _ en)]{
超级。settype face(TF)；
其他{
超级. settype face(helper . gettype face(get context()))；

@ override
public void settype face(type face TF，int style){
string lang = screen . getusersettings()。getString(
常数。lang _ constanst _ str 常量。lang _ constanst _ en：
if(lang . equal miss(常量)。lang _ Constance _ en)]{
超级。settype face(TF)；
其他{
超级. settype face(helper . gettype face(get context()))；

public void setlangtext(string langtext)& gt

m attach text =长文本；

string lang = splash screen . getusersettings()。getString(
常数。lang _ constanst _ str 常量。lang _ constanst _ en：

if(helper . checkstring(lang text)){
this . settext(helper . getstring resource(get context()，lang+&amp；quot_&amp；quot
+lang text，&amp；quot 字符串&amp；quot));
}
}

}
[/sourcecode]

现在我们需要考虑检索字符串资源值的函数，下面的代码片段演示了这一点:

[source code language = " Java "]
/* *

*获取与键相关联的翻译后的字符串值。

*

* @param 上下文

* —访问 API 的活动参考

* @我的钱 name

* —需要字符串的键。

* @param type

*—&quot；字符串&quot；如 R.string.sth

* @return —转换后的值。

*/

公共静态字符串 getStringResource(上下文 Context，字符串名称，

字符串类型){

尝试{

int id = context.getResources()。获取标识符(名称，类型，

context.getPackageName());

返回 context.getResources()。getString(id)；

} catch(异常 e) {

helper . printstacktrace(e)；

返回名称；

}

}

[/sourcecode]

应用于 Android 操作系统，这段代码将以用户选择的语言呈现文本内容，即使该语言是不支持的印度语言之一。我们的自定义代码支持多语言交互，可以在世界任何地方使用。

![aalang](img/3baa4592d11482b1ee49198d2cf4adf6.png)


![aalang3](img/2acf2123ed284bde56dc41fd9e524c36.png)

### 结论

如果你不迎合你自己国家边界以外的用户，你可能会错过巨大的机会。如果你害怕让你的应用程序多语言化，希望这个演示展示了 Android 的多语言能力，以及可以适应甚至不支持的语言的自定义编码。如果你正在寻找更多的用户，更大的受众，或者进入国际市场，不在你的 Android 应用中建立多语言支持是疯狂的。

## 分享这篇文章