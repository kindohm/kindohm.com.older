---
layout: post
title: "Connectable lines and shapes in Silverlight"
---

<p>This is strictly just a UI concept at this point, but it's a very simple Silverlight implementation of drawing some shapes on the screen, connecting them with lines, then clicking and dragging the shapes around while maintaining the line connections.  </p>

<p><span style="color: red;">UPDATE 1/6/2009:</span> The demo page and source code have been updated and compiled for Silverlight 2.</p>

<p>Source Code: <a href="http://www.kindohm.com/files/nodeconnect2.zip">nodeconnect2.zip [12 kb]</a> </p>

<p>Example App: <a href="http://kindohm.com/sl/NodeConnect/TestPage.html">http://kindohm.com/sl/NodeConnect/TestPage.html</a></p>

<p><a href="http://www.kindohm.com/localimages/posts/ConnectablelinesandshapesinSilverlight_14B2C/movablenodes.png"><img height="484" border="0" width="437" src="http://www.kindohm.com/localimages/posts/ConnectablelinesandshapesinSilverlight_14B2C/movablenodes_thumb.png" alt="movablenodes" style="border-width: 0px;" /></a> </p>

<p>It's hard to explain the highlights with only small code snippets (I don't want to paste the entire source code into this post), but here is a shot at some of the key features...</p>

<p>The nodes/shapes were implemented as Silverlight user controls. Clicking and dragging was simply handled on the user control's MouseMove event in combination with up/down state of the left mouse button.  The MoveNode method does the dirty work:</p>

<pre><code>Point lastPoint;<br /><br />//declared as a XAML element...<br />TranslateTransform translation;<br /><br />void MoveNode(Point currentPosition)<br />{<br />  double deltaX = currentPosition.X - this.lastPoint.X;<br />  double deltaY = currentPosition.Y - this.lastPoint.Y;<br />  this.translation.X = this.translation.X + deltaX;<br />  this.translation.Y = this.translation.Y + deltaY;  <br />  this.lastPoint = position;<br />}</code></pre>
<p>As for drawing and re-drawing the lines, I created a Connection class that is just a data structure that describes a Line and where it starts from. To update the lines when a shape is moved, I just iterate through a Dictionary that stores a Connection for each control that the moved control is connected to. The Dictionary is maintained for all controls so that each control is aware of everything it is connected to:</p>

<pre><code>public class NodeControl<br />{<br /><br />...<br /><br />  void MoveConnections()<br />  {<br />    foreach (NodeControl key in this.connections.Keys)<br />    {<br />      Connection connection = this.connections[key];<br />      Point newPosition = <br />        NodeControl.GetCoordinatesForControl(this);<br />      Line line = connection.Line;<br />  <br />      //IsPrimary determines if this <br />      //control holds the start or end<br />      //of the line<br />      if (connection.IsPrimary)<br />      {<br />        line.X1 = newPosition.X;<br />        line.Y1 = newPosition.Y;<br />      }<br />      else<br />      {<br />        line.X2 = newPosition.X;<br />        line.Y2 = newPosition.Y;<br />      }                <br />    }<br />  }<br /><br />...<br /><br />}<br /></code></pre>
<p>Oh the data visualization possibilities...</p>

<p>Enjoy.</p>

<div id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:b3a1f673-6ff8-4d2e-a746-6088481dae99" class="tags">Technorati Tags: <a rel="tag" target="_blank" href="http://technorati.com/tags/silverlight">silverlight</a> <a rel="tag" target="_blank" href="http://technorati.com/tags/code">code</a></div> 
