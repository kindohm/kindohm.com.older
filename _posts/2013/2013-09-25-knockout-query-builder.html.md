---
layout: post
title: "Knockout.js Query Builder"
date: "2013-09-25 20:32"
---

<p>On my current project we are in need of a dynamic query-building
    user interface. In non-geek terms, that means a web page that allows
    a user to specify fields, constraints, and values that evaluate to
    some kind of query or expression. Alright that was still a geeky
    explanation... but if you're reading this then the geek-speak is probably
    appropriate for you. But I digress...</p>

<p>Here's an example that my colleague <a href="http://justaddcode.com/blog">Neil</a>
    shared with me today: <a href="http://redquerybuilder.appspot.com/">redquerybuilder.appspot.com</a>.
    While this example accomplishes most of what I want, it is implemented with jQuery, which
    I want to avoid.</p>

<p>Instead, I want a knockout.js version. It turns out that a basic implementation
is pretty easy!</p>

<p>You can try out a demo of a basic implementation here:
    <a href="http://kindohm.github.io/knockout-query-builder/">kindohm.github.io/knockout-query-builder</a>.
    Source code is at <a href="https://github.com/kindohm/knockout-query-builder">github.com/kindohm/knockout-query-builder</a>.
    Screen capture below:</p>

<p><img src="/hodsmedia/ko-query-builder.png" alt="query builder"/></p>

<h3>How to build this thing...</h3>

<p>This little UI is all about the data structure you want the UI to represent.
    If you can plan the data structure, you can throw the UI on top of it. There
    may be specific theories and methods on how to properly represent a logical
    query like this, but I'm not following any particular approach here.</p>

<p>My data structure is as follows:</p>

<ul>
    
<li>Condition - a unit of logic that defines a field, comparison operator,
        and value (e.g. Color equals 'Black', Quantity is greater than two, etc).</li>
    
<li>Group - contains an array of Conditions and Groups, 
        and specifies whether to perform a <strong>logical-AND</strong> or a 
        <strong>logical-OR</strong> on all items in the array.</li>
</ul>

<h3>Simple Conditions</h3>

<p>Everything starts with a top-level group. In its simplest form, the data
  structure allows a group with multiple constraints:</p>

<p><img src="/hodsmedia/ko-query-builder-data-structure-1.png" alt="data structure 1"/></p>

<p>Such an example would produce something like this in the UI:</p>

<p><img src="/hodsmedia/ko-query-builder-example-1.png" alt="data structure example 1"/></p>

<h3>Groups Within Groups</h3>

<p>The fun part is nesting groups within groups:</p>

<p><img src="/hodsmedia/ko-query-builder-data-structure-2.png" alt="data structure 2"/></p>

<p>This would produce something like this:</p>

<p><img src="/hodsmedia/ko-query-builder-example-2.png" alt="data structure example 2"/></p>

<p>With the knockout.js models in JavaScript, the <code>Group</code> object just
  has an observable array of <code>Children</code> that we can add other 
  <code>Group</code> or <code>Condition</code> objects to:</p>

<pre><code>function Group() {

  var self = this;

  // ... //

  self.addCondition = function(){
      self.children.push(new Condition());
  };

  self.addGroup = function(){
      self.children.push(new Group());
  };

  self.removeChild = function(child){
      self.children.remove(child);
  };
}</code></pre>

<p>Take a look at <a href="https://github.com/kindohm/knockout-query-builder">the
  full source code</a> to see how the HTML wires up to these functions.</p>

<h3>Groups with AND versus OR</h3>

<p>In this design, you must assign a <code>Group</code> the type of logical operator
  that it should use: AND or OR. It will then use that operator for all
  of its children:</p>

<p><img src="/hodsmedia/ko-query-builder-logical-and.png" alt="logical and"/></p>

<p><img src="/hodsmedia/ko-query-builder-logical-or.png" alt="logical or"/></p>

<p>Naturally, nested groups can have different logical operators from their
  parents:</p>

<p><img src="/hodsmedia/ko-query-builder-and-and-or.png" alt="logical and plus or"/></p>

<p>Which could be created with something like this in the UI:</p>

<p><img src="/hodsmedia/ko-query-builder-and-and-or-ui.png" alt="logical and plus or"/></p>

<p>...and might evaluate to an example query like this:</p>

<p><code>(Goals &gt; 100 AND (Shots &lt; 1000 OR ShotPct &gt; .300) AND Points &gt; 0)</code></p>

<h3>Next Steps: Evaluating the Models as a Query</h3>

<p>It's beyond the scope of this article to translate the models that this
  UI generates into a query of some sort. Assuming you're using this code on a web
  page, you probably won't want to actually
  use the JavaScript objects to generate a query anyway. Rather, you'll probably
  persist the models that represent the query into a data store somewhere, which
  means doing a POST or PUT to a web server. Once the definition of the query
  is stored on the server side, you could:</p>

<ul>
  
<li>Generate a T-SQL statement</li>
  
<li>Generate an object-based query in your language of choice (e.g. LINQ in C#)</li>
  
<li>Generate a text query for a NoSQL database</li>
</ul>

<p>The example output you see in the code base is just that: an example. It 
  shows how the data structure <em>could</em> map to a logical query. In reality
  you aren't going to be running a SQL query from your browser client, so 
  don't invest too many brain cycles into that example output.</p>

<p>Enjoy!</p>
