## Lab Inventory

### 1. Van de Graaff Generator
*Status: Functional*  
[Report an Issue with this Demo](https://github.com:)

### 2. Laser Optics Kit
*Status: Functional*  
[Report an Issue with this Demo](https://github.com:)

## Live Issue Tracker
Below is the list of open issues pulled directly from the GitHub API:

<div id="issue-container">
  <p>Loading open issues...</p>
</div>

<div id="issue-container">
  <p>Loading open issues...</p>
</div>

<script>
  async function fetchIssues() {
    const owner = 'gracedono'; // Replace with your GitHub username
    const repo = 'gracedono.github.io'; // Replace with your repository name
    const container = document.getElementById('issue-container');
    
    // API endpoint for listing repository issues
    const url = `https://api.github.com/repos/${owner}/${repo}/issues?state=open`;

    try {
      const response = await fetch(url);
      
      if (!response.ok) {
        throw new Error(`GitHub API error: ${response.status}`);
      }

      const issues = await response.json();

      if (issues.length === 0) {
        container.innerHTML = '<p>No open issues found.</p>';
        return;
      }

      // Filter out Pull Requests (GitHub API returns PRs as issues)
      const filteredIssues = issues.filter(issue => !issue.pull_request);

      let html = '<ul>';
      filteredIssues.forEach(issue => {
        html += `
          <li>
            <strong>#${issue.number}</strong> 
            <a href="${issue.html_url}" target="_blank">${issue.title}</a>
            <br>
            <small>Opened by ${issue.user.login} on ${new Date(issue.created_at).toLocaleDateString()}</small>
          </li>`;
      });
      html += '</ul>';
      
      container.innerHTML = html;
    } catch (error) {
      container.innerHTML = `<p style="color: red;">Error: ${error.message}</p>`;
      console.error('Fetch error:', error);
    }
  }

  // Run the function when the page loads
  document.addEventListener('DOMContentLoaded', fetchIssues);
</script>

