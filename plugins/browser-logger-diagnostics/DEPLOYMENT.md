# Deployment Guide

This guide explains how to deploy the plugin to your GitHub repository and marketplace.

## Overview

The plugin is distributed through two repositories:

1. **Plugin Repository**: `github.com/cfeign/browser-logger-plugin`
   - Contains the complete plugin code
   - Synced from this `browser-logger-plugin/` directory

2. **Marketplace Repository**: `coryfeign.com/tools/cc/plugins`
   - Contains `marketplace.json` listing all your plugins
   - Users add via: `/plugin marketplace add https://coryfeign.com/tools/cc/plugins.git`

## Step 1: Create Plugin Repository on GitHub

```bash
# Create a new repository at github.com/cfeign/browser-logger-plugin
# (Do this via GitHub's web interface)

# Clone it locally
git clone https://github.com/cfeign/browser-logger-plugin.git
cd browser-logger-plugin

# Copy the plugin files into this repository
# Copy everything from browser-logger/browser-logger-plugin/
# into the root of this repository
```

Directory structure after copying:
```
browser-logger-plugin/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── commands/
├── skills/
├── README.md
└── DEPLOYMENT.md
```

```bash
# Commit and push
git add .
git commit -m "Initial plugin release"
git push origin main
```

## Step 2: Set Up Marketplace on Your Website

The marketplace is a simple git repository with a `marketplace.json` file.

### Option A: Create a Git Repository on coryfeign.com

If your website supports git repositories:

```bash
# Create directory structure on your server
mkdir -p /var/www/coryfeign.com/tools/cc/plugins
cd /var/www/coryfeign.com/tools/cc/plugins

# Initialize git repository
git init --bare
```

### Option B: Use GitHub for Marketplace (Simpler)

You can also create a repository just for the marketplace:
- Repository: `github.com/cfeign/plugins` or `github.com/cfeign/coryfeign-plugins`
- Then use: `/plugin marketplace add https://github.com/cfeign/plugins.git`

### Create marketplace.json

In the marketplace repository root:

```json
{
  "name": "coryfeign-plugins",
  "description": "Claude Code plugins by Cory Feign",
  "version": "1.0.0",
  "owner": {
    "name": "Cory Feign",
    "url": "https://coryfeign.com"
  },
  "plugins": [
    {
      "name": "browser-logger-diagnostics",
      "description": "Query and analyze browser console logs from local SQLite database.",
      "source": "github:cfeign/browser-logger-plugin",
      "version": "1.0.0",
      "author": {
        "name": "Cory Feign",
        "url": "https://github.com/cfeign"
      },
      "homepage": "https://github.com/cfeign/browser-logger-plugin",
      "repository": "https://github.com/cfeign/browser-logger-plugin",
      "license": "MIT",
      "keywords": ["browser-logger", "debugging", "console-logs"],
      "tags": ["debugging", "development", "monitoring"]
    }
  ]
}
```

```bash
# Commit and push
git add marketplace.json
git commit -m "Add browser-logger-diagnostics plugin"
git push origin main
```

## Step 3: Test Installation

After deploying both repositories:

```bash
# Users install via Claude Code
/plugin marketplace add https://coryfeign.com/tools/cc/plugins.git

# Browse and install
/plugin

# Or install directly
/plugin install cfeign/browser-logger-plugin
```

## Adding Future Plugins

When you create new plugins:

1. Create a new repository: `github.com/cfeign/your-plugin`
2. Add to `marketplace.json`:
   ```json
   {
     "name": "your-plugin",
     "source": "github:cfeign/your-plugin",
     ...
   }
   ```
3. Push the updated marketplace

## Versioning

### Plugin Versions

Update `version` in `.claude-plugin/plugin.json` when releasing:
- `1.0.0` → `1.1.0` for new features
- `1.0.0` → `1.0.1` for bug fixes

### Marketplace Version

Update `version` in `marketplace.json` when adding/updating plugins.

## Troubleshooting

### Plugin not found in marketplace

Check that:
1. Marketplace is accessible: `git clone https://coryfeign.com/tools/cc/plugins.git`
2. `marketplace.json` is valid JSON
3. Plugin repository exists at `github.com/cfeign/browser-logger-plugin`
4. `source` field matches the actual repository URL

### Installation fails with "404"

- Plugin repository may not be public on GitHub
- Check GitHub repository visibility settings
- Ensure repository name matches in `marketplace.json`

### Commands not working after installation

- Plugin must be in correct directory structure
- Verify `.claude-plugin/plugin.json` has correct `commands` path
- Restart Claude Code after installing plugin
