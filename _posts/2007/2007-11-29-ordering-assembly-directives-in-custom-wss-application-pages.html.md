---
layout: post
title: "Ordering Assembly Directives in Custom WSS Application Pages"
---

<p>Apparently, the ordering of your &lt;%@ Assembly %&gt; directive before your &lt;%@ Page %&gt; in custom WSS application pages makes a difference. Make sure you put the Assembly directive before the Page directive:</p>


<pre><code>&lt;%@ Assembly Name="MyWebApp, Version=1.0.0.0, 
	Culture=neutral, PublicKeyToken=abcdefg817264759" %&gt;
&lt;%@ Page MasterPageFile="~/_layouts/application.master" Language="C#" 
	Inherits="MyWebApp.MyForm" %&gt;</code></pre>
<p>This is <em><strong>incorrect</strong></em>:</p>


  
<pre><code>&lt;!-- INCORRECT --&gt;
&lt;%@ Page MasterPageFile="~/_layouts/application.master" Language="C#"
	Inherits="MyWebApp.MyForm" %&gt;
&lt;%@ Assembly Name="MyWebApp, Version=1.0.0.0, 
	Culture=neutral, PublicKeyToken=abcdefg817264759" %&gt;</code></pre>
    
<p>Doing it the incorrect way, you'll get a generic error message:</p>


<p><img src="http://farm3.static.flickr.com/2159/2074616398_bbcd5a4e6b_o.png" /></p>


<p>This rule probably applies in other ASP .Net cases, but I've never come across it before.</p>


<div class="tags" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:0202a2e3-3ad8-4c75-9620-db7934d7a8f3">Technorati Tags: <a href="http://technorati.com/tags/sharepoint" rel="tag" target="_blank">sharepoint</a> <a href="http://technorati.com/tags/error" rel="tag" target="_blank">error</a> <a href="http://technorati.com/tags/pages" rel="tag" target="_blank">pages</a> <a href="http://technorati.com/tags/asp.net" rel="tag" target="_blank">asp.net</a></div> 
