---
layout: page
title: "Home"
permalink: /
---
<ul class="max-w-xl mx-auto mt-8 space-y-3 text-left text-lg leading-relaxed">
  <li>
    <span class="font-semibold">Deep Reinforcement Learning</span> ·
    DQN, Actor-Critic, PPO-style policy gradients, ablations and evaluation
  </li>
  <li>
    <span class="font-semibold">LLM Agents</span> ·
    reasoning, planning, tool-use, action evaluation, and self-improving agent loops
  </li>
  <li>
    <span class="font-semibold">Robotics</span> ·
    autonomous exploration and object finding guided by vision-language models
  </li>
  <li>
    <span class="font-semibold">Procedural Content Generation</span> ·
    generative level design, conditioning with RL agents, search and evolution
  </li>
  <li>
    <span class="font-semibold">Deep Learning Theory and Practice</span> ·
    training dynamics, scaling, architectures, and reproducible experiments
  </li>
</ul>

<div class="page-body">
  <main class="content">
    {% assign sorted_projects = site.projects | sort: 'date' | reverse %}
    <section id="projects">
      <h2>Projects</h2>
      <div class="projects-grid">
        {% for project in sorted_projects %}
          <article class="project-card">
            {% if project.preview_image %}
              <a href="{{ project.url }}">
                <img src="{{ project.preview_image }}" alt="{{ project.title }}" />
              </a>
            {% endif %}
            <h3><a href="{{ project.url }}">{{ project.title }}</a></h3>
            <p class="muted">{{ project.date | date: "%B %d, %Y" }}</p>
            <p>{{ project.excerpt | strip_html | truncatewords: 40 }}</p>
            <a href="{{ project.url }}" class="read-more">Continue reading →</a>
          </article>
        {% endfor %}
      </div>
    </section>
  </main>

<aside class="sidebar">
  <div class="about-card">
    <img src="/assets/images/profile.png" alt="Andres Aranguren" class="about-photo" />
    <h3>About me</h3>

    <p> I’m a machine learning engineer working on deep reinforcement learning, LLM-based agents, and robotics.</p>
    <p>I’m especially interested in planning and action evaluation for autonomous systems, and open-ended exploration.</p>

    <div class="about-links">
      <a href="https://github.com/ArangurenAndres" target="_blank" rel="noopener">GitHub</a>
      <span>·</span>
      <a href="mailto:aranguren.andres9712@gmail.com">Email</a>
      <span>·</span>
      <a href="https://www.linkedin.com/in/andres-aranguren/" target="_blank" rel="noopener">LinkedIn</a>
    </div>
  </div>
</aside>
</div>
