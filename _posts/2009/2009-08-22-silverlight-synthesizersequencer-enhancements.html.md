---
layout: post
title: "Silverlight Synthesizer/Sequencer Enhancements"
---

<p>I've been working on some new ideas for another Silverlight-based audio synthesizer and sequencer.  I've been able to achieve some key goals in my latest efforts:</p>
  
<ol>   
<li>Make the sequencer polyphonic (e.g. play more than one note at the same time) </li>    
<li>Account for multiple notes played at the same time to prevent clipping or distortion - and do this dynamically rather than pre-load or hard-code a fixed possible number of notes that can be played simultaneously. </li>    
<li>Add the ability to play and sequence wave file samples. </li> 
</ol>  
<p>#3 really makes this project fun.  My app isn't finished yet, but I wanted to give a taste of what it looks like and sounds like.  </p>
  
<p>Here you can download some sample audio of me using the app in real time setting up a drum beat and adding in some synth sounds.  It's pretty monotonous but it illustrates the capability of the sequencer: </p>
  
<p><a href="http://www.kindohm.com/files/ksynth2.demo.mp3" target="_blank">ksynth2.demo.mp3</a> [~7 MB]</p>
  
<p>The sequencer UI is a grid where you can place notes with the mouse.  You can change the length of each note by placing the mouse near the right end of each note, then clicking and dragging its right edge.</p>
  
<p><a href="http://www.kindohm.com/LocalImages/posts/SilverlightSynthesizerSequencerEnhanceme_26C/ksynth2.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="ksynth2" border="0" alt="ksynth2" src="http://www.kindohm.com/LocalImages/posts/SilverlightSynthesizerSequencerEnhanceme_26C/ksynth2_thumb.png" width="504" height="203" /></a> </p>
  
<p>You can see that the drum parts (kick, snare, high hat, etc) are included in the sequencer along with the pitches of the synthesizer:</p>
  
<p><a href="http://www.kindohm.com/LocalImages/posts/SilverlightSynthesizerSequencerEnhanceme_26C/ksynth2zoom.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="ksynth2-zoom" border="0" alt="ksynth2-zoom" src="http://www.kindohm.com/LocalImages/posts/SilverlightSynthesizerSequencerEnhanceme_26C/ksynth2zoom_thumb.png" width="244" height="241" /></a> </p>
  
<p>Hopefully I'll have a release out soon.</p>
  
<div id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:b4a52512-120c-45ea-be04-773b18cda079" class="tags">Tags: <a href="http://technorati.com/tags/silverlight" rel="tag">silverlight</a>,<a href="http://technorati.com/tags/synthesizer" rel="tag">synthesizer</a>,<a href="http://technorati.com/tags/audio" rel="tag">audio</a>,<a href="http://technorati.com/tags/code" rel="tag">code</a></div> 
