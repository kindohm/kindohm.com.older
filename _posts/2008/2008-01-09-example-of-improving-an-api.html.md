---
layout: post
title: "Example of Improving an API"
---

<p>For my upcoming <a href="http://www.twincitiescodecamp.com" target="_blank">Code Camp</a> talk, I'll be discussing the MOSS Search API.  Part of the talk will be demonstrating my own API (known as <a href="http://www.codeplex.com/SPSearchBench" target="_blank">SharePointSearchBench</a>) that wraps the Search API.  The original purpose of SharePointSearchBench was to make it easier to conduct MOSS Searches using either the local Search object model (if you're on the server) or the Search web services provided by SharePoint (if you're on a remote machine).  </p>

<p>My first stab at the API interface months ago satisfied the requirement of wrapping the object model and remote service calls into a single class, but it was ugly to use:</p>

<pre><code>QueryType queryType = QueryType.Keyword;
ConnectionContext context = new ConnectionContext();
context.UseObjectModel = false;
context.Username = "pgibbons";
context.Password = "caseofthemondays";
context.Url = "http://sharepointbox";
SearchBase searchObject = SearchBase.MakeSearchObject(queryType, context);
searchObject.Query = "accounting";
searchObject.Execute();</code></pre>
<p>My that is ugly - especially the SearchBase.MakeSearchObject() crap.  The wrapper API interface suffered as a result of not being sure how to deal with certain challenges between the object model and service calls. Thus, my API was difficult to use and understand. Not good for explaining it to other people.</p>

<p>I took a step back and looked at the SharePoint Search object model and service interfaces and created a new wrapping interface that joins the two worlds in beautiful harmony. The two methods of performing searches are surprisingly quite similar...  if you actually stop and <strong><em>look</em></strong> at them.  No more games or covering up issues that I didn't want to solve before. Now there's a better way to write the code:</p>

<pre><code>Search search = new Search();
search.ApiSource = ApiSource.Service;
search.SearchType == SearchType.Keyword;
search.ContextUri = new Uri("http://mysharepointbox");
search.Credentials = new NetworkCredential("pgibbons", "caseofthemondays"); 
search.SearchText = "accounting";
search.Execute();</code></pre>
<p>Or to do a local object model call...</p>

<pre><code>Search search = new Search();
search.ApiSource = ApiSource.ObjectModel;
search.SearchType == SearchType.Keyword;
search.ContextUri = new Uri("http://mysharepointbox");
search.SearchText = "accounting";
search.Execute();</code></pre>
<p>It's a bit leaner than the old method. It's also more intuitive for the programmer using it. The wrapper code has also become easier to maintain and looks a lot cleaner. </p>

<div class="tags" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:1a73893c-ee02-4ff0-8473-3c88d7ab30fa">Technorati Tags: <a href="http://technorati.com/tags/code" target="_blank" rel="tag">code</a> <a href="http://technorati.com/tags/sharepoint" target="_blank" rel="tag">sharepoint</a> <a href="http://technorati.com/tags/api" target="_blank" rel="tag">api</a></div> 
