---
layout: page
title: Home
id: home
permalink: /
---

I'm Steffen, and this is where I write things down when they've been rattling around in my head for too long. Mostly data platforms and the mess that comes with them, sometimes whatever else is on my mind.

<strong>Last posts</strong>

<ul>
  {% assign recent_notes = site.notes | sort: "last_modified_at_timestamp" | reverse %}
  {% for note in recent_notes limit: 10 %}
    <li>
      {{ note.last_modified_at_timestamp | date: "%Y-%m-%d" }} &mdash; <a class="internal-link" href="{{ site.baseurl }}{{ note.url }}">{{ note.title }}</a>
    </li>
  {% endfor %}
</ul>
