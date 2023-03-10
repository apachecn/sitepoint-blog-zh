# 蛇汤

> 原文：<https://www.sitepoint.com/snake-soup/>

Web 2.0 的(少数)定义点之一是消费远程数据和服务。如果你的服务提供商是亚马逊、雅虎或谷歌，这很好，但如果是你的[地区选举代表](http://www.dotvoid.com/view.php?id=62)，他们可能刚刚到达 Web 1.0，这就不太好了。能够从这样的网站中挖掘数据越来越成为日常 web 开发的一部分。

无论如何，当[思考](https://www.sitepoint.com/forum-comparison/)T2【forummatrix】或 [wikimatrix](http://www.wikimatrix.org) 缺少什么时，我认为这是一个带 [BeautifulSoup](http://www.crummy.com/software/BeautifulSoup/) 兜风的好借口；“为屏幕抓取等快速周转项目设计的 Python HTML/XML 解析器”，这类工具中较好的(如果不是最好的，根据观点)之一(注意还有同一作者的 [RubyfulSoup](http://www.crummy.com/software/RubyfulSoup/) )。

Beautiful Soup 能够处理几乎最糟糕的 HTML，并且仍然给你一个可用的数据结构。例如给出一些 HTML like

```
<i><b>Aargh!</i></b>

```

…穿过美丽的汤，就像；

```
 from BeautifulSoup import BeautifulSoup
print BeautifulSoup('<i><b>Aargh!</i></b>').prettify() 

```

…我明白了；

```
<i>
 <b>
  Aargh!
 </b>
</i>

```

…注意它是如何改变标签顺序的。这种清理允许我访问内部文本，如:

```
 from BeautifulSoup import BeautifulSoup
soup = BeautifulSoup('<i><b>Aargh!</i></b>')
print soup.i.b.string 

```

这并不是一个完整的教程——[文档](http://www.crummy.com/software/BeautifulSoup/documentation.html)是广泛而优秀的。你应该知道的另一个链接是[URL lib 2——丢失的手册](http://www.voidspace.org.uk/python/articles/urllib2.shtml),它描述了 python[URL lib 2](http://docs.python.org/lib/module-urllib2.html)库(除了别的以外，还提供了一个 HTTP 客户端)。

总之，任务是为[secunia](http://secunia.com/)咨询邮件列表挖掘 [MARC](http://marc.theaimsgroup.com/) ，加速[评估](https://www.sitepoint.com/evaluating-php-applications/)安全记录。

MARC 提供了一个[搜索界面](http://marc.theaimsgroup.com/?l=secunia-sec-adv&w=2&r=1&s=phpbb&q=b)，可以一次显示多达 30 页的结果。除了可以通过 HTTP GET 请求轻松获取之外，MARC 似乎没有经历定期的 HTML 更改(看起来仍然和我记忆中的一样，那些<字体/ >标签是一种放弃)，这希望意味着任何挖掘它的 HTML 的东西在不久的将来都不会被“破坏”。

结果在`advisories.py`；

```
 #!/usr/bin/python
"""
Pulls out secunia security advisories from
http://marc.theaimsgroup.com/?l=secunia-sec-adv
DO NOT overuse!

Make sure you read the following:
    http://marc.theaimsgroup.com/?q=about#Robots

Also be aware that secunia _may_ feel you may be making inappropriate
use of their advisories. For example they have strict rules regarding
content _on_ their site (http://secunia.com/terms_and_conditions/) but
this may not applying to the mailing list announcements

License on the script is GPL: http://www.gnu.org/copyleft/gpl.html
"""
import urllib2, re, time
from urllib import urlencode
from BeautifulSoup import BeautifulSoup

def fetchPage(application, page = 1):
    """
    Fetches a page of advisories, using the marc search interface
    """
    url = 'http://marc.theaimsgroup.com/?l=secunia-sec-adv&%s&%s' 
        % (urlencode({'s':application}), urlencode({'r':page}))
    return urllib2.urlopen(url)

def fetchMessage(mid):
    """
    Fetches a single advisory, given it's marc message id
    """
    url = 'http://marc.theaimsgroup.com/?l=secunia-sec-adv&%s&q=raw'
        % (urlencode({'m':mid}))
    return urllib2.urlopen(url).read()

class LastPage(Exception):
    """
    Used to flag that there are no pages of advisories to process
    """
    pass

class FlyInMySoup(Exception):
    """
    Used to indicate the HTML being passed varies wildly from what
    was expected.
    """
    pass

class NotModified(Exception):
    """
    Used to indicate there are no new advisories
    """
    pass

class Advisories:
    """
    Controls BeautifulSoup, pulling out relevant information from a page of advisories
    and 'crawling' for additional pages as needed
    """

    maxpages = 10 # If there are more than this num pages, give up
    requestdelay = 1 # Delay between successive requests - be kind to marc!

    __nohits = re.compile('^No hits found.*')
    __addate = re.compile('.*[0-9]+. ([0-9]{4}-[0-9]{2}-[0-9]{2}).*', re.DOTALL)
    __messageid = re.compile('.*m=([0-9]+).*')

    def __init__(self, application, lastMsgId = None):
        self.__application = application
        self.__lastMsgId = lastMsgId
        self.__advisories = []
        self.__pages = []
        self.__loaded = 0

    def __loadPage(self, page = 0):
        """
        Load a page and store it in mem as BeautifulSoup instance
        """
        self.__pages.append(BeautifulSoup(fetchPage(self.__application, page+1)))
        time.sleep(self.requestdelay)

    def __hasAdvisories(self, page = 0):
        """
        Test whether page has advisors. To be regarded as not having advisories,
        it must contain a font tag with the words "No hits found". Other input
        raises FlyInMySoup and will typically mean something is badly broken
        """
        font = self.__pages[page].body.find(name='font', size='+1')

        if not font:
            if self.__pages[page].body.pre is None:
                raise FlyInMySoup, "body > pre tag ? advisories?n%s"
                    % self.__pages[page].prettify
            return True

        if self.__nohits.match(font.string) == None:
            raise FlyInMySoup, "Nosir - dont like that font tag?n%s"
                % font.prettify

        return False

    def __hasAnotherPage(self, page = 0):
        """
        Hunts for a img src = 'images/arrright.gif' (Next) in
        the advisories page and if found returns a page number
        to make another request with. Other raises a LastPage
        exception
        """
        if page >= self.maxpages: raise LastPage;

        pre = self.__pages[page].body.pre
        imgs = pre.findAll(name='img', src='images/arrright.gif', limit=5)

        if len(imgs) > 0:
            return page + 1

        raise LastPage

    def __fetchAdvisories(self, page = 0):
        """
        Fetches a page of advisories, recursing if more pages of advisories
        were found
        """
        self.__loadPage(page)

        if self.__hasAdvisories(page):
            advisory = {}
            in_advisory = 0
            pre = self.__pages[page].body.pre
            for child in pre:
                if not in_advisory:
                    m = self.__addate.match(str(child))
                    if m is not None:
                        in_advisory = 1
                        advisory['date'] = m.group(1)
                else:
                    try:
                        advisory['mid'] = self.__messageid.match(child['href']).group(1)
                        advisory['desc'] = child.string.strip()
                        self.__advisories.append(advisory)
                        advisory = {}
                        in_advisory = 0
                    except:
                        pass

            # Some sanity checks...
            if len(self.__advisories) == 0:
                raise FlyInMySoup, "No advisories in body > pre!n%s" % pre

            if in_advisory:
                raise FlyInMySoup, "Still looking for the last advisory"

            # More protection for marc
            if self.__lastMsgId and self.__advisories[0]['mid'] == str(self.__lastMsgId):
                raise NotModified, "Not modified - last message id: %s"
                    % self.__lastMsgId

            try:
                nextpage = self.__hasAnotherPage(page)
            except:
                return
            self.__fetchAdvisories(nextpage)

    def __lazyFetch(self):
        """
        Fetch advisories but only when needed
        """
        if not self.__loaded:
            self.__fetchAdvisories()
            self.__loaded = 1

    def __iter__(self):
        self.__lazyFetch()
        return self.__advisories.__iter__()

    def __len__(self):
        self.__lazyFetch()
        return len(self.__advisories)

if __name__ == '__main__':
    import getopt, sys, csv
    from os import getcwd
    from os.path import isdir, isfile, realpath, join

    def usage():
        """
    advisories.py [-p=proxy_url] [-f] [-d=target_dir] <application>

        Pulls a list of security advisories for a given <application>

        Puts a summary list in <application>.csv and raw text in
        <application>_<msgid>.txt

        options:
            -d, --directory= (directory to write csv and raw msgs to)
            -f, --fetchmsgs (fetch raw messages announcements as well)
            -h, --help (display this message)
            -p, --proxy=http://user:pass@proxy.isp.com:8080
        """
        print usage.__doc__

    def lastMsgId(csvfile):
        """
        Pull out the last message id from the csvfile. Used to test for
        changes if the advisories page
        """
        if not isfile(csvfile): return None
        try:
            fh = open(csvfile, 'rb')
            csvreader = csv.reader(fh, dialect='excel')
            csvreader.next()
            id = csvreader.next()[1]
            fh.close()
            return id
        except:
            return None

    app = None
    proxy = None
    fetchMsgs = 0
    dir = getcwd()

    try:

        opts, args = getopt.getopt(sys.argv[1:], 
            "fhp:d:", ["help", "fetchmsgs", "proxy=", "directory="])
        for o, v in opts:
            if o in ("-h", "--help"):
                usage()
                sys.exit(0)
            if o in ("-f", "--fetchmsgs"):
                fetchMsgs = 1
            elif o in ("-p", "--proxy"):
                proxy = v
            elif o in ("-d", "--directory"):
                if isdir(realpath(v)):
                    dir = realpath(v)
                else:
                    raise "Invalid dir %s" % v

        if len(args) == 1:
            app = args[0]
        else:
            raise getopt.error("Supply an app name to fetch advisories for!")

    except getopt.error, msg:
        print msg
        print "for help use --help"
        sys.exit(2)

    if proxy:
        # Use the explicit proxy passed as a CLI option
        proxy_support = urllib2.ProxyHandler({"http" : proxy})
    else:
        # Prevent urllib2 from attempting to auto detect a proxy
        proxy_support = urllib2.ProxyHandler({})
    opener = urllib2.build_opener(proxy_support, urllib2.HTTPHandler)
    urllib2.install_opener(opener)

    csvfile = join(dir,app+'.csv')
    advs = Advisories(app, lastMsgId(csvfile))

    if len(advs) > 0:

        fh=open(csvfile, 'wb')
        csvwriter=csv.writer(fh, dialect='excel')
        csvwriter.writerow(('date','mid','desc'))

        for a in advs:
            csvwriter.writerow((a['date'], a['mid'], a['desc']))
            if fetchMsgs:
                mfh=open(join(dir, "%s_%s.txt" % (app, a['mid'])), 'wb')
                mfh.write(fetchMessage(a['mid']))
                mfh.close()

        fh.close()

        print "%s advisories found for %s" % (len(advs), app)

    else:
        print "No advisories found for %s" % app 

```

假设你已经安装了最新版本的 [python](http://www.python.org) 和 Beautiful Soup 3.x+(下载 [tarball](http://www.crummy.com/software/BeautifulSoup/download/BeautifulSoup.tar.gz) ，解压到某个地方并运行`$ setup.py install`安装到你的 python 库中)，你可以从命令行运行这个脚本(它是为 [cron](http://en.wikipedia.org/wiki/Crontab) 准备的)，就像；

```
$ advisories.py phpbb

```

…它将创建一个包含找到的所有咨询的文件`phpbb.csv`。还有一些其他功能，比如代理支持和下载原始咨询的能力，您可以通过运行`$ advisories.py --help`来了解这些信息。请务必阅读脚本开头的警告！

所以任务基本完成。有趣的部分是找出在代码中何处放置检查。虽然 Beautiful Soup 允许您阅读几乎任何东西[SGML](http://en.wikipedia.org/wiki/SGML)-比如，MARC 的 HTML 标签结构的变化会破坏这个脚本(毕竟它不是一个官方 API)，所以如果需要手动干预，希望它准备好在正确的地方引发异常。

否则，如果你正在研究 HTML 挖掘，另一个要研究的项目是 [webstemmer](http://www.unixuser.org/~euske/python/webstemmer/) (又是 Python)，它在某些情况下(例如新闻网站)可能足够[聪明](http://www.unixuser.org/~euske/python/webstemmer/howitworks.html)，不费吹灰之力就能得到你想要的东西。

## 分享这篇文章