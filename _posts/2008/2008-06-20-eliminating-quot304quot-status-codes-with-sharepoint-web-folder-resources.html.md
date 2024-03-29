---
layout: post
title: "Eliminating &amp;quot;304&amp;quot; Status Codes With SharePoint Web Folder Resources"
---

<p>The title is a trick title.  Plain and simple, just don't use Web Folders.</p>


  
<p>Be careful where you put your images, style sheets, JavaScript files, and other resources on your SharePoint site.  In short, store those items in either a Document Library or an Image Library, or don't store them in SharePoint at all.  Why?  The only other way to store them is in a "Web Folder".  When I say Web Folder, I mean a folder that is created either 1) by a Feature Module or 2) by hand in SharePoint Designer.  Items stored in Web Folders automatically have a "max age" of zero, have no expiration and cause unnecessary browser/server traffic.  A 304 status code on each of those items is sent back to the browser, even if the browser has already loaded those resources already in the past.  On small sites, this likely isn't a big problem.  But on high-traffic sites that serve out pages containing dozens or hundreds of images, it can be a very big deal.  In extreme cases, Web Folder resources can produce server errors and server timeouts (e.g. the server will attempt to serve out the resource for 30 seconds and then just give up).  </p>


  
<p>304's are typically passed around when the browser wants to know if the server has a new version of a file.  The browser shouldn't have to make this check on each page load.  More information on status codes can be found here: <a href="http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html" target="_blank">http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html</a></p>


  
<p>Let's look at an example.  I've developed a custom master page on a MOSS site.  It uses some out-of-the-box resources but also contains two important customizations: a header banner image and a custom CSS:</p>


  
<p><a href="http://www.kindohm.com/localimages/posts/SharePointWebFoldersProduceUnnecessary30_83BA/page.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="742" alt="page" src="http://www.kindohm.com/localimages/posts/SharePointWebFoldersProduceUnnecessary30_83BA/page_thumb.png" width="639" border="0" /></a> </p>


  
<p>Using <a href="http://www.fiddler2.com" target="_blank">Fiddler</a>, let's take a look at the interaction between the server and the browser on this page:</p>


  
<p><a href="http://www.kindohm.com/localimages/posts/SharePointWebFoldersProduceUnnecessary30_83BA/fiddler01.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="225" alt="fiddler01" src="http://www.kindohm.com/localimages/posts/SharePointWebFoldersProduceUnnecessary30_83BA/fiddler01_thumb.png" width="605" border="0" /></a> </p>


  
<p>I've visited this page before, and you can see that for each page resource (CSS style sheets and the JPG header banner image) each produce a 304 status code.  If I visit the page again (without refreshing), the same status codes for each file are generated.</p>


  
<p>Notice the path of the resources.  All of the CSS files are stored in an out-of-the-box document library named "Style Library" (or style%20library for the URL-encoding gifted).  The header banner image (bigpisablur.jpg) is stored under the "/Assets" path.  "Assets" is not a document or image library.  It's a web folder, created with a custom Feature I created for my page branding.  Within the depths of the Feature, I'm using a module to create the web folder and add the image:</p>


  
<pre><code>&lt;Elements 
	xmlns="http://schemas.microsoft.com/sharepoint/"&gt;
	&lt;Module 
	Url="Assets/KindohmMIS/Images" 
	Path="" 
	RootWebOnly="TRUE" 
	Name="Images" &gt;
		&lt;File 
		Url="BigPisaBlur.jpg" 
		Path="assets\KindohmMIS\images\BigPisaBlur.jpg" 
		IgnoreIfAlreadyExists="TRUE" /&gt;
  &lt;/Module&gt;
&lt;/Elements&gt;</code></pre>
<p>While not preferred, another way to create a Web Folder is to create a folder in the site using SharePoint Designer:</p>


  
<p><a href="http://www.kindohm.com/localimages/posts/SharePointWebFoldersProduceUnnecessary30_83BA/spd.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="411" alt="spd" src="http://www.kindohm.com/localimages/posts/SharePointWebFoldersProduceUnnecessary30_83BA/spd_thumb.png" width="580" border="0" /></a> </p>


  
<p>So, how do we eliminate these unnecessary 304 status codes?  </p>


  
<p>For items stored in a Document or Image Library, the answer is Blob Caching.  The Blob Cache will keep a cache of files stored in Document Libraries and prevent the 304 status codes.  You can enable Blob Caching in the site's web.config file by changing the Blob Cache element's "enabled" value to "true":</p>


  
<pre><code>&lt;BlobCache 
    location="C:\blobCache" 
    path="\.(gif|jpg|png|css|js)$" 
    maxSize="2" 
    enabled="true" /&gt;</code></pre>
