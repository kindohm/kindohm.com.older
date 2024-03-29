---
layout: post
title: "Simple Sprites: HTML5 Canvas Paths vs Images"
---

<p>Demo for this post: <a href="/apps/sprites-vs-paths/">kindohm.com/apps/sprites-vs-paths/</a></p>

<p>Full source for this post and demo are on this branch:
<a href="https://github.com/kindohm/space-janitor/tree/sprite-vs-paths">github.com/kindohm/space-janitor/tree/sprite-vs-paths</a></p>

<h3>Hand-drawn PNG: bad. Mathematically calculated path: good</h3>

<p>In developing my <a href="http://kindohm.com/blog/2013/05/24/coquette-javascript-game-spacehsips.html">JavaScript 
	Asteroids clone</a>, I noticed that drawing my "ship" on the screen looked
	better when using a raw HTML5 drawing context path rather than a pre-built PNG
	image:</p>

<p><img src="/hodsmedia/png-vs-path.png" alt="path vs png" /></p>

<p>The ship on the left is drawn with a PNG image. The ship on the right
	is drawn using an HTML5 drawing context path, which is drawn each frame.</p>

<p>Personally, I think the ship on the right (drawn frame-by-frame with a path)
	looks much better. Here is a zoomed-in view:</p>

<p><img src="/hodsmedia/png-vs-path-zoomed.png" alt="path vs png zoomed"/></p>

<p>This is quite subjective, but again I think the ship on the right looks
	much crisper and its lines are brighter. Plus, its geometry is 
	precisely calculated and drawn on the fly. The PNG ship on the left 
	was created using my amateur image-editing skills. It is much less
	precise and not nearly as crisp.</p>

<p>Unfortunately, drawing a ship path each frame does not perform as
	well as drawing an image. This post agrees: 
	<a href="http://simonsarris.com/blog/427-increasing-performance-by-caching-paths-on-canvas">simonsarris.com/blog/427-increasing-performance-by-caching-paths-on-canvas</a>. However, in that post, Simon
	also suggests that we can cache a drawn path on <em>another</em>
	canvas, and then draw that cached canvas on our real canvas.</p>

<p>So, let's try this!</p>

<h3>Set Up</h3>

<p>I added a <code>getSprite()</code> function to my player object that
	will draw a ship path and return a canvas that contains it:</p>

<pre><code>getSprite: function(){

  var canvas = document.createElement('canvas');
  canvas.width = 20;
  canvas.height = 30;
  
  var context = canvas.getContext('2d');
  context.beginPath();
  context.moveTo(0,0);
  context.lineTo(this.halfSize.x,this.size.y);
  context.lineTo(this.size.x, 0);
  context.lineTo(this.halfSize.x,7);
  context.lineTo(0, 0);
  context.closePath();
  context.strokeStyle = '#ccc';
  context.lineWidth = 1;
  context.stroke();

  return canvas;
}</code></pre>

<p>In my player's constructor, I call this function and keep a reference
	to the canvas, thus "caching" it:</p>

<pre><code>function Player(game, settings){

	/* ... */

	// image sprite
	this.sprite = new Image();
	this.sprite.onload = function(){
	  self.spriteReady = true;
	};
	this.sprite.src = 'sprites/player.png';

	// cached sprite
	this.inMemorySprite = this.getSprite();
}</code></pre>

<h3>Draw Three Ships</h3>

<p>Next, I want to draw <em>three</em> ships side-by-side 
	for comparison:</p>

<ol>
	<li>PNG image ship</li>
	<li>Frame-by-frame path</li>
	<li>Cached, in-memory canvas path</li>
</ol>

<p>I'll modify my draw method to now draw the three ships:</p>

<pre><code>draw: function(){

  var context = this.game.coquette.renderer.getCtx();

  // 1) draw a .png ship sprite
  context.save();
  context.translate(this.pos.x, this.pos.y);
  context.rotate(this.rAngle);
  
  context.drawImage(this.sprite, -this.halfSize.x, -this.halfSize.y,
    this.size.x, this.size.y);

  context.rotate(-this.Angle);
  context.translate(-(this.pos.x), -(this.pos.y));
  context.restore();      

  // 2) an on-the-fly path drawing
  var offset = 50;
  context.save();
  context.translate(this.pos.x + offset, this.pos.y);
  context.rotate(this.rAngle);

  context.beginPath();
  context.moveTo(-this.halfSize.x,-this.halfSize.y);
  context.lineTo(0,this.halfSize.y);
  context.lineTo(this.halfSize.x, -this.halfSize.y);
  context.lineTo(0,-7);
  context.lineTo(-this.halfSize.x, -this.halfSize.y);
  context.closePath();
  context.strokeStyle = '#ccc';
  context.lineWidth = 1;
  context.stroke();

  context.rotate(-this.Angle);
  context.translate(-(this.pos.x + offset), -(this.pos.y));
  context.restore();      

  // 3) draw the in-memory canvas sprite      
  var offset = 100;
  context.save();
  context.translate(this.pos.x + offset, this.pos.y);
  context.rotate(this.rAngle);

  context.drawImage(this.inMemorySprite, -this.halfSize.x, -this.halfSize.y,
    this.size.x, this.size.y);

  context.rotate(-this.Angle);
  context.translate(-(this.pos.x + offset), -(this.pos.y));
  context.restore();      
}</code></pre>

<p>The ships look like this:</p>

<p><img src="/hodsmedia/three-ships.png" alt="three ships" /></p>

<p>Left: PNG image. Center: frame-by-frame path. Right: cached
	path on another canvas.</p>

<p>I still think the frame-by-frame path looks the best, however
	I think the cached path image looks a little better than the
	PNG image. I imagine this has more to do with my lack of 
	image-editing skill than the drawing code in web browsers.</p>

<p>Here they are zoomed-in:</p>

<p><img src="/hodsmedia/three-ships-zoomed.png" alt="three ships zoomed"/></p>

<p>I think by caching a path drawn using exact math (rather than an image created
	with a shaky hand) we've gained some clarity and precision.</p>

<h3>What's Next</h3>

<p>I plan on adding other "sprites" to this game (bullets and simple, rectangular 
	asteroids), so I'll plan on using the cached canvas technique for them. I
	like the idea of being able to draw mathematically-precise sprites with 
	HTML5 drawing context paths over PNG images. </p>
