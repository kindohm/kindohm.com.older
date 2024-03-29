---
layout: post
title: "Space Janitor: shooting and bullets in a JavaScript game"
---

<p>Before I dig in: I have named my JavaScript game <strong>Space Janitor</strong>.
	Why Space Janitor?</p>

<blockquote><em>"After centuries of abuse and neglect, space has become littered
	with debris from failed space missions, canisters of unwanted, spent nuclear fuel,
	and rains of small planetary bodies that threaten earth. There is one astronaut
	who is up to the challenge of clearing this debris and cleaning the realm of space:
	The Space Janitor"</em></blockquote>

<p>...but I digress.</p>

<h3>Shooting Stuff</h3>

<p><strong>Try the demo</strong>: 
	<a href="/apps/space-janitor-shooting/">kindohm.com/apps/space-janitor-shooting/</a></p>

<p>Code for this post: 
	<a href="https://github.com/kindohm/space-janitor/tree/shooting">github.com/kindohm/space-janitor/tree/shooting</a></p>

<p><img src="/hodsmedia/space-janitor-shooting.png" alt="shooting"/></p>

<p>Space Janitor is an Asteroids clone, so we will be shooting asteroids or
	other space junk and hopefully blowing it all to smithereens. Thus, the next
	big feature of the game is to introduce the ability to shoot. We'll allow 
	the user to do this with the space bar. In Coquette, we can easily handle
	space bar input in our <code>handleKeyboard()</code> function:</p>

<pre><code>handleKeyboard: function(){

  /* ... */

  if(this.game.coquette.inputter.state(this.game.coquette.inputter.SPACE)) {
    this.shoot();
  }
}</code></pre>

<p>The next challenge is what to do in the <code>shoot()</code> function...</p>

<h3>The shoot() function</h3>

<p>We have four things to do in the <code>shoot()</code> function:</p>

<ol>
	<li>Get the directional vector of the shot</li>
	<li>Calculate the origin position of the bullet, relative to the ship</li>
	<li>Calculate the velocity vector of the bullet</li>
	<li>Create the Bullet as a Coquette game entity</li>
</ol>

<h4>Directional Vector of the Shot</h4>
<p>Using the Maths.js's <code>angleToVector</code> function (found in the
	Coquette advanced demo), we can calculate the directional vector of the
	shot since we are already tracking the ship's angle from 
	<a href="http://kindohm.com/blog/2013/05/24/coquette-javascript-game-spacehsips.html">the
		previous post</a>:</p>

<pre><code>// get ship's direction vector
var vector = this.game.maths.angleToVector(this.angle);</code></pre>

<h4>Origin Position of the Bullet</h4>
<p>I want the bullet to start itself right at the nose tip of the player's
	ship. Since we know the directional vector of the ship, and we know the
	scale/length of the ship from its center, the origin is the directional vector
	multiplied times half the ship's length:</p>

<p><img src="/hodsmedia/bullet-origin.png" alt="bullet origin"/></p>

<pre><code>// calculate bullet origin 
// position relative to ship's center
var bulletPos = {
  x: vector.x * this.halfSize.x,
  y: vector.y * this.halfSize.y
};</code></pre>

<h4>Velocity Vector</h4>

<p>The velocity vector is just the directional vector times an arbitrary
	scalar amount that we can set. In this game, a scalar value of about 35
	feels about right. I've put this scalar value in settings.js so that it 
	can be changed in the same place as other settings:</p>

<pre><code>// calculate bullet velocity vector
var bulletVel = {
  x: vector.x * this.game.settings.BULLET_VELOCITY,
  y: vector.y * this.game.settings.BULLET_VELOCITY
};</code></pre>

<h4>Create the Bullet Game Entity</h4>

<p>Last but not least, we put the bullet into the game engine:</p>

<pre><code>// create bullet entity
this.game.coquette.entities.create(Bullet, 
  {
    pos: {
    	x: this.pos.x + bulletPos.x, 
    	y: this.pos.y + bulletPos.y
    }, 
    vel: bulletVel
  });</code></pre>

<p>The <code>Bullet</code> entity is merely an object that tracks its position
	with a given velocity and draws itself:</p>

<pre><code>
Bullet.prototype = {

	update: function() {

	  this.pos.x += this.vel.x;
	  this.pos.y += this.vel.y;

	  // destory the bullet if it reaches the screen bounds
	  if (this.pos.y &lt;= 0 || this.pos.y &gt;= this.game.height ||
	    this.pos.x &lt;= 0 || this.pos.y &gt;= this.game.width){
	    this.game.coquette.entities.destroy(this);
	  }
	},

	draw: function(context) {
	  context.drawImage(this.sprite, this.pos.x - this.halfSize.x, 
	    this.pos.y - this.halfSize.y, this.size.x, this.size.y);
	}
};
</code></pre>

