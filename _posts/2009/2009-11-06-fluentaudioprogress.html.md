---
layout: post
title: "Fluent Silverlight Audio Synthesis API With UI Injection"
---


<p>I&#8217;m in the middle of working on my Silverlight audio synthesis API which lets you <i>very</i> easily define audio signal chains.  I&#8217;ve now built support to include the ability to wire up your UI controls into the &#8220;fluent&#8221; calls.</p>


<p>Let&#8217;s say you have a Slider control in your XAML:</p>


<pre><code>&lt;Slider 
   x:Name="freqSlider" 
   Minimum="40" 
   Maximum="1000" 
   Value="440"/&gt;</code></pre>
<p>Let&#8217;s say you want to play a sine wave that changes its frequency with the Slider&#8217;s changing value.  All you have to do is this:</p>


<pre><code>StereoPcmStreamSource source = new StereoPcmStreamSource();
source.Input =
   Oscillator.Create()
      .SetFrequency(this.freqSlider, Slider.ValueProperty)
      .SendToMixer();
MyMediaElement.SetSource(source);</code></pre>
<p>You can <a target="_blank" href="http://kindohm.com/files/AudioSliderDemo.htm">try an exmaple of this app out for yourself</a>.</p>


<p>I&#8217;ve added similar UI &#8220;injection&#8221; methods for attenuation, panning, and modulation:</p>


<pre><code>Oscillator.Create(120)
    .SetFrequency(this.frequencySlider, Slider.ValueProperty)
    .WaveForm(this.waveFormList, ListBox.SelectedItemProperty)
    .AmplitudeModulate(this.ampHz, Slider.ValueProperty)
    .Pan(this.panSlider, Slider.ValueProperty)
    .Attenuate(this.volumeSlider, Slider.ValueProperty)
    .SendToMixer(this.mixer);</code></pre>
<p>If UI injection isn&#8217;t your thing, you can hard-code all these values too:</p>


<pre><code>Oscillator.Create(120)
    .WaveForm(WaveForm.Sine)
    .FrequencyModulate(2, 10000)
    .FrequencyModulationWaveForm(WaveForm.Saw)
    .AmplitudeModulate(50)
    .Pan(short.MinValue)
    .Attenuate(-3)
    .SendToMixer();</code></pre>
<p>Full code and a larger demo app are coming soon.</p>


