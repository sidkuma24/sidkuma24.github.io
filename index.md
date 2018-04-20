---
# You don't need to edit this file, it's empty on purpose.
# Edit theme's home layout instead if you wanna make some changes
# See: https://jekyllrb.com/docs/themes/#overriding-theme-defaults
layout: home
title: Siddharth Kumar
---

{% include CV.md %}

## <i class="fa fa-chevron-right"></i> Recent Projects

<table class="table table-hover">
	<tr>
	  <td><a href="/projects/flight-paths/index.html" target="_blank">3D FLight Paths on WebGL globe</a></td>
	</tr>
	<tr>
	  <td><a href="https://github.com/sidkuma24/seq_gen" target="_blank">Sequence Generation Using LSTMs</a></td>
	</tr>
	<tr>
	  <td><a href="https://github.com/sidkuma24/movie-recommender" target="_blank">Movie Recommendation System</a></td>
	</tr>
</table>
<h4><a href="/projects">View all</a></h4>

## <i class="fa fa-chevron-right"></i> Recent Blog Posts

<table class="table table-hover">
  {% for post in site.posts limit: 5 %}
    {% unless post.draft %}
    <tr>
      <td><a href="{{ post.url }}">{{ post.title }}</a></td>
      <td class="col-md-3" style="text-align: right;">{{ post.date | date: "%B %e, %Y" }}</td>
    </tr>
    {% endunless %}
  {% endfor %}
</table>
<h4><a href="/blog">View all</a></h4>