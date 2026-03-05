---
layout: default
title: Lab Inventory Dashboard
---

# 🔬 Lab Demo Inventory
Welcome! Below is a live list of all available laboratory demonstrations.

<div class="demo-list">
  {% for demo in site.demos %}
    <div class="demo-card" data-demo-id="{{ demo.demo_id }}" style="border: 1px solid #ddd; padding: 15px; margin-bottom: 10px; border-radius: 8px;">
      <h3><a href="{{ demo.url }}">{{ demo.title }}</a></h3>
      <p>🆔 <strong>ID:</strong> {{ demo.demo_id }}</p>
      <!-- JavaScript will un-hide this if an issue exists -->
      <p class="issue-badge" style="display:none; color: #d73a49; font-weight: bold;">⚠️ Issue Reported</p>
      <a href="{{ demo.url }}">View Details</a>
    </div>
  {% endfor %}
</div>

<hr>
<h2>⚠️ Current Reported Issues</h2>
<div id="issue-container"><p>Loading...</p></div>

<script>
  async function loadIssues() {
    const repo = 'gracedono/{{ site.github.repository_name }}';
    const api = `https://api.github.com{repo}/issues?state=open&labels=lab-demo`;

    try {
      const response = await fetch(api);
      const issues = await response.json();
      const container = document.getElementById('issue-container');

      if (issues.length === 0) {
        container.innerHTML = '<p>No open issues.</p>';
        return;
      }

      let listHtml = '<ul>';
      issues.forEach(issue => {
        listHtml += `<li><a href="${issue.html_url}">${issue.title}</a></li>`;

        // Look for the Demo ID in the issue title, e.g., "(EA-1)"
        document.querySelectorAll('.demo-card').forEach(card => {
          if (issue.title.includes(`(${card.dataset.demoId})`)) {
            card.style.borderColor = '#d73a49';
            card.querySelector('.issue-badge').style.display = 'block';
          }
        });
      });
      container.innerHTML = listHtml + '</ul>';
    } catch (e) { console.error("API Error", e); }
  }
  document.addEventListener('DOMContentLoaded', loadIssues);
</script>

