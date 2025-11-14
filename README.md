# Cory Feign's Claude Code Plugin Marketplace

A collection of high-quality Claude Code plugins for developers debugging, monitoring, and optimizing their applications.

## Quick Start

### Install the Marketplace

Add this marketplace to Claude Code:

```bash
/plugin marketplace add cfeign/claude-code-marketplace
```

Then browse and install plugins:

```bash
/plugin
```

## Available Plugins

### Browser Logger Diagnostics
**Query and analyze real-time browser console logs directly from Claude Code**

- 🔍 Diagnose JavaScript errors, exceptions, and console messages
- 📊 Monitor network performance and identify slow requests
- 🏷️ Auto-detect and tag application names
- 💾 Local-only storage - all data stays on your machine
- ⚡ Zero performance impact

**Installation:**
```bash
/plugin install browser-logger-diagnostics@cfeign/claude-code-marketplace
```

**Quick Commands:**
- `/errors` - Show last 10 errors
- `/logs` - Show last 20 console logs
- `/network` - Show last 10 network requests
- `/slow` - Show requests slower than 1000ms
- `/failed` - Show failed requests (4xx/5xx)
- `/search` - Search logs by keyword

**Learn More:** See [Browser Logger Diagnostics README](./plugins/browser-logger-diagnostics/README.md)

---

## For Plugin Developers

### Add Your Plugin to This Marketplace

To include your plugin in this marketplace:

1. **Create your plugin** with the standard Claude Code plugin structure:
   ```
   my-plugin/
   ├── .claude-plugin/plugin.json
   ├── commands/
   ├── skills/
   └── README.md
   ```

2. **Submit a pull request** with your plugin in the `plugins/` directory

3. **Update marketplace.json**:
   ```json
   {
     "name": "my-plugin",
     "description": "What your plugin does",
     "source": "./plugins/my-plugin",
     "version": "1.0.0",
     "tags": ["category", "use-case"]
   }
   ```

4. **Merge and deploy** - Your plugin will be available immediately

### Plugin Guidelines

- **Single responsibility**: Each plugin does one thing well
- **Well documented**: Include clear README with examples
- **User-friendly**: Provide slash commands when possible
- **No external dependencies**: Keep plugins portable
- **MIT license**: Use MIT or compatible open source license

## How It Works

This marketplace uses a **monorepo structure** where:
- Each plugin is a directory under `plugins/`
- The `marketplace.json` catalog references local plugins
- All plugins are versioned together with the marketplace
- Easy to maintain and extend with new plugins

## Support & Feedback

- 🐛 [Report Issues](https://github.com/cfeign/claude-code-marketplace/issues)
- 💬 [Discuss Ideas](https://github.com/cfeign/claude-code-marketplace/discussions)
- 🌐 [Visit Website](https://coryfeign.com)

## License

All plugins in this marketplace are MIT licensed. See individual plugin READMEs for details.

---

**Built by developers, for developers.** Made with ❤️ by Cory Feign.
