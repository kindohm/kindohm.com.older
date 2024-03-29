---
layout: post
title: "Detect Code Churn in Mercurial with Powershell"
date: 2/1/2013 5:53:51 PM
---

<p><a href="http://www.flickr.com/photos/blackcountrymuseums/5123015826/" title="A small glass butter churn, 1976_666 by Black Country Museums, on Flickr"><img src="http://farm5.staticflickr.com/4108/5123015826_9f866e49ec_n.jpg" width="213" height="320" style="float: right" alt="A small glass butter churn, 1976_666"></a></p>

<p>Detecting code churn in your team's source code can be valuable. Code churn can give you some insight into problem areas of your code base. What files are being modified most often? What users are modifying the most files? What users seem to be grinding away on the same source file day after day?</p>

<p>My friend <a href="http://www.justaddcode.com/blog/">Neil</a> showed me a <a href="https://github.com/garybernhardt/dotfiles/blob/master/bin/git-churn">git churn</a> command the other day that allows you to get churn information in a git repository. Being Windows devs, we talked about implementing this type of command in Powershell (using <a href="https://github.com/dahlbyk/posh-git">posh-git</a> for git). Since I work with Mercurial for version control these days, I wanted to get this to work for Mercurial repositories in Windows with Powershell and <a href="https://github.com/JeremySkinner/posh-hg">posh-hg</a>.</p>

<p>So how do we do this? We start with Mercurial's <code>log</code> command. Here, we'll show the repo's last two changesets:</p>

	~ hg log -l 2
	changeset:   18:6cb36017ad62
	tag:         tip
	user:        Betty <betty@somewhere.com>
	date:        Thu Jan 31 19:09:29 2013 -0600
	files:       world.txt
	description:
	changed the world


	changeset:   17:84b3ceb8229e
	user:        Betty <betty@somewhere.com>
	date:        Thu Jan 31 19:09:09 2013 -0600
	files:       road.txt
	description:
	updated road
	
<p>Here we can see that our fictional developer, "Betty", has modified a couple of files. The default <code>log</code> command isn't very helpful in showing us <em>churn</em> though. However, can start customizing the output of the <code>log</code> command to get us a few steps closer to that:</p>
<pre><code>~ hg log --template "{node}\n{files}\n\n"
6699f74b9dab
llama.txt

6a537d4de725
world.txt

65c4ab278bb4
llama.txt

248d54f1b323
llama.txt road.txt

2585ab58eaf9
llama.txt

901bf5505685
vehicle.txt world.txt

76a558904dba
chassis.txt llama.txt road.txt

dac38de69a6c
brain.txt llama.txt chassis.txt

394534a3bd69
llama.txt world.txt</code></pre>
<p>Above, we've stripped away some less-useful information about each commit, and now we are able to see what files have changed in each commit. Wouldn't it be cool if we could aggregate this information and get a count of all the files in a given history? We can do the aggregation easy enough with Powershell. However, the Mercurial output isn't helpful since the list of files is all on a single line. Instead of this:</p>
<pre><code>brain.txt llama.txt chassis.txt</code></pre>
<p>We want this:</p>
<pre><code>brain.txt
llama.txt
chassis.txt</code></pre>
<p>This single-file-per-row output will allow us to perform aggregation in Powershell.</p>
<p>Unfortunately, Mercurial's log templating cannot accomplish this. Instead, we need to define a custom Mercurial log style in a separate file and then use that style when we run the <code>log</code> command:</p>
<pre><code>#.hg-churn
changeset = "{files}"
file = "{file}\n"</code></pre>
<pre><code>~ hg log -l 10 --style c:\users\mike\.hg-churn
world.txt
road.txt
base.txt
llama.txt
llama.txt
llama.txt
llama.txt
vector.txt
llama.txt
llama.txt</code></pre>
<p>Ah, now we're getting somewhere! Now we can begin to see over the last 10 commits that the "llama.txt" file is being touched a lot.</p>
<p>Next, we want to pipe this output to Powershell to aggregate the file counts. Extra thanks to <a href="http://justaddcode.com/blog">Neil</a> for providing the Powershell piping. Oh, that's some good alliteration!</p>
<pre><code>~ hg log -l 20 --style .hg-churn | group-object | sort -prop Count
Count Name                      Group
----- ----                      -----
    1 base.txt                  {base.txt}
    1 vector.txt                {vector.txt}
    1 vehicle.txt               {vehicle.txt}
    1 chassis.txt               {chassis.txt}
    1 brain.txt                 {brain.txt}
    2 road.txt                  {road.txt, road.txt}
    3 world.txt                 {world.txt, world.txt, world.txt}
   13 llama.txt                 {llama.txt, ...}</code></pre>
