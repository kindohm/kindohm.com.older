---
layout: post
title: "Another Simulation App..."
---

<p>Robot tanks trying to kill each other.  Well, at least that's what I'm aspiring toward :)</p>
  
<pre><code>git clone git://github.com/kindohm/kindohm-artillery.git</code></pre>
<p><a title="http://github.com/kindohm/kindohm-artillery/tree/master" href="http://github.com/kindohm/kindohm-artillery/tree/master">http://github.com/kindohm/kindohm-artillery/tree/master</a></p>
  
<p>Right now it's super stripped down and simple.  No real UI or interactivity yet.  Just some stationary guns that don't move and some output messages to the console.  The guns fire projectiles at each other in a parabolic arc.  Since the projectile path and firing solution are both predictable, I introduced some randomness so that the first gun to shoot doesn't automatically "win".  Basically my work so far was just to get the trajectories down.  This article was most helpful: <a title="http://en.wikipedia.org/wiki/Trajectory" href="http://en.wikipedia.org/wiki/Trajectory">http://en.wikipedia.org/wiki/Trajectory</a>.</p>
  
<p>Next on tap is to allow guns to adjust the angle they shoot at.  Right now they fire 45 degrees from the horizontal at all times.  In an open line of fire, I'd like the guns to be able to dial in a high-speed, low shot.  If there are obstacles in the way, they could dial in a slow, high-arc shot.  </p>
  
<p>Then there's uneven terrain, obstacles, and tank movement.  I can at least dream...</p>
  
<div class="tags" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:629d9960-2501-45df-8fb8-a2b315dfa76b">Technorati Tags: <a href="http://technorati.com/tags/code" target="_blank" rel="tag">code</a> <a href="http://technorati.com/tags/simulation" target="_blank" rel="tag">simulation</a> <a href="http://technorati.com/tags/ai" target="_blank" rel="tag">ai</a></div> 
