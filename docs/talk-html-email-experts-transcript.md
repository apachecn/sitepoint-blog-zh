# 与专家讨论 HTML 电子邮件-文字记录

> 原文：<https://www.sitepoint.com/talk-html-email-experts-transcript/>

从各方面来看，今天与专家们的对话取得了巨大的成功。正如文章标题所示，主题是 HTML 电子邮件，我们的专家是来自[电子邮件魔法](http://emailwizardry.nightjar.com.au/ "Email Wizardry")的妮可·梅林。如果你订阅了我们的时事通讯，你将会熟悉妮可的作品。

与会者人数接近我在这些聊天中见过的最高水平，问题的标准也非常高。妮可泰然自若地回答了向她提出的每一个问题，同时为我们提供了大量的资源。我已经把这些汇总成一个全面的清单，你可以在下面找到。

**工具和模板**
[响应式电子邮件模板](https://gist.github.com/tinabeans/6996367 "Responsive template")
[另一个响应式模板](http://www.emailonacid.com/blog/details/C13/emailology_a_free_responsive_email_template_using_media_queries_-_part_i "Responsive template")
[HTML 电子邮件样板](http://htmlemailboilerplate.com/ "HTML Email Boilerplate")
[Premailer](http://premailer.dialect.ca "Email pre-flight tool")—HTML 电子邮件的预检工具

**学习资源**

**示例**
一个很好的[设备不可知的电子邮件示例](http://us5.campaign-archive2.com/?u=89c6e02ebaf75bbc918731474&id=d9ca5249ab&e=6182d0070d "Email example")
一个很好的[在电子邮件中使用动画 gif 的示例](http://campmon.createsend.com/t/ViewEmailArchive/y/CD0CC20AD380E615/C67FD2F38AC4859C/?kmi=3CbnpTAp0wI%2FNINHGbp9rxFZcag%3D "Animated gifs in email")

如果你因为不知道而错过了今天的会议，请务必在这里注册电子邮件提醒。接下来我们将在 11 月 20 日星期三太平洋标准时间下午 1:00 谈论**红宝石**

如果您对这些会议有任何反馈，我很乐意倾听。请给我发一封电子邮件告诉我你的想法。

我受够了。这是今天早上演讲的完整文本(或者不是，取决于你在世界的哪个地方)，供你阅读。

[20:30] <HAWK> Ok, so we may as well kick off. NicoleMerlin – will you give us a brief intro to the subject?
**[20:30] <NicoleMerlin> Ok well I guess I’d say email is enjoying a bit of a resurgence at the moment which is fantastic. Email has been a bit of a dark horse in the past because of spammers giving it a bad name, but when done well it can be really really effective.**
**[20:31] <NicoleMerlin> It does require building using tables because email clients have some very limited CSS rendering capabilities, but it can be really fun to get awesome things working with a limited tool set.**
[20:31] <lo_ny> Ive notice animations in them recently
[20:31] <HAWK> Tables. How retro!
**[20:31] <NicoleMerlin> Retro indeed!**
[20:31] <Rob> Which email client do you find breaks HTML emails the most?
**[20:32] <NicoleMerlin> At the moment it’s actually Gmail for Android which is very tricky because they introduced a new feature that compresses your content table to be 100% of your viewport, but does not read media queries.**
**[20:32] <NicoleMerlin> It’s quite annoying.**
[20:32] <ralphm> The animations are usually done with animated gifs.
**[20:32] <NicoleMerlin> Outlook 2013 also has a few annoying quirks.**
[20:33] <Moraima> is there some tool to test for most used email clients?
**[20:33] <NicoleMerlin> The old versions of Lotus Notes are pretty bad but I don’t often have to support Lotus Notes 6 or 6.5 anymore (thank god)**
[20:33] <MalCurtis> Are there many automated tools for checking compatibility with email clients?
**[20:33] <NicoleMerlin> Yes, there are some AMAZING tools.**
[20:33] <Rob> Nicole are you using Responsive Email successfully?
**[20:33] <NicoleMerlin> You can use www.litmus.com or www.emailonacid.com**
**[20:33] <NicoleMerlin> I use both of those**
[20:34] <MalCurtis> What are the benefits of each?
**[20:34] <NicoleMerlin> Hi Rob, all the work I do is responsive.**
[20:34] <MalCurtis> i.e. why would you use both?
**[20:34] <NicoleMerlin> Responsive works really well and it also gives great results for subscriber activity like click-throughs.**
**[20:34] <NicoleMerlin> I use both of them because I do this professionally, so if one of them goes down, I can use the other one.**
**[20:34] <NicoleMerlin> They both have pros and cons.**
[20:35] <Rob> I haven’t tried Responsive yet because many negative reviews about it, when it comes to mobile. Are they wrong?
[20:35] <Kibbage> How often do they go down?
[20:35] <chateaudesducs> May I ask a simple question? Is it possible to create html email that reaches its destination to the same degree as plain-text email? Or is that a contradiction in terms?
**[20:35] <NicoleMerlin> Email on Acid has a wide range of browser options for each client, e.g. you can test Gmail in Safari, Chrome, Firefox etc. Whereas Litmus just gives you one option for each browser.**
[20:35] <MalCurtis> Do you think that’s a little bit of overkill?
**[20:35] <NicoleMerlin> Oh they very rarely go down, but I just can’t risk it**
[20:36] <rokio> How do you do it responsive? Do you use media queries only…
**[20:36] <NicoleMerlin> If I am on a tight deadline, I don’t have 30 minutes to wait for a test to work**
**[20:36] <NicoleMerlin> @Rob, what are the negative reviews?**
[20:36] <Rob> negative in the sense, they break when read
**[20:36] <NicoleMerlin> chateaudesducs, do you mean in terms of spam filters?**
**[20:37] <NicoleMerlin> @Rob if that is the case, they haven’t been built properly :)**
[20:37] <lo_ny> Mail Chimp seems to have good results
[20:37] <Rob> Nicole, thks, I can’t say either since I haven’t given it a real go yet.
[20:37] <chateaudesducs> Yes.
**[20:38] <NicoleMerlin> @rokio you use max-width media queries. This is because you need your ‘base version’ to render in the desktop and webmail clients (like Outlook), and none of these programs render media queries. Smartphones and tablets, however, do render media queries, so they pick up on the max-width and then apply your styles.**
[20:39] <Ragwing> Mail chimp is great and it’s free if you don’t send many
**[20:39]** **<NicoleMerlin> You can also create a responsive email without media queries if you set a max-width in the CSS of your table. I actually haven’t tried that method but I don’t see why it wouldn’t work.**
[20:39] <HAWK> For anyone that is interested in Nicole’s work, you can find her here [http://emailwizardry.nightjar.com.au/](http://emailwizardry.nightjar.com.au/)
[20:39] <MalCurtis> I recently came across this bare bones responsive email template – does this look like an up to date, industry standards template?
[20:39] <MalCurtis> [https://gist.github.com/tinabeans/6996367](https://gist.github.com/tinabeans/6996367)
[20:39] <Robin> It’s been a long while since I had to code an email blast, are there tools that you use to create them or do you hand-code the markup yourself? Does email use HTML5? I guess I’m a newbie although I’m a web developer
[20:39] <Narcisa> also Getresponse seems to have this option
[20:40] <Rob> Nicole, do you you use any software to build Responsive email, or is it all hand coded
[20:41] <Robin> HAWK, where on sitepoint? I’m a learnable member
**[20:41] <NicoleMerlin> @Robin HTML email isn’t that up to date. There are a few HTML5 tags that you can use, and some people have used the HTML5 doctype for their emails, but I have not tried that with good results.**
**[20:41] <NicoleMerlin> This free responsive template from Email on Acid uses the HTML5 doctype [http://www.emailonacid.com/blog/details/C13/emailology_a_free_responsive_email_template_using_media_queries_-_part_i](http://www.emailonacid.com/blog/details/C13/emailology_a_free_responsive_email_template_using_media_queries_-_part_i)**
[20:41] <HAWK> On the homepage Robin. It’s free to access. If you’re on the mailing list for these sessions I’ll mail out a link. Or you can find it on the header of this page when the session isn’t running.
**[20:42]** **<NicoleMerlin> And when I create my email I code them by hand, but I use Sublime Text and make good use of Snippets.**
[20:42] <Robin> thank you, Nicole, I use Sublime as well
[20:42] <listener> I’m like Robin. I’ll like to know how to start making these HTML Email
[20:42] <Robin> Thanks, Hawk
**[20:42] <NicoleMerlin> MalCurtis I’ll have to take a look at that code and let you know**
**[20:42] <NicoleMerlin> :)**
**[20:43] <NicoleMerlin> The thing with email is that you can basically try ANYTHING. Nothing is too crazy — if you think “gee, surely this HTML5 thing will work” then you can just go and build a file and try it. Then run it through a test (with Litmus or Email on Acid and using some live devices) and see if it works. Sometimes you’ll be surprised — I discovered that web fonts actually work in some versions of Outlook Express and Lotus Notes 8\. It’s always worth a try.**
[20:43] <lo_ny> What kind of service do you use to send them out?
[20:44] <lo_ny> to send out to a large list, that is.
**[20:44] <NicoleMerlin> My preference is Campaign Monitor as I really love their interface. I also work for clients using everything from MailChimp to ExactTarget, which is a more enterprise-level platform.**
[20:45] <Robin> NicoleMerlin, what is the basic width you use for your table width? also, is there a limit on file size for faster downloads? also, same question for mobile
[20:45] <Rob> hmm, but if some things only work in a few clients, isn’t that dangerous?
[20:45] <mindlogik> I am developing HTML emails in BC but find that Outlook 2013 does not display view in browser correctly – any suggestions ?
[20:46] <Mike> has someone mentioned Thunderbird? I read that it was good.
**[20:46] <NicoleMerlin> @Rob I mean you can try things but if they don’t work, you don’t use them.**
[20:46] <john> I have a database-driven web app that uses ASP.NET SMTPClient to send out emails but formatting the HTML is a string-building nightmare. Any tools for improving the email content generation?
[20:46] <Rob> I love Thunderbird, everything in Thunderbird works. No breaking emails
[20:47] <Rob> NicoleMerlin gotcha ;-)
**[20:47] <NicoleMerlin> @Robin I tend to stick to between 480-600px for the desktop width. Filesize is best to always keep it under 200kb, but obviously leaner is better! Especially on mobile. 100k or less on mobile is the holy grail.**
[20:47] <HAWK> Whoa. You guys have all the good questions today! Great session so far.
**[20:47] <NicoleMerlin> Yeah Thunderbird is a great client.**
[20:47] <lo_ny> @Rob how many can you send out in one shot with T-bird?
[20:48] <NicoleMerlin> mindlogik what is BC?
[20:48] <NicoleMerlin> There is a fix for that… [20:49] <HAWK> Saved editor: [https://gist.github.com/745617](https://gist.github.com/745617) [20:49] <NicoleMerlin> That is the fix from the HTML Email Boilerplate  

[20:49] <NicoleMerlin> [http://htmlemailboilerplate.com](http://htmlemailboilerplate.com)
[20:49] <Rob> @lo_ny I’ve never sent out anything in Thunderbird mail list wise
[20:49] <Robin> NicoleMerlin, can you discuss your workflow for creating and deploying your email blast? Many people here are speaking of browsers, several mail clients and testing sites. I’m a little lost as to the “sequential” steps passed the initial html coding the table layout.
[20:49] <Robin> :0
**[20:49] <NicoleMerlin> The Email Boilerplate is a really great resource. I don’t recommend taking the whole document to start your emails, as I found it was too bloated and caused some problems, but it is great as a reference and to pick and choose fixes from.**
[20:50] <mindlogik> Business Catalyst
**[20:50] <NicoleMerlin> Oh ok.**
[20:50] <rokio> I second Robin
[20:50] <listener> Thanks, Hawk
**[20:50]** **<NicoleMerlin> john I’m afraid I don’t have any experience with .net or with string-building, I am always lucky enough to just be building straight-up HTML.**
[20:52] <Mike> has someone mentioned Thunderbird? I read that it was good.
[20:52] <HAWK> They have Mike – everyone is a fan, including Nicole
[20:52] <ArchaicLord> Hi there, I though this was about HTML e-mail templates?
[20:52] <HAWK> ArchaicLord – it is :)
**[20:53] <NicoleMerlin> Sure @Robin, So what normally happens is first the wire framing and design stage. Then I start with my own blank ‘template’ in Sublime Text and start building. I build a structure first, then test in Email on Acid or Litmus to make sure the structure works on desktop and mobile. Then I go in and apply all the styling and then I test that, and I also have a few devices that I test on (an Android, an iPhone, an iPad etc). Once all bugs are eradicated, it’s ready to go. **
[20:53] <mindlogik> thanks for the 2013 ‘fix’ . It works !
**[20:53]** **<NicoleMerlin> So then it needs to be loaded into a mail sending platform. Campaign Monitor or MailChimp are examples of these.**
[20:53] <HAWK> ArchaicLord – if you have specific questions, ask away and I’ll queue them for Nicole to answer
**[20:53] <NicoleMerlin> You sign up with one of these providers and then you create a ‘Campaign’ which will allow you to upload the HTML that you have created.**
**[20:53] <NicoleMerlin> You also need to have a list of subscribers loaded into your chosen platform.**
**[20:54] <NicoleMerlin> Once you have both of these ready to go, you are able to send your email to them through the system.**
**[20:54] <NicoleMerlin> mindlogik Yay!**
[20:54] <ArchaicLord> To my knowledege is there any better way to create an e-mail campaign without using tables in html for placement of elements
[20:55] <john> NicoleMerlin – forget the .Net part of my question for now. I just have some code that runs on a web server and takes data from a database and pumps out emails. Yet the building of the content of the email cries out for something other than a massive exercise of concatinating HTML tags with variables from the database. There must be a better way.
[20:55] <Robin> @john could you create a basic template in your string and then write and run a small program where your text is injected into the proper table tags via id or classnames using jQuery and then copy paste that html into your form?
[20:55] <rokio> How do you build the structure? Do you build the structure using a table? Do you use inline css or do you put in the head section?
**[20:56] <NicoleMerlin> @ArchaicLord No. You can sometimes use divs inside tables to achieve various things, but there is no point trying to work without tables. Tables are the only thing that works universally, and it’s futile to try anything else :)**
[20:56] <Narcisa> @Nicole: do you use external style sheets or only inline CSS?
[20:57] <Mo7sin> .= and which one is better>
**[20:57] <NicoleMerlin> @rokio Yes you definitely build the structure using a table. Normally you have a big table that is 100% width which acts as your ‘body’ (some clients don’t allow body tag styling) and then you have another table within that.**
**[20:57] <NicoleMerlin> When you send your email, your CSS needs to be inline**
[20:57] <Rob> @john I’ve just finished building a complete email delivery system with templates. I just use variables in a template for search & replacing from the database.
[20:57] <Robin>NicoleMerlin, when you say you “test in Email” on Acid or Litmus, do you mean you send yourself an email to your accounts (ie gmail, yahoo, etc.) then open them on various devices?
**[20:57] <NicoleMerlin> Because some clients (like Gmail) ignore CSS in the head and/or strip it out.**
**[20:58] <NicoleMerlin> Your media queries stay in the head, they will be ignored by every client that can’t read them**
**[20:58] <NicoleMerlin> But you can CODE with the CSS in the head, however before you send, you MUST use a tool to bring it all inline.**
**[20:58] <NicoleMerlin> This is one such tool [http://premailer.dialect.ca](http://premailer.dialect.ca)**
[20:58] <Robin> NicoleMerlin, sorry I hit return too quick. Here is my entire question: When you say “test on Email” on Acid or Litmus, do you mean you send yourself an email to your accounts (ie gmail, yahoo, etc.) then open them on various devices?
**[20:58] <NicoleMerlin> Campaign Monitor brings CSS inline by default, as does MailChimp (you can also turn it off)**
**[21:00] <NicoleMerlin> @john I actually have no idea, I generally supply my work to other genius humans who do the integration, and for all I know they had to do a big bunch of concatenation. My assumption is that the email HTML exists and then variables and values get ‘injected’ into it, but I don’t know if it can work like that.**
[21:01] <john> Rob – thanks for the idea. Where could I find out more about the template(s)? My architecture is a windows .exe running on a server that periodically generates an email to a recipient. The construction of the email content is a pain.
[21:01] <Rob> Here’s a hint I found when using many images in tables so it won’t break in Gmail. add border=”0″ style=”display:block;” to all <img> tags
**[21:01]** **<NicoleMerlin> @Robin, good question, I should have been clearer about that part of the process.**
**[21:03] <NicoleMerlin> @Robin When you test in Email on Acid, you can upload a ZIP of your file, or you can pop in a URL of your remotely hosted file, or you can send an email to youraccountname@emailonacid.com and it will receive the email. That way you load your file into their system, and then THEY run a test on lots and lots of different email clients and they display a screenshot of each.**
[21:03] <Steve> Hello Nicole, Do you space table cells by the width attribute alone, or do you use a spacer gif?
**[21:03] <NicoleMerlin> When you test in Litmus, you can either paste in your HTML or send your email to a specific address they give you, it’s randomly generated. Then they do the same thing.. run the tests, and give you the screenshots.**
**[21:04] <NicoleMerlin> They recommend sending the email to them from the actual platform that you’ll be using, as that gives the most accurate results.**
[21:04] <Robin> NicoleMerlin, thanks. I’ll have to check out those tools
**[21:05] <NicoleMerlin> I often load my emails into Campaign Monitor or MailChimp and then also send myself test emails, and then I have that email address set up on my test devices so I can receive it on all of them at once.**
[21:05] <Rob> @john I had to think the process through myself and found bits & pieces with searches. I basically create a db table “templates” holding the html code in 1 field. Identify variables like %address% to be replaced.with db data.
[21:05] <Robin> NicoleMerlin, so the markup is just a table with nested tables containing content (divs, a, ul, ol, etc.)? there is no <html><title></title><body></body></html> dom structure?
**[21:06] <NicoleMerlin> @lizardlounger That is a great question that I was actually thinking about the other day! I was going to run some tests on that. I’m thinking that maybe if you add a lot of &nbsp; tags? Then you’d just end up with a big gap. That’s my best idea so far but I literally thought about that 2 days ago and need to test it!**
[21:06] <HAWK> FYI everybody, lizardlounger’s question was: Is there a way to hard code a preheader so it only displays the sentence you designate without pulling up additional text?
[21:07] <CaptainSin> Nicole, When I send out table formatted emails from a regular email inbox, the images appear as attachments.
[21:07] <MalCurtis> NicoleMerlin I wouldn’t be surprised if many clients trimmed multi whitespace into a single one
[21:07] <MalCurtis> Since technically there’s lots of newlines as well when you strip out html tags
[21:07] <CaptainSin> Do you have any suggestions on how to handle embedded images. better? At the moment they are up as attachments to the email it’s self.
**[21:08] <NicoleMerlin> Hi @Steve, I prefer to use padding as much as possible, as I find it’s the best and easiest way to achieve space, and there are occasionally some rumblings that having too many empty cells with &nbsp; tags inside them is bad from a SPAM perspective (though I can neither confirm nor deny that). Padding is FULLY supported, contrary to popular belief, but you have to make sure you specify every single value. For example, <td style=”padding: 0 0 5px 0;”>**
**[21:09] <NicoleMerlin> If I cannot use padding for some reason, then I would use table cells with the width/height attribute alone.**
[21:09] <Robin> ah, thanks, Steve for asking that and Nicole for the padding tip!
[21:09] <john> Rob – Thanks. I think these tools and techniques mentioned here mostly deal with marketing emails. Mine are very much tied to a recipients database data (unlike Litmus and these other tools).
[21:09] <Robin> NicoleMerlin, are you using cellpadding or padding property?
[21:10] <Robin> or either?
[21:10] <Robin> NicoleMerlin *or both
[21:11] <Nelmedia> Since we’re using tables for display, that means that you don’t put the size in the html but just the css for responsive layout, or the css will override the html attributes?
[21:11] <Steve> Looks like I need to go back to Email coding school. Any good resources or links to help with my studies? You’ve already provided some food for thought!
**[21:11] <NicoleMerlin> Hi CaptainSin, I’ve never had great results sending HTML email FROM an email client. To avoid the images being sent as attachments, in my experience, you have to use a service like Campaign Monitor or MailChimp.**
[21:11] <HAWK> Welcome if you’ve just joined. Feel free to jump in at any time and I’ll queue questions for Nicole. I’ll post a transcript up on SitePoint later today so you can see what you’ve missed
[21:12] <MalCurtis> @john If you’re looking for a service that handles templating of emails, and you can just send data to it, I’m actually just about to launch one
**[21:12] <NicoleMerlin> @Robin No, pardon me, there is all the other stuff. If you have a look at the HTML Email Boilerplate [http://htmlemailboilerplate.com](http://htmlemailboilerplate.com) You will see the structure. You have a doctype, HTML tag, HEAD tag, BODY tag etc.**
[21:12] <Rob> @john yes, but, in the end, your email still needs to be built correctly for the email clients. Good luck.
**[21:12] <NicoleMerlin> @MalCurtis That could indeed happen.. I will need to test it.**
[21:13] <Robin> NicoleMerlin, ok thanks for clarifying, I’m learning some good stuff here today!
[21:13] <lizardlounger> Thanks Nicole. I’ll have to see how &nbsp; works as well?! My concern is if their will be SPAM issues in doing so?
[21:13] <listener> Is anyone aware of any videos online or books that show the basics for how to create HTML Emails? Thanks.
[21:14] <Robin> I second Steve
[21:14] <MalCurtis> “Create Stunning HTML Emails that just works!” By SitePoint: [https://www.sitepoint.com/store/create-stunning-html-email-that-just-works/](https://www.sitepoint.com/store/create-stunning-html-email-that-just-works/)
**[21:14] <NicoleMerlin> @Robin I always use CSS padding as opposed to the HTML cellpadding attribute because it gives you more control.**
[21:15] <Robin> NicoleMerlin, I see. Ok, I’ll remember that
[21:15] <Rob> @listener Lynda.com has a tutorial
[21:15] <Nelmedia> Sitepoint will have to update it’s book to take mobiles into account…
**[21:15] <NicoleMerlin> @Nelmedia You can put the width and size stuff into the HTML, the CSS in the media queries does indeed override HTML width and height attributes (thank god). I like to set as few widths as possible, so I generally set my outer table to be a width (i.e. 600px) and then everything within that is done using percentages. That way I only have to override that single 600px value. Often it’s not THAT neat & tidy, but that is the aim.**
[21:16] <Robin> Good info
**[21:16] <NicoleMerlin> @lizardlounger Yeah that is a possibility. This is definitely something I want to figure out though.. we should ‘connect’ on Twitter (hate that word :p)**
[21:17] <ralphm> @Steve CampaignMonitor has some great free templates that teach you a lot about email layout, as does MailChimp. CM also has some great learning resources, like this one for responsive email layouts: [http://www.campaignmonitor.com/guides/mobile/](http://www.campaignmonitor.com/guides/mobile/)
[21:18] <HAWK> Welcome if you’ve just joined. We have about 10 minutes left in the session so if you have an important question, make sure you get it in!
[21:18] <HAWK> Doesn’t have to be in context, I can queue them
[21:18] <CaptainSin> NicoleMerlin Thanks
[21:18] <HAWK> Has anyone asked a question that hasn’t been answered yet>
**[21:18] <NicoleMerlin> @Steve Glad to hear! I wrote a basic tutorial for Tuts+ [http://webdesign.tutsplus.com/tutorials/htmlcss-tutorials/build-an-html-email-template-from-scratch/](http://webdesign.tutsplus.com/tutorials/htmlcss-tutorials/build-an-html-email-template-from-scratch/) But it is NOT responsive, sorry. I am literally in the middle of my responsive tutorial this week and hope to post that. In the meantime, you can use Campaign Monitor’s guides [http://www.campai](http://www.campaignmonitor.com/guides/)[gnmonitor.com/guides/](http://www.campaignmonitor.com/guides/)**
[21:18] <listener> Thanks, Rob
[21:18] <NicoleMerlin> Yeah SitePoint have a book I believe? :)
[21:19] <Robin> ralphm answered the one I seconded Steve on. But would also like Nicole’s recommendation of tools to learn
[21:19] <Robin> oops.
[21:19] <Robin> she just answered
**[21:20] <NicoleMerlin> There is a video but I am just sussing out if it’s member-only material at Litmus**
**[21:20] <NicoleMerlin> Maybe I should make a video although I hate the sound of my own voice :p**
[21:20] <Vetski> Does anyone recommend VerticalResponse as a good email delivery system, or do you all think MailChimp or htmlemailboilerplate better?
[21:20] <Robin> ha, ha, don’t we all !
**[21:21] <NicoleMerlin> Ok here we go, this page has a lot of good links [https://litmus.com/blog/our-favorite-responsive-and-mobile-email-resources](https://litmus.com/blog/our-favorite-responsive-and-mobile-email-resources)**
[21:21] <ralphm> For sending out your own test emails, I find if you place the images online and link to them there, sending out an email via Safari works fine (no images as attachments problem). Safari has a Share > Email the page option.
[21:21] <lizardlounger> listener aside from Sitepoint “Create Stunning HTML Emails that just works!”, I recommend [http://modernhtmlemail.com/](http://modernhtmlemail.com/) by Jason Rodriguez. Great resource for modern email/responsive!
**[21:22] <NicoleMerlin> Hi @Vetski, I’ve never used VerticalResponse, it does look like an email SENDING platform, just like MailChimp. The HTML email boilerplate is NOT a send platform — it’s just a base ‘template’ of the code. You can’t send your emails using it.**
[21:22] <Nelmedia> For RWD email, I guess we should just target smart phones, since tablet should already display it well (since we target around 600px for the table)… Or there is other things to consider for a tablet that is different from a desktop mail client?
[21:22] <Vetski> ah, thanks for the clarification!
[21:23] <Narcisa> @Nicole: regarding the mobile devices. What should I do in order to display my emails correctly? It’s enough to follow the <600px width rule?
[21:23] <Robin> regarding the question on images showing as attachments. Is this a default behavior in the email client? I know there are times I get an email and the client will ask me if I want to download images. Also, would linking them with absolute URL on your server display the images as embedded instead of as attachment?
**[21:23] <NicoleMerlin> @Nelmedia In my experience tablets are totally fine and display the desktop version, but obviously there has recently been a proliferation in tablet sizes (even just with Samsung, Kindle Fire and Kobo Vox) so it can depend.**
[21:24] <Robin> NicoleMerlin, regarding the question on images showing as attachments. Is this a default behavior in the email client? I know there are times I get an email and the client will ask me if I want to download images. Also, would linking them with absolute URL on your server display the images as embedded instead of as attachment?
[21:24] <Rob> If your looking for a low cost email delivery service with a great AP & LibrariesI try postmarkapp.com I used a few times No templates though.
[21:24] <lizardlounger> Hahhaa NicoleMerlin that would be great! @dmageli for twitter handle.
[21:25] <ralphm> @Robin For testing, yes, placing images online somewhere should deal with the attachment issue, but I’d only do that for testing purposes. An alternative to having to download ‘do you want to downoad’ issue is to embed images with data URIs. It’s pretty nifty, although won’t work perfectly in all email clients.
[21:26] <Rob> Nicole, how can you lock a font size when read by mobile apps. I have an email template where the font is 6 & 7 point, but the mobiles bump it up 10pt automatically.
**[21:26] <NicoleMerlin> @Narcisa There are a few things you can do. If you don’t want to make a responsive HTML email, then it’s best to keep your width at about 450px. That way, when it resizes to mobile, it won’t shrink very much. A good example of this ‘device-agnostic’ approach is Aeon, one of my favourite emails. [http://us5.campaign-archive2.com/?u=89c6e02ebaf75bbc91](http://us5.campaign-archive2.com/?u=89c6e02ebaf75bbc918731474&id=d9ca5249ab&e=6182d0070d)[8731474&id=d9ca5249ab&e=6182d0070d](http://us5.campaign-archive2.com/?u=89c6e02ebaf75bbc918731474&id=d9ca5249ab&e=6182d0070d)**
[21:26] <Robin> ralphm, ah, ok. I’ve used the data URIs in sprites, will try that as well
**[21:27] <NicoleMerlin> @Narcisa You’ll notice on that email that the fonts are very large on desktop, so that they will be a good size on mobile. It works well.**
[21:27] <ralphm> Here’s CM’s atest info on embedded images: [http://www.campaignmonitor.com/blog/post/3927/embedded-images-in-html-email](http://www.campaignmonitor.com/blog/post/3927/embedded-images-in-html-email)
[21:27] <Robin> Sweet! thanks, ralphm
[21:28] <Narcisa> got it, thanks :)
[21:29] <Robin> someone mentioned that they saw animations in email
**[21:29] <NicoleMerlin> @Robin There is a bit of a difference between images showing as attachments (where they actually show up with the little paperclip and are files that come with the email) and them requiring download. There is no getting around having to ‘download images’ as is this behaviour is built in to email clients. When you send out HTML email using your sending platform, it will automatically put absolute remote URLs in for your images, and they are ’embedded’ into the HTML file for display, but users still have to say ‘download images’ if they are using a client that blocks images by default.**
[21:29] <lo_ny> Is there a maximum recommended size for your email?
[21:29] <ralphm> Animations are usually done with animated gifs. Pretty cool.
**[21:30] <NicoleMerlin> @ralphm I have never heard about that! Sounds sneaky :)**
[21:30] <Robin> ah, ok. Thanks, NicoleMerlin!
[21:30] <Rob> HAWK NicoleMerlin how can you lock a font size when read by mobile apps. I have an email template where the font is 6 & 7 point, but the mobiles bump it up 10pt automatically.
**[21:30] <NicoleMerlin> Hi @Rob, That is a tough one because there is no point having 6px type on a mobile. Are you trying to hide your unsubscribe links? ;)**
**[21:31]** **<NicoleMerlin> You can disable font resizing, though I think it is a LOT trickier on Android now.**
[21:32] <Rob> NicoleMerlin My client sends out Promotional coupons & postcards, the small print is the legaleze. It’s mostly for Desktop, but I obviously need to take into account mobile.
[21:32] <HAWK> Ok, well that’s a wrap, unless Nicole has anything else she’d like to say.
[21:33] <ralphm> Regarding animaed gifs in email, look at this gorgeous email CM sent out recently. Demonstrates a really nice example of a simple animated gif to great effect: [http://campmon.createsend.com/t/ViewEmailArchive/y/CD0CC20AD380E615/C67FD2F38AC4859C/?kmi=3CbnpTAp0wI%2FNINHGbp9rxFZcag%3D](http://campmon.createsend.com/t/ViewEmailArchive/y/CD0CC20AD380E615/C67FD2F38AC4859C/?kmi=3CbnpTAp0wI%2FNINHGbp9rxFZcag%3D)
**[21:33] <NicoleMerlin> @Robin any animations are usually animated GIFs HOWEVER CSS3 animation does work on new devices like smartphones. It degrades gracefully too.. you can do some pretty awesome stuff with it!**
[21:33] <Rob> NicoleMerlin thanks for the talk and info
[21:33] <Robin> lo_ny no worries there is also a transcript
[21:34] <Tasha> Hello, where can a non-member find the transcript?
[21:34] <Robin> I use CSS3 annimations alot
**[21:34] <NicoleMerlin> @Rob I just posted a snippet from the HTML Email Boilerplate, but from memory it’s a little trickier with Android at the moment and I remember I had to do a bit of Googling recently. Sorry that I don’t have a direct resource to share right now.**
[21:35] <HAWK> Thanks SO much NicoleMerlin for your time this morning. It is much appreciated.
[21:35] <Rob> NicoleMerlin thanks. I did try some suggested code to disable resizing, but it did not work for iOS or Android.

## 分享这篇文章