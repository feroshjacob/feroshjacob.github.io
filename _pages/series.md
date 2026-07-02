---
layout: archive
title: "Series"
permalink: /series/
description: "Organized article series by Ferosh Jacob on AI-assisted software development, application generation, software scarcity, self-learning retail search, and practical AI systems."
tags:
  - article series
  - ai-assisted development
  - software engineering
  - search relevance
  - retail search
author_profile: true
---

These series collect related articles into ordered reading paths.

{% assign rendered_series = "" %}
{% assign recent_posts = site.posts | sort: "date" | reverse %}

{% for post in recent_posts %}
  {% if post.series %}
    {% assign series_key = post.series %}
    {% assign series_token = "|" | append: series_key | append: "|" %}
    {% unless rendered_series contains series_token %}
      {% assign series = site.data.series[series_key] %}
      {% if series %}
        {% assign series_posts = site.posts | where: "series", series_key | sort: "series_order" %}

<h2><a href="{{ series.permalink }}">{{ series.title }}</a></h2>
<p>{{ series.description }}</p>
<p><strong>{{ series_posts.size }}</strong> articles</p>

        {% assign rendered_series = rendered_series | append: series_token %}
      {% endif %}
    {% endunless %}
  {% endif %}
{% endfor %}

{% for entry in site.data.series %}
  {% assign series_key = entry[0] %}
  {% assign series_token = "|" | append: series_key | append: "|" %}
  {% assign series = entry[1] %}
  {% unless rendered_series contains series_token %}
    {% assign series_posts = site.posts | where: "series", series_key | sort: "series_order" %}

<h2><a href="{{ series.permalink }}">{{ series.title }}</a></h2>
<p>{{ series.description }}</p>
<p><strong>{{ series_posts.size }}</strong> articles</p>
  {% endunless %}
{% endfor %}