<p>Here we can see pretty clearly what files have been touched over the last 20 commits. The <code>group-object</code> cmdlet in Powershell automatically counts the number of occurrences of an item in a list. The <code>sort</code> cmdlet is self-explanatory.</p>
<p>I like to make this output a little more concise and eliminate that Group column:</p>
<pre><code>~ hg log -l 20 --style .hg-churn | group-object | sort -prop Count | format-list Count,Name

Count : 1
Name  : base.txt

Count : 1
Name  : vector.txt

Count : 1
Name  : vehicle.txt

Count : 1
Name  : chassis.txt

Count : 1
Name  : brain.txt

Count : 2
Name  : road.txt

Count : 3
Name  : world.txt

Count : 13
Name  : llama.txt</code></pre>
<p>Slick.</p>
<p>Now, maybe we want to know <em>who</em> has been making all these modifications. Maybe a particular developer has been touching the same file over and over and needs a little bit of help. The <code>.hg-churn</code> style we made earlier can be enhanced to output user/author information:</p>
<pre><code>#.hg-churn-user
changeset = "{files}"
file = "{author|user}:{file}\n"</code></pre>
<p>Now when we specify this new log style:</p>
<pre><code>hg log -l 10 --style "c:\users\mike\.hg-churn-user"

betty:world.txt
betty:road.txt
betty:base.txt
hank:llama.txt
hank:llama.txt
hank:llama.txt
hank:llama.txt
hank:vector.txt
hank:llama.txt
hank:llama.txt</code></pre>
<p>Next, we can apply the same Powershell formatting:</p>
<pre><code>hg log -l 20 --style .hg-churn-user | group-object | sort -prop Count | format-list Count,Name

Count : 1
Name  : betty:road.txt

Count : 1
Name  : betty:base.txt

Count : 1
Name  : hank:vector.txt

Count : 1
Name  : hank:road.txt

Count : 1
Name  : betty:vehicle.txt

Count : 1
Name  : betty:chassis.txt

Count : 1
Name  : hank:brain.txt

Count : 1
Name  : hank:world.txt

Count : 2
Name  : betty:world.txt

Count : 3
Name  : betty:llama.txt

Count : 10
Name  : hank:llama.txt</code></pre>
<p>Now it is clear that Hank has been grinding away on the llama.txt file. Maybe we should go check in with him?</p>
<p>This is all great, but having to type all that junk at the command line is a pain. How about a few PoSh functions to stick in your profile:</p>
<pre><code>function hg-churn-template($template, $opts){
	if ([system.string]::IsNullOrEmpty($opts) -eq $true) {
		$opts = '-l 20'
	}
	hg log $opts --style $template | group-object | sort -prop Count | format-list Count, Name
}

function hg-churn(){
	$opts = $args -join ' '
	hg-churn-template('c:\\path\\to\\.hg-churn', $opts)
}

function hg-churn-user(){
	$opts = $args -join ' '
	hg-churn-template('c:\\path\\to\\.hg-churn-user', $opts)
}</code></pre>
<p>Now you can just run <code>hg-churn</code> or <code>hg-churn-user</code> at your Powershell command prompt to get some sweet Mercurial churn info:</p>
<pre><code>~ hg-churn

Count : 1
Name  : base.txt

Count : 1
Name  : vector.txt

Count : 1
Name  : vehicle.txt

Count : 1
Name  : chassis.txt

Count : 1
Name  : brain.txt

Count : 2
Name  : road.txt

Count : 3
Name  : world.txt

Count : 13
Name  : llama.txt</code></pre>
<p>If you look at the <code>$opts</code> variable, you'll see that any <code>hg log</code> args will be respected:</p>
<pre><code>#find churn in revisions #5 through #15
~ hg-churn -r 5::15

Count : 1
Name  : hank:road.txt

Count : 1
Name  : betty:world.txt

Count : 1
Name  : betty:llama.txt

Count : 1
Name  : hank:vector.txt

Count : 8
Name  : hank:llama.txt</code></pre>
<p>Now, time to go ask Hank about his llama code...</p>
<p>---</p>
<p>Example code: <a href="https://gist.github.com/4692291">https://gist.github.com/4692291</a></p>
<p>Reference: <a href="http://hgbook.red-bean.com/read/customizing-the-output-of-mercurial.html">http://hgbook.red-bean.com/read/customizing-the-output-of-mercurial.html</a></p>
<p>Glass butter churn image: <a href="http://www.flickr.com/photos/blackcountrymuseums/5123015826/">http://www.flickr.com/photos/blackcountrymuseums/5123015826/</a></p>

