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

<script id="github-issue-loader">
(function() {
  const container = document.getElementById('issue-container');
  if (!container) return;

  // We are NOT using variables here. This is one solid, unchangeable string.
  const myUrl = "https://api.github.com";

  console.log("FINAL TEST - Fetching from:", myUrl);

  fetch(myUrl)
    .then(response => {
      if (!response.ok) throw new Error("GitHub API Error: " + response.status);
      return response.json();
    })
    .then(issues => {
      if (!Array.isArray(issues)) {
        container.innerHTML = "❌ Unexpected data format from GitHub.";
        return;
      }

      // Filter out Pull Requests
      const actualIssues = issues.filter(i => !i.pull_request);

      if (actualIssues.length === 0) {
        container.innerHTML = "✅ No open issues reported.";
        return;
      }

      let listHtml = "<ul>";
      actualIssues.forEach(issue => {
        listHtml += `<li><a href="${issue.html_url}" target="_blank">${issue.title}</a></li>`;
        
        // Highlight matching cards
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
      container.innerHTML = listHtml + "</ul>";
    })
    .catch(err => {
      console.error("Fetch failed:", err);
      container.innerHTML = `<p style="color:red;">❌ ${err.message}. Ensure the repo is Public and Issues are enabled.</p>`;
    });
})();
</script>
