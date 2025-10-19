---
layout: standard
title: Homepage
mainless: true
---
<main markdown="1">

## Hi, I'm Renée!
<p>I can do:</p>
<ul>
<li>Teaching</li>
<li>Software Development</li>
<li>Video Captioning</li>
<li>Technical Writing</li>
<li>Copyediting</li>
</ul>
<p>My background:</p>
<ul>
<li>Bachelor's in Computer Science</li>
<li>Master's in English Linguistics</li>
<li>3 years of experience in software dev at research laboratories</li>
<li>2 years of experience in teaching undergraduate-level English courses</li>
<li>2 years of experience at makerspaces</li>
</ul>
<p>My professional interests:</p>
<ul>
<li>Accessible Curriculum Design</li>
<li>Sustainable 3D Printing</li>
<li>Writing modern software that runs on legacy systems</li>
</ul>

<!--<a href="/about/">Read Longer About Page Here</a>-->
</main>
<aside class="hr-above">
<h2>Recent Blog Posts</h2>
<ul>
{% for post in site.posts %}
<li><time datetime="{{ post.date }}">{{ post.date | date: "%Y-%m-%d" }}</time>&nbsp;<a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></li>
{% endfor %}
</ul>
</aside>
<aside class="hr-above" markdown="1">

## Tumblelog

{% capture 2025-08-05 %}
### Library Catalog/OPAC Experiment
I've been kicking around the idea of properly cataloging all of the books in my collection and setting up a website to lend them out, much like a library.  I'm trying to see if jekyll can work for this purpose.  You can see the work in progress OPAC under [tiny-apps/opac/genre-tiles.html](/tiny-apps/opac/genre-tiles.html).

{% endcapture %}
{% include tumble.html date="2025-08-05" tags="library" content=2025-08-05 %}

{% capture 2025-04-13 %}
### Site Update
Replaced homepage with a tumblelog and added an optional dark mode (set by browser/os preference).

{% endcapture %}
{% include tumble.html date="2025-04-13" tags="meta" content=2025-04-13 %}


<!-- [Older Posts &rarr;](/tumble_archive.html) -->
</aside>
