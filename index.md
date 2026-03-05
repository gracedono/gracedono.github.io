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
    // 1. Define the container FIRST so it's always available for errors
    const container = document.getElementById('issue-container');
    
    // 2. Ensure the REPO path is correct (username/repo-name)
    const repo = "gracedono/YOUR-REPO-NAME"; 
    const api = `https://api.github.com{repo}/issues?state=open&labels=lab-demo`;

    try {
      if (!container) return; // Exit if the HTML element is missing

      const response = await fetch(api);
      
      if (!response.ok) {
        container.innerHTML = `<p style="color:red;">❌ Error: ${response.status}. Check repo name in script.</p>`;
        return;
      }

      const issues = await response.json();
      const actualIssues = issues.filter(i => !i.pull_request);

      if (actualIssues.length === 0) {
        container.innerHTML = '<p>✅ No open issues at this time.</p>';
        return;
      }

      let listHtml = '<ul>';
      actualIssues.forEach(issue => {
        listHtml += `<li><a href="${issue.html_url}" target="_blank">${issue.title}</a></li>`;

        // Highlight matching cards in the inventory
        document.querySelectorAll('.demo-card').forEach(card => {
          const cardId = card.getAttribute('data-demo-id');
          if (cardId && issue.title.includes(`(${cardId})`)) {
            card.style.borderColor = '#d73a49';
            card.style.backgroundColor = '#fff5f5';
            const badge = card.querySelector('.issue-badge');
            if (badge) badge.style.display = 'block';
          }
        });
      });
      
      container.innerHTML = listHtml + '</ul>';

    } catch (e) {
      if (container) {
        container.innerHTML = '<p style="color:red;">❌ Connection error. Check your internet.</p>';
      }
      console.error("Tracker Error:", e);
    }
  }

  document.addEventListener('DOMContentLoaded', loadIssues);
</script>
{% endraw %}
