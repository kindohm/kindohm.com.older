---
layout: post
title: "Generate Random Numbers from a Distribution in .Net"
---

<p>I stumbled across a great .Net class library and article posted on <a target="_blank" href="http://www.codeproject.com">codeproject.com</a>: <a target="_blank" href="http://www.codeproject.com/useritems/Random.asp">.NET random number generators and distributions</a>. It's a library that allows you to generate random numbers based on well-known statistical distributions:</p>


<code> 
<pre>
//EXAMPLE
//Generate a random number based on a 
//normal distribution centered
//around 50 with a standard deviation of 7...
NormalDistribution normal = new NormalDistribution();
normal.Mu = 50;
normal.Sigma = 7;
double nextValue = normal.NextDouble();
</pre>
</code> 
<p>Piece of cake to use, and from what I've seen it performs well. The code author supplies a test/demo app which gives an indication of how well it performs - not to mention demonstrating all of the distributions it can handle.</p>


  
<p class="tags">tags: <a href="http://technorati.com/tag/random" target="_blank" rel="tag">random</a> <a href="http://technorati.com/tag/.net" target="_blank" rel="tag">.net</a> <a href="http://technorati.com/tag/code" target="_blank" rel="tag">code</a> <a href="http://technorati.com/tag/programming" target="_blank" rel="tag">programming</a> <a href="http://technorati.com/tag/number" target="_blank" rel="tag">number</a> <a href="http://technorati.com/tag/randomnumber" target="_blank" rel="tag">randomnumber</a> </p>


 
