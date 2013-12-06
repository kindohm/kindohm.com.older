---
layout: post
title: "SharePoint Search Bench Initial Release"
---

<p><a title="Photo Sharing" href="http://www.flickr.com/photos/kindohm/1813374111/" target="_blank"><img style="margin: 5px" height="230" alt="SharePoint Search Bench Screen Shot" src="http://farm3.static.flickr.com/2268/1813374111_f0f0642e5b_m.jpg" width="240" align="right" border="0" /></a> <a href="http://codeplex.com/SPSearchBench" target="_blank">Download SharePoint Search Bench from www.codeplex.com/SPSearchBench</a></p>
<p>SharePoint Search Bench is a WPF application used to run full-text SQL and keyword queries against Microsoft Office SharePoint Server (MOSS) search.   </p>
<p>This project is something I've had in the works for a little while.  Extending MOSS search is a common task among SharePoint developers, yet there aren't really any tools available to test, try, or develop queries against MOSS search.  Unlike SQL Server, MOSS doesn't ship with a "Query Analyzer".  On projects where I was extending MOSS search, I found myself writing my own app to write and test queries.  </p>
<p>Here are some notable features about the app:</p>
<ul> 
<li>Supports searching against MOSS using MOSS Search  </li>
<li>Supports searching via object model (on server)  </li>
<li>Supports searching via web service (remote clients)  </li>
<li>Supports Full Text searches using full text SQL syntax  </li>
<li>Supports keyword searches, including custom returned columns/properties  </li>
<li>Lists available search scopes and managed properties  </li>
<li>Ability to change query packet XML for web service calls  </li>
<li>Ability to save your connection and query settings between sessions </li></ul> 
<p>App characteristics:</p>
<ul> 
<li>Built on WPF  </li>
<li>Built using VS 2005 and WPF extensions  </li>
<li>.Net 3.0 required  </li>
<li>Does not support WSS, CAML or List-based queries/searches  </li>
<li>Does not support WSS v2 or SPS </li></ul> 
<p>Another purpose of developing the app was to continue learning WPF.  I've had my head in WPF for a long, long time, but haven't ventured outside of its 3D features very much.  I wanted this WPF app to be clean and take advantage of certain features that WPF provides.  Specifically, lots of data binding, heavy re-use of styles, data templates, and control templates.</p>
<p>The CodePlex project has a forum and issue tracker open, so feel free to leave feedback or report problems.  </p>
<div class="tags" id="0767317B-992E-4b12-91E0-4F059A8CECA8:15260473-e2ef-4e21-bc7f-086ef04cb103" contenteditable="false">Technorati Tags: <a href="http://technorati.com/tags/sharepoint" target="_blank" rel="tag">sharepoint</a> <a href="http://technorati.com/tags/moss" target="_blank" rel="tag">moss</a> <a href="http://technorati.com/tags/search" target="_blank" rel="tag">search</a> <a href="http://technorati.com/tags/wpf" target="_blank" rel="tag">wpf</a> <a href="http://technorati.com/tags/code" target="_blank" rel="tag">code</a></div> 
