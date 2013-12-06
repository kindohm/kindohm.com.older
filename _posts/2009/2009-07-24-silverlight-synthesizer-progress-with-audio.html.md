---
layout: post
title: "Silverlight Synthesizer Progress (with audio!)"
---

<p><strong>Sample audio</strong>: <a href="http://www.kindohm.com/sl/ksynth/ksynth.demo.mp3">ksynth.demo.mp3</a> [8 MB - 5:27]</p>
<p>These days I've been more about producing and futzing with fun stuff than blogging and explaining.  Well, maybe you can see (or hear) what I've been up to now...</p>
<p>I've been chipping away at a multi-voice audio synthesizer in Silverlight 3 and C# for the past few days.  I'm at a point where I just couldn't wait to share what the heck this thing sounds like any more.  Here are a few points of interest:</p>
<ul>
<li>There are three separate wave oscillators generating the sound </li>
<li>Each oscillator can generate sine, saw, square, triangle, or a "custom" wave form </li>
<li>Each oscillator can be panned right or left, and can be phase-shifted from 0 to 360 degrees </li>
<li>A simple sequencer is used to control 16 timed steps. Each step has 8 possible notes.  </li>
<li>Each possible note can be set up to play any of the 12 half musical steps, with a 6-octave range </li>
<li>There is a dynamic envelope that controls each note's attack, sustain, and decay </li>
<li>Tempo can be altered. </li>
<li>Added a pitch bender to boot </li>
<li>Much help received from the example work of Charles Petzold's <a href="http://www.charlespetzold.com/blog/2009/07/Simple-Electronic-Music-Sequencer-for-Silverlight.html" target="_blank">Simple Silverlight 3 Sequencer</a> and Pete Brown's <a href="http://community.irritatedvowel.com/blogs/pete_browns_blog/archive/2009/07/19/Silverlight-Synthesizer-Source-Code-_1320_-MediaStreamSource-Raw-Sound.aspx" target="_blank">Silverlight Synthesizer</a>. </li>
</ul>
<p>The sample mp3 demonstrates most of the synth's capabilities - starting from a simple single sine voice to full-blown madness with everything turned to 11.  Around 2:35 or so, I add in a custom wave form that I authored using a wave form editing tool I'm working on.  </p>
<p>Here is a screen shot:</p>
<p><a href="http://www.kindohm.com/LocalImages/posts/SilverlightSynthesizerProgresswithaudio_13843/ksynth.beta.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="ksynth.beta" border="0" alt="ksynth.beta" src="http://www.kindohm.com/LocalImages/posts/SilverlightSynthesizerProgresswithaudio_13843/ksynth.beta_thumb.png" width="644" height="445" /></a> </p>
<p>And here is a glimpse of the waveform editor:</p>
<p><a href="http://www.kindohm.com/LocalImages/posts/SilverlightSynthesizerProgresswithaudio_13843/editor.png"><img style="border-bottom: 0px; border-left: 0px; display: inline; border-top: 0px; border-right: 0px" title="editor" border="0" alt="editor" src="http://www.kindohm.com/LocalImages/posts/SilverlightSynthesizerProgresswithaudio_13843/editor_thumb.png" width="623" height="342" /></a> </p>
<p>I just saw that Charles Petzold blogged about a multi-voice synthesizer approach.  I'm not showing source code here, but I'm glad to see that he and I agree on a similar approach of using a generic C# List of child sample providers summed up (and divided) by a parent provider.  </p>
<p>Next I'll be working on voice de-tuning and a delay effect.  I also need to polish up the custom wave editor feature and add the ability to save synth settings for future recall.</p>
<p>Until next time...  keep those earplugs in.</p>
<div id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:5ec00f25-ce53-4b62-a068-0058b44973e4" class="tags">Tags: <a href="http://technorati.com/tags/silverlight" rel="tag">silverlight</a>,<a href="http://technorati.com/tags/audio" rel="tag">audio</a>,<a href="http://technorati.com/tags/synthesizer" rel="tag">synthesizer</a></div>

