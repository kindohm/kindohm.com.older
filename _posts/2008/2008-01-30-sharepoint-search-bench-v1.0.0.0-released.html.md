---
layout: post
title: "SharePoint Search Bench v1.0.0.0 Released"
---

<p>SharePoint Search Bench v1.0.0.0 has been released: <a href="http://codeplex.com/SPSearchBench">http://codeplex.com/SPSearchBench</a>.  </p>


<p>The latest release contains a slightly enhanced user interface and a completely overhauled API.  The API is actually "public" in this version and you can code against it to your liking:</p>


<pre><code>// example object model keyword query
Search search = new Search();
search.SearchType = SearchType.Keyword;
search.SearchText = "dog cat llama";
Uri siteUri = new Uri("http://SharePointSite");
search.ContextUri = siteUri;
search.ApiSource = ApiSource.ObjectModel;
DataSet results = search.Execute();</code></pre>
<pre><code>// example service full text query
Search search = new Search(); search.SearchType = SearchType.FullText; NetworkCredential credentials = CredentialCache.DefaultCredentials; Uri serviceUri = new Uri("http://SharePointSite/_vti_bin/Search.asmx"); search.SearchText = "SELECT Title, Author FROM Scope() " +     "WHERE FREETEXT(DEFAULTPROPERTIES, 'dog cat llama') " +     "ORDER BY RANK"; search.Credentials = credentials; search.ApiSource = ApiSource.Service; search.ContextUri = serviceUri; DataSet results = search.Execute();</code></pre> 
<p>The next feature I'm working on is full text SQL query generation. That is, you shouldn't have to know how to type a long SELECT...FROM...WHERE...ORDER BY statement for Full Text queries, let alone understand how to use FREETEXT and CONTAINS predicates. <a href="http://justaddcode.com">Neil</a> has been helping a bit with suggestions for the query generation interface. It'll look something like this:</p>


<pre><code>FullTextBuilder query = FullTextBuilder.Select("title, author");
query = query.Where(Condition.Contains("Description", "llama dog cat"));
query = query.OrderBy("rank");
string queryText = query.ToString();</code></pre>
<p>The above code would evaluate to:</p>


<pre><code>SELECT title, author FROM Scope() WHERE CONTAINS(Description, 'llama dog cat') ORDER BY rank</code></pre>
<p>The Conditions are where it'll be at.  They'll not only support CONTAINS predicates, but FREETEXT, basic comparisons, Scope specifications, and logical concatenation too.</p>


  