<p>You can read more on setting up Blob Caching in this TechNet article: <a href="http://technet.microsoft.com/en-us/library/cc263099(TechNet.10).aspx#section7" target="_blank">http://technet.microsoft.com/en-us/library/cc263099(TechNet.10).aspx#section7</a>.</p>


  
<p>After enabling the Blob Cache, the Fiddler output changes after loading the page (without a Refresh):</p>


  
<p><a href="http://www.kindohm.com/localimages/posts/SharePointWebFoldersProduceUnnecessary30_83BA/Fiddler02.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="231" alt="Fiddler02" src="http://www.kindohm.com/localimages/posts/SharePointWebFoldersProduceUnnecessary30_83BA/Fiddler02_thumb.png" width="522" border="0" /></a> </p>


  
<p>As you can see, the resources stored in the Style Library no longer produce the 304 status codes.  Less traffic across the wire!</p>


  
<p>With the /Assets web folder, we will never be able to eliminate the 304 status codes if we keep it as a web folder.  There is nothing within the Module or File elements within the custom Feature that will allow us to control content expiration or the "max age".  Blob Caching can only affect resources stored in Document or Image Libraries.  </p>


  
<p>Lets try eliminating the Assets folder as a Web Folder and changing it to a file-system folder under the root of the site.  To do this, we can re-name the folder using SharePoint Designer.  I'll rename it to Assets_OLD:</p>


  
<p><a href="http://www.kindohm.com/localimages/posts/SharePointWebFoldersProduceUnnecessary30_83BA/assets_old.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="166" alt="assets_old" src="http://www.kindohm.com/localimages/posts/SharePointWebFoldersProduceUnnecessary30_83BA/assets_old_thumb.png" width="202" border="0" /></a> </p>


  
<p>Then just to check/prove that the path is no longer valid, the page loads without the header banner:</p>


  
<p><a href="http://www.kindohm.com/localimages/posts/SharePointWebFoldersProduceUnnecessary30_83BA/site_nobanner.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="472" alt="site_nobanner" src="http://www.kindohm.com/localimages/posts/SharePointWebFoldersProduceUnnecessary30_83BA/site_nobanner_thumb.png" width="624" border="0" /></a> </p>


  
<p>Now, I'll add the Assets folder (along with its contents) to the file system directly under the virtual root of the web site in IIS:</p>


  
<p><a href="http://www.kindohm.com/localimages/posts/SharePointWebFoldersProduceUnnecessary30_83BA/assets_FS.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="359" alt="assets_FS" src="http://www.kindohm.com/localimages/posts/SharePointWebFoldersProduceUnnecessary30_83BA/assets_FS_thumb.png" width="557" border="0" /></a> </p>


  
<p>Now let's use Fiddler and see if we took care of the 304 status on the background image:</p>


  
<p><a href="http://www.kindohm.com/localimages/posts/SharePointWebFoldersProduceUnnecessary30_83BA/no304.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="230" alt="no304" src="http://www.kindohm.com/localimages/posts/SharePointWebFoldersProduceUnnecessary30_83BA/no304_thumb.png" width="521" border="0" /></a> </p>


  
<p>No more 304 status codes!</p>


  
<p>In a small web site with a small amount of traffic, getting rid of the 304 status codes may or may not be worth this amount of effort.  However in a large site that serves out dozens or hundreds of images on a single page, I'd encourage you to eliminate these codes.  That's a lot of extra chatter going across the wire that you don't need.</p>


  
<div class="tags" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:57505e19-af1c-490f-95ac-f19187457b2b">Technorati Tags: <a href="http://technorati.com/tags/sharepoint" target="_blank" rel="tag">sharepoint</a> <a href="http://technorati.com/tags/304" target="_blank" rel="tag">304</a> <a href="http://technorati.com/tags/web%20development" target="_blank" rel="tag">web development</a></div> 
