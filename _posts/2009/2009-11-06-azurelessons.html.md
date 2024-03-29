---
layout: post
title: "Azure Lessons from the Trenches"
---


<p>For the past two months I&#8217;ve had the fortune of working on an Azure cloud project. Within the Azure technology spectrum, our project team is using a lot of Azure features including web roles, worker roles, storage (table, blob, and queue), SQL Azure, and calling native/unmanaged code.</p>




<p>It&#8217;s been quite a wild ride for the last two months.</p>




<p>Azure offers up many new features that can make projects and development easier, but in many ways Azure makes projects more difficult.  If our team knew two months ago what we know now about Azure, we&#8217;d probably be farther along in the project - and we may have approached the project a little differently.</p>




<p>At the time of this writing Azure is in a &#8220;CTP&#8221; state, so there are bugs and the technology is subject to change in the near future.  However, I think there are some high level takeaways from the technology that you will want to account for on your Azure project:</p>




<ul>
<li>Differences between the local Azure dev fabric and &#8220;live&#8221; Azure</li> 
<li>Minimize web.config and app.config usage</li> 
<li>Visibility into your application</li> 
<li>Availability of Azure environments for your team</li> 
<li>Deployment time</li> </ul>
<p><b>Azure &#8220;dev fabric&#8221; vs. the real thing</b></p>




<p>What you develop on your own computer with the Azure SDK and the Azure Development Fabric won&#8217;t necessarily work on the live Azure site.  Even better, features of Azure that work on the live Azure site won&#8217;t necessarily work on your local dev fabric.  For example, programmatic creation of new tables in Table Storage is not allowed in the local dev fabric.  We&#8217;ve also seen problems where our native c++ code works fine in the dev fabric but won&#8217;t run in the real Azure cloud.</p>




<p>This local-versus-live issue also applies to SQL Azure, as it is a completely different animal than SQL 2005 or SQL 2008.  The database scripts you use in your local dev environment were likely generated from a SQL Management Studio script wizard (or another 3rd party tool) and likely won&#8217;t pass the SQL Azure litmus test.  SQL Azure offers a limited subset of the Microsoft SQL Server features you are used to.  I guarantee SQL Azure will bark at you about things it doesn&#8217;t like in your auto-generated scripts [1]. You are asking for trouble if you run your generated scripts in SQL Azure for the first time the morning before a live demo.</p>




<p>You will need to handle these challenges through frequent test deployments to a live Azure environment.  There is no other way to make sure your app works in the cloud than to actually deploy it to the cloud.  Make time in your development process to deploy often to Azure and test your features.</p>




<p><b>Minimize web.config and app.config usage</b></p>




<p>In an Azure application package, web.config and app.config files are &#8220;compiled&#8221; into a single package file (.cspkg) along with binary and static content.  You deploy that package to Azure and start up your web or worker roles.  If you need to make a configuration change to a setting in a config file, you need to re-package your entire Azure application, suspend your Azure roles, upgrade them, and finally spin up your roles again.</p>




<p>This is obviously a lengthy process for an otherwise simple change - especially if you merely need to make a config change on a test site (which you will do very often during development and testing).</p>




<p>In short, don&#8217;t use web.config or app.config files for your configuration settings.  Instead, leverage the Azure configuration settings files (.cscfg) to store your settings or obtain them from a database table or another storage location. Azure configuration settings can be updated while your Azure roles are running and doesn&#8217;t require them to be suspended.</p>




<p>This may be the simplest lesson you can take away from this article.  It&#8217;s easy to account for and it will save you tons of time during development.</p>




<p><b>Visibility Into Your Application</b></p>




<p>When you run your app in the Azure cloud, it&#8217;s kind of like driving a car blindfolded: you might get to your destination but do you know if you hit any mailboxes?  Since your app is &#8220;in the cloud&#8221; you can&#8217;t obtain any operating system debug output or use any operating system tools to help you see what is going on in your app.  Azure provides some logging features, but at the time of this writing those logging features are disabled.  The rumor is that <a href="http://blogs.msdn.com/windowsazure/archive/2009/10/03/upcoming-changes-to-windows-azure-logging.aspx" target="_blank">new logging features are coming</a>.</p>




<p>Granted, the new logging features may solve all of your problems with visibility into your app, but until those features arrive you will need to provide your own visibility.</p>




<p>I highly recommend using a logging library such as <a target="_blank" href="http://logging.apache.org/log4net/index.html">log4net</a> as it will allow you to write diagnostic and error statements consistently from anywhere in your app (including web roles and worker roles). Through configuration you can direct those logging statements anywhere you choose such as database tables.</p>




<p>On our project we&#8217;ve favored logging to SQL tables, but you may want to consider logging to Azure Table storage instead as I believe it is cheaper by the byte (please do your own research to verify that).  The down side is that Table storage isn&#8217;t as easily queried as SQL tables.  Logging to text files won&#8217;t help much as you can&#8217;t really get at them in the cloud.</p>




