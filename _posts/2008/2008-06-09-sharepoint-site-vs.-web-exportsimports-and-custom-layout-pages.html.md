---
layout: post
title: "SharePoint Site vs. Web exports/imports and Custom Layout Pages"
---

<p>If you are writing code using the SharePoint migration API (e.g. using the SPExport/SPImport classes, which <a href="http://blogs.technet.com/stefan_gossner/archive/2007/08/30/deep-dive-into-the-sharepoint-content-deployment-and-migration-api-part-1.aspx" target="_blank">Stefan Go?ner has blogged in detail about</a>), you'll want to be careful about the scope of your exports when exporting custom layout pages in MOSS.  In short, custom layout pages get copied to the wrong folder when performing an export scoped at the site collection level.  Make sure you use an export scoped at the top-level web rather than the site collection in order to get layout pages in their proper place.</p>



  
<p>Let's start with a scenario - let's build a custom content type and a custom layout page (in SharePoint Designer *gasp* just to be quick) in a source environment.  Here I've created a very simple "article" Page Layout content type with nothing fancy added:</p>



  
<p><a href="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/contenttype.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="481" alt="contenttype" src="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/contenttype_thumb.png" width="674" border="0" /></a></p>



  
<p>Then I'll author up a custom layout page.  </p>



  
<p><a href="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/newlayout.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="454" alt="newlayout" src="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/newlayout_thumb.png" width="637" border="0" /></a>  </p>



  
<p>Here is the layout page markup:</p>



  
<pre><code>&lt;%@ Page language="C#" Inherits="blah" %&gt;
&lt;%@ Register Tagprefix="SharePointWebControls" Namespace="blah" %&gt; 
&lt;%@ Register Tagprefix="WebPartPages" Namespace="blah" %&gt; 
&lt;%@ Register Tagprefix="PublishingWebControls" Namespace="blah" %&gt; 
&lt;%@ Register Tagprefix="PublishingNavigation" Namespace="blah" %&gt;

&lt;asp:Content 
	ContentPlaceholderID="PlaceHolderPageTitle" 
	runat="server"&gt;
	&lt;SharePointWebControls:FieldValue id="PageTitle" 
		FieldName="Title" 
		runat="server"/&gt;
&lt;/asp:Content&gt;

&lt;asp:Content 
	ContentPlaceholderID="PlaceHolderMain" 
	runat="server"&gt;

	&lt;h1&gt;t3h custom&lt;/h1&gt;
	&lt;PublishingWebControls:RichHtmlField 
		FieldName="PublishingPageContent" 
		runat="server" /&gt;

&lt;/asp:Content&gt;</code></pre>
<p>If you're following along in your own environment, make sure to dot your t's and cross your i's by checking-in and approving your new layout page.</p>



  
<p>Next, I'll create a new page with the new layout, add a little content, and approve the new page:</p>



  
<p><a href="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/createpage.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="461" alt="createpage" src="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/createpage_thumb.png" width="644" border="0" /></a></p>



  
<p><a href="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/newpage.png"><img style="border-right: 0px; border-top: 0px; border-left: 0px; border-bottom: 0px" height="443" alt="newpage" src="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/newpage_thumb.png" width="644" border="0" /></a></p>



  
<p>You can see that the custom layout is being used by the "<strong>t3h custom</strong>" heading.  </p>



  
<p>So now let's migrate this thing over to a destination environment using a "Site" scope.  That is, a site collection scope.  The code looks something like this:</p>



  
<pre><code>SPExportObject exportObject = new SPExportObject(); 
exportObject.Id = site.ID; 
exportObject.Type = SPDeploymentObjectType.Site; 

string oldChangeToken = SomehowGetOldChangeTokenFromPreviousFullExport();

SPExportSettings settings = new SPExportSettings();  
settings.SiteUrl = "http://lyra:6666/sites/source02"; 
settings.ExportMethod = SPExportMethodType.ExportAll;  
settings.FileLocation = @"c:\export"; 
settings.FileCompression = false; 
settings.ExportObjects.Add(exportObject); 
settings.ExportMethod = SPExportMethodType.ExportChanges;  
settings.ExportChangeToken = oldChangeToken;

SPExport export = new SPExport(settings); 
export.Run();  