<h3>Controlling Shot Frequency</h3>

<p>With the above code, if the user holds down the space bar it will fire shots
	at an astonishing rate:</p>

<p><img src="/hodsmedia/space-janitor-infinite-shot-rate.png" alt="astonishing" /></p>

<p>While very cool, this isn't what we're going for in the game. We want to limit
	the rapid fire rate so that it doesn't feel too slow to the user, but is still
	slow enough that it will make shooting space junk enough of a challenge.</p>

<p>This can be done by adding a simple delay tick counter and making sure that
	the counter has run to zero before actually performing the shot:</p>

<pre><code>shoot: function(){

  if (this.shotTicksLeft === 0){

    // get ship's direction vector
    var vector = this.game.maths.angleToVector(this.angle);

    // calculate bullet origin position relative to ship's center
    var bulletPos = {
      x: vector.x * this.halfSize.x,
      y: vector.y * this.halfSize.y
    };

    // calculate bullet velocity vector
    var bulletVel = {
      x: vector.x * this.game.settings.BULLET_VELOCITY,
      y: vector.y * this.game.settings.BULLET_VELOCITY
    };

    // create bullet entity
    this.game.coquette.entities.create(Bullet, 
      {
        pos: {x:this.pos.x + bulletPos.x, y:this.pos.y + bulletPos.y}, 
        vel: bulletVel
      });

    this.shotTicksLeft = this.game.settings.BULLET_DELAY_TICKS;
  }
}</code></pre>

<p>The <code>BULLET_DELAY_TICKS</code> value is held in the game settings and
	can be adjusted to your liking.</p>

<p>One more thing though - we need to decrement the <code>shotTicksLeft</code>
	field in the player's <code>update()</code> function:</p>

<pre><code>this.shotTicksLeft = Math.max(0, this.shotTicksLeft - 1);</code></pre>

<h3>Drawing the Bullet Sprite</h3>

<p>The bullet sprite is just a trivial rectangle:</p>

<p><img src="/hodsmedia/space-janitor-bullet-sprite.png" alt="bullet sprite"/></p>

<p>However, since there may be a lot of bullets on the screen at one time I want
	to make sure that the bullet sprite is created only once, then re-used for each
	bullet entity instance. I also want to utilize the 
	<a href="http://kindohm.com/blog/2013/05/26/canvas-game-images-vs-paths.html">canvas 
		caching technique</a> I blogged about previously.</p>

<p>To accomplish this, I decided to create a <code>SpriteFactory</code> object
	and move all sprite creation and caching to it. The <code>SpriteFactory</code>
	has a method for each type of sprite in the game, which I don't like, but
	I plan on having only a handful of different types of sprites in the game
	so I can live with this.</p>

<p>The <code>SpriteFactory</code> is used like this:</p>

<pre><code>var factory = new SpriteFactory();
var sprite = factory.getBulletSprite();</code></pre>

<p>Since I only want one instance of the factory going around, I made it accessible
to entities as a field on the <code>Game</code> object:</p>

<pre><code>var Bullet = function(game, settings){
	this.game = game;
	this.sprite = game.spriteFactory.getBulletSprite();
};</code></pre>

<h4>Caching Sprites</h4>

<p>Ok, so now we can create sprites from a factory, but how are they cached?</p>

<p>First, I needed a hashtable and decided to use
	<a href="http://www.timdown.co.uk/jshashtable/">jshashtable</a> by 
	Tim Down.</p>

<p>The cache is pretty simple. I just recall a pre-made sprite from the hashtable
	if it already exists. If it doesn't exist, I create the sprite and store it
	in the hashtable. I use the functions that create the sprites as the keys:</p>

<pre><code>getBulletSprite: function(){

  if (!cache.containsKey(this.getBulletSprite)){
    var canvas = document.createElement('canvas');
    canvas.width = this.game.settings.BULLET_SIZE_X;
    canvas.height = this.game.settings.BULLET_SIZE_Y;

    var context = canvas.getContext('2d');
    context.fillStyle = '#ccc';
    context.fillRect(0,0,this.game.settings.BULLET_SIZE_X, this.game.settings.BULLET_SIZE_Y);
    cache.put(this.getBulletSprite, canvas);
  }

  return cache.get(this.getBulletSprite);

}</code></pre>

<p>Voilà! Now we can create just a single instance of a sprite canvas and re-use
	it for multiple game entity instances!</p>

<h4>Conclusion</h4>

<p>In this post I showed:</p>

<ul>
	<li>How the ship fires a shot when the user presses the space bar</li>
	<li>How to calculate the origin position and velocity vector of the bullet</li>
	<li>How to efficiently re-use a single cached sprite for multiple game objects</li>
</ul>

<p>Next up I will either dig in to game sounds or a visual effect for thrusting.</p>
