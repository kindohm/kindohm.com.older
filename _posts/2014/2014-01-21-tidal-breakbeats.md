---
layout: post
title: Mastering Breakbeats with Tidal
---

<p>I love slicing breaks. There's nothing else like scrambling up a drum beat.</p>

<p><a href="//yaxu.org/tidal">Tidal</a> and real-time coding puts a fresh spin
	on slicing up breaks. I'm going to share some of my super-secret techniques
	(a.k.a. accidental discoveries) on how I slice up breaks in Tidal.</p>

<p><strong class="text-danger">Caution</strong>: this write-up assumes you know a
	<em>little</em> bit about wave files, samples, time-stretching, etc. </p>

<p>The video below is a real-time explanation and demonstration of everything,
	but continue reading if you want more of a textual explanation.</p>

<pre><code>;; wow. such video. very sound.</code></pre>

<p>Mentally, I break down slicing into a few steps:</p>

<ol>
	<li>Find a good break</li>
	<li>Decide on a good tempo</li>
	<li>Striate, striate, striate!</li>
	<li>Shift beats</li>
	<li>Reversal</li>
	<li>Pitch shift</li>
	<li>Season all of the above to taste</li>
</ol>

<h3>Find a good break</h3>

<p>For those new to samples and breaks, start out with a break that is 
four or eight beats. Just a basic drum pattern. Make sure that the sample is
edited and cropped so that the tempo stays smooth and constant when the
sample is looped constantly.</p>

<p>For those experienced with breaks, you'll probably have curated a number of
	favorite breaks already. Any will do.</p>

<p>My overall advice with Tidal is to choose breaks that are <em>long enough</em>.
	Find a break that will be longer than a single Tidal pattern cycle. Your 
	mileage may vary depending on your tempo/bps.</p>

<p>As a general guideline, I like using breaks that:</p>

<ul>
	<li>Have some syncopation in their rhythm (e.g. they include some notes
		or accents on up-beats or on the 2nd or 4th notes of a 16th note
		pattern). This comes in handy when shifting beats around.</li>
	<li>Have some sustain in the drums and cymbals. Virtually all acoustic drum
		sets will have this. Sustain comes in handy when reversing.</li>
</ul>

<h3>Decide on a good tempo</h3>

<p>Ultimately, we will use Tidal's striation to stretch the sample to the tempo
	we need. However, a tempo closer to the break's original tempo will make the
	break sound truer to its original sound.</p>

<p>Tempos slower than the original will result in some grainy sound artifacts...
	which is completely fine. You will decide what sounds best for you. Otherwise,
	choose a tempo that is faster than your original break to make sure there are no
	gaps in your breaks slices. More on this in the next section...</p>

<h3>Striate, striate, striate!</h3>

<p><code>striate</code> is the super secret sauce in Tidal for slicing up breaks.
	First, you may want to read up on Alex's description of <code>striate</code>
	in this post (4th video down): 
	<a href="http://yaxu.org/demonstrating-tidal/">yaxu.org/demonstrating-tidal</a>.</p>

<p><code>striate</code> chops a sample into a number of segments that you
	specify. It then concatenates and plays those segments in order:</p>

<pre><code>d1 $ striate 128 ( sound "mybreak" )</code></pre>

<p>The code above will chop up the .wav file into 128 equally-sized sub-samples.
	and then play them at an evenly distributed time over the duration of the
	Tidal pattern cycle. This is what makes slicing breaks in Tidal
	<span style="text-decoration: line-through">possible</span> magical.
	<code>striate</code> stretches (or compresses) your sample to fit within
	a single Tidal cycle.</p>

<p>The larger the striation value (e.g. 128), the more granular you can get
	with manipulating the break. Changing the striation value will also change
	the sound of the break if it is stretched to match the Tidal cycle time. Keep
	in mind that very large striation values can also cause more latency and
	the Tidal/Dirt code may not be able to keep up with your sound device, depending
	on your hardware.</p>

<h3>Shift Beats</h3>

<p>At this point you should be playing a break that fits the tempo of your
	Tidal code. Great. Now lets do something interesting with it.</p>

<p>The go-to, easy transformation that you can apply is beat shifting:</p>

<pre><code>d1 $ every 4 (0.25 &lt;~) $ striate 128 ( sound "mybreak" )</code></pre>

<p>The above code will shift the pattern by 25% every fourth pattern. Pretty basic,
	but it adds a little variation in your break. Nice!</p>

<pre><code>d1 $ every 3 (0.25 &lt;~) $ every 4 (0.25 &lt;~) 
    $ striate 128 ( sound "mybreak" )</code></pre>

<p>Above, we've applied a <em>second</em> shift transformation of 25%. This one is
	every three beats. Naturally, you will now hear cycles of two shift transforms
	occurring at different times - sometimes arriving at the same time and
	resulting in a single transformation shift of 50%. Yes!</p>

<p>Of course you can get as crazy as you want with this technique:</p>

<pre><code>d1 $ every 5 (0.25 ~&gt;) $ every 7 (0.333 &lt;~) 
    $ every 3 (1.716 &lt;~) $ striate 256 ( sound "break")</code></pre>

<p>Not that the listener will be able to make much sense of that, but the point
	is that you can apply multiple beat shifts to create some classic, sliced-up
	break sounds.</p>


<h3>Reversal</h3>

<p>With <code>striate</code>, we can reverse the order in which the individual
	sample segments are played back:</p>

<pre><code>d1 $ rev $ striate 64 ( sound "mahbreak" )</code></pre>

<p>The higher striation value, the more resolute the reversal is. Compare
	how these two sound:</p>

<pre><code>d1 $ rev $ striate 4 ( sound "mahbreak" )
d1 $ rev $ striate 128 ( sound "mahbreak" )</code></pre>

<p>Of course, we can apply a reversal only at certain times, using <code>every</code>:</p>

<pre><code>d1 $ every 4 rev $ striate 64 ( sound "mahbreak" )</code></pre>

<p>Combine with beat shifting, then dance a little:</p>

<pre><code>d1 $ every 4 rev $ every 3 (0.25 &lt;~) 
	$ striate 64 ( sound "mahbreak" )</code></pre>

<h3>Pitch Shift</h3>

<p>You can apply the <code>speed</code> parameter to the result of 
	<code>striate</code>:</p>

<pre><code>d1 $ striate 128 (sound "verybreak" )
	|+| speed "1.2"</code></pre>

<p>This will increase the speed of each striated sub-sample by 20%, resulting
	in an apparent increase in the pitch of the entire break. Very cool.</p>


<pre><code>d1 $ striate 128 (sound "verybreak" )
	|+| speed "[1.2 0.8]/2"</code></pre>

<p>Above, we apply two speeds (one at 120%, then another at 80%) over twice
	the time, resulting in the break being played at a higher pitch for one 
	cycle, then a lower pitch for a second cycle.</p>

<p>Using this lesser-known Tidal technique, you may also choose to apply 
	speed changes using <code>every</code>:</p>

<pre><code>d1 $ every 4 (|+| speed "0.5") $ striate 128 (sound "brake")</code></pre>

<h3>Season to Taste</h3>

<p>Experiment. I've shown beat shifting, <code>every</code>, <code>rev</code>,
	and <code>speed</code> here, but there is more to Tidal than just those
	parameters and transforms. Feel free to experiment with others, like
	<code>jux</code>. There are quite a few possibilities.</p>

<p>Happy slicing.</p>

