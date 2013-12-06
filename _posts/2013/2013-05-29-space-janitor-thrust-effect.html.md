---
layout: post
title: "Space Janitor - adding a thrust effect"
---

<p><a href="https://github.com/kindohm/space-janitor/tree/thrust-effect">Source
  code for this post/feature</a></p>

<p><a href="http://kindohm.github.io/space-janitor/index.html">Full game demo</a></p>

<p>The next feature I wanted to complete for <em>Space Janitor</em> was a visual
  effect when the player's ship accelerates. Here's a concept:</p>

<p><img src="/hodsmedia/space-janitor-thrust-effect-idea.jpg" alt="thrust effect idea"/></p>

<h3>The Design</h3>

<p>My approach for implementing this effect:</p>

<ol>
  
<li>When the player starts to thrust, add a small path or shape behind the ship.</li>
  
<li>On each tick, grow the path's or shape's size by a given amount.</li>
  
<li>After a given number of ticks go by, while the player is still thrusting, add another 
    small path or shape. Keep adding shapes as the player accelerates.</li>
  
<li>Decrease the path or shape's opacity with each tick. Opacity could be a function of
    the current tick and the total number of ticks that a path lives.</li>
  
<li>After a given number of ticks go by for a single path/shape, destroy it.</li>
</ol>

<p>I also decided to vary the design from the concept and render rectangles instead of 
circles for the paths behind the ship. I felt that this resulted in a more classic
game feel, consistent with the rest of the game. The circles looked too out of place
to me.</p>

<h3>Implementation</h3>

<p>I created <code>ThrustEffect</code> and <code>ThrustBubble</code> objects to 
  make this all work. The <code>Player</code> object uses the <code>ThrustEffect</code>
  in its <code>update()</code> function:</p>

<pre><code>Player.prototype = {

  thrustEffect: null,

  update: function(){
    /* ... */

    this.thrustEffect.update(this);
  }

};</code></pre>

<p>Inside <code>ThrustEffect</code> is where all the magic happens. I felt it was
  important to keep all of the effect logic separate from the player object.</p>

<p>Implementing my approach from above was pretty simple. Here are a few
  functions from <code>ThrustEffect</code>:</p>

<pre><code>update: function(player){

  // decrement ticks and add another "bubble" if it is time
  this.thrustEffectTicksLeft = Math.max(0, this.thrustEffectTicksLeft - 1);
  if (this.thrustEffectTicksLeft === 0 &amp;&amp; player.thrusting){
    var vector = this.game.maths.angleToVector(player.angle + 180);
    var effectPos = {
      x: player.pos.x + vector.x * player.halfSize.x,
      y: player.pos.y + vector.y * player.halfSize.y
    };
    var vel = {
      x: vector.x * this.game.settings.THRUST_EFFECT_VEL,
      y: vector.y * this.game.settings.THRUST_EFFECT_VEL
    };
    this.add(effectPos, vel);
    this.thrustEffectTicksLeft = this.game.settings.THRUST_EFFECT_TICKS;
  }

  // remove old effects, update current effects
  for(var i = this.effects.length - 1; i >= 0; i--){
    if (this.effects[i].ticksLeft === 0){
      this.effects.splice(i, 1);
    } else {
      this.effects[i].update();
    }
  }
},

draw: function(context){
  for(var i = 0; i &lt; this.effects.length; i++){
    this.effects[i].draw(context);
  }
}</code></pre>

<p>As you can see, <code>update()</code> does exactly what I set out to do in my
  initial approach: add a "bubble" when thrusting after every few ticks, and remove
  bubbles after they have expired. The only unique thing I added was a small
  velocity to the bubbles in the opposite direction of the ship. I thought that
  this helped space out the bubbles from the ship and made the thrust effect
  look more authentic.</p>

<h3>Drawing the Effect "Bubbles"</h3>

<p>The <code>ThrustBubble</code> object does all the drawing:</p>

<pre><code>colorBase: '204,204,204',

draw: function(context){

  var side = this.radius * 2;
  context.beginPath();
  context.rect(this.pos.x - this.radius, this.pos.y - this.radius,
    side, side);
  context.closePath();
  context.lineWidth = 1;
  var ratio = this.ticksLeft / this.totalTicks;
  context.strokeStyle = 'rgba(' + this.colorBase + ',' + ratio.toString() + ')';
  context.stroke();
}</code></pre>

<p>If you read my earlier post on <a href="/blog/2013/05/26/canvas-game-images-vs-paths.html">sprite caching</a>,
  you may notice that I'm not using any sprite caching here. I'm drawing the 
  effect in real time. I chose to do this because after attempting to draw 
  cached canvases with a pre-drawn "bubble" at varying sizes it looked awful.
  The other cached canvases I've been using thus far have been fixed sizes. This
  thrust effect changes the size of each bubble on each tick, and it renders poorly.</p>

<p>So, I've chosen to draw paths on each tick instead. The game will take a 
  performance hit for this, but I'm ok with that since this is the only real
  dynamic visual aspect of the game I'm planning on creating.</p>

<p>The end result:</p>

<p><img src="/hodsmedia/space-janitor-thrust-effect.png" alt="thrust effect"/></p>




