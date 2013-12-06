---
layout: post
title: "Fluent interface for Silverlight audio API"
---

<p>I've been working on a fluent interface API for producing synthesized audio in Silverlight.  <a href="http://www.solutionizing.net" target="_blank">Keith Dahlby</a> originally suggested this idea to me, and as I've been testing my own complex signal chains in code I decided it was time to implement the idea.  Setting up signal chains using a more conventional syntax was just getting too tedious.</p>


  
<p>I've been using Charles Petzold's <a href="http://www.charlespetzold.com/blog/2009/07/Simple-Electronic-Music-Sequencer-for-Silverlight.html" target="_blank">Simple Sequencer</a> as a base and have tweaked and modified the code to include support for frequency modulation, amplitude modulation, and panning.  </p>


  
<p>To set up a simple signal chain with a mixer, here is what the code looks like:</p>


  
<pre><code>StereoPcmStreamSource source = new StereoPcmStreamSource();

Mixer mixer =
   Oscillator.Create(440)
      .FrequencyModulate(150, 50000000)
      .AmplitudeModulate(77)
      .Pan(short.MinValue)
      .Attenuate(-6)
      .SendToMixer();

   Oscillator.Create(640)
      .FrequencyModulate(130, 51000000)
      .AmplitudeModulate(55)
      .Pan(short.MaxValue)
      .Attenuate(-6)
      .SendToMixer(mixer);

   Oscillator.Create(540)
      .FrequencyModulate(140, 53000000)
      .AmplitudeModulate(45)
      .Pan(short.MinValue)
      .Attenuate(-6)
      .SendToMixer(mixer);

   Oscillator.Create(740)
      .FrequencyModulate(160, 59000000)
      .AmplitudeModulate(41)
      .Pan(short.MaxValue)
      .Attenuate(-6)
      .SendToMixer(mixer);

source.Input = mixer;
this.media.SetSource(source);</code></pre>
<p>The code above generates a "droning" sound with four oscillators under heavy and fast vibrato.  Two oscillators are panned hard right and the other two are hard left.  </p>


  
<p>The main idea is to make it easier to set up signal chains.  Contrast the first oscillator setup code above with this conventional approach:</p>


  <code>
<pre>Mixer mixer = new Mixer();

Oscillator osc1 = new Oscillator();
osc1.Frequency = 440;
osc1.FrequencyModulator.Amplitude = 150;
osc1.FrequencyModulator.ModulationFrequency = 50000000;
osc1.AmplitudeModulator.ModulationFrequency = 77;
Panner pan = new Panner();
pan.Pan = short.MinValue;
pan.Input = osc1;
Attenuator attenuator = new Attenuator();
attenuator.Attenuation = -6;
attenuator.Input = pan;
mixer.Inputs.Add(attenuator);</pre></code>
<p>Ouch.</p>


  
<p>The next step is to integrate the fluent interface with UI controls.  What good is this code if you can't tweak the parameters on the screen?!</p>


  
