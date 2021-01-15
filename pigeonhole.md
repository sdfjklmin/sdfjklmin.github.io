---
layout: page
title: Pigeonhole
permalink: /pigeonhole/
---
<div class="post-category">
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
        <h5>
        {{ post.date | date:"%F" }} >>
        <a class="category-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
        </h5>
    </li>
  {% endfor %}
</ul>
</div>
