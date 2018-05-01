---
layout: blogIndex
title: "Blog Index"
---

# Blog Posts

<div>
  {% for post in site.posts %}
    <table class="table table-hover">
    <tr>
      <td><a href="{{ post.url }}">{{ post.title }}</a></td>
      <td class="col-md-3" style="text-align: right;">{{ post.date | date: "%B %e, %Y" }}</td>
    </tr>
    </table> 
  {% endfor %}
</div>
