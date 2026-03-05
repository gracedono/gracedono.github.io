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

<!-- 1. Move this line OUTSIDE the raw block so Jekyll can fill it in -->
<script>
  const repoPath = "{{ site.github.repository_nwo }}"; 
</script>

{% raw %}
<script>
async function loadIssues() {
  const container = document.getElementById('issue-container');
  if (!container) return;

  // 2. Added a "/" before ${repoPath} to fix the URL construction
  const apiUrl = `https://api.github.com{repoPath}/issues?state=open&labels=lab-demo`;

  try {
    console.log("Fetching from:", apiUrl);
    const response = await fetch(apiUrl);
    
    if (!response.ok) {
      container.innerHTML = `<p style="color:red;">❌ GitHub API Error: ${response.status}</p>`;
      return;
    }
    
    const issues = await response.json();
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
    container.innerHTML = '<p style="color:red;">❌ Connection error. Check the console for details.</p>';
    console.error("Detailed Error:", e);
  }
}
document.addEventListener('DOMContentLoaded', loadIssues);
</script>
{% endraw %}
