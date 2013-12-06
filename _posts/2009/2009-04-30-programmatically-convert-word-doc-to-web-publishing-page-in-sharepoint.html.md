---
layout: post
title: "Programmatically Convert Word Doc to Web Publishing Page in SharePoint"
---

<p>MOSS 2007 ships with a Word 2007 to Web Page document converter.  Normally you configure and execute this converter from the UI.  You can enable the converter on any document library in SharePoint, then convert a document in the doc lib while specifying the target SharePoint site where the new page should be created.  If your target web page library is a Pages library in a Publishing site, then the new file automatically gets created as a Publishing page and the Word content gets inserted into a content region in your new page.  </p>

  
<p>For an upcoming document migration, we need to convert Word documents to Publishing pages while they are inserted into SharePoint.  Not surprisingly, I couldn't find a single bit of documentation in the SharePoint SDK on how to do this.  </p>

  
<p>Do <strong><em>NOT</em></strong> use SPFile.Convert().  You won't be able to target a destination Pages library that way.  The file will only be converted and placed in the origin document library.</p>

  
<p>By piecing together small bits of info from class library documentation I found a decent approach where I could convert a document <strong><em>and</em></strong> send it to a target Pages library:</p>

  
<ol>   
<li>Get a handle to the SPFile you want to convert/move </li>    
<li>Get a handle to the PublishingPageCollection where you want to convert/move the file to </li>    
<li>Use PublishingPageCollection.Add() to add the page, and pass in the Guid of the document converter you want to use.  </li> 
</ol>  
<p>Example:</p>

  
<pre><code>
//code not compiled or tested
SPSite site = new SPSite("http://server");
SPWeb web = site.OpenWeb("/some/web/site");
SPList docs = web.Lists["MyWordDocs"]
SPFile file = docs.Items[0].File;

PublishingWeb pubWeb = 
	PublishingWeb.GetPublishingWeb(web);
PublishingPageCollection pages = 
	pubWeb.GetPublishingPages();
PageConversionPriority priority = 
	PageConversionPriority.Immediately;

Guid converterId = 
	new Guid("6dfdc5b4-2a28-4a06-b0c6-ad3901e3a807");

PublishingPage newPage = 
	pages.Add(	
	"MyNewPage.aspx", 
	file,
	converterId,
	priority);
</code></pre>
<p>The one catch is obtaining the Guid of the converter. In SharePoint Central Administration you can go to the screen where you can configure your document converters. It's at this Url: http://server:[centraladminport]/_admin/DocTransAdmin.aspx:</p>

  
<p><a href="http://www.kindohm.com/LocalImages/Posts/ProgrammaticallyConvertWordDoctoWebPubli_A07E/conversions01.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="conversions01" border="0" alt="conversions01" src="http://www.kindohm.com/LocalImages/Posts/ProgrammaticallyConvertWordDoctoWebPubli_A07E/conversions01_thumb.png" width="501" height="480" /></a> </p>

  
<p>Click the converter you want to customize, and on the following screen you can grab the Guid from the Url query string in the browser's address bar.</p>

  
<div id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:3a7d8315-c616-4831-a6a2-e032008c20e3" class="tags">Technorati Tags: <a href="http://technorati.com/tags/sharepoint" rel="tag">sharepoint</a>,<a href="http://technorati.com/tags/conversion" rel="tag">conversion</a>,<a href="http://technorati.com/tags/document" rel="tag">document</a>,<a href="http://technorati.com/tags/codeplex" rel="tag">codeplex</a></div> 
