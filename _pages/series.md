---
layout: archive
title: "Series"
permalink: /series/
description: "Organized article series by Ferosh Jacob on AI-assisted software development, application generation, software scarcity, and retail search."
tags:
  - article series
  - ai-assisted development
  - software engineering
  - search relevance
author_profile: true
---

These series collect related articles into ordered reading paths.

{% for entry in site.data.series %}
  {% assign series_key = entry[0] %}
  {% assign series = entry[1] %}
  {% assign series_posts = site.posts | where: "series", series_key | sort: "series_order" %}

  <h2><a href="{{ series.permalink }}">{{ series.title }}</a></h2>
  <p>{{ series.description }}</p>
  <p><strong>{{ series_posts.size }}</strong> articles</p>
{% endfor %}
