---
layout: default
title: Jekyll Themes
---

{% for tutorial in site.tutorial %}


<a href="{{ tutorial.url | prepend: site.baseurl }}">
        <h2>{{ tutorial.title }}</h2>
</a>

<p class="post-excerpt">{{ tutorial.description | truncate: 160 }}</p>

{% endfor %}      