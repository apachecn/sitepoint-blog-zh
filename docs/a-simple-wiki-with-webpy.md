# 带有 web.py 的简单 wiki

> 原文：<https://www.sitepoint.com/a-simple-wiki-with-webpy/>

在这里偶遇 [web.py](http://webpy.org/) [，而它还未发布，并被 API 设计和](https://www.sitepoint.com/mvc-and-web-apps-oil-and-water/#comment-12309) [Aaron](http://www.aaronsw.com/weblog/) 的评论[这里](http://www.aaronsw.com/weblog/rewritingreddit)所吸引

> 第三个原则是，默认情况下，web.py 应该通过 web 做正确的事情。这意味着正确区分 GET 和 POST。它意味着同义词重定向到的简单、规范的 URL。这意味着具有适当 HTTP 头的可读 HTML。

从那时起，web.py 已经发布，最初的反应是[这里是](http://e-scribe.com/news/183)——同意那些评论，所以不需要重复。

更有趣的是在阅读[文档](http://webpy.org/documentation)和[教程](http://webpy.org/tutorial)的同时，用它拼凑了一些东西——一个非常简单的维基，花了大约 2 个小时才到达它所在的位置(如下)。注意这不是很好的代码 HTML 直接嵌入，违反了 Aaron 的原则(也不想和 [Cheetah](http://cheetahtemplate.org/) 发生冲突),加上我的 Python 技巧不是最好的，但也许它是一个有用的初学者例子。

要安装并运行(Linux)到一个像`wiki.py`这样的文件中，请执行以下操作:

```
 $ wget http://webpy.org/web.py
$ wget http://webpy.org/markdown.py
$ mkdir pages
$ chmod +x wiki.py
$ ./wiki.py 

```

然后将浏览器指向 http://localhost:8080/page/some page 即可开始

代码；

```
 #!/usr/bin/python

import web
from markdown import Markdown
import os, time, re, cgi

# For debugging use only
web.internalerror = web.debugerror

urls = (
    '/', 'WikiPages',
    '/page/([a-zA-Z_]+)', 'WikiPage',
    '/editor/([a-zA-Z_]+)', 'WikiEditor'
)

wikidir = os.path.realpath('./pages')

class WikiPages:

	def GET(self):
		web.header("Content-Type","text/html; charset=utf-8")
		t = re.compile('^[a-zA-Z_]+$')
		wikipages = os.listdir(wikidir)
		print "<html><head><title>wiki pages</title></head><body>"
		print "<h1>Wiki Pages:</h1><ul>"
		for wikipage in wikipages:
			if os.path.isfile(os.path.join(wikidir, wikipage)) and t.match(wikipage):
				print "<li><a href="%(path)s/page/%(page)s">%(page)s</a></li>" 
					% {'path':web.ctx.home+web.ctx.path[1:],'page':wikipage}
		print "</ul></body></html>"

class WikiPage:

	def GET(self, name):
		page = os.path.join(wikidir,name)
		web.header("Content-Type","text/html; charset=utf-8")
		if os.path.exists(page):
			print "<html><head><title>%s</title></head><body>" % name
			print "<h1>%s</h1>" % name
			print "<p>"
			print "[<a href="%s">Pages</a>] " 
					% (web.ctx.home+"/")
			print "[<a href="%s">Edit</a>] " 
					% (web.ctx.home+'/editor/'+name)
			print "</p>"
			print Markdown(open(page).read())
			print "</body></html>"
		else:
			web.ctx.status = '404 Not Found'
			print "<html><head><title>Does not exist: %s</title></head><body>" % name
			print "<p>Page %s does not yet exist - " % name
			print "<a href="%s">Create</a>" % (web.ctx.home+'/editor/'+name)

	def POST(self,name):
		page = os.path.join(wikidir,name)
		if os.path.exists(page):
			newpage = page+'.'+time.strftime("%Y%m%d%H%M%S", time.gmtime())
			os.rename(page,newpage)
		f = open(page, "w")
		f.write(web.input(page='').page)
		f.close()
		web.redirect(web.ctx.home+'/page/'+name)

class WikiEditor:

	def GET(self,name):
		web.header("Content-Type","text/html; charset=utf-8")
		print "<html><head><title>Editing %s</title></head><body>" % name;
		print "<h1>Editing: %s</h1>" % name
		print "<form method="POST" accept-charset="utf-8" action="%s">" 
			% (web.ctx.home+'/page/'+name)
		print "<textarea name="page" cols="100" rows="20">"

		page = os.path.join(wikidir,name)
		if os.path.exists(page):
			print cgi.escape(open(page).read())
		print "</textarea><br><input type="submit" value="Update"></form>"
		print "<p><a href="http://daringfireball.net/projects/markdown/syntax">Markdown Syntax</a></p>"

		print "</body></html>"

if __name__ == "__main__": web.run(urls) 

```

换个时间想想这段经历，我认为亚伦正朝着正确的方向前进。

注意，我把它放在了 [PHP](https://www.sitepoint.com/php/) 下，因为 Sitepoint(还)没有 Python 类别，所以这是从[这里](https://www.sitepoint.com/mvc-and-web-apps-oil-and-water/)开始的，我认为我应该坚持 PHP 主题。

*更新:*需要指出的是，下面的代码有两个问题。

首先，Markdown 解析器不是为转义 HTML 特殊字符而设计的，因此 web.py 中有 web.safemarkdown()函数。换句话说，下面的代码暴露了 XSS 漏洞的风险。

第二，在写入 wiki 页面时，它应该进行某种文件锁定。

## 分享这篇文章