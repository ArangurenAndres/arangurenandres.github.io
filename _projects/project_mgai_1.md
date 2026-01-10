---
layout: default
title: Procedural Content Generation in Minecraft
preview_image: /assets/mgai/assignment_1/many.png
date: 2025-02-15
---

# Procedural Content Generation in Minecraft

Application of **Procedural Content Generation (PCG)** in *Minecraft* to autonomously generate believable, adaptable, and diverse architectural structures—specifically modern two-story houses—that integrate seamlessly with the game’s dynamic terrain. The system selects optimal build locations within a **100×100** region using terrain statistics, constructs adaptive foundations without flattening terrain, and generates randomized exterior styles and interior layouts across iterations.

---

## Abstract

Procedural Content Generation (PCG) enables the automated creation of game content with minimal user input. In this project, I implement a terrain-adaptive PCG pipeline in Minecraft using the **GDPC (Generative Design Python Client)** framework. The pipeline evaluates terrain via heightmaps, identifies the most suitable build area using a sliding-window flatness metric with water awareness, constructs an elevated platform supported by adaptive pillars (including underwater support when needed), and generates a complete modern house with randomized structural parameters, architectural styles, and interior layouts. The result is a system that balances **believability**, **adaptability**, and **controlled randomness**, producing coherent but visually diverse structures across multiple iterations.

---

## 1. Introduction

Procedural Content Generation (PCG) is a growing field in game AI that focuses on the autonomous generation of game content such as levels, maps, environments, rules, textures, stories, items, and characters. One of its most common applications is terrain and environment generation, where the goal is to create rich, large-scale worlds while minimizing manual design effort.

Beyond reducing development costs, PCG acts as a creativity amplifier, enabling small teams to generate complex and expressive content that would otherwise require significant artistic resources. The objective of this project is to apply PCG techniques in Minecraft to generate houses that **adapt to their environment** rather than imposing rigid structures on procedurally generated terrain. Throughout development, Large Language Model (LLM) tools were used to assist with code structuring, optimization, and debugging.

---

## 2. Platform and Framework

### 2.1 Minecraft as a PCG Testbed

Minecraft is widely used as a benchmark environment for non-machine-learning PCG due to its procedurally generated terrain, diverse biomes, and flexible building primitives. Its stochastic terrain generation makes it particularly suitable for evaluating terrain-adaptive structure placement under varied environmental conditions.

### 2.2 GDPC

This project uses the **GDPC (Generative Design Python Client)** framework as a Python-based HTTP interface to communicate with a live Minecraft world. GDPC allows direct block placement, world slicing, and terrain querying while the game is running, making it well suited for iterative procedural structure generation.

---

## 3. Adaptive Terrain Structure Placement

A core requirement for believable PCG is adaptability: generated structures should blend naturally into the environment rather than feeling artificially placed. In this project, houses are generated within a user-defined **100×100 block build area** and are designed to integrate smoothly with the terrain **without flattening or deleting it**.

---

## 3.1 Terrain Evaluation

The pipeline begins by defining the 100×100 build region and converting it into a rectangular coordinate representation. A world slice corresponding to this region is loaded, enabling extraction of terrain heightmaps and additional world information.

To obtain a robust representation of terrain height, the **OCEAN_FLOOR** heightmap is used. This heightmap identifies the lowest solid block while ignoring air and water, allowing the system to prefer stable ground whenever possible. If vegetation clearance is required, the **MOTION_BLOCKING_NO_LEAVES** heightmap can be used.

![Build area heightmap](/assets/mgai/assignment_1/height.png)

The resulting elevation map provides an overview of terrain topology and serves as the basis for selecting an appropriate construction site.

---

## 3.2 Finding the Best Build Area

To select the most suitable location for construction, a custom `find_flattest_area` function scans the build region using a sliding window of size **n×n**. For each candidate window, the algorithm extracts height values from the terrain heightmap and computes the elevation variance. Lower variance corresponds to flatter terrain and is therefore preferred.

In addition to flatness, the algorithm evaluates water coverage using a reference water level. Regions with higher land coverage are prioritized, though partially water-covered regions may be selected if no fully land-based region is available. The algorithm also avoids overlap with previously used areas, enabling the generation of multiple structures within the same build region.

Once the optimal region is identified, trees within the footprint are removed to ensure a clear foundation. The region is then marked as used to prevent future overlap and to avoid unnecessary reloading of world slices.

![Best suitable region for generation in 100×100 area (heightmap)](/assets/mgai/assignment_1/best_area_height.png)
![Best suitable region for generation in 100×100 area (heightmap gradient)](/assets/mgai/assignment_1/best_area_gradient.png)

