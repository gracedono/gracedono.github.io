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
(function() {
  async function loadIssues() {
    const container = document.getElementById('issue-container');
    if (!container) return;

    // We are using a completely unique variable name to prevent conflicts
    const finalApiUrl = "https://api.github.com";

    try {
      console.log("Attempting to fetch from:", finalApiUrl);
      const response = await fetch(finalApiUrl);
      
      if (!response.ok) {
        const errorData = await response.json();
        container.innerHTML = `<p style="color:red;">❌ GitHub Error: ${response.status} (${errorData.message})</p>`;
        return;
      }
      
      const issues = await response.json();

      if (!Array.isArray(issues)) {
        container.innerHTML = `<p style="color:red;">❌ Error: Received unexpected data from GitHub.</p>`;
        return;
      }

      const actualIssues = issues.filter(i => !i.pull_request);

      if (actualIssues.length === 0) {
        container.innerHTML = '<p>✅ No open issues reported.</p>';
        return;
      }

      let listHtml = '<ul>';
      actualIssues.forEach(issue => {
        listHtml += `<li><a href="${issue.html_url}" target="_blank">${issue.title}</a></li>`;
        
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
      container.innerHTML = '<p style="color:red;">❌ Connection error. See console for details.</p>';
      console.error("Fetch failed:", e);
    }
  }

  // Run the function immediately
  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', loadIssues);
  } else {
    loadIssues();
  }
})();
</script>