<p>If the Azure logging features play out nicely, you may want to consider looking at this Azure log4net appender: <a href="http://neilmosafi.blogspot.com/2009/01/azure-event-log-and-log4net.html." target="_blank"><a href="http://neilmosafi.blogspot.com/2009/01/azure-event-log-and-log4net.html" target="_blank">http://neilmosafi.blogspot.com/2009/01/azure-event-log-and-log4net.html</a></a>.</p>




<p>Through logging, you&#8217;ll be able to get a near-real time snapshot of what is going on in your app.</p>




<p><b>Availability of Azure environments for your team</b></p>




<p>Your team will likely have one Live ID and one Windows Azure host for your production app.  Windows Azure hosts offer two &#8220;slots&#8221; for deployment: a production slot and a staging slot.  Let&#8217;s assume that your production slot is the real .com site and that the staging slot is a &#8220;QA&#8221; site that you use for beta testing or to give a controlled user base a preview of the next release.  For all intents and purposes, both the production and QA slots are <i>always</i> live.</p>




<p>Thus, you will need another slot to quickly and frequently test your app because you won&#8217;t want to overwrite or disrupt the live or QA slots.  Since each host can only hold two &#8220;slots&#8221;, you will need another Azure hosting account on another Live ID to do this.  At the time of this writing, CTP accounts are free and you can sign up for them quickly and easily.  However, when Azure goes live with &#8220;v1&#8221; I don&#8217;t know if that will be the case.</p>




<p>I&#8217;d recommend having Azure hosting accounts for each person who will be performing any type of deployment on the project.  This could be both developers and testers.  You don&#8217;t want to risk only having one &#8220;deployment guy&#8221; since Azure deployments aren&#8217;t trivial.  Make sure you can pass the &#8220;hit by a bus&#8221; test [2] for this.</p>




<p>Deploying an Azure app to the live cloud isn&#8217;t like creating a virtual directory on an internal server - it requires a Live ID account and only offers two slots per Live ID.  In other words, treat Azure accounts and deployment slots as a scarce resource.  You will probably be able to allocate them, but they are owned by an individual Live ID and Azure accounts are handed out and controlled by Microsoft.</p>




<p><b>Deployment Time</b></p>




<p>I laughed out loud when I saw this message for the first time on the Azure deployment screen:</p>




<p><a title="azure deploy time by kindohm, on Flickr" href="http://www.flickr.com/photos/kindohm/4079931111/" target="_blank"><img src="http://farm3.static.flickr.com/2478/4079931111_a88ac599e7_o.png" alt="azure deploy time" width="378" height="226"/></a></p>




<p>&#8220;90% of the time, this operation takes less than 54 seconds&#8221;.  It appears that the duration is dynamically calculated and is a real-time system metric.  The metric and message may be true, but are misleading.  That &#8220;Deploy&#8230;&#8221; button only involves uploading your deployment package and configuration file.  It does <b><i>not</i></b> account for the time spent spinning up your roles</p>




<p>Spin up time has been highly inconsistent in my experience.  By spin up time, I mean the time it takes for your roles to be in a &#8220;Ready&#8221; state:</p>




<p><a title="spinup by kindohm, on Flickr" href="http://www.flickr.com/photos/kindohm/4079993769/" target="_blank"><img src="http://farm3.static.flickr.com/2551/4079993769_56c3e15212_o.png" alt="spinup" width="374" height="157"/></a></p>




<p>Be prepared to get extremely frustrated with the length of time it takes to spin up your roles and how reliably Azure will actually spin them up successfully.  Again, I&#8217;m speaking from a CTP perspective here and the technology isn&#8217;t even &#8220;v1&#8221; yet, but this is something to watch out for.  On a great day, the entire upload and initialization process can take less than 10 minutes.  On a bad day, the services may take more than 30 minutes to spin up - or not spin up at all.</p>




<p>This assumes that your code and configuration are correct the first time you upload and spin up the roles.  If you find a bug with your configuration or code, you&#8217;ll need to suspend the services and start the entire process over again.</p>




<p>For larger, more visible deployments at the end of a development cycle you&#8217;ve probably allocated more time for performing a deployment and you can probably cope with a longer deployment if there are any mistakes.  However, in the middle of a development cycle when you need to test changes in a live environment and there is a lot of code churn, you will be prone to make mistakes or find bugs in the code or configuration - and you will need to deploy again and again.  Be prepared for a lot of frustration.</p>




<p><b>Are you sure about this?<br/></b></p>




<p>In conclusion, make sure you are ready to invest <i>extra</i> time into your development process to make sure your Azure projects are successful.  Part of the extra time will be due to an Azure learning curve (which I have not addressed here).  The remainder (and possibly the majority) of the time will be spent dealing with these issues I&#8217;ve talked about here.  You will need to weigh this extra time and potential stress against the benefits you will gain from Azure as a cloud host.</p>




<p>[1] Microsoft may provide SQL Azure scripting tools in the future which would help deal with this challenge.</p>




<p>[2] The &#8220;hit by a bus&#8221; test passes when you can confidently say your project won&#8217;t suffer (much) if any of your project team members gets hit by a bus</p>




