---
permalink: /
title: "About Me"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---

{% include base_path %}

I am a 2nd year doctoral student of [Miyazaki Lab](https://www.lsc.comp.isct.ac.jp/), Institute of Science Tokyo. Before joining Miyazaki Lab, I obtained my bachelor degree in Dalian University of Technology, supervised by [Prof. Weimin Wang](https://wei-min.wang/). My research has been focusing on the large-scale point cloud processing algorithm acceleration with GPU & CPU.


News
----

2026.10 Present our work on NDBC 2026.  
2026.3 Present our work on DEIM 2026.  
2025.5 One paper accepted by ICIP 2025.  
2025.3 Present our current work on DEIM 2025.  
2024.8 Start my internship in SECOM IS Lab. Research topic: LiDAR Upsampling.  
2024.7 I will join SECOM IS Lab as an intern in Aug!  
2024.6 I have joined the OCRD Team in Tokyo Tech!  
2024.4 One paper accecpted by ICASSP 2024.


Publications
----

{% for post in site.publications reversed %}
  {% include publication-single.html heading="h3" %}
{% endfor %}


Education
----

* Ph.D in Computer Science, Institute of Science Tokyo, 2028 (expected)
* M.S. in Computer Science, Institute of Science Tokyo, 2025
* B.S. in Digital Media Technology, Dalian University of Technology, 2023


Work Experience
----

* Oct 2025 - Present: Research Assistant
  * Miyazaki Lab , Institute of Science Tokyo, Japan
  * Duties included: Tagging issues
  * Supervisor: Jun Miyazaki

* Jun 2026 - Dec 2026: Learning Asistant
  * OCRD, Institute of Science Tokyo, Japan
  * Duties included: Architecture design of AI TA with openclaw, RAG and slack. 

* Aug 2024 - Oct 2024: Research Intern
  * Secom IS Lab, Japan
  * Duties includes: Optimize LiDAR upsampling algorithms
