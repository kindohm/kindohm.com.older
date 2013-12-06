---
layout: post
title: "SharePoint Search Bench v2.0 Released"
---

<p><img style="margin-bottom: 5px; margin-left: 5px" alt="search" src="http://craig.senate.gov/i/mag1.jpg" width="150" align="right" />I'm pleased to announce the release of SharePoint Search Bench 2.0!</p>
<p><a href="http://www.codeplex.com/SPSearchBench">http://www.codeplex.com/SPSearchBench</a></p>
<p>Version 2.0 contains a major API change that eliminates a dependency on Microsoft.Office DLL's for web service calls.  For a web service call, you shouldn't need to depend on the underlying provider of the data you are accessing remotely.  The API now executes web service calls successfully if the Microsoft DLL's are not found on the local machine.  Version 2.0 causes a breaking change if you are using the SP Search Bench API because a Microsoft enum is now "wrapped" by a custom SP Search Bench enum.</p>
<p>A new, minor release (v2.1) is planned for the near future, which will contain a JavaScript implementation of the SP Search Bench C# API and will execute exclusively against the SharePoint Search web service.  I've already begun development on this feature and have overcome all of the technology barriers I was expecting to face so far.  Now it is just a matter of tidying up the JavaScript API interface and testing....  and overcoming any <strong><em>unforeseen</em></strong> barriers! </p>
<p>Further down the road, I'd like to completely overhaul the SP Search Bench WPF user interface.  Right now, the UI doesn't use screen real-estate very wisely.  My initial attempts at a new layout haven't worked out so well due to difficulties with getting the results grid to scroll and re-size properly.  That'll be another challenge for a new day.</p>
<div class="tags" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:2fcbb8e5-e4ff-4cdc-926a-7a2870dd0239">Technorati Tags: <a href="http://technorati.com/tags/sharepoint" rel="tag">sharepoint</a>,<a href="http://technorati.com/tags/search" rel="tag">search</a>,<a href="http://technorati.com/tags/code" rel="tag">code</a>,<a href="http://technorati.com/tags/codeplex" rel="tag">codeplex</a></div> 
