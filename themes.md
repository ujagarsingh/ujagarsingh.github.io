---
layout: default
title: Jekyll Themes
---

{% for themes in site.js-in-hindi %}


<a href="{{ js-in-hindi.url | prepend: site.baseurl }}">
        <h2>{{ js-in-hindi.title }}</h2>
</a>

<p class="post-excerpt">{{ js-in-hindi.description | truncate: 160 }}</p>

{% endfor %}   
