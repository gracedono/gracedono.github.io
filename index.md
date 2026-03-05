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

<!-- PLACE THE SCRIPT HERE AT THE VERY BOTTOM -->
{% raw %}
<script>
  async function loadIssues() {
    // This part stays exactly as is
    const repo = "gracedono/YOUR-REPO-NAME"; // Replace with your actual repo name
    const api = `https://api.github.com{repo}/issues?state=open&labels=lab-demo`;

    try {
      const response = await fetch(api);
      const issues = await response.json();
      const container = document.getElementById('issue-container');

      if (!issues || issues.length === 0) {
        container.innerHTML = '<p>No open issues at this time.</p>';
        return;
      }

      let listHtml = '<ul>';
      issues.forEach(issue => {
        if (!issue.pull_request) {
          listHtml += `<li><a href="${issue.html_url}" target="_blank">${issue.title}</a></li>`;

          // Match with cards in inventory
          document.querySelectorAll('.demo-card').forEach(card => {
            const cardId = card.getAttribute('data-demo-id');
            if (issue.title.includes(`(${cardId})`)) {
              card.style.borderColor = '#d73a49';
              card.style.backgroundColor = '#fff5f5';
              card.querySelector('.issue-badge').style.display = 'block';
            }
          });
        }
      });
      container.innerHTML = listHtml + '</ul>';
    } catch (e) {
      container.innerHTML = '<p>Error loading tracker.</p>';
    }
  }
  document.addEventListener('DOMContentLoaded', loadIssues);
</script>
{% endraw %}

