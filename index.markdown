---
layout: page
title: "Research & projects"
permalink: /
---
<ul class="max-w-none mx-auto px-8 mt-12 space-y-6 text-left text-lg leading-relaxed">
  <li style="padding-left:1rem; border-left:4px solid #e5e7eb;">
    <span style="font-weight:600; color:#1e3a8a;">
      Agent Reasoning, Planning, and Decision-Making
    </span>
    <span style="color:#4b5563;"> ·
      Evaluation of LLM agents on LMAct and BALROG, in-context learning, memory and planning failures in long-horizon environments, adaptive demonstration selection as an MDP
    </span>
  </li>

  <li style="padding-left:1rem; border-left:4px solid #e5e7eb;">
    <span style="font-weight:600; color:#1e3a8a;">
      Adaptive & Intelligent Robotics
    </span>
    <span style="color:#4b5563;"> ·
      multimodal LLM-based perception and control, semantic-to-action grounding, closed-loop navigation without task-specific training, real-time adaptation
    </span>
  </li>

  <li style="padding-left:1rem; border-left:4px solid #e5e7eb;">
    <span style="font-weight:600; color:#1e3a8a;">
      LLM Pre-training Efficiency & Robustness
    </span>
    <span style="color:#4b5563;"> ·
      layer-wise heterogeneous transformer architectures, pruning-inspired scaling profiles, controlled pre-training ablations, depth-wise parameter allocation, poisoning attacks and robustness vulnerabilities
    </span>
  </li>

  <li style="padding-left:1rem; border-left:4px solid #e5e7eb;">
    <span style="font-weight:600; color:#1e3a8a;">
      Procedural Content Generation and Open-Ended Systems
    </span>
    <span style="color:#4b5563;"> ·
      quality–diversity (MAP-Elites), structured content representations, behavior descriptors, validity constraints, archive-based open-ended search
    </span>
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

<p>
  My name is <strong>Andres Aranguren</strong>,  MSc Computer Science student at Leiden University working at the intersection of reinforcement learning, large language models, and robotics.
</p>

<p>
  My research focuses on <strong>agent reasoning and planning</strong>, I study how LLMs can be transformed from static predictors into interactive decision-making policies through in-context learning, structured feedback, and MDP demonstrations selection I am particularly interested in <strong>embodied LLM-guided navigation</strong> and adaptive robot control in uncertain environments, as well as <strong>parameter-efficient and robust LLM architectures</strong>. 
</p>


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
