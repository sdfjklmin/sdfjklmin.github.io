---
layout: page
title: Pigeonhole
permalink: /pigeonhole/
---

 <ul>
  {% for post in site.posts %}

    {% unless post.next %}
      <h2 id="y{{ post.date | date: '%Y' }}">{{ post.date | date: '%Y' }}</h2>
    {% else %}
      {% capture year %}{{ post.date | date: '%Y' }}{% endcapture %}
      {% capture nyear %}{{ post.next.date | date: '%Y' }}{% endcapture %}
      {% if year != nyear %}
        <h2 id="y{{ post.date | date: '%Y' }}">{{ post.date | date: '%Y' }}</h2>
      {% endif %}
    {% endunless %}

    <li>
        <time>
        {{ post.date | date:"%F" }} .
        </time>
        <a class="title" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
    </li>

  {% endfor %}
</ul>
