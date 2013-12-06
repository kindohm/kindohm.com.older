---
layout: post
title: "Guidelines for Good Times with SharePoint Publishing Internet Sites"
---

<p>There are good ways and bad ways to use Microsoft Office SharePoint Server (MOSS) for hosting an Internet web site.  When at its best, MOSS will take care of all the details of navigation, storage, content management, and page layouts for you.  It might even perform really, really well.  It's these times when MOSS allows you (a developer) to focus on the killer features and writing code - rather than the mundane site management tasks.  </p>
  
<p>At its worst, MOSS can be a performance hog, clog up SQL server connections, and be very difficult to manage.  Every custom artifact introduced into a MOSS Internet site needs to be managed, and when things aren't done efficiently you can end up managing the SharePoint equivalent of Classic ASP spaghetti code.  And who wants spaghetti when you can have a burger?  </p>
  
<p>Here are some guidelines for a successful Internet site with MOSS.</p>
  
<h3>#1 Begin Branding With a Minimal Master Page</h3>  
<p>Don't subscribe to the "start from an existing template" theory when creating a custom Master page for branding your MOSS site. Start from a bare minimum. This MSDN article instructs how to create a minimal master page: <a href="http://msdn.microsoft.com/en-us/library/aa660698.aspx" target="_blank">http://msdn.microsoft.com/en-us/library/aa660698.aspx</a>.</p>
  
<p>The reason you want to start from scratch is that the out-of-the-box MOSS master pages can be a bit bloated with extra SharePointy stuff. They're a great starting point for showing your CIO a demo of the MOSS product. They're great for prototyping. That's about it. For your Internet site, only put what you need in it. You can always add some of the SharePointy stuff back in.</p>
  
<h3>#2 Store Master Pages in Source Control</h3>  
<p>Customizing MOSS is a software project. In software projects, you put your project artifacts and code under source control. This includes master pages. </p>
  
<p>Unfortunately, you'll typically see SharePoint Designer as the promoted tool of choice for creating and authoring master pages. I'd argue that Visual Studio is a better choice for master pages because it has better integration with Microsoft source control tools such as TFS or VSS.  In addition, getting your master pages out of SharePoint Designer will allow you to integrate your master pages into the rest of your <em><a href="http://msdn.microsoft.com/en-us/library/aa543214.aspx" target="_blank">SharePoint deployment solution</a></em>. There isn't really anything critical in SharePoint Designer that you need for creating master pages.</p>
  
<p>This goes for related branding assets too, such as images or CSS.</p>
  
<p>If you go down the road of authoring master pages in SharePoint Designer and choose not to keep them in source control, they're just going to sit in a SharePoint content database somewhere.  You'll need to put a really ugly plan in place for promoting those master pages to your live environment (I'm assuming you aren't going to author a master page directly in your live, production Internet site). </p>
  
<h3>#3 Deploy Master Pages and Apply Branding with Solutions and Features</h3>  
<p>This ties in with #2 above. If you keep your master pages and other assets under source control and work with them within your Visual Studio toolbox, you can more easily integrate them into SharePoint Solutions and Features. </p>
  
<p>Becky Bertram has a fantastic article about deploying master pages and also applying them to your site using a Feature: <a href="http://www.beckybertram.com/index.php/2007/10/14/apply_custom_master_page_globally" target="_blank">http://www.beckybertram.com/index.php/2007/10/14/apply_custom_master_page_globally</a>. I really like this approach because it is a controlled, repeatable way to turn on or turn off branding on a MOSS site.</p>
  
<p>A caveat of this is that you can run into some challenges when trying to "upgrade" a master page that is already in use. A workaround if you're lazy is to create a 2nd master page with a new name and apply it as a 2nd feature, but I wouldn't recommend this as over time if you make a lot of changes you'll start to make a mess.  Mess = bad.</p>
  
<h3>#4 Layout Pages Are For Structure, Not Functionality</h3>  
<p>Layout pages are great for defining common "layouts" on your site.  Hence the name "Layout Page".  Examples: body only, body with left column, body with right column, three-column body, etc.  Create a minimum number of layout pages that you need for the layouts on your site.  Chances are you'll only have a small handful.  Maybe even just one or two.</p>
  
<p>Don't use Layout Pages to provide specific functionality for a single page on your site.  For example, don't create a layout page specifically for your Contact Us page.  Instead, use a generic layout page, create a Contact Us web part, and add the web part to the page.  If you create a layout page for every planned page or feature on your site, you'll have dozens (or hundreds) of layout pages - your content database size goes up, migrations (or content deployments) take longer, and your site now has more stuff that you have to manage.  </p>
  
