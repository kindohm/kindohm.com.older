---
title: Live Coding With Tidal
layout: post
tags: audio music tidal code haskell
---

So I made a thing with Tidal:

<iframe width="420" height="315" src="//www.youtube.com/embed/S1DZ5WTOG4k" frameborder="0">&nbsp;</iframe>

My friend <a  href="http://thoughtstoblog.com">Jake</a> recently shared the article <a href="http://www.vice.com/read/algorave-is-the-future-of-dance-music-if-youre-an-html-coder">'Algorave' Is the Future of Dance Music (if you're a nerd)</a>. I've been interested in making music and sounds with code for years, and this is a new area I've never heard of before.

I'm giving <a href="http://yaxu.org/Tidal">Tidal</a> a try first. Tidal, written by <a href="http://yaxu.org/">Alex McLean</a>, is a Domain-Specific Language written in Haskell, and it is used to create music with code. You don't need to know Haskell to use it (I certainly don't), but you can use some of its features in Tidal. 

Here's an example of a 4/4/ kick-snare pattern:

    sound "[bd sn bd sn]"

And here's the same beat with a bass melody added:

    sound "[bd sn bd sn, jvbass jvbass jvbass jbass]" 
        |+| speed "[1.0 1.3 1.0 1.5]"

Tidal is not really about creating linear patterns and melodies, though. It's more about using functions to manipulate and change patterns. Like using a sine wave to pan the sound right and left:

    sound "[bd sn bd sn]"
        |+| pan (slow 2 sinewave1)

Or to change the pattern every few measures:

    every 4 (0.25 &lt;~) $ sound "[bd sn bd sn]"

You can find more details about how patterns can be transformed at <a href="http://yaxu.org/tidal/">yaxu.org/tidal/</a>.

Tidal is just a language. Sounds are actually produced using a software synthesizer called <a href="https://github.com/yaxu/Dirt">Dirt</a>, which is written by the same author as Tidal. Dirt ships with a large array of wav file samples to play with. 

All of this runs on Debian-flavored Linux easily. Emacs hosts Tidal as a musical-developer environment. Emacs is required at this point - until another Tidal host is written (e.g. Vim, etc). The Tidal docs do a decent job of explaining how to get this all up and running.

I'm really excited to play around more with Tidal and other code-based musical tools. Another one I'm interested in is Overtone - a Clojure library for synthesis. I got it up and running but haven't really played with it yet. 

Being a C# programmer, I'm also inspired to try and create something similar to Tidal in C#. 

Happy noise-making!

