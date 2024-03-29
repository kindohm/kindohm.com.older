---
layout: post
title: "Click-n-Drag Functionality Improved"
---

<p>In my last post about <a href="http://kindohm.com/archive/2008/08/25/connectable-lines-and-shapes-in-silverlight.aspx">connectable nodes in Silverlight</a>, my code had an annoying problem where if you moved the mouse too quickly the selected shape would fall behind and stop moving.  I corrected this problem by moving the move logic to the containing Page rather than keep it inside the shape user control.  The containing Page is aware of mouse movement outside of each shape, and therefore can control shape movement when the mouse speeds ahead outside of a shape while dragging it around.  </p>
<p>New code and example app are available: </p>
<p>Source: <a target="_blank" href="http://drop.io/kindohm/asset/nodeconnect2" title="nodeconnect2">nodeconnect2.zip</a> </p>
<p>Example App: <a href="http://www.kindohm.com/sl/nodeconnect/testpage.html">http://www.kindohm.com/sl/nodeconnect/testpage.html</a></p>
<div id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:dd74bc19-58c5-4777-a052-2e8cf592692c" class="tags">Technorati Tags: <a rel="tag" target="_blank" href="http://technorati.com/tags/code">code</a> <a rel="tag" target="_blank" href="http://technorati.com/tags/silverlight">silverlight</a> <a rel="tag" target="_blank" href="http://technorati.com/tags/programming">programming</a></div> 
