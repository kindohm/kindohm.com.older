---
layout: post
title: "PowerShell Script for SubText Tag Generation"
---


<p>Here's the script I use to generate tags for my <a title="Subtext" href="http://www.subtextproject.com" target="_blank">Subtext</a> posts.  It writes the tag HTML to the screen and also copies it to the clipboard for immediate pasting:</p>  
  if ($args.Length -ne 2)
  {
    write-host 'Two arguments (site and tag list) expected'
    write-host 'Example: tags del.icio.us "scripts tags llama"'
    return
  }

  $site = $args[0]
  $input = $args[1]
  $delim = ' '
  $splits = $input.Split($delim.ToCharArray())
  $header = '&lt;p class="tags"&gt;tags: '
  $footer = '&lt;/p&gt;'
  $format = '&lt;a href="http://' + $site + '/tag/{0}"'
  $format += ' target="_blank" rel="tag"&gt;{0}&lt;/a&gt; '

  $html = new-object  System.Text.StringBuilder

  [void]$html.Append($header)

  for ($i = 0; $i -le $splits.Length; $i++)
  {
    if ($splits[$i].Length -ge 1)
    {
      $newText = [System.String]::Format($format, $splits[$i])
      [void]$html.Append($newText)
    }
  }

  [void]$html.Append($footer)
  set-clipboard -text $html
  write-host $html

<p>Example:</p>  
<pre><code>tags del.icio.us 'snow cookie alpaca'</code></pre>

<p>... generates the following HTML:</p>  
  &lt;p class="tags"&gt;tags: 
  &lt;a href="http://del.icio.us/tag/snow" 
    target="_blank" rel="tag"&gt;snow&lt;/a&gt; 
  &lt;a href="http://del.icio.us/tag/cookie" 
    target="_blank" rel="tag"&gt;cookie&lt;/a&gt; 
  &lt;a href="http://del.icio.us/tag/alpaca" 
    target="_blank" rel="tag"&gt;alpaca&lt;/a&gt; 
  &lt;/p&gt;

<p>Have fun.</p>  
<p class="tags">tags: <a href="http://del.icio.us/tag/subtext" target="_blank" rel="tag">subtext</a> <a href="http://del.icio.us/tag/tags" target="_blank" rel="tag">tags</a> <a href="http://del.icio.us/tag/powershell" target="_blank" rel="tag">powershell</a> <a href="http://del.icio.us/tag/script" target="_blank" rel="tag">script</a> </p> 
