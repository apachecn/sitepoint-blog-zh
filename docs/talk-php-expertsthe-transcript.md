# 与专家讨论 PHP:抄本

> 原文：<https://www.sitepoint.com/talk-php-expertsthe-transcript/>

[与专家交谈](https://experts.learnable.com/ "Talk with the Experts")今天上午很忙，出席人数接近创纪录水平。考虑到主题是 PHP，这并不奇怪。对我来说幸运的是，专家是我们自己的开发人员 Jude Aakjaer 和 Michael Sauter，他们非常快。我甚至不用排队提问。太容易了。

关于聊天，最让我兴奋的事情是，我们终于开始使用我几个月前委托的代码窗口，这是一个巨大的成功。如果你通读下面的文字记录，你会在整个对话中看到保存到 Gists 的代码。

下周的会议很可能是另一个受欢迎的会议——我将与 [Wave Digital 的](http://wavedigital.com.au/ "Wave Digital") Matt Evans 谈论**开发移动应用**。该会议将于 10 月 23 日周三下午 1:30 在**举行，您可以在此参加会议[。](https://experts.learnable.com/ "Talk with the Experts")**

如果你因为不知道而错过了今天早上的讲座，请确保在这里注册接收未来讲座的电子邮件通知。

如果你想知道今天发生了什么，你可以在这里阅读全文:

[20:28] <johnlacey> What’s the most important a beginner to PHP needs to know to get started?
**[20:30] <santouras> johnlacey: like learning any new thing, I think its pretty important to be getting taught by someone who knows what they’re doing**
[20:30] <weebit> Is learning PHP a good thing to do for anyone wanting to make themes for CMS’s?
[20:31] <johnlacey> I notice a lot of people/sites recommend XAMPP for Windows based local development (including Sitepoint’s Kevin Yank). Am I missing out on anything by using WAMP instead?
***[20:31] <michaelsauter> @johnlacey I think there is nothing particular that one needs to know to get started. PHP is one of the languages that is really easy to get into and there is lots of tutorials etc. on the web. i would say that’s one of the problems too: there is a lot of old/outdated/bad information out there***
***[20:31] <michaelsauter> @weebit yes, definitely***
***[20:31] <michaelsauter> weebit lots of CMS are written in PHP, so knowing some PHP to write templates will be helpful***
**[20:31] <santouras> johnlacey I’d recommend using a virtual machine setup personally instead of xampp/wampp**
[20:32] <HAWK> I’ll plug our popular PHP course [https://learnable.com/courses/php-mysql-web-development-for-beginners-13](https://learnable.com/courses/php-mysql-web-development-for-beginners-13)
**[20:32] <santouras> almost all deployment is on linux machines and if you’re coding on windows, it is far easier to have your dev environment mach your production environment**
**[20:33] <santouras> if you’re coding on osx/linux then dev on your local machine becomes more attractive but I’d never code php directly onto windows again**
[20:33] <bobo> hello everybody! how would you prevent post data being resubmitted on refresh/navigation?
***[20:34] <michaelsauter> same here. always better to have a portable virtual machine for dev around***
[20:34] <evilnick> PHP is evil!
[20:34] <Brad82> I second not coding php directly on windows.
[20:34] <McGeough> 1) which is the most used development using PDO or MySQLi? and 2) Which is the best framework around? Codeigniter, laravel, zend etc
**[20:35] <santouras> @bobo this is more of a browser question :) but some strategies include processing the data on post and then forwarding the user to a results page**
[20:35] <HAWK> Welcome if you just joined. Jump in with questions at any time. :)
**[20:35] <santouras> @McGeough I tend to use PDO when I’m not using an abstraction layer**
[20:35] <evilnick> McGeough PDO is better because it provides some level of abstraction, so you can use any database you want
***[20:35] <michaelsauter> McGeough 2) depends very much on your preference. i personally favour Symfony2***
[20:35] <Brad82> McGeough 2) is a very subjective question, some are more suitable than others for different circumstances. I’m a fan of FuelPHP personally
**[20:35] <santouras> the framework question is like a religious argument**
[20:35] <evilnick> also MySQL kinda sucks, try PostgreSQL
[20:36] <McGeough> Ive heard of Symfony2 but never got looking at it
[20:36] <IckleChris> 2) is definitely project based. It’s like trying to decide what size gun to use to shoot a mouse
[20:36] <weebit> So how far up the ladder in PHP in learning should I go if I am just learning PHP for themes? Is there a certain part of PHP I should learn?
**[20:37] <santouras> @McGeough I’ve recently been using Silex which is a trimmed down version of symfony2, definitely handy for small projects**
***[20:37] <michaelsauter> to figure out “which framework should i use”, I think it’s best to look first at your requirements, then what coding style / approach you like and maybe also who you want to work with, then pick a framework***
[20:37] <Paul> Does PDO work with mongoDB
**[20:37] <santouras> @weebit what kind of theming are you referring to? which platform specifically?**
[20:37] <evilnick> Paul of course not, PDO is for SQL
[20:37] <weebit> WordPress and maybe Joomla later on
[20:38] <HAWK> If you just got here, hi. :) Feel free to join in with questions when you’re ready
[20:38] <McGeough> Also which templating engines are best for PHP, so far ive been using Smarty through work and we are wondering if there is anything better and faster?
***[20:38] <michaelsauter> So WordPress uses PHP in a very …. let’s say … interesting … way. Not too much learn to figure out what’s happening in templates***
**[20:39] <santouras> McGeough I’ve been a fan of smarty in the past and used it in a lot of projects. Twig is similar in syntax and is also worth looking at**
***[20:39] <michaelsauter> McGeough again, tempting is also a matter of preference. I’m again in favour of Twig***
[20:39] <sh4d0ws> i think that we need to think about which one will continue having suport in the future… 1 or 2 year… because if we take a lot of time learning and developing something and then after 2 or 3 year the framework is “dead”, its worthless
[20:39] <Brad82> What advantages is there over using a templating engine over just plain old PHP in templates
[20:39] <McGeough> santouras michaelsauter is twig any faster or lightweight etc?
***[20:40] <michaelsauter> In Twig you don’t have to / can’t write any PHP, but it’s still very fast as it compiles to PHP***
[20:40] <Brad82> Of course I only mean echo and foreach() for example in templates.
**[20:40] <santouras> Brad82 the biggest advantage is separation of concern**
[20:40] <evilnick> Brad82 well, they’re not as ugly as PHP
[20:40] <johnlacey> I realise this is a very subjective/personal thing… but just out of curiosity what’s your preferred code editor?
**[20:40] <santouras> it clearly defines the logic and presentational aspects of your application**
[20:40] <cliffgs> Net Beans is my preferred editor
[20:40] <IckleChris> I’m an Aptana Studio guy
**[20:40] <santouras> johnlacey my personal is ST2, and ST3 when it becomes stable, but I use vim from time to time**
[20:41] <McGeough> ST2
[20:41] <Brad82> ST2 for me also
[20:41] <McGeough> ST3 just isnt stable enough right now
[20:41] <sh4d0ws> anyone now how to get all partitions in nix from PHP without parsing “df” ?
[20:41] <evilnick> Sublime and vim are the best
[20:41] <programmer> My question is about security. I am connecting to a MySQL database, and I have the connection constants in a separate file. Should I place that file outside the document root, just to be sure? And if so, how do I reference it?
***[20:41] <michaelsauter> i used to code a lot in PHPStorm, now using Sublime***
[20:42] <Brad82> McGeough I’ve been using ST3 for months now and it hasnt crashed on me once (touchwood)
[20:42] <sh4d0ws> ST2 :D
**[20:42] <santouras> programmer absolutely. If at all possible the only code in the web root should be the bootstrapping index.php file and any public assets**
**[20:42] <santouras> almost all modern frameworks operate like this**
***[20:43] <michaelsauter> programmer PHP can still include that file, but there is a setting in php.ini which allows/denies this. I think it’s on by default***
[20:43] <programmer> I haven’t been able to find an example of this, however. I am using IIS.
[20:43] <McGeough> Brad82 ive used it a few times and it did crash, maybe i just had bad luck, but will be upgrading
**[20:43] <santouras> programmer are you deploying to IIS or just using that for development?**
**[20:43] <santouras> welcome MalCurtis :)**
[20:44] <MalCurtis> Thanks! I have to say the two experts have very handsome profile pics
[20:44] <amir> ST3 is good in terms of speed
[20:44] <programmer> I’m actually using apache for local development, but have a true development environment in IIS. Production is IIS.
[20:44] <amir> but something is still missing
[20:44] <Brad82> I still have nightmares about using IIS at work for production.
**[20:44] <santouras> Brad82 me too :( and I haven’t had to deploy to IIS in around 8 years!**
[20:44] <programmer> I’m not too crazy about IIS myself, but the project is an addition to an existing site, so I don’t have much choice.
[20:45] <HAWK> Yay! The code editor!
**[20:45] <santouras> magic is happening**
[20:45] <Brad82> programmer is it IIS7 you are deploying to?
[20:45] <johnlacey> I don’t think I’ve ever seen anyone use the code editor in here before. lol. Exciting times.
[20:45] <McGeough> Slightly off topic but for those using Version Control would you ever take your whole projects (all projects on system) folder as a repository or separate repository for each project
**[20:46] <santouras> separate repo**
[20:46] <HAWK> I know johnlacey! I challenged the guys to use it ;)
[20:46] <McGeough> whos the magician using it?
**[20:46] <santouras> McGeough most of my php projects these days use composer so this is very natural**
**[20:46] <santouras> michaelsauter I believe!**
[20:47] <McGeough> composer?
***[20:47] <michaelsauter> yup that’s me***
[20:47] <evilnick> can’t edit in the code editor :| how do we use it
***[20:47] <michaelsauter> forgot how to write constants outside a class :D***
[20:47] <Brad82> evilnick ask HAWK
**[20:47] <santouras> [http://getcomposer.org/](http://getcomposer.org/)**
**[20:47] <santouras> basically PHP’s version of bundler**
[20:47] <programmer> so relative ../ path from where I start? I assume I have to go all the way up from wherever the code is.
***[20:48] <michaelsauter> yea in my example your document root is htdocs/***
***[20:48] <michaelsauter> config/ is not accessible by IIS***
[20:48] <evilnick> IIS sucks
**[20:48] <santouras> the rubygems equivalent is [https://packagist.org/](https://packagist.org/)**
**[20:48] <santouras> evilnick this has been established and agreed upon**
[20:49] <programmer> I wish I didn’t have to use IIS
**[20:49] <santouras> almost all actively developed php projects are available on packagist**
[20:49] <programmer> I don’t think trying to mix two web servers is a good idea, though.
**[20:49] <santouras> if you’re putting together a new project, you should definitely give it a look, it will make using external libraries a lot easier**
[20:50] <programmer> thanks
***[20:51] <michaelsauter> Saved editor: [https://gist.github.com/6998459](https://gist.github.com/6998459)***
[20:51] <evilnick> HAWK can I use the editor?
[20:52] <HAWK> Yup evilnick – give it a go now
[20:52] <programmer> must run a quick errand. back later.
***[20:52] <michaelsauter> composer really is an awesome project. it also helps with autoloading, all that comes with it by default***
**[20:52] <santouras> php has made enormous strides in the last few years. if things like composer and PSR-0 had been around 8 years ago it would have helped a lot**
***[20:53] <michaelsauter> that’s one of the things which is relatively new and which might not be mentioned in a lot of tutorials, although it is incredibly helpful to learn after you know some PHP basics***
**[20:53] <santouras> and things like boris -> [https://github.com/d11wtq/boris](https://github.com/d11wtq/boris)**
**[20:54] <santouras> welcome new people**
[20:54] <Paul> I think grunt also fits into this picture
***[20:54] <michaelsauter> grunt is more for frontend tasks, and is running on nodejs. but definitely something that makes life a lot easier :)***
[20:56] <Gerwin> can we talk about using namespaces?
**[20:56] <santouras> sure**
***[20:56] <michaelsauter> sure!***
***[20:56] <michaelsauter> what would interest you?***
[20:56] <Brad82> namespaces <3
[20:57] <Gerwin> i haven’t used them before but i really like what they do, can we go over a good use case, when and when not to use them?
[20:57] <Gerwin> any short falls etc
[20:57] <pamelasue101> I’m listening today, I don’t know what questions to ask and I’m just learning
[20:57] <eip56> Can answer mine after. But hows does everyone feel about get/set methods in PHP OOP. Create gets and sets or use magic methods?
[20:57] <HAWK> All good pamelasue101 :)
[20:57] <harlem> I wanted to secure this “news.php?news_id=$row[‘id’]”, I heard that I could encrypt the id ? Can you someone guide me with this ? Sorry I speak french my sentences will not come gramaticaly correct
[20:58] <Paul> don’t classes pretty much put namespaces to the wayside?
[20:58] <Brad82> harlem what advantages would you gain by encrypting the ID?
***[20:58] <michaelsauter> I think it’s almost always a good idea to use namespaces, except when you just have maybe 3-4 files***
**[20:58] <santouras> Paul, no not really**
[20:58] <evilnick> namespaces is an attempt to create something like Modules in Ruby
***[20:58] <michaelsauter> if you’re writing a library or using a library you shoulddefinitely use them***
[20:58] <Gerwin> thought as much
[20:59] <Gerwin> to avoid clashes with other common class names?
[20:59] <Gerwin> so
[20:59] <Gerwin> users
[20:59] <Gerwin> would be gerwins_plugin\users
[20:59] <Gerwin> something like that?
***[20:59] <michaelsauter> the trickiest is to autoload the namespaces, but there are standards which make that quite easy. so if you stick to those standards you and other libraries can load the classes by default***
***[20:59] <michaelsauter> see for example [https://www.sitepoint.com/autoloading-and-the-psr-0-standard/](https://www.sitepoint.com/autoloading-and-the-psr-0-standard/)***
[20:59] <Gerwin> awesome
[21:00] <IckleChris> I hear a lot about how creating a CMS/Framework is a great way to further develop basic PHP skills.. Is this really a good place to start, and are there any pointers for this?
**[21:00] <santouras> eip56 I like them in theory because it cuts down on the number of accessor methods you need to write, but they are slower than direct function calls**
[21:01] <Hulkur> Ickle, i don’t recommend
***[21:01] <michaelsauter> @eip56 I personally prefer get/set methods, but they are a bit tedious to write, which is why I prefer PHPStorm when I do extensive PHP programming***
[21:01] <Brad82> IckleChris some say a blog is a good thing to create if you are new to PHP
***[21:01] <michaelsauter> (or some other IDE)***
**[21:01] <santouras> IckleChris it probably isn’t the best idea to create a cms/framework unless you’ve had extensive experience working on one in the past**
[21:01] <Hulkur> if you don’t know language you are not able to use it properly
[21:01] <harlem> I wanted to secure the transfer of that information, I read somewhere it can open up for different attacks
[21:01] <eip56> Also in my opinion IckleChris pick up like CodeIgniter or something similar
***[21:01] <michaelsauter> Gerwin yea, but you would use CamelCase, so GerwinPlugin\Users***
**[21:01] <santouras> @harlem it sounds like you’re referring to SQL injection?**
[21:01] <Brad82> harlem would the ID be numeric or alphanumeric?
[21:01] <eip56> it will accelerate your learning curve especially on OOP topics
**[21:02] <santouras> @harlem are you using a framework or library? or writing all of your code directly**
[21:02] <Paul> I’d like to know how to reverse engineer wordpress so I can see exactly how it works. Any input on that?
[21:02] <Brad82> @paul as a wordpress user and lover myself, that is a very… interesting task
[21:02] <Gerwin> oh right that looks nicer
***[21:02] <michaelsauter> Paul WordPress is open source, so you can just read the code base***
**[21:02] <santouras> @Paul [https://github.com/WordPress/WordPress](https://github.com/WordPress/WordPress)**
[21:02] <evilnick> is my code correct?
***[21:03] <michaelsauter> Paul However, I wouldn’t recommend that :)***
[21:03] <harlem> yes
[21:03] <Brad82> Paul learn to love the WordPress Codex, it is extremely useful
[21:03] <eip56> @Paul there is really no need to reverse engineer it is well documented int he codec
***[21:03] <michaelsauter> WordPress has a lot legacy code …. and is a huge and complex system***
[21:03] <evilnick> haven’t used PHP for a long time, and not going to
**[21:03] <santouras> hmm, I’m not seeing the code editor update**
[21:03] <harlem> the id is numeric
***[21:03] <michaelsauter> me neither***
[21:04] <HAWK> evilnick – what code?
[21:04] <evilnick> hmm
[21:04] <evilnick> so it doesn’t show up?
[21:04] <eip56> Whats everyone’s favorite IDE… Ive used several but my main atm is just Programmers notepad
**[21:04] <santouras> @harlem are you using a framework?**
[21:04] <eip56> Id like to find a good one for my Mac
**[21:04] <santouras> eip56 ST2 is popular**
**[21:04] <santouras> michaelsauter is a fan of phpstorm**
[21:05] <Hulkur> what’s ST2 ?
[21:05] <HAWK> evilnick Save it to gist using the settings cog and see if we can see it then
[21:05] <evilnick> Saved editor: [https://gist.github.com/6998654](https://gist.github.com/6998654)
[21:05] <santouras> sublime text 2
[21:05] <Hulkur> storm is good, netbeans is free
[21:05] <Brad82> harlem, in reality I see very little reason to encode the ID. You should focus more on securing your scripts internals so that anything the user sets as ID can be parsed securely.
[21:05] <harlem> I read this book PHP Mysql web development
[21:05] <cliffgs> That’s what I usually do. I connect by include_once $_SERVER[‘DOCUMENT_ROOT’] . ‘/../code/admindb.inc.php’;
[21:05] <cliffgs> And Net Beans is cross platform
[21:05] <HAWK> Ok, so that worked evilnick – not sure why we’re not seeing it live
[21:06] <Hulkur> netbeans is java, so it eats memory
[21:06] <harlem> santouras I am not using any framework
**[21:06] <santouras> harlem the most important thing is sanitisation of any user input**
***[21:06] <michaelsauter> Hulkur I think almost all IDEs are Java :(***
[21:06] <Brad82> harlem Remember, even if in your app you encode the query string, I can always type into the address bar manually an exploit, in this case encoding it has no effect
[21:06] <OliverThomas> Is there any useful PHP plugins for Notepad++?
**[21:06] <santouras> unless you’ve verified it, don’t trust it**
[21:07] <cliffgs> Hulkar memory is cheap and plentiful
**[21:07] <santouras> use a regex to ensure/strip out, anything that is not what you’re expecting. and if you get funny results, don’t go further**
***[21:07] <michaelsauter> OliverThomas not sure, i have never used it.***
[21:07] <harlem> Brad82 what can I do than I am using a php regex script to make what I $_GET is what I want
[21:08] <OliverThomas> Hi michael, i see. what program do you recommend for PHP development?
***[21:08] <michaelsauter> Give sublime text a try, it has lots of plugins***
[21:08] <Hulkur> Memory is plentyful, but NB still eats it all
[21:08] <Hulkur> and is slow
[21:08] <evilnick> yea :D
[21:08] <Paul> I don’t know what it’s written in, but aptana eats memory too
[21:08] <evilnick> Aptana is Eclipse
[21:08] <Hulkur> storm somehow is faster and less hungry
**[21:08] <santouras> aptana is java**
[21:08] <Brad82> harlem, you said the ID will be numeric? In that case all you require is to do is_numeric() on the $_GET variable, no slow regex required!
[21:08] <OliverThomas> Thank you i will check it out
[21:08] <Michael> I use UltraEdit Studio for PHP dev
**[21:09] <santouras> if your sentence is “[editor] eats memory” it’s most likely java based ;)**
[21:09] <Hulkur> does sublime support navigating to class/function ?
[21:09] <Aaron> I like PHPStorm
**[21:09] <santouras> yes**
**[21:09] <santouras> Hulkur yes**
***[21:09] <michaelsauter> Yup, so that’s why PHPStorm was/is my choice, it seemed to be the fastest amongst the heavier IDEs. But nowhere near ST or Textmate etc.***
[21:09] <Brad82> hawk can I get editor for a sec?
[21:09] <harlem> Brad82 santouras
[21:09] <HAWK> It doesn’t seem to be working properly
[21:09] <OliverThomas> michaelsauter is it dangerous to loop through $_POST superglobal when processing form data
**[21:09] <santouras> [http://www.sublimetext.com/](http://www.sublimetext.com/) <- go to anything**
[21:09] <Gerwin> I’m guessing most people here use phpMyAdmin? That right?
[21:10] <harlem> thank you guys Brad82 santouras any links ?
[21:10] <HAWK> Give it a go now Brad82
***[21:10] <michaelsauter> OliverThomas not unless you’re doing something with that data like entering it into the database***
[21:10] <Paul> I just started using vim. it’s pretty powerful
[21:10] <evilnick> What the hell am I even doing here, I just remembered that I hate PHP :D
***[21:10] <michaelsauter> OliverThomas what do you want to achieve?***
**[21:10] <santouras> Gerwin no, I used SQLYog on windows/linux, and SequelPro on mac**
**[21:10] <santouras> I’m allergic to phpmyadmin :)**
[21:10] <OliverThomas> I use prepared statements to insert
[21:10] <Aaron> What’s the best way to analyze code for security issues? I don’t have the money for Acunetix, are there any other options?
[21:10] <Gerwin> Cool, if anyone is interested, i recently built this [http://cabindb.com/](http://cabindb.com/)
[21:11] <OliverThomas> just seeking advice on the best way to do certain things
[21:11] <Hulkur> new phpmyadmin tree is awful
[21:11] <Gerwin> yeah
***[21:11] <michaelsauter> Gerwin looks nice!***
**[21:11] <santouras> Gerwin nice, very nice, will look at that**
[21:11] <Michael> I use Navicat
**[21:11] <santouras> I almost always prefer desktop apps, but will give that a look**
[21:11] <Gerwin> thanks, it’s pretty young and i’d love for people to try it out, but don’t rely on it yet :)
***[21:12] <michaelsauter> OliverThomas so your looping over $_POST, and inside the loop using prepared statements?***
[21:12] <Brad82> harlem, check editor as simple as that!
[21:12] <Hulkur> there is something better than PHP ?
[21:12] <Hulkur> can’t possibly be
[21:12] <MalCurtis> Looks good Gerwin (and Hi!)
[21:12] <jaequery> people still learn PHP?
***[21:12] <michaelsauter> OliverThomas I’d be very cautios even then, maybe better to just loop over keys you define, and then look up the superglobal value from there***
[21:13] <Brad82> harlem [https://gist.github.com/6998745](https://gist.github.com/6998745)
[21:13] <eip56> jaequery you blasphemer
[21:13] <eip56> PHP rocks
[21:13] <OliverThomas> michaelsauter no no, I am processing the form data using a foreach() to trim the data and then go through each field individually checking for the various requirements, then including a prepared stateement script.
[21:13] <evilnick> thou shalt use Ruby
[21:13] * evilnick commences the holy war
[21:13] <Gerwin> ruby syntax may induce vomiting
**[21:13] <santouras> Aaron have you looked at PHPMD?**
[21:13] <jaequery> it was a serious question :x
**[21:13] <santouras> Gerwin evilnick there is room in the world for many languages :)**
[21:14] <Hulkur> ruby syntax does induce vomiting
[21:14] <evilnick> not for PHP
[21:14] <santouras> some people still use cobal
***[21:14] <michaelsauter> jaequery I think PHP is not a bad choice. It’s easy to get into, and depending where you live, lots of jobs. Sure other languages have a better design, but there are lots of apps written in PHP***
[21:14] <Aaron> santouras I haven’t…
***[21:14] <michaelsauter> OliverThomas sounds good then***
[21:14] <evilnick> PHP doesn’t even have a naming convention
[21:14] <Gerwin> MalCurtis hey man, saw your name. Yeah it’s a start, with limited amount of spare time I have haha.
[21:14] <jaequery> my php codes are good. i just can’t stand the others (wordpress, drupal, joomla, yuck)
[21:14] <Hulkur> evilnick, you mean not forced upon you naming convention
[21:14] <Paul> I havn’t figured out and easy way how to paste code from an outside file into vim yet other than some long command in a macro
[21:15] <evilnick> I mean core library naming convention
***[21:15] <michaelsauter> evilnick [https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md)***
[21:15] <Paul> I wish I could in vim
[21:15] <MalCurtis> Gerwin can you run arbitrary SQL queries? That’s one of my main uses for any sql tool
[21:15] <Elie> naming convention? lol use your own
***[21:15] <michaelsauter> PHP has changed in the past few years***
[21:15] <Gerwin> MalCurtis Yes sir, with syntax highlighting!
[21:15] <OliverThomas> Thanks michael for the feedback
[21:15] <MalCurtis> sheeet son.
[21:15] <Hulkur> about namespaces, there is really not use if yoou name your classes something like My_Package_Class
[21:15] <jaequery> honestly, i think it has turned for the worst
[21:15] <evilnick> yeah but it’s no way to determine which function to use because they use differrent naming conventions
[21:15] <Gerwin> And user permissions are easy to do!
[21:15] <Hulkur> then there is no conflict
[21:16] <harlem> thanks Brad82
**[21:16] <santouras> Hulkur I would disagree, mainly due to code beauty :)**
[21:16] <MalCurtis> Hulkur yea that’s great until you see some big ones
[21:16] <jaequery> the autoloader discussion on php-fig sickens me
[21:16] <Hulkur> i work on magento, big enough
[21:16] <Gerwin> DL it and try get it going locally, have a play. I haven’t tested on too many machines yet, mac and pc works and my AWS machine too. Still needs more testing, all js one page styles
[21:16] * evilnick ragequits
[21:16] <HAWK> evilnick has just ragequit
[21:17] <MalCurtis> Like: Sitepoint_Courses_PageController_CourseAdminController
***[21:17] <michaelsauter> haha :)***
[21:17] <eip56> HAWK this one you probably know best but anyone else as well. A little more general can anyone recommend a service for checking cross browser compatibility. Hopefully free but I know there are a few services where I can check my site in older virtual-ized browsers
**[21:17] <santouras> MalCurtis :(**
[21:17] <MalCurtis> * insert vomit emoticon here *
[21:17] <jaequery> what is this chatroom made in?
[21:17] <Hulkur> how it’s shorter with namespace ?
[21:17] <Brad82> MalCurtis I just had a mini stroke looking at that
[21:17] <jaequery> is it opensource?
[21:17] <MalCurtis> when you’re inside a namespace you don’t need to type the whole thing…
[21:17] <programmer> Are there any other recommended checks for get and post variables? I see something in the code window.
***[21:17] <michaelsauter> Hulkur you can import namespaces, and then just use the class name***
[21:18] <MalCurtis> jaequery kinda, I started with kiwiirc, which is an open source node.js irc client, then added in a bucket load of customizations
[21:18] <Hulkur> can you show me some correctly made namespaces
[21:18] <Brad82> programmer that was a very simplified example to check if a get var was numeric
***[21:18] <michaelsauter> programmer so either you’re using a framework that does all that for you, or the best is to know what you’re using a variable for, and then sanitizing it specifically for that***
[21:18] <Hulkur> i think i don’t understand them properly yet
**[21:19] <santouras> Saved editor: [https://gist.github.com/6998820](https://gist.github.com/6998820)**
[21:19] <Brad82> programmer it depends what you are doing with said variable on how you sanitise/verify it
[21:19] <HAWK> Can anyone help out eip56 with good browser compatibility testing services?
[21:19] <Hulkur> this is small, how do you place models and views ?
[21:19] <programmer> What is the best way to sanitize, then? Mostly I have strings.
[21:19] <Hulkur> you still need long names
[21:20] <Hulkur> just / instead of _
[21:20] <Brad82> @hulker
[21:20] <jaequery> is it more recommended to do Sitepoint or \Sitepoint
[21:20] <programmer> And most of those should be only a single character Y or N
***[21:20] <michaelsauter> @programmer depends where you want to use that string. when you want to display it, use htmlspecialchars or similar, when for mysql, use some mysql escape function***
**[21:20] <santouras> jaequery generally you would use Sitepoint unless you wanted to ensure you hit the root level**
[21:21] <programmer> OK, I think I’ve got mysqli_real_escape or something like that.
**[21:21] <santouras> jaequery you see this most often with people explicitly using the \Exception class**
[21:21] <programmer> Because they go into a db query
[21:21] <Paul> elp56 I like [http://quirktools.com/screenfly/](http://quirktools.com/screenfly/)
[21:21] <jaequery> why wouldnt you always want to start from root?
[21:21] <Paul> but that’s screen resolution
[21:21] <HAWK> eip56 Are you on Twitter? I’ve just tweeted your question from @sitepointdotcom
***[21:22] <michaelsauter> Hulkur see my extension of the code. you can use the short names once you import a namespace at the top of the file***
**[21:22] <santouras> jaequery because it resolves to any imported namespaces**
***[21:22] <michaelsauter> programmer yea that’s good if you use it inside a sql query***
[21:22] <AlexF> Another nice benefit of namespaces is aliasing use Some\Other\Namespace as NS;
**[21:22] <santouras> so starting from root kind of takes away the whole point of “use”**
[21:22] <jaequery> how does namespace handle things where you have Sitepoint\* , and someone else also has Sitepoint\* out of sheer luck?
[21:23] <Nofel> i had been fan of php since many years but how can I apply, u can’t get project without proving ur a php expert so how u practice a php project to test skills
[21:23] <Brad82> programmer running $_REQUEST variables through mysqli_real_escape… is enough before you put it in a SQL query
[21:23] <santouras> Nofel best thing is to write an application as a personal project and host the code on github
[21:23] <Paul> elp56 this is free crossbrowser [http://browsershots.org/](http://browsershots.org/)
[21:24] <programmer> Excellent. I also am checking length of input.
[21:24] <eip56> HAWK I am 
[21:24] <Hulkur> thou shalt read manual ! :) found my missing knowledeg use My\Full\Classname as Another;
[21:24] <eip56> :-( and i thought we were friends!
[21:24] <Nofel> santouras u mean make ur own project?
***[21:24]** **<michaelsauter> jaequery that’s a problem then. however, if you follow standards and upload your library to packagist.org, that shouldn’t happen***
[21:24] <Hulkur> namespace aliasing and no long names anymore
[21:24] <Nofel> what is
**[21:24] <santouras> jaequery the documentation covers resolution – [http://php.net/manual/en/language.namespaces.rules.php](http://php.net/manual/en/language.namespaces.rules.php)**
[21:24] <Brad82> programmer first run all the variables through your mysql_escape… and then use those cleaned variables in strlen($var) checks afterwards
[21:24] <jaequery> thats why im confused , isnt the puprose of namespace to avoid collision when there is one?
[21:25] <MalCurtis> The purpose is to provide an encapsulated and insulated place to write a package of code, which happens to avoid collisions as a part of it
***[21:25] <michaelsauter> Hulkur afaik this only aliases the namespace, not the class***
[21:25] <Hulkur> yes, but then you use alias as short namespace
**[21:26] <santouras> Nofel yes, writing your own project as a sample application**
[21:26] <Nofel> I follow the php training by Kevin skougland on Lynda. He do refactoring in essential training which gets out of understanding, how important it is to know refactoring at early stage.
[21:26] <Brad82> programmer remember, its de facto to use $variable = mysqli_escape..($_GET[‘variable’]); and then use $variable throughout the script later on (for example in the strlen() check).
[21:26] <HAWK> There you go eip56 :) Anyway, check out our stream later on to see what people suggest
[21:26] <programmer> What about input that will be returned to the user as a file?
[21:26] <Hulkur> according to php.net you can alias classes
***[21:26] <michaelsauter> Hulkur so, it would be \My\BadlyNamedNamespace as PerfectlyNamedNamespace. and then PerfectlyNamedNamespace\SomeClass***
[21:26] <jaequery> MalCurtis, what about Zend / PEAR way , as long as you have started your folder as vendor name, it won’t cause conflicts right?
[21:26] <Brad82> programmer that would not need any sanitising
[21:26] <HAWK> Ok people, 5 mins left. If you have a question that you haven’t asked, now is your last chance!
[21:27] <Nofel> I was reading php OOP and I couldn’t understand parent and what does it do
[21:27] <Brad82> parent refers to the class from which the current class was extended from.
***[21:27] <michaelsauter> jaequery using namespace, you still start with the vendor name***
[21:27] <Nofel> Brad82 like a example?
**[21:27] <santouras> Nofel parent will call the parent function from a subclass. which is handy if you are redefining a function**
[21:27] <programmer> My other question is probably too complicated and I should probably ask on the Sitepoint forum.
[21:27] <Hulkur> B extends A => parent is A
[21:27] <jaequery> only thing namespace solves is that it can shorten the class name
[21:28] <jaequery> which imo w/ IDE is not really that big of an issue
***[21:28] <michaelsauter> jaequery basically, only use libraries that follow psr0,1,2\. then you won’t have conflicts and good interoperability***
[21:28] <Brad82> Nofel there is a great example of parent at [http://php.net/manual/en/keyword.parent.php](http://php.net/manual/en/keyword.parent.php)
[21:28] <jaequery> and why do we have so many PSRs
**[21:28] <santouras> jaequery it is also important as MalCurtis mentioned to help you write encapsulated code**
[21:28] <jaequery> shouldnt we just have a “single” one?
[21:29] <Paul> Notel … yep I’m doing it now
[21:29] <jaequery> how do we standardize it when there are so many w/ new ones being added every month
***[21:29] <michaelsauter> jaequery they address different issues***
[21:29] <Hulkur> 0 is autoloader, 2 is code style iirc
[21:29] <Paul> Notel do it with restful services and ajax or json
***[21:29] <michaelsauter> See [https://github.com/php-fig/fig-standards](https://github.com/php-fig/fig-standards)***
[21:29] <Nofel> Brad82 that was helpful.
[21:30] <jaequery> ah gotcha
[21:30] <Brad82> @nor
[21:30] <jaequery> i like this chat, when is the next one and how often do you guys do it?
[21:30] <Brad82> Nofel, do you need that explaining any further?
**[21:30] <santouras> jaequery every week!**
[21:30] <Paul> Notel do a wordrpess plugin
**[21:30] <santouras> but not always on PHP ;)**
[21:31] <Nofel> i am trying to learn magento and it’s all OOP so what I need php or OOP or mvc
[21:31] <HAWK> Aaaannnnddd… that’s a wrap!
[21:31] <jaequery> i’d like to see a chat for angular
[21:31] <HAWK> I’m going to cut our experts free whenever they want to go
[21:31] <HAWK> The rest of you are free to stay as long as you like
[21:31] <programmer> Thank you so much. This was great.
[21:31] <Hulkur> @Nofel you need someone who knows magento
[21:31] <Nofel> Brad82 why use parent when u can refer to the function
[21:31] <HAWK> You’re in luck jaequery – there is an Angular one in a couple of weeks
**[21:31] <santouras> thanks HAWK :)**
***[21:31] <michaelsauter> Thanks for joining!***
**[21:31] <santouras> thanks room!**

## 分享本文