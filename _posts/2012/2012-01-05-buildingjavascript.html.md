---
layout: post
title: "Compiling Minified  Multi-File Javascript"
---

<style> pre{ overflow-x: hidden; overflow-y: hidden; } </style>
<p>I've been doing a <em>lot</em> of Javascript development lately. For  most of 2011 I focused almost exclusively on Kinect device development in WPF, and I really missed <strong>the web</strong> after all that time on the desktop. Now that I'm getting back in to web development, I've been really curious about Javascript as a language.</p>
<p>While doing all this js development I quickly ran in to the issue of dealing with multiple js source files and combining them and minify-ing them  into a single file. Most of what I've leanred about this process came from the  <a href="https://github.com/jussi-kalliokoski/audiolib.js">audiolib.js project</a>.  Jussi Kalliokoski has put together some great structure on that project.  However, I've found very few posts on this topic so I thought I'd write  about it.</p>
<p>As we all know, a single minified .js file is  optimal for the web. I've been working with about five bulky .js files and needed to shrink them down. Combining multiple source js files into a single file is trivial  with <code>cat</code>:</p>

<pre><code>cat src/*.js &gt; final.js</code></pre>

<p>For my first dive into js minification, I've been using  <a href="https://github.com/mishoo/UglifyJS">UglifyJS</a>. Most folks out there seem to be using the YUI or Closure compressors, but I thought I'd give UglifyJS a shot. UglifyJS is actually <em>built</em> in  Javascript and runs on node.js.  <a href="http://badassjs.com/post/971960912/uglifyjs-a-fast-new-javascript-compressor-for-node-js">This post on badassjs.com</a> suggests that UglifyJS is the fastest and <strong>best</strong> js minifier out there, but I'll leave the performance and language wonks out there to be the judge of that.</p>
<p>Install UglifyJS:</p>

<pre><code>npm install uglify-js</code></pre>

<p>Running UgilifyJS is cake:</p>

<pre><code>uglifyjs final.js &gt; final.min.js</code></pre>

<p>And poof, we've now combined and minified multiple js files into a single file!</p>
<h3>Module Wrappers</h3>
<p>Wait, there's a little more to the story. In my js development I've been working on a re-usable, extendable <a href="http://www.adequatelygood.com/2010/3/JavaScript-Module-Pattern-In-Depth">Javascript module</a>. All of the objects in my js source files needed to be wrapped inside of a module. With a single file this is obviously easy. But how do you do this with multiple js source files?</p>
<pre><code>var LLAMAMODULE = (function() {
    var myLlama = {};

    // content of multiple source files go here
    // uhhhh, how do I do this?

    return myLlama;
}());</code></pre>
<p>The answer is to create starting and ending wrapper .js files and include them in the right order when using <code>cat</code>:</p>
<pre><code>// wrapper-start.js

var LLAMAMODULE = (function(){
    var myLlama = {};
</code></pre>
<pre><code>// wrapper-end.js

    return myLlama;
}());</code></pre>
<p>When you use <code>cat</code>, just make sure to place the wrappers appropriately:</p>
<pre><code>cat src/wrapper-start.js src/*.js src/wrapper-end.js > final.js</code></pre>
<p>BAM, now you've got a single file with all your awesome stuff wrapped in to a module.</p>
<h3>Putting it together with Make and a test page</h3>
<p>Now how do you test your js code in a browser? I created a makefile so that I can compile my Javascript on demand:</p>
<pre><code>all: lib/final.min.js

lib/final.js: src/wrap-start.js src/*.js src/wrap-end.js
        mkdir lib/ -p
        cat $^ &gt; $@

%.min.js: %.js
        uglifyjs $^ &gt; $@</code></pre>
<p>My js code now resides inside <code>lib/</code>, and I can reference it from an HTML page (for testing, or for your real pages):</p>
<pre><code>
&lt;html&lt;&gt;
	&lt;head&lt;&gt;
	&lt;script src="../lib/final.js"&lt;&gt;&lt;/script&lt;&gt;
	&lt;/head&lt;&gt;
...
&lt;/html&lt;&gt;</code></pre>
<p>It should be obvious, but don't reference the minified js when doing tests or debugging. You'll be spending a lot of time banging your head against the wall trying to find those rows and columns and reconciling minified var names!</p>
<p>In summary:</p>
<ul>
<li>Use <code>cat</code> to combine multiple js files together</li>
<li>Use wrapper .js files for modules</li>
<li>Use UglifyJS (or your favorite minifier) to minify your js</li>
<li>Use a makefile (or another flavor of scripting)  for concatenation and minification</li>
<li>Don't reference the minified js when testing or debugging</li>
</ul>
<p>Happy minify-ing!</p>

