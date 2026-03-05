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
  const container = document.getElementById('issue-container');
  if (!container) return;

  // We are building the URL in pieces so nothing can cut it off
  const part1 = "https://api.github.com";
  const part2 = "/repos/gracedono/gracedono.github.io";
  const part3 = "/issues?state=open&labels=lab-demo";
  const finalUrl = part1 + part2 + part3;

  console.log("SENDING REQUEST TO:", finalUrl);

  fetch(finalUrl)
    .then(res => {
      if (!res.ok) throw new Error("Status: " + res.status);
      return res.json();
    })
    .then(data => {
      if (!Array.isArray(data)) {
        container.innerHTML = "❌ GitHub returned an error object instead of a list.";
        return;
      }
      
      const issues = data.filter(i => !i.pull_request);
      if (issues.length === 0) {
        container.innerHTML = "✅ No open issues reported.";
        return;
      }

      let html = "<ul>";
      issues.forEach(is => {
        html += `<li><a href="${is.html_url}" target="_blank">${is.title}</a></li>`;
      });
      container.innerHTML = html + "</ul>";
    })
    .catch(e => {
      console.error("ERROR:", e);
      container.innerHTML = "<p style='color:red;'>❌ Connection Error. Check console.</p>";
    });
})();
</script>

