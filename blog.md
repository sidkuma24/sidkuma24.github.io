---
layout: blogPage
title: "Blog Posts"
---

# Blog Posts

<div>
  {% for post in site.posts %}
    {% unless post.draft %}
    <table class="table table-hover">
    <tr>
      <td><a href="{{ post.url }}">{{ post.title }}</a></td>
      <td class="col-md-3" style="text-align: right;">{{ post.date | date: "%B %e, %Y" }}</td>
    </tr>
    </table>
    {% endunless %}
  {% endfor %}
</div>

<h2>Categories</h2>
<ul>

{% assign categories_list = site.categories %}
  {% if categories_list.first[0] == null %}
    {% for category in categories_list %}
      <li><a href="#{{ category }}">{{ category | capitalize }} ({{ site.tags[category].size }})</a></li>
    {% endfor %}
  {% else %}
    {% for category in categories_list %}
      <li><a href="#{{ tag[0] }}">{{ category[0] | capitalize }} ({{ category[1].size }})</a></li>
    {% endfor %}
  {% endif %}
{% assign categories_list = nil %}
</ul>

{% for tag in site.categories %}
  <h3 id="{{ tag[0] }}">{{ tag[0] | capitalize }}</h3>
  <ul>
    {% assign pages_list = tag[1] %}
    {% for post in pages_list %}
      {% if post.title != null %}
      {% if group == null or group == post.group %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
      {% endif %}
      {% endif %}
    {% endfor %}
    {% assign pages_list = nil %}
    {% assign group = nil %}
  </ul>
{% endfor %}
