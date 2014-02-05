---
layout: post
title: "The 50th Tidal Pattern"
---

<p>Today I produced the 50th pattern of my 
	<a href="http://365tidalpatterns.tumblr.com">365 Tidal Patterns project</a>:</p>

<iframe width="100%" height="166" scrolling="no" frameborder="no" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/133213075&amp;color=ff5500&amp;auto_play=false&amp;hide_related=false&amp;show_artwork=true"> </iframe>

<p>For those late to the show, since November 2013 I've been getting 
	acquainted with a live-coding environment called 
	<a href="http://yaxu.org/tidal">Tidal</a> - which is a Haskell-based
	DSL used to produce pattern-based sound/audio/noise/music.</p>

<p>My intention with this project was to create a pattern a day. 
	If I were to play by my own rules, I should be on about #37 today, not #50.
	I plan to keep going for a full year, try to create a pattern each day, and
	at minimum produce 365 patterns.</p>

<p>I've gotten to know Tidal pretty well over the past 37 days. Making
	a Tidal pattern each day, and trying to do it well, has forced me to understand
	Tidal better though obvious means such
	as reading documentation, referring to examples, and asking questions. More
	surprisingly, I've come to understand Tidal better through creative forces.</p>

<h3>Constraints Breed Creativity</h3> 

<p>Creating something every day with the same tool forces you to look at
	what you're doing in new ways. In my case, I want to create a unique sound
	that is different from the past 49, but I have to use the exact
	same toolbox as the day before.</p>

<p>Eventually you get to a point where you run out of toolbox features to exploit and 
	you have to start looking at your creation process differently. Using what
	you already know, you take a different approach. If you're
	lucky, you'll learn something new about the tool in the process, and the
	creative cycle repeats itself.</p>

<p>After this 365 Tidal Patterns project is done, I look forward to a new
	thing-a-day project because I've been pleasantly surprised at what I've
	learned with Tidal. Some ideas for the future (not all original):</p>

<ul>
	<li>Take a photograph</li>
	<li>Take a photograph of a specific subject (e.g. a backpack, a car, etc)</li>
	<li>Write one page in a journal</li>
	<li>Write a 50-word <a href="http://en.wikipedia.org/wiki/Minisaga">Minisaga</a></li>
	<li><a href="http://jenniferdewalt.com/index.html">Build a small web site</a></li>
	<li>Learn a guitar chord</li>
	<li>Sight read a piano etude</li>
	<li>Prepare a new food</li>
</ul>

<p>I've considered "do-a-thing-a-week" projects, like learn a new programming
	language, but personally I'm afraid that I wouldn't stay committed. A small
	thing a day is a good amount of scope. A bigger thing that takes multiple
	days...  I can see myself putting it off and not getting it done.</p>

<h3>What I've Learned About Tidal</h3>

<p>I've learned that Tidal resonates with me because it is a very
	rhythmic sound-creation tool; I am a drummer and I also love dense drill-n-base
	electronic music. It is also great for producing noise. 
	Tidal caters to my interests.</p>

<p>My favorite feature of Tidal is <code>striate</code>, which is used to 
	chop up an audio sample into pieces and then play back those pieces (sometimes
	in order, sometimes in a different order). <code>striate</code> opens up all
kinds of possibilities for time stretching, granulation, slicing, and transforming
an innocent little wav file into a sonic monster.</p>

<p>Maybe the most powerful feature of Tidal is its ability to create sound in
	terms of expressions rather than explicit patterns, named samples, or notes.
	Arguably, this is also the best part about live-coding environments in general.</p>

<p>Take the inner-most part of the 50th pattern, for example:</p>

<pre><code>sound ( pick &lt;$&gt; "uldrums*8" &lt;*&gt; density 3 (run 8))</code></pre>

<p>This lets me specify just a small set (e.g. a directory) of samples and tell 
Tidal to play them all in a linear sequence of eight. Then I use <code>density</code>
to speed up the rate at which it picks the next sample to play, without changing
the rhythm of the original pattern. It allows me to automate creation of a pattern
and not be verbose about it.</p>

<h3>The Next 328 Days</h3>

<p>My intention is to keep producing a pattern a day. I have a feeling that these
	first 37 days are going to feel easy compared to when I reach pattern #100!</p>


