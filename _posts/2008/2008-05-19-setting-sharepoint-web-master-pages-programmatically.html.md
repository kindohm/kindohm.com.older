---
layout: post
title: "Setting SharePoint Web Master Pages Programmatically"
---

<p>For "Publishing" sites In Microsoft Office SharePoint Server, you have the ability to specify a master page that all pages in the web should use:</p>


  
<p><a href="http://www.kindohm.com/localimages/posts/SettingSharePointWebMasterPagesProgramma_CC3E/SelectMaster.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="322" alt="SelectMaster" src="http://www.kindohm.com/localimages/posts/SettingSharePointWebMasterPagesProgramma_CC3E/SelectMaster_thumb.png" width="355" border="0" /></a> </p>


  
<p>Optionally, you have the ability to inherit the master page of the parent web (if a parent exists):</p>


  
<p><a href="http://www.kindohm.com/localimages/posts/SettingSharePointWebMasterPagesProgramma_CC3E/InheritMaster.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="337" alt="InheritMaster" src="http://www.kindohm.com/localimages/posts/SettingSharePointWebMasterPagesProgramma_CC3E/InheritMaster_thumb.png" width="368" border="0" /></a></p>


  
<p>Setting these two values programmatically is easy, but not necessarily intuitive.</p>


  
<p>First, to set the master page, it is as easy as setting the value of an <code>SPWeb</code> object's <code>CustomMasterUrl</code> property to the server-relative path of the master page:</p>


  
<pre><code>SPSite site = new SPSite("http://myserver");
SPWeb web = site.OpenWeb("/some/web/path");
web.CustomMasterUrl = "/_catalogs/masterpage/MyCustomMasterPage.master";
web.Update();</code></pre>
<p>There is also a property named <code>MasterUrl</code> on an <code>SPWeb</code> object. <code>MasterUrl</code> will set the master page of all <em>system</em> pages in the web (e.g. list pages, administrative pages, etc). You can basically accomplish the same thing for system pages by setting this property's value to a master page path.</p>


  
<p>The "inheritance" setting is a little more tricky. There is no property on an <code>SPweb</code> object where you can set whether or not the web should inherit its master page from the parent.  Using <a href="http://www.aisto.com/roeder/dotnet/" target="_blank">Reflector</a>, I chose to reverse-engineer the system web page in SharePoint where these properties are set.  The system page is located at <code>/_layouts/ChangeSiteMasterPage.aspx</code> and inherits from <code>Microsoft.SharePoint.Publishing.Internal.CodeBehind.AreaChromeSettingsPage</code>, which is in the <code>Microsoft.SharePoint.Publishing.dll</code> assembly.</p>


  
<p>In the "OK" button click event handler, eventually a call is made to update the inheritance value on a class named <code>InheritableStringProperty</code>, which is a public, abstract class. There are three classes that derive from it - one of which we are interested in, named <code>CustomMasterUrlProperty</code>:</p>


  
<p><a href="http://www.kindohm.com/localimages/posts/SettingSharePointWebMasterPagesProgramma_CC3E/hierarchy.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="147" alt="hierarchy" src="http://www.kindohm.com/localimages/posts/SettingSharePointWebMasterPagesProgramma_CC3E/hierarchy_thumb.png" width="420" border="0" /></a> </p>


  
<p><code>CustomMasterUrlProperty</code> is marked as <em>internal</em>, so we can't use it directly. But upon inspecting it further in Reflector, I came across a property that returns the name of an entry stored in an <code>SPWeb</code> property bag:</p>


  
<p><a href="http://www.kindohm.com/localimages/posts/SettingSharePointWebMasterPagesProgramma_CC3E/reflector.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="260" alt="reflector" src="http://www.kindohm.com/localimages/posts/SettingSharePointWebMasterPagesProgramma_CC3E/reflector_thumb.png" width="546" border="0" /></a> </p>


  
<p>If you've worked a lot with <code>SPWeb</code> property bags, then you'll probably guess that the name "__InheritsCustomMasterUrl" looks like a typical "Property Bag" entry in an <code>SPWeb</code>.</p>


  
<p>If we open up an <code>SPWeb's</code> property bag in PowerShell, we can see that the <code>__InheritsCustomMasterUrl</code> entry exists:</p>


  
<pre><code>C:\&gt; $s = new-object microsoft.sharepoint.spsite("http://lyra")
C:\&gt; $w = $s.OpenWeb("/sub")
C:\&gt; $w.AllProperties

Name                           Value
----                           -----
vti_defaultlanguage            en-us
vti_associateownergroup        3
vti_associatevisitorgroup      4
__InheritsMasterUrl            True
vti_associatemembergroup       5
__DocumentsListId              5134728e-a8a5-40bb-adc7-08a60f01ec2a
__PagesListId                  9aacb3d7-f4db-4874-b51b-9ab31cb7c286
__PublishingFeatureActivated   True
__InheritWebTemplates          True
vti_associategroups            5;4;3;7;8;9;10;11;12
__NavigationShowSiblings       True
vti_approvallevels             Approved Rejected Pending\ Review
NavigationPropertiesSet        True
__ImagesListId                 bd76f653-c89f-4d91-b10e-740daff56750
__IncludeSubSitesInNavigation  True
vti_extenderversion            12.0.0.4518
__InheritsCustomMasterUrl      True
__PageLayouts                  __inherit
__WebTemplates
__InheritsAlternateCssUrl      True</code></pre>
<p>Note that you'll need to access the <code>SPWeb's</code> <code>AllProperties</code> property (rather than the "<code>Properties</code>" property) in order to find the "hidden" property bag entries.</p>


  
<p>So, to finish things off, setting the master page inheritance value is as simple as updating the property bag entry's value.  Note that the value of <code>__InheritsCustomMasterUrl</code> is a string:</p>


  
<pre><code>SPSite site = new SPSite("http://myserver");
SPWeb web = site.OpenWeb("/some/relative/path");
web.AllProperties["__InheritsCustomMasterUrl"] = "False";
web.Update();</code></pre>
<p>Enjoy.</p>


  
<div class="tags" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:a66f832a-5af3-46f9-a46e-519061a0d375">Technorati Tags: <a href="http://technorati.com/tags/sharepoint" target="_blank" rel="tag">sharepoint</a> <a href="http://technorati.com/tags/code" target="_blank" rel="tag">code</a> <a href="http://technorati.com/tags/programming" target="_blank" rel="tag">programming</a> <a href="http://technorati.com/tags/master%20page" target="_blank" rel="tag">master page</a></div> 
