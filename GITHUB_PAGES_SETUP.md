# GitHub Pages Setup Guide

This guide provides step-by-step instructions for setting up GitHub Pages for your projects to ensure smooth deployment.

## Prerequisites

1. **Git installed** - Check with `git --version`
2. **GitHub CLI installed** - Check with `gh --version`
3. **Authenticated with GitHub** - Run `gh auth login` if not already authenticated

## Quick Setup Process

### 1. Create and Initialize Repository

```bash
# Create project directory
mkdir your-project-name
cd your-project-name

# Initialize Git repository
git init

# Add your files
git add .

# Commit changes
git commit -m "Initial commit"
```

### 2. Configure Git User (if not already configured)

```bash
git config --global user.email "your-email@example.com"
git config --global user.name "Your Name"
```

### 3. Create GitHub Repository and Push

```bash
# Create repository on GitHub and push
gh repo create your-repo-name --public --push --source . --remote origin
```

### 4. Set Up GitHub Pages

```bash
# Enable GitHub Pages
gh api --method POST -H "Accept: application/vnd.github.v3+json" /repos/{owner}/{repo}/pages -f "source[branch]=main"
```

Note: Replace `{owner}` with your GitHub username and `{repo}` with your repository name.
If your default branch is `master` instead of `main`, adjust accordingly.

## Important File Naming

For a simple static site:
- Name your main HTML file `index.html` - this will be served as the homepage
- Place all files in the root directory or in `/docs` folder

## Alternative: GitHub Pages Configuration via Repository Settings

If the API approach doesn't work:

1. Go to your repository on GitHub
2. Click on **Settings** tab
3. Scroll down to **Pages** section
4. Under **Source**, select:
   - **Deploy from a branch** 
   - Choose your branch (main or master)
   - Select **/ (root)** or **/docs** as folder
5. Click **Save**

## Troubleshooting

### Common Issues

1. **Pages not deploying**: 
   - Check that your branch name is correct (main vs master)
   - Ensure your HTML file is named `index.html`
   - Verify in GitHub Settings > Pages that the source is correctly configured

2. **Custom domain issues**:
   - Add a `CNAME` file with your domain in the root of your repository
   - Configure DNS settings with your domain provider

3. **Authentication problems**:
   - Run `gh auth status` to check authentication
   - Re-authenticate with `gh auth login` if needed

### Checking Deployment Status

```bash
# View deployment status
gh api repos/{owner}/{repo}/deployments

# View deployment statuses
gh api repos/{owner}/{repo}/deployments/{deployment_id}/statuses
```

## Best Practices

1. **Use semantic versioning** for your projects
2. **Include a README.md** with project information
3. **Add a .gitignore** file to exclude unnecessary files
4. **Use relative paths** in your HTML/CSS/JS for proper linking
5. **Test locally** before deploying:
   ```bash
   # For simple HTML projects, you can use:
   npx serve .
   # Or with Python:
   python -m http.server 8000
   ```

## Example Workflow Script

Create a simple deployment script for future projects:

```bash
#!/bin/bash
# deploy.sh

REPO_NAME=$1
if [ -z "$REPO_NAME" ]; then
    echo "Usage: ./deploy.sh <repository-name>"
    exit 1
fi

# Create and initialize repository
mkdir $REPO_NAME
cd $REPO_NAME
git init

# Add your project files here
# cp /path/to/your/files/* .

# Commit files
git add .
git commit -m "Initial commit"

# Create GitHub repository and push
gh repo create $REPO_NAME --public --push --source . --remote origin

# Set up GitHub Pages
gh api --method POST -H "Accept: application/vnd.github.v3+json" /repos/{owner}/$REPO_NAME/pages -f "source[branch]=main"

echo "Repository created at: https://github.com/{owner}/$REPO_NAME"
echo "GitHub Pages will be available at: https://{owner}.github.io/$REPO_NAME/"
```

## Useful Commands

```bash
# Check authentication status
gh auth status

# View repository in browser
gh repo view --web

# List repository settings
gh api repos/{owner}/{repo}

# Update repository settings
gh api --method PATCH /repos/{owner}/{repo} -f homepage="https://your-site-url"
```

## Resources

- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [GitHub CLI Documentation](https://cli.github.com/manual/)
- [GitHub REST API](https://docs.github.com/en/rest)