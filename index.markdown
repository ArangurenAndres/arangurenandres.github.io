---
layout: page
title: "Home"
permalink: /
---

<header class="hero bg-gray-50 rounded-2xl text-center py-16 px-6">
  <h1 class="text-4xl font-bold mb-2">Welcome</h1>
  <p class="text-lg text-gray-600">My Machine Learning Blog</p>

  <p class="max-w-2xl mx-auto mt-6 text-base text-gray-700 leading-relaxed">
    Here I share projects, experiments, and ideas at the intersection of 
    <span class="font-semibold">machine learning</span>, 
    <span class="font-semibold">reinforcement learning</span>, 
    <span class="font-semibold">robotics</span>, and 
    <span class="font-semibold">computer vision</span>.
  </p>
  <ul class="max-w-xl mx-auto mt-8 space-y-3 text-left text-lg leading-relaxed">
    <li>📌 <span class="font-semibold">Reinforcement Learning</span> — from DQNs on CartPole to Actor-Critic methods</li>
    <li>🤖 <span class="font-semibold">Robotics</span> — Raspberry Pi LLM guided navigation in unknown environments</li>
    <li>🎮 <span class="font-semibold">Game AI</span> — MCTS agents for Pacman Capture the Flag</li>
    <li>🧠 <span class="font-semibold">Deep Learning</span> — neural networks, recommendation systems, generative models</li>
  </ul>

  <p class="mt-10 text-gray-500">Thanks for stopping by — I’ll keep adding new projects regularly!</p>
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
      <p>👋 I’m a machine learning engineer focused on deep reinforcement learning, generative models, and robotics.</p>
      <p>Currently researching autonomous systems using policy gradients and model-based control.</p>
      <p>🔗 <a href="https://github.com/yourusername" target="_blank">GitHub</a> | <a href="mailto:your@email.com">Email</a></p>
    </div>
  </aside>
</div>
