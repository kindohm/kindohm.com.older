---
layout: bigpage
title: "Mike's Blog"
---

<section>

<p>Below are recent posts. <a href="/blog/archive.html">View the full archive</a>.</p>

{% for post in site.posts limit:5 %}


<article>
<h2 class="title"><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></h2>
<p class="text-muted"><span> {{ post.date | date_to_string }} </span> </p>

{{ post.content }}

</article>

<hr/>
    
{% endfor %}

</section>