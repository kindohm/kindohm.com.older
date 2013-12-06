---
layout: post
title: "Released - kSynth &amp;ndash; a Silverlight Audio Synthesizer"
---

<p>For the past 1-2 weeks I've been working on an audio synthesizer in Silverlight 3.  The app is in enough of a stable state that I decided to throw it out into the world.</p>
<p>Try it out:  <a href="http://kindohm.com/ksynth">http://kindohm.com/ksynth</a></p>
<p>Source code: <a title="http://code.google.com/p/kindohm-ksynth/" href="http://code.google.com/p/kindohm-ksynth/">http://code.google.com/p/kindohm-ksynth/</a></p>
<p><a href="http://www.kindohm.com/LocalImages/posts/ReleasedkSynthaSilverlightAudioSynthesiz_ADB8/ksynth.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="ksynth" border="0" alt="ksynth" src="http://www.kindohm.com/LocalImages/posts/ReleasedkSynthaSilverlightAudioSynthesiz_ADB8/ksynth_thumb.png" width="404" height="338" /></a> </p>
<p>kSynth is a <em>synthesizer</em>. I've been caught off guard when people ask me where I got the audio for the app.  I have to explain that the app is <em>generating</em> the audio from scratch (thus, a synthesizer). The app generates raw sound waves from sine, saw, square, and triangle wave calculations.  It then mixes manipulates the generated waves through other controls.</p>
<p>The other half of kSynth is a sequencer.  Using the note grid you can arrange up to eight pitches over a maximum of 32 timed steps.  Each placed note will play a sound from the synthesizer.</p>
<p>The synthesizer itself is made up of three voices.  Each voice has five parameters that you can manipulate: </p>
<p><a href="http://www.kindohm.com/LocalImages/posts/ReleasedkSynthaSilverlightAudioSynthesiz_ADB8/voice.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="voice" border="0" alt="voice" src="http://www.kindohm.com/LocalImages/posts/ReleasedkSynthaSilverlightAudioSynthesiz_ADB8/voice_thumb.png" width="230" height="150" /></a> </p>
<ul>
<li>Wave form (sine, saw, square, triangle, or noise) </li>
<li>Level - a.k.a. volume </li>
<li>Pan - how far to the right or left each voice is played </li>
<li>Phase - an offset of how much the wave calculation is delayed </li>
<li>Detune - a slight variation of the voice's pitch </li>
</ul>
<p>These parameters are fairly common parameters used with most modern synthesizer hardware or software.  </p>
<p>The synthesizer also has a dynamic envelope that is used to control the shape of each note played from the synthesizer.  The dynamic envelope has four parameters:</p>
<p><a href="http://www.kindohm.com/LocalImages/posts/ReleasedkSynthaSilverlightAudioSynthesiz_ADB8/Envelope.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="Envelope" border="0" alt="Envelope" src="http://www.kindohm.com/LocalImages/posts/ReleasedkSynthaSilverlightAudioSynthesiz_ADB8/Envelope_thumb.png" width="230" height="147" /></a> </p>
<ul>
<li>Attack - how abruptly or smoothly the note "fades in" </li>
<li>Sustain - the length of the note </li>
<li>Decay - how abruptly or smoothly the note "fades out" </li>
<li>Pitch - bends the pitch of each note up or down </li>
</ul>
<p>The sequencer also has some additional controls:</p>
<p><a href="http://www.kindohm.com/LocalImages/posts/ReleasedkSynthaSilverlightAudioSynthesiz_ADB8/SequencerControls.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="SequencerControls" border="0" alt="SequencerControls" src="http://www.kindohm.com/LocalImages/posts/ReleasedkSynthaSilverlightAudioSynthesiz_ADB8/SequencerControls_thumb.png" width="230" height="148" /></a></p>
<p><a href="http://www.kindohm.com/LocalImages/posts/ReleasedkSynthaSilverlightAudioSynthesiz_ADB8/delay.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="delay" border="0" alt="delay" src="http://www.kindohm.com/LocalImages/posts/ReleasedkSynthaSilverlightAudioSynthesiz_ADB8/delay_thumb.png" width="232" height="73" /></a>  </p>
<ul>
<li>Level - the master volume </li>
<li>Tempo - speeds or slows up the playback of notes </li>
<li>Steps - the number of slots available in the sequencer grid </li>
<li>Delay - adds a delay or "echo" effect to the audio </li>
</ul>
<p>You can save the voice, envelope, and delay settings by using 16 preset banks:</p>
<p><a href="http://www.kindohm.com/LocalImages/posts/ReleasedkSynthaSilverlightAudioSynthesiz_ADB8/presets.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="presets" border="0" alt="presets" src="http://www.kindohm.com/LocalImages/posts/ReleasedkSynthaSilverlightAudioSynthesiz_ADB8/presets_thumb.png" width="504" height="54" /></a> </p>
<p>Click and hold down a preset bank to save the current state of the voices, envelope, and delay to that bank.  Recall a preset by clicking once on a bank.</p>
<p>What is the purpose of this application?  Easy: to have fun.  It's not a musical plugin that will work with any other audio software.  I suppose you could use it as an instrument - although it doesn't have a keyboard.  It's more of a sequencer than a true instrument.  </p>
<p />
<p />
<p />
<p />
<p>For me, I had a huge interest in developing an application like this because 1) I love working with synthesizers in electronic music and 2) I knew Silverlight 3 would have audio synthesis capabilities.  From the day Silverlight 3 was released (7/10/2009) I started trying to learn how to build this app.  </p>
<p>I can't finish writing this without referencing Charles Petzold's article on building a <a href="http://charlespetzold.com/blog/2009/07/Simple-Electronic-Music-Sequencer-for-Silverlight.html" target="_blank">simple sequencer in Silverlight 3</a>.  His example helped with the sequencer logic along with some of the base calculations used in the fundamental wave forms and attenuation (amplitude).  I also took some idea's from Pete Brown's <a href="http://community.irritatedvowel.com/blogs/pete_browns_blog/archive/2009/03/23/Creating-Sound-using-MediaStreamSource-in-Silverlight-3-Beta.aspx" target="_blank">Silverlight synthesizer</a>.</p>
<p>What <em>doesn't</em> the app have?  I really wanted to add a custom wave form editor where you could draw your own wave form and use it in the app.  However, this feature got scrapped after successfully proving it out.  It was just much more work from a UI perspective than I wanted to invest.</p>
<p>In addition to custom wave forms, there are a few other possible enhancements to this app that I may implement some day:</p>
<ul>
<li>Save audio output to wave file </li>
<li>Save sequencer state </li>
<li>Upload custom wave files to sequence with </li>
<li>Support for unlimited number of voices </li>
<li>EQ and high/low pass filters </li>
</ul>
<p>I hope to write a few blog posts soon detailing some of the technical aspects of this application.</p>
<p>Enjoy, and happy noisemaking!</p>
<div id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:42289213-b2fb-4380-beb5-0a392f3fd14a" class="tags">Tags: <a href="http://technorati.com/tags/silverlight" rel="tag">silverlight</a>,<a href="http://technorati.com/tags/code" rel="tag">code</a>,<a href="http://technorati.com/tags/audio" rel="tag">audio</a>,<a href="http://technorati.com/tags/synthesizer" rel="tag">synthesizer</a></div>