Visualizing both the heightmap and its gradient highlights that the selected region minimizes elevation variance and local slope, improving structural stability and believability.

---

## 3.3 Building the Structure Base

After selecting the optimal build location, the system constructs an **elevated platform** that serves as a flat surface for the house. The platform size is randomly sampled from a predefined set to introduce variation across iterations.

The platform is placed at the height of the highest terrain point within the selected region and built using randomly selected concrete colors. To support the platform without altering the terrain, **stone brick pillars** are placed at regular intervals and extended downward until they reach the ground.

![Elevated platform supported by adaptive pillars](/assets/mgai/assignment_1/pilars_nice.png)

This approach allows the platform to adapt to uneven terrain, maintaining stability while preserving the underlying landscape.

If the selected region contains water, the platform is constructed at sea level, and pillars extend downward to the terrain below the water surface. This ensures stability and maintains environmental consistency.

![Elevated platform supported by adaptive pillars under water](/assets/mgai/assignment_1/water.png)

The same adaptive strategy is robust across dense forests and steep terrain.

![House built in trees region](/assets/mgai/assignment_1/trees.png)
![House built in high gradient region](/assets/mgai/assignment_1/hills.png)

---

## 4. House Building

The generated structure follows a **modern two-story architectural style** with a rooftop garden and a glass roof supported by four corner columns. The first floor contains a living room with a kitchen, library, coffee table, and music player. The second floor contains a bedroom, office desk, and seating area with a couch and small tables. Each floor is illuminated with corner candles to enhance atmosphere.

The total height of the house is determined by a randomly sampled floor height, ensuring vertical variation across iterations.

---

## 4.1 Random Structural Variables

Each generation samples structural parameters from predefined sets, including:

- Building area size within the 100×100 region  
- Platform dimensions  
- House width and length  
- Height per floor  
- Glass roof column height  

This controlled randomness produces visually distinct houses while preserving architectural coherence.

![Example of small house](/assets/mgai/assignment_1/small.png)
![Example of bigger house](/assets/mgai/assignment_1/bigger.png)

---

## 4.2 Exterior Appearance

Exterior appearance is controlled via **style-specific material palettes** representing distinct architectural themes. For each iteration, a style is randomly selected, and corresponding materials are applied to walls, corners, and floors.

This approach ensures high visual diversity while maintaining stylistic consistency within each generated house.

![Example of house using beach house style](/assets/mgai/assignment_1/house.png)
![Multiple house iterations with different styles](/assets/mgai/assignment_1/many.png)

---

## 4.3 Interior Generation

Interior decoration is procedurally generated across two floors and a rooftop terrace.

On the first floor, the living room contains a bookshelf with variable length and a coffee table with a randomly selected color. A kitchen and sink are placed on the opposite corner.

![Living room with variable size bookshelf and coffeetable](/assets/mgai/assignment_1/iving.png)
![Kitchen](/assets/mgai/assignment_1/kitchen.png)

On the second floor, the bedroom, office desk, couch, and table are placed with randomized offsets. Object positions shift within bounded ranges along both horizontal axes, introducing variation while preserving functional layout.

![Bedroom and office area](/assets/mgai/assignment_1/bedroom.png)
![Bedroom and office area second iteration](/assets/mgai/assignment_1/decor.png)

The rooftop terrace contains a garden covered by a glass roof of variable size. The number and type of flowers are randomly selected for each iteration.

![Garden](/assets/mgai/assignment_1/garden.png)

---

## 5. Results

Across multiple iterations, the system consistently produces houses that adapt to diverse terrain conditions without modifying the environment. Elevation variance minimization leads to stable placement, while adaptive pillar-supported platforms enable construction on slopes, forests, and water. Randomized structural parameters, style palettes, and interior layouts result in meaningful visual diversity without sacrificing coherence.

---

## 6. Conclusion

This project demonstrates a complete PCG pipeline for generating terrain-adaptive houses in Minecraft. By combining statistical terrain analysis, gradient-aware site selection, adaptive foundations, and constrained randomness, the system generates believable structures that feel naturally embedded in procedurally generated worlds. Future work could extend this approach to multi-building settlements, agent-based evaluation of playability, or learning-based selection of architectural styles and layouts.

---

## References

- van der Staaij, A., Preuss, M., & Salge, C. (2024). *Terrain-Adaptive PCGML in Minecraft*. IEEE Conference on Games.  
- Anthropic (2024). *Claude AI: Conversational and Generative AI Model*.  
- GDPC (Generative Design Python Client) documentation.
