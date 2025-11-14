# Cory Feign's Claude Code Plugin Marketplace

This is the marketplace configuration for hosting Claude Code plugins on coryfeign.com.

## What's Inside

- `marketplace.json` - The marketplace definition that lists all available plugins
- `SETUP.md` - This setup guide

## Setup Instructions

### Option 1: Git Repository on coryfeign.com (Recommended)

If your website supports git hosting (like Gitea, GitLab, or similar):

```bash
# On your server, create the repository directory
mkdir -p /var/www/coryfeign.com/tools/cc/plugins
cd /var/www/coryfeign.com/tools/cc/plugins

# Initialize as a bare git repository
git init --bare

# Or create a regular git repo and configure as bare later
git init
```

Then push from your local machine:

```bash
# From your local machine
cd /path/to/marketplace-coryfeign

# Initialize and commit
git init
git config user.name "Cory Feign"
git config user.email "your-email@example.com"
git add marketplace.json
git commit -m "Initial marketplace setup"

# Add remote pointing to your server
git remote add origin ssh://your-server.com/var/www/coryfeign.com/tools/cc/plugins
# Or if using HTTPS:
git remote add origin https://coryfeign.com/tools/cc/plugins.git

# Push to server
git push -u origin main
```

### Option 2: GitHub Repository (Simpler)

Create a public GitHub repository for the marketplace:

```bash
# Create repo on GitHub at github.com/cfeign/coryfeign-plugins (or similar)

# From your local machine
cd /path/to/marketplace-coryfeign
git init
git config user.name "Cory Feign"
git config user.email "your-email@example.com"
git add marketplace.json
git commit -m "Initial marketplace setup"
git remote add origin https://github.com/cfeign/coryfeign-plugins.git
git branch -M main
git push -u origin main
```

Then update the installation URL to:
```bash
/plugin marketplace add https://github.com/cfeign/coryfeign-plugins.git
```

## How Users Install

After setting up the marketplace, users add it to Claude Code:

```bash
# If hosted on coryfeign.com
/plugin marketplace add https://coryfeign.com/tools/cc/plugins.git

# If hosted on GitHub
/plugin marketplace add https://github.com/cfeign/coryfeign-plugins.git
```

Then they can browse and install plugins:
```bash
/plugin
```

## Adding New Plugins

When you create a new plugin:

1. Create the plugin repository on GitHub
2. Add an entry to `marketplace.json`:
   ```json
   {
     "name": "new-plugin",
     "description": "Plugin description",
     "source": "github:cfeign/new-plugin",
     ...
   }
   ```
3. Commit and push to the marketplace repository:
   ```bash
   git add marketplace.json
   git commit -m "Add new-plugin to marketplace"
   git push origin main
   ```

## Marketplace.json Format

The marketplace file is simple JSON with these fields:

- `name` - Marketplace name (kebab-case)
- `description` - What this marketplace contains
- `version` - Marketplace version (semantic versioning)
- `owner` - Object with `name` and `url`
- `plugins` - Array of plugin entries

Each plugin entry needs:
- `name` - Plugin name
- `description` - Plugin description
- `source` - Source location (e.g., `github:cfeign/plugin-repo`)
- `version` - Plugin version
- `author`, `homepage`, `repository`, `license` - Metadata
- `keywords`, `tags` - For discoverability

## Troubleshooting

### Marketplace not found

Ensure:
1. Repository is accessible at the URL
2. Repository is public (if on GitHub)
3. `marketplace.json` exists in repository root
4. JSON is valid (use [jsonlint.com](https://jsonlint.com))

### Plugins not showing

Check:
1. Plugin entries in marketplace.json are valid
2. Plugin repositories exist and are public
3. Plugin version field matches repo version (optional but recommended)

### Plugin installation fails

- Verify plugin repository exists at the `source` URL
- Check plugin repository has `.claude-plugin/plugin.json` file
- Ensure repository is public on GitHub

## Website Integration

To link this from coryfeign.com:

```html
<!-- Add to your website -->
<a href="https://coryfeign.com/tools/cc/plugins">Claude Code Plugins</a>
```

Or document it in your site's tools/resources section:

```markdown
## Claude Code Plugins

Install my plugin marketplace in Claude Code:

```bash
/plugin marketplace add https://coryfeign.com/tools/cc/plugins.git
```

Then browse available plugins:

```bash
/plugin
```
```

## License

MIT License - Same as included plugins

## Support

For issues with:
- **Marketplace setup**: See the troubleshooting section above
- **Browser Logger plugin**: https://github.com/cfeign/browser-logger-plugin/issues
- **General Claude Code plugins**: https://code.claude.com/docs
