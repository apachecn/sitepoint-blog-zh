# 与专家对话:HTML5 文字记录

> 原文：<https://www.sitepoint.com/talk-experts-html5-transcript/>

没有什么能说服我错过在健身房的一次训练，这表明我对在我们的专家聊天室里招待今天早上的客人是多么兴奋。这些专家是 SitePoint 的首席作家克雷格·巴克勒和前歌剧开发员蒂芙尼·布朗。今天的主题是 **HTML5** ，这两个人非常有资格谈论它——他们是即将推出的 Jump Start HTML5 背后的团队。

时间一分一秒地过去，问题纷至沓来，公平地说，克雷格和蒂芙尼以令人钦佩的沉着应对了压力！

如果你因为不知道而错过了今天早上的会议，你可以通过在这里注册未来会议的电子邮件提醒来纠正。

接下来是为网络写作:和汤姆·帕金一起拼写 T4 语法。该会议将于太平洋时间 9 月 12 日星期四下午 2:30 举行，或者您可以在您的地方[这里](http://www.timeanddate.com/worldclock/fixedtime.html?msg=Talk+Spelling+%26+Grammar+with+the+Experts&iso=20130913T0930&p1=22&ah=1 "Time Zone converter")找到会议时间。

不管怎样，事不宜迟，这是今天早上会议的完整记录。我希望你和我一样喜欢它。

[17:58] <HAWK> So let’s kick off. Thanks very much to Craig and Tiffany for their time today.
[17:58] **<Craig> It’s a pleasure.**
[17:58] ***<TiffanyBrown> Thanks for hosting us.***
[17:59] <HAWK> Craig and Tiffany are the team behind the upcoming SitePoint book Jump Start HTML5, which I’m really looking forward to.
[17:59] <HAWK> So maybe we could kick off with a very brief intro to exactly what HTML5 is TiffanyBrown
[17:59] ***<TiffanyBrown> Sure.***
[18:00] ***<TiffanyBrown> HTML5 is the latest version of HTML, but I think that’s too simple of an answer.***
[18:00] **<Craig> It’s a big question!**
[18:00] ***<TiffanyBrown> What’s really new is that 1\. it provides rules for a consistent DOM***
[18:01] ***<TiffanyBrown> and 2\. It adds a slew of new APIs to HTML — things that we would have used plugins for or JavaScript for are now native to HTML5***
[18:02] ***<TiffanyBrown> (And I wish I could type as fast as I think :-) )***
[18:02] <HAWK> Heh. Hearing you.
[18:02] **<Craig> I think it’s worth pointing out that HTML5 can be thought of in two ways…**
[18:02] **<Craig> 1\. The next revision of HTML with 30 or so new tags, or**
[18:03] **<Craig> 2\. The media term “HTML5” which means modern, cutting-edge cool browser stuff!**
[18:03] **<Craig> In that respect, it’s a superset of HTML, CSS3 and JavaScript APIs.**
[18:04] ***<TiffanyBrown> I’m going to disagree slightly Craig.***
[18:04] **<Craig> Oh…!**
[18:04] ***<TiffanyBrown> A lot of those JavaScript APIs have been built into the HTML5 spec.***
[18:04] <Wynnefield> As well as the new CSS3 attributes.
[18:05] <Wynnefield> Indirectly?
[18:05] ***<TiffanyBrown> They’re really an HTML5 API***
[18:05] **<Craig> Well, yes, that’s true. Although the specifications are modular, so features are added and removed all the time.**
[18:05] <ParkinT> As an “old timer” (who remembers when HTML first appeared), I disagree with all that Javascript API stuff being part of the Hyper Text standard
[18:05] <ralphm> I think of them as “browser APIs” rather than HTML5 APIs.
[18:05] <ParkinT> I think it has diverged far from its roots as a “text” “transport” protocol
[18:06] **<Craig> HTML as a separate technology still exists … until something better comes along!**
[18:06] **<Craig> (Which is unlikely!)**
[18:06] <Peter> I have a general question and please excuse any dumb assumptions: as the HTML versions grow is HTML getting closer to XML in that we will be able to create our own tag?
[18:06] ***<TiffanyBrown> It’s a little bit of a semantic / pedantic debate of course. But I usually explain HTML5 as Markup+APIs.***
[18:06] <Wynnefield> HTML5 already allows custom tags, yes?
[18:06] <Mathi> is there any possible wirte html5 in ie8 browsers.. i mean any addon there?
[18:07] **<Craig> It’s happening now. I’ll just see if I can find the spec…**
[18:07] ***<TiffanyBrown> I think this is it:** [https://dvcs.w3.org/hg/webcomponents/raw-file/tip/spec/custom/index.html](https://dvcs.w3.org/hg/webcomponents/raw-file/tip/spec/custom/index.html)*
[18:08] **<Craig> You beat me to it!**
[18:08] ***<TiffanyBrown> My Google-fu is amazing Craig :-)***
[18:08] <Peter> ah thanks
[18:08] <Wynnefield> Thanks for the ref
[18:08] **<Craig> There are also related technologies such as modules and the Shadow DOM.**
[18:08] ***<TiffanyBrown> to answer @mathi’s question, however: HTML5 can be written for any browser, but IE8 doesn’t support all HTML5 elements***
[18:09] <programmer> So, is HTML5 technology more about doing things you could not do before, or is it a better way of doing them?
[18:09] **<Craig> …but a small shim can enable HTML5 tags in IE8.**
[18:09] <Warppe> HTML5 rocks had an article about defining new elements as well, was a nice one :) sorry for late comment :)
[18:09] **<Craig> programmer: a bit of both, I’d say.**
[18:09] <Mathi> TiffanyBrown Thanks :-)
[18:09] <Wynnefield> I believe it is a much “cleaner” way to create the page.
[18:10] ***<TiffanyBrown> But keep in mind that a shim won’t be able to add video or audio support.***
[18:10] <HAWK> Link to it if you have it Warppe
[18:10] <Warppe> [http://www.html5rocks.com/en/tutorials/webcomponents/customelements/](http://www.html5rocks.com/en/tutorials/webcomponents/customelements/) there ya go
[18:10] <Wynnefield> As well as easily documented by using custom tags to define containers and page areas.
[18:10] <dms222> how long do we think until HTML5 is the accepted standard?
[18:10] <ralphm> One of the features of HTML5 is the new document outline, which I gather is not recognised by devices yet. There seems to be some concern that it never will be recognised, or at least that there is enough confusion over the right use of the new sectioning elements that we have already sort of broken this document outline already. Do you guys have a
[18:10] <ralphm> ny views on that?
[18:10] **<Craig> Yes, that’s true. Although you can use shims to add fallback support for some features, e.g. flash**
[18:10] <Warppe> had to get that from feedly, its a bummer it doesnt have a search feature :S
[18:11] <lorio> Safari doesn’t seem to recognize my <section> <nav> tags…would they be considered tags?
[18:11] **<Craig> ralphm: are you referring to <section>, <article> etc?**
[18:11] <ralphm> Yes.
[18:11] <Craig> lorio: it should – check your doctype.
[18:11] ***<TiffanyBrown> dms222 asked: how long do we think until HTML5 is the accepted standard? it’s already an accepted standard.***
[18:12] <lorio> it is HTML
[18:12] <Wynnefield> it is HTML on steroids
[18:12] **<Craig> The HTML5 specs can be quite vague about certain use-cases.**
[18:12] ***<TiffanyBrown> honestly, the question now is browser adoption and support***
[18:12] <dms222> tiffany: yes, thats more what i mean – full browser adoption
[18:12] <ParkinT> I wanted to ask an expert’s opinion. Regarding HTML5 do you recommend (or not) using Twitter Bootstrap or something similar?
[18:13] <ParkinT> I write very little markup – mostly write code. So, I need a simple way to embrace the changes.
[18:13] **<Craig> ralphm: that said, use whatever you think is correct. Don’t fret too much!**
[18:13] <Mathi> i have problem with inspect html elements in iphone and ipad. is there any developer tool for iphone
[18:13] <Mathi> like fore bug
[18:13] ***<TiffanyBrown> that’s hard to say, dms222\. the spec is kind of a moving target. IE10 &11 have much better support than previous versions, but user adoption plays a role too.***
[18:13] <Warppe> Adobe inspect works for me Mathi
[18:14] <Mathi> Ok thanks warppe.. let me check
[18:14] **<Craig> ParkinT: you can use Bootstrap or any other toolset but, personally, I’d recommend diving in and writing your own code.**
[18:14] ***<TiffanyBrown> re: whether to use Bootstrap or another framework, I think that depends on your project.***
[18:14] <Warppe> No props m8! ^,^
[18:15] ***<TiffanyBrown> frameworks are often faster when building applications rather than static pages. but there’s also a learning curve involved. there isn’t a clear answer. i usually don’t use them.***
[18:15] **<Craig> Nor me.**
[18:15] <Wynnefield> What about using Webkit, Firefox spec or any other non-standard CSS3 attributes?
[18:15] <HAWK> I had an interesting one yesterday. A Learnable member complained about our use of HTML5 to embed video because he has lost the functionality that he liked with Flash.
[18:15] **<Craig> There’s a lot to be said for starting with a blank sheet.**
[18:15] <dms222> TiffanyBrown, from what i understand, its going to be a while before all the browser manufacturers can agree on practices. I wonder how long before everyone can agree on things and i can stop using moz-rulename css
[18:16] <ParkinT> dms222 makes a great point. Isn’t the whole idea of STANDARDS to keep all things consistent?
[18:16] <Wynnefield> Last year I was using webkit until realized not all browsers are friendly with it.
[18:16] <flutebrarian> I’d like to embed audio/video – is there one codec that is better than another for converting from CD/DVD?
[18:16] **<Craig> There are several HTML5 libraries, such as video.js, which provide Flash-like features. You can also write your own in JavaScript.**
[18:16] ***<TiffanyBrown> ah… @dms222, that’s happening far faster now than ever. for many properties, you can stop using prefixes.***
[18:16] <ParkinT> Why (in 2013) are we STILL struggling with hacks based on different browsers?!
[18:17] <dms222> @parkinT from what i gather, thats the intention of HTML5 but manufacturers still can’t agree on things yet
[18:17] ***<TiffanyBrown> no, flutebrarian there isn’t. you will need to encode in H.264 and WebM or Ogg for the time being.***
[18:17] <lorio> Do you recommend Modernizr for better browser acceptance of the HTML5 elements?
[18:17] <Wynnefield> Yes. I probably have some old CSS code to replace with the new standard.
[18:17] **<Craig> Wynnefield: I don’t think it’s a problem using vendor prefixes, but be wary about components which are only supported in one browser.**
[18:17] <ralphm> @Craig I guess you can just look at it as an array of new element options, but it’s interesting to consider what might happen if the new sectioning rules do get recognised by browsers. For example, it seems that a <nav> with no heading element might report back to the user the absence of a heading. I’m not convinced a <nav> should really be a secti
[18:17] <ralphm> oning element.
[18:17] ***<TiffanyBrown> that’s sound advice, craig***
[18:17] <Wynnefield> agreed!
[18:18] ***<TiffanyBrown> there’s also prefix-free by lea verou***
[18:18] **<Craig> ParkinT: things are much, much better than they were!**
[18:18] ***<TiffanyBrown>** [http://leaverou.github.io/prefixfree/](http://leaverou.github.io/prefixfree/)*
[18:18] ***<TiffanyBrown> and SCSS / LESS can smooth out a lot of the prefix stuff for you***
[18:18] ***<TiffanyBrown> or Compass***
[18:18] **<Craig> …”hacks” are rarely necessary, but you may require a shim for cross-browser support if you’re coding for older browsers.**
[18:19] <Warppe> or grunt w/ node and bower and.. :D
[18:20] **<Craig> lorio: Mozernizr has its uses, but you won’t necessarily need it for every project. Unless you’re using something cutting-edge, many of the elements and APIs are consistent.**
[18:20] **<Craig> Progressive enhancement is still a good technique too.**
[18:20] <dms222> re: hacks. i guess thats the crux, making HTML5 pages that older browsers can see. last time i looked 55% of browsers are html5 ready. any comment?
[18:20] ***<TiffanyBrown> oh, and flutebrarian if you need to convert from one video format to another, try FFMpeg or Miro Video Converter***
[18:21] ***<TiffanyBrown> how do you define “HTML5 ready” dms222\. HTML5 as a doctype is supported by just about every browser.***
[18:21] <flutebrarian> TiffanyBrown- thanks! checking them out now for a big conversion project from Real Media
[18:21] **<Craig> dms222: 55% sounds a little pessimistic!**
[18:21] ***<TiffanyBrown> but elements are a different story.***
[18:22] **<Craig> If you look at every HTML5 element, API etc, no browser supports them all.**
[18:22] <dms222> EXACTLY
[18:22] <ralphm> @lorio Also, Modernizer doesn’t actually make up for the lack of browser support, but just detects what the browser does support.
[18:22] **<Craig> … but they never supported HTML4 or CSS2.1 either!**
[18:22] ***<TiffanyBrown> … element and API support, I mean. Some of that can be fixed with shims, plugins, and polyfills.***
[18:23] <Wynnefield> True
[18:23] ***<TiffanyBrown> It’s kind of the same situation we’ve always had.***
[18:23] **<Craig> A site such as caniuse.com will help you determine whether a range of target browsers supports the technology you want to use.**
[18:23] <chuck> Is there a utility to convert flash video to an html5 video and does this make sense?
[18:23] <Wynnefield> Anytime there is an enhanced tech, it takes a while for the browsers to catch up.
[18:23] ***<TiffanyBrown> And they’re faster now than they used to be :-)***
[18:23] <lorio> ralphm it does replace the shim needed to recognize the new elements as divs, as I understand it.
[18:24] <Wynnefield> And just before all of them have arrived, the tech enhances again?!?
[18:24] <HAWK> Isn’t that the nature of our industry Wynnefield? ;)
[18:24] ***<TiffanyBrown> do you mean an FLV, chuck?***
[18:24] **<Craig> chuck: Flash video is often a standard format (such as MP4) in a FLV wrapper.**
[18:24] **<Craig> You can convert most formats to any other!**
[18:24] <dms222> i’ve also heard that HTML5 will be the ‘last’ update too. Don’t know how much water that holds!
[18:24] <bruce> is there any site or tool that shows what your web pages looks like with different browsers at different levels… I have been trying to design for only htl5
[18:24] <Wynnefield> TiffanyBrown Agreed regarding browser speed up.
[18:25] <chuck> @TiffanyBrown, yes
[18:25] **<Craig> It’s not. The HTML5.1 specification is being worked on.**
[18:25] ***<TiffanyBrown> I think Adobe Media Converter will let you export to H.264 or MP4\. Miro and FFMpeg will let you convert formats.***
[18:25] <Wynnefield> unfortunately, it is, HAWK
[18:25] <Wynnefield> :(
[18:25] <DiskScanner> @Chuck Try DVDVideoSoft. It will convert almost anything
[18:26] **<Craig> Bruce: services such as browsershots works, but there’s no substitute for running it on a real browser.**
[18:26] <Escodsm> Was Adobe Media converter discontinued?
[18:26] <chuck> Thank you.
[18:26] <plinkplink> Unrelated question: have any of you guys used Adobe Edge for a production project? Is it worth learning in your opinion?
[18:26] **<Craig> Microsoft provide a range of free virtual machines at** [http://modern.ie/](http://modern.ie/)
[18:26] <Wynnefield> Isn’t that available only with CC?
[18:26] ***<TiffanyBrown> dms222: there are two versions of the HTML5 spec…one from the WHATWG and one from the W3C***
[18:27] <bruce> I find it very challenging to have browsers at different levels of release for testing purposes….
[18:27] ***<TiffanyBrown> the W3C’s spec is versioned. the WHATWG version is kind of a rolling spec: features are added and removed based on consensus. it’s not entirely clear which version most vendors follow.***
[18:27] <Wynnefield> @Escodsm Adobe Media, I meant.
[18:27] <plinkplink> Wynnefield: I got the beta download a while back, don’t know if it’s available as stand-alone
[18:27] <Warppe> Have you guys gotten real chances to use the webstorage apis? What have been the major points of use, other than applicationcache for offline support with web apps :) just wondering. Kind of off topic of whats rolling down the screen at the moment, but Ill just throw this in.
[18:27] **<Craig> plinkplink: I’ve not tried it, but it sounds quite good. Personally, I still prefer coding in a text editor though.**
[18:27] <programmer> Say I’m designing a website from scratch. I like to get the best bang for the buck. What should I do to get the most out of my efforts from HTML5? Are there some features or APIs that are “must know”?
[18:27] <Wynnefield> oops, Media=Edge
[18:27] ***<TiffanyBrown> so HTML5 is “the last version” in that the WHATWG version is rolling.***
[18:28] <Escodsm> Wynnefield I only have AMC with 5.5
[18:28] <plinkplink> Craig: agreed. I just don’t want to miss out an easier way to do something. I suspect that it will be the equivalent of Dreamweaver…
[18:28] <DiskScanner> programmer I will be using BOOTSTRAP.
[18:28] <bruce> thank you… I was not aware of browsershots !!!
[18:28] **<Craig> Warppe: Offline support is probably the most obvious use of the web storage APIs, but it has also been used for local caching to speed up web apps.**
[18:29] <HAWK> If any of you lurkers have a question, don’t be shy :)
[18:29] <GP> I do
[18:29] <davisem> do you think we’ll see games and the like moving from flash to HTML5?
[18:29] <HAWK> Go for it GP
[18:29] ***<TiffanyBrown> Warppe I’ve only tinkered with LocalStorage. it can be a little bit slow because it’s synchronous. i don’t recommend storing and reading a lot of data using LocalStorage.***
[18:29] <GP> ok writing it…it’s big
[18:29] <HAWK> All good :)
[18:30] **<Craig> programmer: it’s difficult to keep up with what’s available. I recommend following HTML5 websites … like SitePoint!**
[18:30] <dms222> davisem the best games i’ve seen out there are ones that are HTML5 and incorporate flash as well
[18:30] <HAWK> Doesn’t that kind of defeat the purpose of HTML5 dms222?
[18:30] <Warppe> Craig, yeah, though I’ve been trying to use my imagination to get other points of use as well :)
[18:30] ***<TiffanyBrown> Warppe once asynchronous databases like IndexedDB get adopted by browsers, I think you’ll be able to do more powerful things, like document storage.***
[18:30] ***<TiffanyBrown> davisem: yes.***
[18:31] <dms222> HAWK for sure it does!
[18:31] **<Craig> bruce: I think modern.ie is still offering 3 months free on BrowserStack.**
[18:31] <davisem> dms222 the only one i’m addicted to is unfortunately in flash ^^;
[18:31] <programmer> I was going to write a webapp in Flash, but decided html5 would be better use of my time at this point.
[18:31] ***<TiffanyBrown> @davisem, that’s the short answer. WebGL and 2D canvas offer a lot of possibilities for game development. As always, browser support is the problem.***
[18:31] <HAWK> Considering the size of the mobile market these days, wouldn’t HTML5 always be a better call than Flash?
[18:31] <Warppe> :) indexedDB is something I just recently dug into :) seems to be a blast.
[18:32] <dms222> HAWK absolutely, but there are things flash can do that html5 just can’t – at least not easily
[18:32] ***<TiffanyBrown> it’s kind of a draw, HAWK for games you need processing power.***
[18:32] <HAWK> Got it. That makes sense.
[18:32] <GP> ok here we go
[18:32] <GP> Write now I am on a project with a coworker where we’re creating a LAYOUT that displays 3 products in a comparison type fashion. He wants to use tables now because he is having difficulty re-aligning the column sizes after page load. (different content lengths, JS resizes columsn so that titles and images are in line) I’m trying to convince him N
[18:32] <GP> OT to use tables, but he won’t listen. I’m throwing it out here for some opinions. Use tables for non-tabular data or not?
[18:32] **<Craig> I think it’s worth pointing out that Flash is still a viable technology. Browser support is good (iOS apart) and it’s easier to write an app in a mature development environment.**
[18:33] <Escodsm> flash is so awesome for apps and games. I don’t understand why people can’t just write cleaner AS3 to make CPUs stop overheating
[18:33] **<Craig> However, if it’s a choice between learning HTML5 or Flash, go for HTML5.**
[18:33] <Wynnefield> CSS Tables or Table tags?
[18:33] <dms222> @GP sounds like you should use vertical divs with a % based width
[18:33] <GP> table tags
[18:33] <dms222> tables are SO 1996!
[18:33] <Escodsm> Css Tables??? HUH?
[18:33] ***<TiffanyBrown> @GP is it a comparison table?***
[18:33] <Escodsm> What is a CSS Table?
[18:33] **<Craig> Tables are not dead!**
[18:33] <Warppe> :D I remember how I started doing front-end with tables :) hehe
[18:33] <Escodsm> Tables Are dead
[18:34] <HAWK> Warppe ditto
[18:34] <GP> TiffanyBrown no, not a comparison table. just represents products side by side
[18:34] <davisem> tables are for food!
[18:34] **<Craig> They’re not: HTML tables should be used for tabular data.**
[18:34] ***<TiffanyBrown> Tabular data doesn’t necessarily mean “numeric data”***
[18:34] <GP> there is no tabular data in it
[18:34] <Escodsm> LOL davisem
[18:34] <DiskScanner> davisem LOL
[18:34] <Warppe> I think tables can be still used for displaying data, but I use em with caution.. alot of caution :D
[18:34] <jeff> tables are still completely valid for tabular data. hence the name. the right tool for the job always.
[18:34] <Escodsm> tables are for Excel
[18:35] <programmer> Craig ” it’s easier to write an app in a mature development environment. ” Yes! that’s just my problem. I want to use something using an immature tech and don’t know where to start!
[18:35] ***<TiffanyBrown> @gp in that case, i would go with Craig’s suggestion.***
[18:35] <dms222> tables shouldn’t be used for layout
[18:35] <DiskScanner> Again @gp take a look at Bootstrap
[18:35] <GP> @TiffanyBrown Thank you!
[18:35] **<Craig> Tables should not be used for layout though.**
[18:35] <jeff> 1px x 1px transparent gifs!!!!!
[18:35] <GP> DiskScanner – we’re using bootstrap too :) haha
[18:35] <programmer> I hate tables.
[18:35] <GP> Some people are just impossible to convince. :)
[18:36] **<Craig> programmer: I recommend picking a project and starting. You’ll learn a lot on the way even if that project is ultimately scrapped.**
[18:36] <Escodsm> I have one
[18:36] <GP> Okay, thank you for answering my query everyone! Cheers! :)
[18:36] <programmer> Does Bootstrap have a steep learning curve?
[18:36] <davisem> Wait. What is a CSS Table though?
[18:36] <Escodsm> Should I remove “Flash Developer” from my resume to make it more attractive
[18:36] <Warppe> programmer, nah :) just like any other “framework” I think :)
[18:37] **<Craig> davisem: do you mean a CSS grid?**
[18:37] <Escodsm> And replace it with “AS3/AIR Developer”
[18:37] <GP> Escodsm – I would target the resume to the job.
[18:37] <HAWK> Escodsm No way. it’s a totally valid skill to have, even if it’s not required.
[18:37] <ParkinT> Escodsm: change it to FLASHY Developer. <g>
[18:37] <davisem> Someone said it earlier
[18:37] <Warppe> programmer, and check out Foundation as well :) good alternative
[18:37] **<Craig> Escodsm: if you have Flash skills, there’s no harm in having it on your CV. It’s still being used.**
[18:37] <kathy> console games use AS3
[18:37] ***<TiffanyBrown> programmer bootstrap is easy to get started with, but it takes a little more work to become an expert. just like anything. and +1 to checking out Foundation.***
[18:37] <Escodsm> console games? like what
[18:37] <kathy> some console games
[18:37] <ralphm> There’s no CSS table as such, but table display, as in display: table, display: table-cell etc. Very handy.
[18:38] <lorio> CSS table is a table styled in CSS instead of inlineHTML, or display: table
[18:38] <davisem> What would be a good way to learn frameworks?
[18:38] ***<TiffanyBrown> build small projects, davisem***
[18:38] <davisem> Like how to use them?
[18:38] <Wynnefield> @Ecodsm: This is similar to my earlier ref to css tables: [http://en.wikibooks.org/wiki/XForms/CSS_tables](http://en.wikibooks.org/wiki/XForms/CSS_tables)
[18:38] <kathy> Adobe is re-purposing Flash and mre will be coming on that soon
[18:38] <kathy> more
[18:38] * Fletcher (6307f8bb@localhost) has joined #sitepoint
[18:38] <davisem> I’m a bit amateur at this :|
[18:38] <jeff> question: the new elements (article, aside, etc…) are they really so much better than just class=”article”
[18:38] ***<TiffanyBrown> Building a To Do list application is a good way, IMO.***
[18:39] <HAWK> davisem – That’s why we run these sessions :)
[18:39] <programmer> That would be “Foundation” from zurb.com?
[18:39] <GP> Escodsm – I’ve interviewed a lot of front end developers at my company and it was always nice to see their past experience even if it wasn’t related to the job. However, if it’s not related to the job you’re applying, I would add it to a technical addendum along with your years of experience with the technology.
[18:39] <Warppe> yeah :)
[18:39] ***<TiffanyBrown> yes, jeff :-)***
[18:39] **<Craig> davisem: your best bet – start using it. But I would recommend writing your own HTML5 pages and apps before jumping in with a framework.**
[18:39] <HAWK> That’s what we used to write the new SitePoint programmer
[18:40] ***<TiffanyBrown> @jeff what the new elements offer is more consistent repurpose-ability,***
[18:40] <kathy> Here is Adobe’s movement for Flash for gaming: [http://www.adobe.com/devnet/games.edu.html](http://www.adobe.com/devnet/games.edu.html)
[18:40] <GP> HAWK – that’s cool. I guess I need to read the newsletters a bit more. I didn’t know you used bootstrap! :)
[18:40] <Warppe> I love this type of discussions :) I work with mainly java programmers, which dont seem to care for the stuff that happen in the browsers end :( its sad. Though I will always fight for it :D
[18:40] **<Craig> The reason the new HTML5 elements exist was because many people were using <div id=”header”>, etc in their code.**
[18:40] <HAWK> GP – not bootstrap, Foundation :)
[18:40] <ParkinT> Is Adobe Air now passe ?
[18:41] <GP> HAWK – oops, guess I missed that too. I’m on a roll.
[18:41] ***<TiffanyBrown> @jeff the original goal of HTML was to have a consistent way of describing a document’s structure so that a user agent would know what to do with it.***
[18:41] <Ahmad> sorry if the question seemed foolish, but what is the use of <!doctype html>
[18:41] <HAWK> GP ;)
[18:41] <HAWK> No question is foolish Ahmad!
[18:41] ***<TiffanyBrown> Ahmad it’s a legacy thing …***
[18:41] **<Craig> ParkinT: perhaps – Adobe Air possibly arrived a little too late, but it’s still a viable technology should you need it.**
[18:41] <kathy> Air runs many crossplatform mobile applications via PhoneGap.. Not yet
[18:41] ***<TiffanyBrown> most browsers added DOCTYPE switching at some point***
[18:42] **<Craig> in about 1999!**
[18:42] <ParkinT> The ‘doctype’ is part of the XML legacy. It defines the Schema to reference
[18:42] <lorio> HAWK how did you like using Foundation?
[18:42] <davisem> is there anything that is completely no good anymore?
[18:42] <GP> So here’s an HTML5 question… When I start out a project, things are very well structured in terms of using the new HTML5 tags (header, footer, etc…). However, as the project grows, things seem to get out of hand and it becomes a guessing game as to where to use <section> vs <div>, etc…. Has anyone else experienced this?
[18:43] ***<TiffanyBrown> @ahmad … and any document without a doctype would be rendered in “quirks mode.” the shorter HTML5 doctype forces/ tricks the browser into using standards mode.***
[18:43] <jeff> davisem – you mean other than IE? ;)
[18:43] <Wynnefield> @davisem: IE5? … lol (Sorry MS)
[18:43] <programmer> The nice thing about using browsers without plugins is that people don’t have so many things to update. I hate the way adobe and Java need to be updated all the time, on top of updating the browsers.
[18:43] ***<TiffanyBrown> @ahmad that was actually a very good question :-)***
[18:43] <ralphm> @Ahmad Without a doctype declaration at the top of your page, the browser may go into “quirks mode”, which you want to avoid. That new doctype is the shortest one that will stop browsers doing that.
[18:43] <Warppe> Oh, this is kind of off topic.. and maybe a question with too many loose ends, but do you guys still support ie7 or ie8?
[18:44] <davisem> I mean more like programs or coding languages.
[18:44] **<Craig> GP: it can be tricky and the specs are vague, but just use the most appropriate tag for the content. It won’t always be obvious, but don’t have sleepless nights!**
[18:44] <HAWK> lorio – I wasn’t part of the dev team in this case, but I know that the the guys loved it. One of our front end guys recommended it and it was uncomplicated from what I understand.
[18:44] <TonyChungca> jeff +∞
[18:44] <Warppe> I know it depends on your customers far end clients.. and alot of other things, but in general.
[18:44] <Ahmad> TiffanyBrown ok, thanks a lot :)
[18:44] <Escodsm> thanks
[18:44] <davisem> @GP Me! I rarely know what to use
[18:44] <Escodsm> GP
[18:44] <lorio> Could you explain the use of container as it is being used in modern HTML
[18:44] ***<TiffanyBrown> what do you mean lorio?***
[18:45] <GP> Escodsm – you’re welcome.
[18:45] <Wynnefield> Browser support depends on your audience.
[18:45] <GP> Craig – thanks Craig. Definitely no sleepless nights over such a trivial thing, but was curious given the community on this chat. :)
[18:45] <HAWK> Warppe – we still *mostly* support IE8\. Def not 7!
[18:45] <Escodsm> As a Flash IDE user and AS3 programmer, I don’t understand what else to use were I not to have those tools anymore
[18:45] <Warppe> Wynnefield I know :) but in general :)
[18:45] **<Craig> Personally, I don’t bother with IE6 or 7 any more and only test in IE8\. However, never aim for pixel perfection – it’s futile!**
[18:45] <Escodsm> How else would you make the assets? Sprite sheets?
[18:45] <Wynnefield> I mean working for a defense contractor, support for IE was mandatory and the only browser concerned with.
[18:45] <lorio> I mean it seems to be used in different ways.. to contain all the elements inside body, or as a class with several containers
[18:45] **<Craig> Incidentally, I am available for hire at exorbitant rates to solve IE6 issues!**
[18:46] <lorio> TIffanyBrown
[18:46] <Wynnefield> Of course this upset all us Mac-aholics, since I checked everything at home in Safari and Chrome.
[18:46] ***<TiffanyBrown> @escodsm you can use sprite sheets. you can also use SVG, depending on your project.***
[18:46] <jeff> Craig?Tiffany….of the changes introduced in HTML5, which would you say have had the most impact on the way you work and/or have made your life easier.
[18:46] <Warppe> Wynnefield, :) aggreed. I was involved with a labor union site project, and we even supported ie6 back then, and this was a year ago :)
[18:46] <Wynnefield> Ouch!
[18:46] <Warppe> Though I understood it! Its allright :)
[18:47] <GP> Warppe – this can be a difficult one. We constantly fight the battle of old browsers being used and all would like to use the most latest and greatest. However, sometimes your browser choice is tied to finances. If your customer base only uses (and purchases) using an old browser, then you’d want to offer a great experience for them to keep those
[18:47] <GP> $$$ rolling in.
[18:47] ***<TiffanyBrown> I would also like to volunteer my IE6 skills at a more reasonable price :-P***
[18:47] <programmer> Escodsm: Flash would probably have been overkill for my project.
[18:47] <Warppe> heh, but pain.. and alot of tears n blood.
[18:47] <Escodsm> ie6 – I solve it by browser detection, and saying rudely: “UPDATE!”
[18:47] **<Craig> jeff: wow. Big question. I think cross-browser consistency has been the biggest change. HTML5 forms are great too.**
[18:47] <kathy> @Escodsm – if you want to use an IDE for HTML 5 animation and interactivity and use your HTML and JavaScript skills, try Adobe Edge Animate
[18:47] ***<TiffanyBrown> @jeff the consistent HTML5 parsing rules. hands down***
[18:47] <Wynnefield> Well, when I was designing an Intranet, I guess it made sense. Except now employees use so many mobile devices to access internal sites, it pays to be cautious.
[18:47] <Warppe> :)
[18:48] <Escodsm> thanks kathy
[18:48] <lorio> TiffanyBrown do you see container being used as a class or as a div in a page
[18:48] ***<TiffanyBrown> but to support older browsers, use progressive enhancement or graceful degradation.***
[18:48] <kathy> @Escodsm – sure, you will catch on so fast, it will look familiar.
[18:48] <Warppe> hehe, i love this site: [http://dowebsitesneedtolookexactlythesameineverybrowser.com/](http://dowebsitesneedtolookexactlythesameineverybrowser.com/)
[18:48] <Wynnefield> Doesn’t it depend on repetition? e.g. class vs. div.
[18:49] <Craig> Warppe: I love that one. Shame clients never see it…
[18:49] <Warppe> They do!
[18:49] ***<TiffanyBrown> @lorio i still don’t quite understand your question***
[18:49] <Warppe> At least I show it alot, at the end of my presentation :)
[18:49] <jeff> cross browser forms have always been a bit of a nightmare (to say the least). Frameworks like Bootstrap improve this, but do HTML5 form elements out of the box work well cross browser? Any advice for form building?
[18:49] <Warppe> It brings good laughs, and loads of nods (sometimes, not always :D)
[18:50] ***<TiffanyBrown> sort of.***
[18:50] <TonyChungca> Note: distinction is not “class” vs “div”, it’s “class” vs “id”
[18:50] <GP> TiffanyBrown – yes, I concur. In our company, the site could degrade a lot as long as the purchase path was not blocked, EVER. :)
[18:50] <Wynnefield> I mean if a container is repeated on the page, i.e. columns, photos, I usually define a class. If it is a primary (one time use container), I use a div id.
[18:50] <TonyChungca> a “div” is just a generic block container. not the new standard HTML5 containers.
[18:50] ***<TiffanyBrown> @jeff HTML5 form elements will default to text if the browser doesn’t support the type***
[18:50] **<Craig> jeff: in general, most new HTML5 form elements will fall-back to <input type=”text”>. So, yes, they’re backward compatible – you’ll just be missing a little client-side validation.**
[18:50] <TonyChungca> I’m interested in hearing more about the thinking behind the new <aside> elements, as well as when to use <article> and <section> without having me read the spec. ;-)
[18:50] <HAWK> There is 10 mins left in the session people. If you have a question, now would be the time to get it in :)
[18:50] <Wynnefield> Correct, @TonyChungca .. my bad
[18:51] <lorio> Wynnefield, TiffanyBrown yes, but just wondered if there is a convention as far as using the container…how are developers using it.
[18:51] ***<TiffanyBrown> @jeff so you can use the types, but not all users will see the correct UI and like craig said, you’ll loose the validaiton***
[18:51] <TonyChungca> Rats. I have a meeting. i look forward to the transcript. Cheers!
[18:51] ***<TiffanyBrown> um “lose the validation,” i mean :-)***
[18:51] **<Craig> TonyChungca: glad I don’t have to answer the article vs section vs aside question then!**
[18:51] <Allen> a unique “ID” is a most. yes a “class” defines a style for related elements.
[18:52] ***<TiffanyBrown> but for others who might be interested…***
[18:52] <GP> Craig – Hey that’s my question, too!
[18:52] <Warppe> Oh, about forms, is there an actual working way of styling selects, simply? there are plenty of js solutions out there, but havent really found an ultimate one.
[18:52] **<Craig> Er, um. Read Tiffany’s book!**
[18:52] <jeff> link to Tiffany’s book?
[18:52] ***<TiffanyBrown> <article> is for “self-contained content that may be repurposed,” think of <aside> as a side bar***
[18:53] <HAWK> jeff It hasn’t launched yet. Look out for it in the next month or so at SitePoint & Learnable
[18:53] <HAWK> It’s called Jump Start HTML5
[18:53] <davisem> those just say what they are, right? they don’t do any formatting?
[18:53] **<Craig> <section> is a a generic section.**
[18:53] ***<GP> TiffanyBrown – RE: Aside. When you say repurposed, would a product be considered “repurposeable”?***
[18:53] <Wynnefield> Thank you so much for the education, SitePoint folks and everyone else in attendance. I appreciate your time and consideration.
[18:53] <davisem> Oh hey, I have that book!
[18:53] **<Craig> Unfortunately, you can have <articles> inside <sections> and vice-versa!**
[18:53] ***<TiffanyBrown> @GP the clearest case is for a newspaper article or blog post***
[18:54] ***<TiffanyBrown> but it could apply to blog comments too***
[18:54] <GP> TiffanyBrown – Right, and that’s the use case I see in all of the examples online. However, in terms of real world use, what would you use as a container for a product? :)
[18:54] ***<TiffanyBrown> section***
[18:54] ***<TiffanyBrown> (sorry, i misread your question at first)***
[18:55] <GP> TiffanyBrown Ah ok, np.
[18:55] **<Craig> However, if it’s a single page about one product, <article> may be appropriate!**
[18:55] <Allen> Where do I find Tiffany’s book?
[18:55] **<GP> TiffanyBrown Ah ok. So, you would use ID attribute as a mechanism to store the data ID of the product?**
[18:55] <dms223> yes, fonts! what method are people having the most success? squirrelfont, googlefonts, etc??
[18:55] ***<TiffanyBrown> yes, craig. you are correct.***
[18:56] <GP> Craig – good point!
[18:56] ***<TiffanyBrown> @gp maybe :-)***
[18:56] **<Craig> In fact, you could use many <article>s in one page.**
[18:56] <HAWK> Allen – it will launch on learnable.com in the next month or so
[18:56] <lorio> Wynnefield so on your page I might see both #container and .container (s)
[18:56] <jeff> I’ve never dabbled in the offline storage side of HTML5 – is there anything to be said about security or the ability to encrypt locally stored data?
[18:56] ***<TiffanyBrown> @gp it depends on what you plant to use it for. using ID is an old pattern. but you could use data-* attributes as well***
[18:56] <Escodsm> googlefonts FTW
[18:57] <Wynnefield> you might see #pageContainer, #pageHeader, #pageContent, #pageFooter
[18:57] **<Craig> jeff: because it’s stored locally, that computer could be shared – caution should be used when storing sensitive data.**
[18:57] ***<TiffanyBrown> @jeff in some ways it’s more secure, because it’s tied to the domain***
[18:57] **<GP> TiffanyBrown – all great points, but I was curious since you put it in youre example. :) That’s the danger of writing examples! :)**
[18:57] ***<TiffanyBrown> yes :-)***
[18:57] <Wynnefield> @lorio: apparently I need to re-examine the new HTML5 spec, yes?
[18:57] <jeff> but is the data stored like a cookie? a raw text file that could be opened and read?
[18:57] **<Craig> Nah – use the HTML5.1 one!**
[18:58] <dms223> i find google fonts to render poorly in some broswers…
[18:58] <jeff> any thoughts on a good use of local storage?
[18:58] **<Craig> jeff: yes, there are local storage inspectors.**
[18:58] ***<TiffanyBrown> @jeff i’m not 100% sure about the mechanism, but it can be read from the developer tools, yes.***
[18:58] <GP> TiffanyBrown and Craig – thank you so much for this chat and answering all of our questions. I’ll definitely be checking out your book! Maybe we can have another one of these sometime?
[18:58] <lorio> Wynnefield I guess I do too. I don’t know that it says anything about container.
[18:58] ***<TiffanyBrown> i’m game for that @GP***
[18:58] <HAWK> It’s almost time to wrap up the session. If this was your first time and you want to be kept in the loop for future sessions (I run them every week) then make sure you join the mailing list. There is a link on the main page when you refresh this window.
[18:59] **<Craig> jeff: put it this way – don’t put unencrypted passwords in there (unless it’s session only, perhaps?)**
[18:59] <Wynnefield> Hey, hopefully we both learn some new attacks at our Web pages!!
[18:59] <GP> Great – thanks all! Cheers!
[18:59] <Wynnefield> @lorio: thanks for planting the seed!
[18:59] <jeff> sure, but what kinds of things do you anticipate using local storage for? shopping carts? preferences?
[18:59] <programmer> Thank you
[18:59] <jerry> This isn’t strictly an HTML5 question, but… Is there any benefit to using ID instead of CLASS when there is only one instance of the entity on a page?
[18:59] **<Craig> jeff: it depends on your app, really!**
[19:00] ***<TiffanyBrown> local/session storage isn’t sent over the wire like cookies are…***
[19:00] <DiskScanner> Thanks HAWK, Craig, and TiffanyBrown
[19:00] <dms223> yes thanks guys for today, its nice to chat with fellow enthusiastic nerds
[19:00] <HAWK> And that’s a wrap. I’m going to cut our experts loose whenever they are ready. A huge thanks to them for their time today.
[19:00] <lorio> we still need containers and wrappers, though they are not semantic
[19:00] **<Craig> Thanks guys. I hope you found it useful.**
[19:00] <HAWK> You will find a transcript posted up on sitepoint.com later today
[19:00] <Allen> ID can only be used once. Class defines a group with the same characteristics
[19:01] ***<TiffanyBrown> yes jeff or a token that says you’re authenticated***
[19:01] <ralphm> @jerry An ID is handy for on page links, and also as a hook for JS.
[19:01] <HAWK> Next week we’ll be talking about Writing for the Web
[19:04] ***<TiffanyBrown> i did want to throw out one last thing about HTML5 security, especially offline storage…***
[19:05] **<Craig> joeblow: it’ll be linked from the SitePoint home page**
[19:05] **<Craig> Go for it, Tiffany!**
[19:05] ***<TiffanyBrown> brad hill did an excellent talk at W3Conf earlier this year that may answer some questions / illuminate HTML5 security as a topic***
[19:05] ***<TiffanyBrown>** [http://www.youtube.com/watch?feature=player_embedded&v=WljJ5guzcLs](http://www.youtube.com/watch?feature=player_embedded&v=WljJ5guzcLs)*
[19:06] **<Craig> Can you summarize that video in a sentence, Tiffany?**
[19:07] ***<TiffanyBrown> i’ll try.***
[19:07] **<Craig> (I wasn’t serious!)**
[19:07] ***<TiffanyBrown> oh… *whew****
[19:07] <HAWK> Heh!
[19:08] ***<TiffanyBrown> but brad mentions things like Content Security Policy and CORS that mitigate cross-site scripting attacks.***
[19:08] **<Craig> Does he mention local storage?**
[19:08] ***<TiffanyBrown> and the validation algorithm for forms means we don’t have to write our own JS validation***
[19:08] ***<TiffanyBrown> honestly craig, i can’t remember off hand. it’s been awhile since I watched the video.***
[19:09] **<Craig> I’m playing it now…**
[19:09] ***<TiffanyBrown> but i will say this: cookies are sent with every request, localstorage and sessionstorage are stored locally, once.***
[19:10] **<Craig> That’s a very good reason to use them for state variables which aren’t required server side.**
[19:10] ***<TiffanyBrown> the comparison for them happens on the user’s machine. in that way, they offer better protection against man-in-the-middle attacks. but not against access-to-the-machine attacks.***
[19:11] <jeff> craig – any reason why you would use local storage over session in this case?
[19:11] ***<TiffanyBrown> localstorage is persistent. sessionstorage hangs around until the browser closes.***
[19:12] **<Craig> Since local storage is retained, you could use it for “safe” configuration variables, e.g. the user’s chosen background color, width of a widget, etc.**
[19:12] <jeff> Tiffany – really? I thought it was persistent even after the browser closes?
[19:12] <jeff> like a cookie
[19:13] <jeff> presumably there are some options to set expiry dates?
[19:13] ***<TiffanyBrown> localstorage is.***
[19:13] ***<TiffanyBrown> sessionstorage is for the session.***
[19:13] <jeff> right – sorry, misread what you had said. I thought you were saying the inverse.
[19:14] **<Craig> It probably differs between browsers, but users can clear the local storage at any point.**
[19:15] **<Craig> Also: a mostly arbitrary limit of five megabytes per origin is suggested. But browser may allow more or less.**
[19:15] ***<TiffanyBrown> iirc, most browsers will request more if necessary***
[19:16] **<Craig> Cookies are limited to 4Kb**
[19:16] ***<TiffanyBrown> but again: local and session storage are synchronous meaning they can slow down the browser.***
[19:16] <jeff> wasn’t there a thing going around a while ago – internet scare of some kind – where someone had found a way to dump out gigs of data onto a HD from chrome?
[19:16] **<Craig> And I think it’s only 20 cookies per domain too.**
[19:16] <jeff> via local storage
[19:17] <jeff> maybe I’m making that up though.
[19:17] **<Craig> If you can physically access the PC, the data is available. But – implemented correctly – a browser shouldn’t permit access to local storage from a different domain.**
[19:18] <jeff> [http://www.bbc.co.uk/news/technology-21628622](http://www.bbc.co.uk/news/technology-21628622)
[19:18] <jeff> I believe that was the story I was referring to.
[19:20] **<Craig> jeff: ah, in this case, the developer forced lots of data into the local storage which would lead to stability problems. But no data was obtained.**
[19:20] **<Craig> The article states: So far, no malicious use of the exploits has been observed.**
[19:20] <jeff> something I’m sure Chome will/would have cleaned up
[19:21] <jeff> eventually. :)
[19:21] **<Craig> Hmm. You’d hope so.**
[19:21] **<Craig> Although the jQuery team have been complaining about the number of bugs still in webkit after many years…**
[19:21] **<Craig> But Chrome’s using Blink now.**
[19:22] **<Craig> OK – I’m off now, but thanks to everyone for your challenging questions!**

## 分享这篇文章