site.Dispose();</code></pre>
<p>Please refer to <a href="http://blogs.technet.com/stefan_gossner/archive/2007/08/30/deep-dive-into-the-sharepoint-content-deployment-and-migration-api-part-2.aspx" target="_blank">Stefan's post</a> on incremental exports for more information on change tokens.</p>



  
<p>Now you can see the new page in the target environment. Just so you know I'm not faking it, the new url is outlined in red:</p>



  
<p><a href="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/newpageintarget.png"><img style="border-right: 0px; border-top: 0px; border-left: 0px; border-bottom: 0px" height="484" alt="newpageintarget" src="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/newpageintarget_thumb.png" width="618" border="0" /></a>   </p>



  
<p>Ok, so it looks like everything got migrated over correctly. Wrong. Take a look at the layout pages list in the <em>source</em> environment. You'll see that the custom layout page shows up correctly:</p>



  
<p><a href="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/sourcelayoutlist.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="484" alt="sourcelayoutlist" src="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/sourcelayoutlist_thumb.png" width="462" border="0" /></a></p>



  
<p>Now look at the same list in the target site collection:</p>



  
<p><a href="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/targetlayoutlist.png"><img style="border-right: 0px; border-top: 0px; border-left: 0px; border-bottom: 0px" height="484" alt="targetlayoutlist" src="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/targetlayoutlist_thumb.png" width="419" border="0" /></a>  </p>



  
<p>The custom layout page is nowhere to be found.  But, if you click on the "en-us" folder, and then click the "preview images" sub-folder, you'll find the migrated layout page:</p>



  
<p><a href="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/pageinprevimages.png"><img style="border-right: 0px; border-top: 0px; border-left: 0px; border-bottom: 0px" height="484" alt="pageinprevimages" src="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/pageinprevimages_thumb.png" width="420" border="0" /></a></p>



  
<p>For some reason the layout page was placed in the English language preview images folder.  How can this be?  Who knows...  but the API just doesn't work how you'd expect it.  Somehow, SharePoint keeps it all together and the migrated page works correctly, but I'd rather not let this content stay this way.</p>



  
<p>Here is a better way to do it.  Go through this example again and create a new content type, new page layout, and a new page in the source environment:</p>



  
<p><a href="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/2ndcontenttype.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="64" alt="2ndcontenttype" src="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/2ndcontenttype_thumb.png" width="420" border="0" /></a> </p>



  
<p><a href="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/newlayoutpage2.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="451" alt="newlayoutpage2" src="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/newlayoutpage2_thumb.png" width="636" border="0" /></a></p>



  
<p><a href="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/newpage2.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="334" alt="newpage2" src="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/newpage2_thumb.png" width="367" border="0" /></a>  </p>



  
<p>Save and publish the page, then run an <em><strong>incremental web export</strong>, </em>instead of a site-level export.  This export will be almost identical to the one performed above with a site, except the export settings are set up for the top-level web:</p>



  
<pre><code>SPExportObject exportObject = new SPExportObject(); 
exportObject.Id = web.ID; 
exportObject.Type = SPDeploymentObjectType.Web;</code></pre>
<p>Now when the web is imported, the layout page(s) arrive in their correct destination:</p>



  
<p><a href="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/finallayoutlist.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="484" alt="finallayoutlist" src="http://www.kindohm.com/localimages/posts/Sha.WebexportsimportsandCustomLayoutPage_9AA9/finallayoutlist_thumb.png" width="470" border="0" /></a> </p>



  
<p>Note that the original layout (MikeLayout.aspx) I tried to migrate first now shows up in the correct location.  It actually resides in two locations now - one at the root of the masterpages folder and the other copy in the en-us/previewimages folder.  I haven't explored what the effects of this are yet - but I'd recommend either starting over or cleaning up anything that is not where it is supposed to be.  In this case, I'd just recommend completely starting over and deleting the content that was not migrated correctly (and its dependents).  </p>



  
<div class="tags" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:9be47866-9592-4fda-a748-f5c56adce491">Technorati Tags: <a href="http://technorati.com/tags/sharepoint" target="_blank" rel="tag">sharepoint</a> <a href="http://technorati.com/tags/code" target="_blank" rel="tag">code</a> <a href="http://technorati.com/tags/migration" target="_blank" rel="tag">migration</a></div> 
