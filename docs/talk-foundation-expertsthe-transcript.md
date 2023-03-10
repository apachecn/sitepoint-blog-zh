# 与专家交谈基础:文字记录

> 原文：<https://www.sitepoint.com/talk-foundation-expertsthe-transcript/>

今天，我主持了一场非常有趣的与专家的对话。主题是响应式前端框架[基金会](http://foundation.zurb.com/ "Foundation by Zurb")，我们的专家是设计师亚历克斯·沃克和前端开发人员西蒙·塔加特(两人都使用基金会建立了这个网站)。本周早些时候，我们发布了一个名为[自举还是基金会的视频？](https://learnable.com/jumpcasts/bootstrap-vs-foundation-68/video "Bootstrap or Foundation?")并提供了今天早上的聊天，以便人们在有问题时跟进。

这一小时中出现了一些资源，我在下面列出了它们。

可学习的 Jumpcast: [Bootstrap 还是 Foundation？](https://learnable.com/jumpcasts/bootstrap-vs-foundation-68/video "Bootstrap or Foundation?")
[基础模板](http://foundation.zurb.com/templates.html "Foundation templates")
[基础培训视频](http://foundation.zurb.com/learn/video-started-with-foundation.html "Foundation training videos")
[Sass 培训](http://sass-lang.com/guide "Sass training")
[前端风格指南](http://www.fivesimplesteps.com/products/front-end-style-guides "Front end style guide book")Anna Debenham
[基础侧导航模块](http://foundation.zurb.com/docs/components/sidenav.html "SIde nav module")

在假期结束前，我们还有一年的会议。凯尔·维穆伦拍摄并编辑视频**，将于太平洋标准时间 12 月 3 日周二下午 12:30 开播。你可以在这里找到你那里[的时间。](http://www.timeanddate.com/worldclock/fixedtime.html?msg=Shooting+and+Editing+Video&iso=20131204T0730&p1=152&ah=1 "Time zone converter")**

对于那些对今天早上发生的事情感兴趣的人，这是记录:

[20:27] <HAWK> Is anyone already using Foundation?
[20:28] <Rob> I’ve used it on one live site with version 4.
[20:28] <Rob> Started a new build on 4 and now just porting across to 5, going good so far. :)
[20:28] <jessirwin> I tried using it for the first time (first responsive site for me also) but after spending a couple of days trying to do it, I scrapped Foundation.
[20:28] <HAWK> You didn’t like it jessirwin?
[20:28] <elcapitan> Just started with version 4\. Am really looking forward to version 5.
[20:29] <jessirwin> I was only using the grid – I can see how it could be awesome, but struggled to incorporate it into my build. It is my first responsive site, so maybe I was going/thinking too complicated.
[20:29] <Rob> I specifically used it for the responsive side of the framework and found it really nice on V4\. I’m missing the custom forms on V5 but not too much!
[20:30] <HAWK> So for those of you that haven’t used this chatroom before, you can @ tag people to get their attention
[20:31] <HAWK> I will queue questions if things get busy, otherwise the session is a relaxed one. If you have a question, ask it.
[20:31] <HAWK> Has anyone seen our latest video about Foundation [https://learnable.com/jumpcasts/bootstrap-vs-foundation-68/video](https://learnable.com/jumpcasts/bootstrap-vs-foundation-68/video)
[20:31] <mike> Though I just saw this on FB, I have a quick question. Would it be easy to bounce back and forth between Bootstrap and Foundation?
[20:32] <elcapitan> Just saw it – really nice video.
[20:32] <elcapitan> And I also prefer Sass……
**[20:32] <Simon> Hi mike, I wouldn’t say it’s easy to bounce between, but doable if you have good knowledge of both**
[20:33] <HAWK> Simon – can you give us a brief intro to Foundation? A couple of sentences is cool.
**[20:33] <Simon> Sure**
**[20:34] <Simon> So Foundation pitches itself as the most advance responsive front-end framework in the world, which is a bold claim**
[20:34] <mike> Well, I am sure there are subtle differences between the two but if you knoe one framework well enough, it shouldn’t be too hard to understand and work with the other.
[20:35] <HAWK> For those of you that don’t know Simon, he was one of the team of people that helped us to build the new SitePoint, which uses Foundation.
**[20:35]** **<Simon> I like to think of it less as a framework and more of a very concise, and well tested library of modules and mixins for your front-end development**
[20:35] <mike> and I bet many kegs flowed when the new site went live lol.
[20:36] <sassy> Can the modules be used separately?
[20:37] <Simon> Unlike Bootstrap used to be, Foundation from version 4, was very easily and quickly customised to make a solid responsive site look nothing like the built in styles of the framework
**[20:37] <Simon> sassy Yes totally, as it’s based on Sass, you can pull in the bits you wish to use**
**[20:37] <Simon> for example when I build dashfolio.com, I only used the Foundation semantic grid mixin**
**[20:38] <Simon> You can do similar with Bootstrap of course**
[20:38] <sassy> and is Foundation a compass extension?
**[20:39] <Simon> It’s built using compass**
**[20:39] <Simon> so not an extension**
***[20:40] <alexmwalker> Hi everyone. Sorry I’m a touch late. The internet is down in the SitePoint office so I’m using my phone connection***
[20:40] <jessirwin> Is there an easy way to use it without Compass?
[20:40] <HAWK> No problems. Thanks for being here!
**[20:40] <Simon> I’ve never tried, but I guess you could swap compass for Bourbon**
**[20:40] <Simon> as some find much better performance improvements on compilation times**
***[20:41] <alexmwalker> What’s your issue with using Compass, jessirwin?***
**[20:41] <Simon> But I’m afraid you’re stuck with one mixin library for CSS3 things, like compass**
[20:41] <mike> Thats the one thing that turns me off the most about CSS frameworks in that in order to get the most out of them that you need to know sass or less.
[20:42] <Randyman5775> That’s a swap all right! a Compass helps you find your way, and Bourbon is meant to help you forget where you are and why you’re there. LOL
**[20:42] <Simon> That’s not entirely a downside mike**
[20:42] <johnlacey> LOL Randyman
***[20:42] <alexmwalker> lol Randyman5775***
[20:42] <Viii> Hey you guys … I wanted to ask this question
[20:42] <Viii> wat are the features you desperately want to see in Foundation
[20:42] <Viii> current or next version
[20:42] <persona> what is the best way of using foundation if I want to update it always to the latest version? is adding my own css in top of foundation’s one and overwriting for example input fields, top bar, etc. is a good approach?
[20:43] <Viii> I personally wanted an autocomplete feature very badly
***[20:43]** **<alexmwalker> yes mike, have you tried one of the desktop apps like Scout or Koala?***
**[20:43] <Simon> Viii Yeah a good type ahead module would be excellent, I have to agree**
[20:43] <Viii> persona the best way is to never modify code in the core. You can override any of the !default variables and add your own CSS/SCSS separately
[20:44] <Viii> its okay to override default styling if you don’t have an option
[20:44] <psjmy> Does anyone have any tips on using multiple versions of Foundation with Compass? We need to support old sites, but after upgrading the gem we can no longer compile older versions without errors. I see the command line has changed slightly with F5\. Will it be a struggle to work on sites using F4 if we upgrade our gem?
[20:44] <Viii> but I burnt my fingers meddling with core files…so don’t do it unless you know what you are doing
**[20:45] <Simon> Correct @vii and persona, Only really aim to amend the default variables in the settings file, that way you should be ok to keep updating to the latest version. Using the Foundation Gem is also a really good way**
[20:45] <Viii> Simon I am working on a foundation port for chosen
[20:45] <Viii> [http://harvesthq.github.io/chosen/](http://harvesthq.github.io/chosen/)
[20:45] <mike> well the way I think and my mind works, I can’t force myself to learn to use sass or less feels more complicated than it should.
***[20:45]** **<alexmwalker> @mike, you just set , say Scout, to monitior a folder and it does all the Sass to CSS conversions as you hit SAVE each time***
**[20:46]** **<Simon> Nice, thats a good plugin Viii,**
[20:46] <persona> what are the advantages (if any) of using SASS over adding my own CSS on top of foundation’s one?
[20:46] <Randyman5775> If you use Firebug in Firefox, you can simply copy and past the properties that you need to change and paste in into another style sheet and make your changes
[20:46] <mike> well I am not much of a command line person, I blame windows for that and so I am waiting out gui’s for sass or less
**[20:47] <Simon> persona The only advantage really is the extra goodness Sass can give you for you’re own mixins and variables etc**
**[20:47] <Simon> otherewise, just as good to use plain CSS on top**
***[20:47]** **<alexmwalker> @mike, so if you were to set up Scout and write exactly the same the CSS as you do now, but just save it as ‘filename.scss’, it will work fine. All classic CSS works in SCSS files so you don’t have to learn anything new immediately***
[20:47] <persona> Simon, so there’s no performance differences at all?
**[20:48] <Simon> Randyman5775 has a good point, I often create a partial for any extensions to a Foundation module**
[20:48] <HAWK> psjmy – I have queued your question for the guys :)
[20:48] <psjmy> thanks!
**[20:48] <Simon> so if I need to customise the topbar I create a _topbar.scss file to do just that so it’s away from the core files**
[20:49] <elcapitan> Will the Foundation 5 snippets be available through the Sublime Text Package Manager? I upgraded to Sublime Text 3 a while ago.
[20:49] <Rob> I just downloaded the latest version of Foundation5 last night and replaced 4 on a build, the first thing I noticed (without looking at the logs) was the omission of custom forms. I’m not overly missing it, but what was the main decision behind removing it for 5?
**[20:49] <Simon> persona obviously the more Sass you need to compile the slower your build will get**
**[20:50] <Simon> @psjmy Do you need to support multiple versions is the same website?**
[20:50] <psjmy> Rob – I saw on gitHub that there were problems with the custom forms on mobile. [https://github.com/zurb/foundation/issues/2090#issuecomment-29141263](https://github.com/zurb/foundation/issues/2090#issuecomment-29141263)
[20:50] <mike> alexmwalker if I am going to use classic CSS then using that file extension wouldn’t make much sense. I understand the concept of what Sass and Less are doing but I feel its in another language to me lol
[20:50] <psjmy> @simon, no this is for different sites
[20:50] <psjmy> sites that we have already built using F4
[20:50] <Randyman5775> I would like to see the navigation in the Foundation website cleaned up a bit. Now with all of the F5 stuff there, it’s a bit messy going back to F4.
[20:51] <persona> do you agree that Bootstrap is more for the backend developers who are not necessarily good in design and Foundation is more for designers?
**[20:51] <Simon> OK thats good, psjmy. That shouldn’t be too hard to manage, what seems to be the difficulty? Surely you can tackle one site at a time?**
**[20:51] <Simon> persona controversial question, but yeah I agree ;)**
[20:51] <Rob> @psjmy cheers, I did eventually look through the changelog and see it was out, but as I said, not overly missing it – the biggest change I’m having to make it just with checkboxes, but no biggie. :)
[20:52] <Randyman5775> I like Foundation better than bootstrap, because with the design flexibility a Foundation site doesn’t look like “just another bootstrap site”.
[20:52] <psjmy> Well, the difficulty I had between F3 and F4 was that after upgrading the gem the F3 site would no longer compile correctly
**[20:52] <Simon> Ah, yeah. There is quite a difference between 3 and 4\. There used to be an upgrade guide on the 4 docs**
[20:53] <persona> I’m a PHP developer and I know nothing about design, but I prefer Foundation myself. I’m just struggling to find some free nice looking themes for it. Do you have any suggestions? :-)
[20:53] <Layzie> Hi, guys. I’d like to hear your thoughts on semantics and code bloat while using CSS frameworks. I mean, in order to make large, big, blue button in Twitter Bootstrap you have to use btn btn-lg btn-primary. Does Foundation have any advantage over Twitter Bootstrap regarding this? Thanks.
**[20:53] <Simon> I don’t think that will be an easy fix to make, from what I understand there were some large breaking changes. Which for a framework to make, isn’t exactly great practice. But it happens**
***[20:53]** **<alexmwalker> @mike, at first I think i just used Sass variables, and wrote pretty much pure CSS into those Sass files. Little by little I found new Sass tricks that made my CSS more powerful. The cool thing is is, you don’t have to know all of Sass for it to be immediately useful to you on day 1.***
[20:53] <psjmy> If I upgraded to F5, would I be able to compile an F4 site still then?
[20:54] <Rob> persona – likewise, I’m a horrid designer, but with Foundation I found it easy to get stuck into attempting a bit of design!
**[20:54] <Simon> psjmy I think the upgrade docs only really have to handle differences in the JS modules, which have been rewritten from what I’ve read**
**[20:54] <Simon> so 4 – 5 _should_ be an easier upgrade**
[20:54] <flounder> I guess a LESS user may be more inclined toward Bootstrap, even if they like Foundation.
[20:55] <jaymiejones86> Less, sass, both are very similar and very easy to switch between.
[20:55] <flounder> Nice.
**[20:55] <Simon> persona I haven’t come across any themes, I guess thats the thing I like about Foundation, it’s up to you to apply the design layer**
[20:55] <mike> For me, it just comes down to me sitting down with a stack of books about the topic and going through the process.
**[20:55] <Simon> maybe alexmwalker has?**
***[20:56] <alexmwalker> Layzie, both Foundation and TWBS have mix-ins that let you build neater, more semantic structures. You don’t have to use them, but they work well .***
[20:56] <flounder> So knowing or liking Ruby does not play a roll in choosing Sass?
[20:56] <jaymiejones86> You can still use less with ruby
**[20:56] <Simon> Layzie Good question. The nice thing about Foundation is that most modules are available as a mixin. So you can @include the module into your own css classname**
**[20:57] <Simon> flounder Not in the slightest**
***[20:58]** **<alexmwalker> Less has improved, but generally Sass is seen to be a little more full-featured.***
**[20:58] <Simon> as jaymiejones86 says, LESS and Sass are very similar in a way an quite easy to switch between, I’m currently working on lots of projects with LESS. Although I will say I find Sass much more powerful**
[20:59] <jaymiejones86> I prefer sass as it has some more interesting functionality. But for most sites, what you will use, they are basically on par.
[20:59] <HAWK> Have we missed anyone’s question?
[21:00] <flounder> The question I raise with myself lately. Is learning a framework that much more beneficial ? because surely were just learning another set of syntax.
[21:00] <elcapitan> mine – regarding the Sublime Text snippets being available through the Package manager.
[21:00] <jaymiejones86> Flounder it’s more about speeding up development, not reinventing the wheel.
***[21:01]** **<alexmwalker> On the Foundation theme question. Can’t say i’ve seen a lot out there. Bootstrap has a big theme ecosystem, so if that was a prime considerations for you, Bootstrap might make more sense.***
**[21:01] <Simon> elcapitan I guess only the author of the snippet package can answer that question**
[21:01] <HAWK> Got it elcapitan – hang fire for an answer.
[21:01] <flounder> So once mastered it does truly speed things up ?
[21:01] <Randyman5775> You can always write HTML by hand in a text editor – I’ve been doing it for years. But Foundation is a great place to start with a framework.
[21:02] <flounder> I enjoy writing my html
***[21:02] <alexmwalker> flounder unquestionably***
[21:02] <flounder> Cool.
[21:02] <jaymiejones86> Flounder yes greatly. If you really want to future proof your site though, use your own markup and the mixins
[21:02] <Simon> flounder it makes development extremely fast. We build the majority of SitePoint.com in under 4 weeks
[21:02] <persona> alexmwalker, performance is my main focus and I think Foundation wins this. :-)
[21:03] <persona> I might get a designer to do the design
[21:03] <Randyman5775> @flounder – I always have too, but for making your sites mobile as well as desktop viewable, it’s great.
**[21:04] <Simon> flounder You still get to write your HTML, with the mixins structure you’re not tied to the HTML patterns or css classes**
**[21:04] <Simon> or at least, not so tightly**
***[21:04] <alexmwalker> @flounder If you use the Foundation mixins to construct your classes, there’s no reason to change the way you code your HTML now (by and large)***
[21:05] <flounder> Good response. I have dabbled in Foundation and Bootstrap. Know is time to delve deeper :)
[21:05] <sassy> Simon how did you use or create templates
[21:06] <Simon> How do you mean sassy? For SitePoint?
[21:06] <sassy> yes
[21:07] <sassy> Im not too familiar with Fdtn…does it consist of templates for optional use?
**[21:07] <Simon> So we didn’t have access to Ruby on Rails as SitePoint in a wordpress site, so we started with the downloadable package of Foundation**
**[21:08] <Simon> and ended up using Grunt to compile the stylesheets.**
[21:08] <flounder> Is Sitepoint/Learnable planning on a Foundation intro course ?
**[21:08] <Simon> And then we started how I start every site, by creating an HTML styleguide to build static versions of all our modules we’d need**
[21:09] <Randyman5775> There are tutorials for foundation on the foundation site
***[21:09] <alexmwalker> sassy, this is a relatively new addition to Foundation, but yes, there are now basic templates that you can work with here -> [http://foundation.zurb.com/templates.html](http://foundation.zurb.com/templates.html)***
**[21:09] <Simon> Foundation 4 used to come with what they called the kitchen sink, which I can’t find in 5\. That was a great start for a new build**
**[21:09] <Simon> It listed practically every basic module in Foundation in one page**
**[21:10] <Simon> and you can start by just changing the settings files to match your design variable**
**[21:10] <Simon> from then you can start building your own HTML templates or use some of the pre built ones like alexmwalker said**
[21:11] <Randyman5775> Here’s a link for training videos [http://foundation.zurb.com/learn/video-started-with-foundation.html](http://foundation.zurb.com/learn/video-started-with-foundation.html)
***[21:12]** **<alexmwalker> The templates have Foundation’s generic class names coded into them – like this “<div class=”large-3 columns”>”..***
[21:12] <flounder> TY Randy
[21:12] <Randyman5775> Like most other sites, you have to look for the freebies, because they also have paid training.
[21:13] <HAWK> Are we up to date with questions?
[21:13] <Randyman5775> You can also get Sass training at [http://sass-lang.com/guide](http://sass-lang.com/guide)
***[21:14]** **<alexmwalker> However, if you use the mixins, you can call your DIV something more meaningfull.. say <div class=”main-gallery-display”>.. and then attach all the stuff that ‘large-3 column’ does to your new class***
[21:15] <sassy> Simon I would be interested in knowing your process for creating an “HTML Styleguide”
[21:16] <Randyman5775> One thing I didn’t like about Foundation was finding out how to do vertical navigation. They didn’t make that a very obvious piece of the puzzle.
[21:17] <sassy> but maybe that is another topic
**[21:17]** **<Simon> That’s quite a big topic of conversation sassy. But the main goal is to have a navigable static “styleguide” for your site, which describes every module it’s built with**
[21:17] <Randyman5775> I know that vertical navs are kind of going out of style, but I specialize in updating older sited that the owners want to keep the look the same, but be able to be mobile friendly.
[21:18] <sassy> Well if you know of any articles or tutorials on doing that…thanks.
[21:18] <Steve> What’s this chat thing run on? Some kind of IRC and JS mvc library?
**[21:18]** **<Simon> A back-end developer can then reference the styleguide when integrating the site’s functionality. It’s almost like a living breathing design artefact. A useable step up from Photoshop files**
***[21:18]** **<alexmwalker> sassy, as Simon said earlier, when you set up Foundation, it has a page called ‘kitchensink’ that renders out almost every Foundation element. That ends up being a really good starting point for your own styleguide.***
[21:19] <sassy> I like the idea of using the mixing within one
[21:19] <HAWK> Steve – it’s a heavily modified version of a node.js client called KiwiIRC
[21:19] <sassy> ‘s own style
[21:19] <sassy> mixins
[21:20] <flounder> sassy. This may help you /[http://www.fivesimplesteps.com/products/front-end-style-guides](http://www.fivesimplesteps.com/products/front-end-style-guides)
***[21:20] <alexmwalker> sassy, yes, it does keep things neat.***
[21:21] <sassy> Thanks flounder. just looking for a way to automate things a bit and be more productive instead of reinventing the wheel each time
**[21:21] <Simon> Here’s a collection of Front-end styleguides and pattern libraries that Anna Debenham made on Gimmebar [https://gimmebar.com/collection/4ecd439c2f0aaad734000022/front-end-styleguides](https://gimmebar.com/collection/4ecd439c2f0aaad734000022/front-end-styleguides)**
[21:22] <Randyman5775> @sassy – You could build a page then strip all of the content out of it, and save that as a template.
**[21:22]** **<Simon> Might be useful to look at how others build theirs**
***[21:22]** **<alexmwalker> Randyman5775 The top nav bar at the top if this screen is a standard Foundation component. It breaks down to what people are calling ‘the hamburger’ navigation automatically.***
[21:22] <sassy> There was a learning curve to Bootstrap and I feared the same for Foundation..so I did not try it yet..just built my own from design
[21:22] <sassy> I do like singularitygs though. It is really hands off.
***[21:23] <alexmwalker> The hamburger stacks the nav items vertically and slides the nav panels sideways as you navigations deeper.***
[21:24] <Randyman5775> @alexwalker – yes I know. But I had to figure that out, because the Foundtion website said that a side nav was done with “sections”, which was really code heavy to me.
***[21:25] <Simon> I always thought their “side nav” module was enough of a vertical nav starting point for me [http://foundation.zurb.com/docs/components/sidenav.html](http://foundation.zurb.com/docs/components/sidenav.html)***
**[21:25]** **<alexmwalker> @sassy Randyman5775 Actually it’s maybe even a better process to build the styleguide version, and then copy it to your site. Then you never forget to port your stuff back to the styleguide, even when you’r ein a hurry.**
[21:27] <Randyman5775> Thanks Simon – I hadn’t found that page in the docs!
[21:27] <Viii> Side nav is sufficient for a vertical nav
[21:27] <sassy> The difficulty is in foreseeing the different requirements needed for a site’s pages like galleries, info pages, etc. I guess once you find a inche and with experience you find the patterns
[21:27] <sassy> niche
**[21:28] <Simon> sassy Randyman5775 best practice to definitely to have your site and styleguide share the same assets, css, JS and images**
[21:28] <sassy> But I’m l think this info about style guides will be helpful
[21:29] <sassy> Yes they do that, Simon…but always looking for a way to do it more efficiently to stay competitive…to compete with the likes o squarespace or wiz
[21:29] <sassy> wix
**[21:30] <Simon> sassy In addressing that, I spend a lot of time looking at designs or wireframes first, to identify the majority of patterns for the site up front, before I start building my styleguide**
[21:30] <HAWK> We only have a couple of minutes of the session left. That was a fast hour…
[21:30] <HAWK> Does anyone have any final questions before I let the guys get on with their day?
***[21:30]** **<alexmwalker> sassy you’ll still need to solve HTML/CSS problems. It’s not a lego-like plug and play solution. But it gives you a great bunch of nuts and bolts that you can use to solve issues as they arise.***
[21:30] <Randyman5775> I just want to that you guys for taking the time to educate us a bit more.
[21:31] <sassy> Thanks experts
***[21:31]** **<alexmwalker> Randyman5775 Absolute pleasure***
**[21:31] <Simon> No problem at all ladies and gents, was a pleasure**

## 分享这篇文章