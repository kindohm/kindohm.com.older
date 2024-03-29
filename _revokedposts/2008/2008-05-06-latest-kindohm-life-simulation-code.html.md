---
layout: post
title: "Latest Kindohm-Life Simulation Code"
---

<p>The latest changes/enhancements to my home-grown simulation have been committed.  I've also made the latest source available as its own downloadable zip archive (view the downloads page to download the archive):</p>
  
<p><a title="http://code.google.com/p/kindohm-life/" href="http://code.google.com/p/kindohm-life/">http://code.google.com/p/kindohm-life/</a></p>
  
<p>It's a C# project - you'll need to build the code to run the exe.</p>
  
<p><strong>UPDATE</strong>: compiled binaries have now been uploaded (2008.05.07)</p>
  
<p>The changes include:</p>
  
<ul>   
<li>Improved "free will" AI for carnivores and herbivores.  They aren't so rigid in their ways any more.  The old "hunt-at-all-costs-or-die" mentality has been replaced with an algorithm that looks for the nearest food/mate/prey at the time. </li>    
<li>Planned agent activities - if a creature sees food, prey, or a mate in the distance, they'll set their mind and go after it until it is gone.  Before, creatures would be a bit wishy-washy and get sidetracked easily. </li>    
<li>Mutations - by chance, offspring may mutate one of their characteristics (such as sight, resistance to starvation, eagerness to mate) - for better or for worse.  This is a simple attempt at introducing natural selection into the system. </li>    
<li>More extensive event handling in the UI.  Births, deaths, attacks, mutations, and eating are all broadcast to the screen and can be toggled on/off.  You'll even be notified when a creature's corpse decays to the point of turning into a plant.  </li> </ul>  
<p>One big change to the model was the progression of death to corpse to plant, and at what point those items are "edible" to be consumed by another creature.  Basically, a carnivore will prey on an herbivore and attack it.  The herbivore itself isn't food though.  After it is killed, a corpse is produced, and the attacker may choose to consume the corpse on the next heartbeat.  A corpse has a finite amount of "nutrition" which decreases over time.  After a corpse's nutrition has depleted, it continues to decay until eventually it produces enough imaginary fertilizer to produce a plant or vegetation - which can then be tracked down and consumed by herbivores.  </p>
  
<p>Heartbeats, corpses, plants....  it's all in the code :)  Just go take a look for now...  some day I might try and explain it better.</p>
  
<p>It's been tough coming up with a good set of starting parameters for the simulation.  Lots of stuff is hard-coded, such as starting attribute values for agents and the normal distributions used to randomly assign those values.  Other things are configurable, such as the board size and the numbers of herbivores, carnivores, and plants.  Right now, a 300x300 board with 300 herbivores, 50 carnivores, and 200 plants seems to run close to a "steady state".  </p>
  
<p>As for what is next....  I don't have any specific plans.  Maybe introduce better AI into the herbivores and carnivores, and look into performance improvements.  </p>
  
<div class="tags" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:e30f828d-4a76-48b0-bc9d-c2f954f90257">Technorati Tags: <a href="http://technorati.com/tags/simulation" target="_blank" rel="tag">simulation</a> <a href="http://technorati.com/tags/ai" target="_blank" rel="tag">ai</a> <a href="http://technorati.com/tags/code" target="_blank" rel="tag">code</a> <a href="http://technorati.com/tags/programming" target="_blank" rel="tag">programming</a> <a href="http://technorati.com/tags/csharp" target="_blank" rel="tag">csharp</a></div> 
