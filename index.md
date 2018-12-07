---
layout: index
category: home
date: 2018-01-05 15:17:55
order: 1
---

<h1>Latest Posts</h1>
<ul class="timeline">
    {% for section in site.sections %}
    {% assign attr = section[0] %}
    {% assign label = section[1] %}
    <li class="timeline">
    <span>{{ label }}</span>
    <ul class="list-unstyled" id="{{ label }}">
    {% for page in site.categories[attr] %}
        <li ><a href="{{ site.baseurl }}{{ page.url }}">{{ page.title }}</a></li>
    {% endfor %}
    </ul></li>
    {% endfor %}
</ul>




