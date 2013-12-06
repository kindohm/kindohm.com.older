---
layout: post
title: "The End of Browser Plugins, First Hand"
---

<script src="/scripts/jquery-1.5.2.min.js" type="text/javascript"></script>
<script src="/hodsmedia/audiolib.min.js" type="text/javascript"></script>
<script type="text/javascript"> 
var dev, osc, lfo, playing;  
$(document).ready(function () {     
$('#playButton').click(function () {         
playing ? stop() : play();     });     
dev = audioLib.AudioDevice(audioCallback, 2);     
osc = audioLib.Oscillator(dev.sampleRate, 440);     
osc.waveShape = 'triangle';     
lfo = audioLib.Oscillator(dev.sampleRate, 1.0);     
osc.addAutomation('frequency', lfo, .9, 'additiveModulation');  });  
function play() {     playing = true;     
$('#playButton').html('Stop'); }  
function stop() {     playing = false;     
$('#playButton').html('Play'); }  
function audioCallback(buffer, channelCount) {     
if (playing) {         lfo.generateBuffer(buffer.length / channelCount);         
osc.append(buffer, channelCount);     } }  </script>  


<p>Until right about now, I've thought that plugins like Flash and Silverlight would have a place in this world. Click the button below. It will play a sound generated from a Javascript-based digital audio synthesis library. No plugin required.</p>
<p><button id="playButton">Play</button> <em>Put your headphones on first!</em></p>
<p>The audio synthesis is performed with <a href="http://audiolibjs.org">audiolib.js</a>. audiolib.js can also do some crazy stuff with PCM/mp3 encoding, audio recording, and it even has a node.js install. Did I mention that no browser plugin is required?</p>
<p>Here's the code, based off of one of their tutorials:</p>

<pre><code>var dev, osc, lfo, playing;

$(document).ready(function () {
    $('#playButton').click(function () {
        playing ? stop() : play();
    });
    dev = audioLib.AudioDevice(audioCallback, 2);
    osc = audioLib.Oscillator(dev.sampleRate, 440);
    osc.waveShape = 'triangle';
    lfo = audioLib.Oscillator(dev.sampleRate, 1.0);
    osc.addAutomation('frequency', lfo, .9, 'additiveModulation');

});

function play() {
    playing = true;
    $('#playButton').html('Stop');
}

function stop() {
    playing = false;
    $('#playButton').html('Play');
}

function audioCallback(buffer, channelCount) {
    if (playing) {
        lfo.generateBuffer(buffer.length / channelCount);
        osc.append(buffer, channelCount);
    }
}
</code></pre>

