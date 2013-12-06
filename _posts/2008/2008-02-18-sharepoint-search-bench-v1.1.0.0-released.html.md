---
layout: post
title: "SharePoint Search Bench v1.1.0.0 Released"
---

<p>Version 1.1.0.0 of SharePoint Search Bench has been released:</p>
<p><a title="http://www.codeplex.com/SPSearchBench" href="http://www.codeplex.com/SPSearchBench">http://www.codeplex.com/SPSearchBench</a></p>
<p>There are a few new features in this release:</p>
<ol> 
<li>Full Text query generation tools.  If you're not familiar with the syntax of Full Text search queries, there is a <code>FullTextBuilder</code> class (and some other supporting classes) that can build the queries for you.  Something like this:<br /><br /><code style="padding-right: 3px; padding-left: 3px; padding-bottom: 3px; padding-top: 3px; background-color: #cccccc">FullTextBuilder text = FullTextBuilder.Select("Title, Author").Where(Condition.FreeText("dog, cat"));</code><br /><br />Any number of conditions may be "and'ed" and "or'ed" together.<br /> </li>
<li>Support for verbose logging with log4net.  </li>
<li>A CHM help file with API documentation.  The documentation goes a little beyond listing the types and members in the assembly by providing some examples.  It is not 100% comprehensive yet.  </li>
</ol> 
<div class="tags" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:7ebda640-538b-48e7-8a64-e341cf00d55c">Technorati Tags: <a href="http://technorati.com/tags/sharepoint" target="_blank" rel="tag">sharepoint</a> <a href="http://technorati.com/tags/code" target="_blank" rel="tag">code</a></div> 