<h3>#5 Store Layout Pages in Source Control</h3>  
<p>Just like master pages, keep your layout pages in source control.  You could author them in SharePoint Designer and just keep them on the site, but then how do you get them to your target environment?  No, copying and pasting the markup HTML text from one environment to the other (using SharePoint Designer in the destination environment) is not allowed, my friends.  Keep the layouts in source control and promote them to target environments using SharePoint Solutions and Features.  It's more repeatable.</p>
  
<h3>#6 Use Features to Build Out Core Pages</h3>  
<p>Let's say that your site requires ten core pages that will always be there (e.g. Home, Contact Info, Departments, Events, Careers, etc).  Don't create these pages by hand using the out-of-the-box methods.  If you need the site to exist in Dev and Staging environments you don't want to have to re-create all that stuff by hand.  Plus, what if your Dev and Staging environments need to be re-built?  Sure you can backup and restore your site but that's not cool at all.  And you want to be cool, don't you?</p>
  
<p>Instead, create these pages with the click of a button - a Feature <em>Activate</em> button.  Ok, activating the Feature at a command line would be cool too.  But the point is you should be able to turn <em>on</em> and turn <em>off</em> the existence of your core pages from the site.  </p>
  
<p>This might require some custom code, but all the better.  Ideally, I'd recommend deploying a configuration file as a part of your feature, and read from the config file when creating your pages.  The config file can store the titles, content types, and layout pages you need to create each page.  To do this programmatically, you're basically going to be dealing with the <code>PublishingWeb</code> and <code>PublishingPage</code> classes in the MOSS API.</p>
  
<p>For more dynamic content such as news articles, etc, definitely use the in-product features to create those items.</p>
  
<h3>#7 Lean on SharePoint for Functionality</h3>  
<p>Leverage every drop of SharePoint you can to make your life easier.  Remember, you want to spend time writing code for a killer feature - not writing navigation or a recent news feed.</p>
  
<p>I can't emphasize enough that you need to use the built-in MOSS navigation providers and navigation controls.  They're basically just fancy ASP.NET menus and can be styled to your liking.  The most important point is that the built-in MOSS navigation providers get their navigation data very, very efficiently.  Getting this data yourself (e.g. traversing SPWeb trees) is a <em>very</em> expensive operation, programmatically speaking.  If your sub-site tree is deep, then the expense grows exponentially.  </p>
  
<p>In addition to navigation, utilize lists, list view web parts, content types, and content query web parts to do mundane tasks with boring data that isn't otherwise interesting to write code against.  Recent news, favorite links, "what's new", etc....  leave those things for out-of-the-box functionality so that you can go back to your desk and write code for the killer feature. </p>
  
<h3>#8 Use Blob Caching (a.k.a. disk-based caching)</h3>  
<p>Blob caching makes static resources load faster from the server and also downstream to the browser.  Instead of pulling items from SQL Server, Blob caching will write cached items to the hard disk as needed.  These two articles describe how to use and set up Blob Caching:</p>
  
<p><a title="http://tinyurl.com/67z64f" href="http://tinyurl.com/67z64f">http://tinyurl.com/67z64f</a></p>
  
<p><a title="http://tinyurl.com/6bopqs" href="http://tinyurl.com/6bopqs">http://tinyurl.com/6bopqs</a></p>
  
<p>Blob caching only works for artifacts stored in SharePoint document and image libraries, and it is primarily for caching images, css, swf, etc files.  Use it for anything that is a static resource that won't change per user.  </p>
  
<p>Without implementing Blob caching, the server will return a 304 response code for these items stored in document and image libraries.  Enabling the blob cache will improve page load time at the browser - especially if your pages are image-heavy.</p>
  
<h3>On With the Good Times...</h3>  
<p>Obviously there is more than these eight points to building Internet publishing sites with MOSS, but these are some of the lower-level developer-related items that will make your life easier.  Have fun! </p>
  
<div class="tags" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:4cf4593f-734f-4d4d-92c0-2e0681474bd6">Technorati Tags: <a href="http://technorati.com/tags/sharepoint" target="_blank" rel="tag">sharepoint</a> <a href="http://technorati.com/tags/web" target="_blank" rel="tag">web</a> <a href="http://technorati.com/tags/content%20management" target="_blank" rel="tag">content management</a></div> 
