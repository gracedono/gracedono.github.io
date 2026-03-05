---
layout: default
title: Lab Inventory Dashboard
---

# 🔬 Lab Demo Inventory
Welcome! Below is a live list of all available laboratory demonstrations.

<div class="demo-list">
  {% for demo in site.demos %}
    <div style="border: 1px solid #ddd; padding: 15px; margin-bottom: 10px; border-radius: 8px;">
      <h3><a href="{{ demo.url }}">{{ demo.title }}</a></h3>
      <p>🆔 <strong>ID:</strong> {{ demo.demo_id }}</p>
      <a href="{{ demo.url }}">View Details & Instructions</a>
    </div>
  {% endfor %}
</div>

<hr>

## ⚠️ Current Reported Issues
<!-- This is where your JavaScript tracker script from before stays -->
<div id="issue-container">
  <p>Loading open issues...</p>
</div>

