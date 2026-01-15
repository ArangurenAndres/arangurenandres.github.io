---
layout: page
title: "Home"
permalink: /
---

<header class="hero bg-gray-50 rounded-2xl text-center py-16 px-6">
  <h1 class="text-4xl font-bold mb-2">Welcome</h1>
  <p class="text-lg text-gray-600">My Machine Learning Blog</p>

  <p class="max-w-2xl mx-auto mt-6 text-base text-gray-700 leading-relaxed">
    Here I share projects, experiments, and research notes around 
    <span class="font-semibold">deep reinforcement learning</span>, 
    <span class="font-semibold">LLM-based agents</span>, and 
    <span class="font-semibold">robotics</span> with a focus on planning, reasoning, and decision-making in complex environments.
  </p>

  <ul class="max-w-xl mx-auto mt-8 space-y-3 text-left text-lg leading-relaxed">
    <li>🎯 <span class="font-semibold">Deep Reinforcement Learning</span>  DQN, Actor-Critic, PPO-style policy gradients, ablations & evaluation</li>
    <li>🧩 <span class="font-semibold">LLM Agents:  reasoning, planning, tool-use, action evaluation, and self-improving agent loops</li>
    <li>🤖 <span class="font-semibold">Robotics: autonomous exploration and object finding guided by vision-language models</li>
    <li>🎮 <span class="font-semibold">Procedural Content Generation: generative level design, conditioning with RL agents, search + evolution</li>
    <li>📚 <span class="font-semibold">Deep Learning Theory & Practice: training dynamics, scaling, architectures, and reproducible experiments</li>
  </ul>

  <p class="mt-10 text-gray-500">
    Thanks for stopping by I’ll keep adding new projects and write-ups regularly!
  </p>
</header>

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
      <p>👋 I’m a machine learning engineer working on deep reinforcement learning, LLM-based agents, and robotics.</p>
      <p>I’m especially interested in planning and action evaluation for autonomous systems, and open-ended exploration.</p>
      <p>🔗 <a href="https://github.com/ArangurenAndres" target="_blank">GitHub</a> | <a href="aranguren.andres9712@gmail.com">Email</a></p>
    </div>
  </aside>
</div>
