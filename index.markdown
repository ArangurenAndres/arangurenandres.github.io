---
layout: page
title: "Home"
permalink: /
---

<div style="display: flex; flex-wrap: wrap; gap: 2rem; align-items: flex-start;">

  <!-- LEFT: PROJECTS LIST (now wider) -->
  <div style="flex: 3; min-width: 300px;">
    {% assign sorted_projects = site.projects | sort: 'date' | reverse %}
    {% for project in sorted_projects %}
      <div style="margin-bottom: 2rem; padding: 1rem; background: #f9f9f9; border-radius: 8px;">
      <!-- Optional Image Preview -->
      {% if project.preview_image %}
        <img src="{{ project.preview_image }}" alt="Preview" style="width: 100%; max-height: 200px; object-fit: cover;" />
      {% endif %}
        <h3><a href="{{ project.url }}">{{ project.title }}</a></h3>
        <p style="color: #777;">{{ project.date | date: "%B %d, %Y" }}</p>
        <p>{{ project.excerpt | strip_html | truncatewords: 40 }}</p>
        <a href="{{ project.url }}" style="font-weight: bold;">Continue reading →</a>
      </div>
    {% endfor %}
  </div>

  <!-- RIGHT: SIDEBAR -->
  <div style="flex: 1; min-width: 200px; background: #fff; padding: 1rem; border-left: 1px solid #ccc;">
    <img src="/assets/images/profile.png" alt="Andres Aranguren" style="max-width: 100%; border-radius: 10px;" />
    <p>👋 I’m a machine learning engineer focused on deep reinforcement learning, generative models, and robotics.</p>
    <p>Currently researching autonomous systems using policy gradients and model-based control.</p>
    <p>🔗 <a href="https://github.com/yourusername" target="_blank">GitHub</a> | <a href="mailto:your@email.com">Email</a></p>
  </div>

</div>
