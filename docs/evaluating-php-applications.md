# 评估 PHP 应用程序

> 原文：<https://www.sitepoint.com/evaluating-php-applications/>

从这里的[继续](https://www.sitepoint.com/attack-of-the-killer-bbs/)，当谈到评估 PHP 应用程序时，也许我见过人们问的最常见的两个问题是；

*   看起来好吗？
*   容易安装吗？

现在，并不是每个人都是程序员或系统管理员——“普通人”把这些列得很高，因为它们与他们面临的两个最紧迫的问题直接相关:他们想要一个视觉上有吸引力的网站，并且由于有限的技术专长，安装可能是一个需要克服的重大障碍。

但是当涉及到安全性或维护时，这些需求的级别就相当低了。因此，这里有一些不同的事情需要考虑，从第 19 页的[这个演讲](http://zh.phpug.ch/_media/meetings/phpsecprimer.pdf?id=meetings%3A20060314&cache=cache) (PDF)开始，我认为在评估你计划使用的项目时，这些事情的排名要高得多(感谢进一步的建议)。

请注意，在理想的情况下，您手头有时间和专业知识来进行全面的代码审查，但在现实中这是不可能的，所以我在这里建议的是一个合理的折衷方案，帮助您在不付出巨大努力的情况下建立对应用程序的“大致”感觉。

安全记录怎么样？要找到答案，显而易见的方法是通过谷歌搜索“appName 漏洞”、“appName 漏洞”、“appName 安全”。一个获得更好印象的地方是搜索 [Bugtraq 邮件列表](http://marc.theaimsgroup.com/?l=bugtraq)。

当然，你必须记住，信息的质量可能会有所不同——仅仅找到一个随机的在线意见“appName rox / sux”是不够的。此外，新的或不太受欢迎的应用程序不会吸引足够的注意力，以这种方式形成有效的意见。你必须记住，几乎每一个已经存在并拥有真实用户的应用程序都会在某个时候出现问题，但是比较[这个](http://marc.theaimsgroup.com/?l=bugtraq&w=2&r=1&s=phpbb&q=b)和[这个](http://marc.theaimsgroup.com/?l=bugtraq&w=2&r=1&s=fudforum&q=b)，很容易就能发现不同之处。

顺便提一下，我建议在这个邮件列表的[上注册——几乎所有众所周知(和不太为人知)的 PHP 开源代码库的安全问题都在这里公布。](http://www.phparch.com/phpsec/)

**代码是什么样的？虽然完整的代码审查是不现实的，但是通过一点努力和专业知识，您可以很好地了解代码的味道。**

这里的头号工具是 [phpxref](http://phpxref.sourceforge.net/) ，它使得识别 PHP 函数的使用(或缺少)变得非常容易——通过它运行源代码来检查结果。例如，你可能会寻找使用 [eval](http://www.php.net/eval) (和[friends](http://www.phpwact.org/security/functions/eval_functions))——一般来说，没有任何有效的理由使用 eval，所以如果你找到了，询问开发人员他们到底为什么使用它。你可能还会发现一些函数指标的缺失——如果应用程序在后端使用 MySQL，你是否会发现任何 [mysql_escape_string](http://www.php.net/mysql_escape_string) 、 [mysql_real_escape_string](http://www.php.net/mysql_real_escape_string) 或 [addslashes](http://www.php.net/addslashes) 对 SQL 查询的参数进行转义。 [htmlspecialchars](http://www.php.net/htmlspecialchars) 或 [htmlentities](http://www.php.net/htmlentities) 是否被用于转义输出？对于验证之类的东西， [PCRE](http://www.php.net/pcre) 或 [POSIX 扩展的](http://www.php.net/regex)正则表达式函数有任何用处吗？

否则，您认为代码是什么样的？这是高度主观的，取决于你的经验，但它看起来“理智”吗？

代码是如何被管理的？另一个需要调查的领域是项目实际上是如何运行的。有多少人参与其中，他们是否活跃？他们是否有意识制定发布/升级政策——清晰的版本编号，关于如何升级的良好文档，他们是否使用版本控制，他们的沟通渠道是什么等。？

克里斯·昆兹在给[做这个演讲](http://www.christopher-kunz.de/serendipity/archives/88-PHP-Conference-UK.html)的时候说了一句讽刺的话。他帮助运营一家共享主机公司，并指出他们的大多数用户在第一时间安装应用程序时都非常高兴——一旦安装完毕，他们就不会冒险用升级来破坏它。

作为一个应用程序的用户，你必须意识到跟上新版本的步伐是你的责任，尤其是当它们包含 bug 或安全补丁的时候。作为一个在这里做得很好的项目的例子，查看一下 [Serendipity 的升级文档](http://www.s9y.org/37.html)。你需要问自己的问题是“我能这样做吗？”。你还需要努力保持消息灵通——订阅相关的邮件列表/ RSS 源等。，所以你会听到新的版本。

它有规模吗？在维护方面，当你使用了一段时间，收集了大量数据和一群活跃用户后，这个应用程序是什么样的？那个论坛能应付大量的帖子和并发用户吗？维基是如何处理大量文档的？使用打包的 RSS 提要就像是自愿参加一次 [DOS 攻击](http://en.wikipedia.org/wiki/Denial-of-service_attack)吗？备份/恢复数据有多容易？只有 FTP 访问权限的共享主机帐户足以维护这个应用程序吗？管理界面允许你应付 20，000 个注册用户吗？

其中一些问题可以通过与其他用户交谈来回答。其他的可以通过查看开发人员在做什么来确定，例如，他们是在对他们的代码进行基准测试吗？

谁在使用它？维基百科背后的代码 [Mediawiki](http://sourceforge.net/projects/wikipedia) 显然是一个很好的指标。同时 [Zend](http://www.zend.com/forums/) 使用 [fudforum](http://fudforum.org/forum/) 。顺便说一句，我并不是建议盲目跟随——选择的原因可能与情况不符(你可以随时问),但这确实是一个有用的指标。

你也要小心“从众”。仅仅因为“每个人”都使用它，并不总是意味着它是最聪明的选择。与其他人一样使用*而不是*可能还有一个特殊的好处——大型安装基地是攻击目标。

谁有意见？有很多人“了解”PHP，因此获得意见不成问题。同时，值得考虑的是一个观点来自哪里，并且记住这只是一个观点。有时甚至最有经验的[也不同意](http://shiflett.org/archive/211)。所以这条路虽然有用，但也可能会误导人，但不应该被忽视。

无论如何——我一时想不起来。还有别的吗？

## 分享这篇文章