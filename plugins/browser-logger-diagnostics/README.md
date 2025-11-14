# Browser Logger Diagnostics Plugin

A Claude Code plugin for querying and analyzing real-time browser console logs captured by the [Browser Logger](https://github.com/yourusername/browser-logger) extension.

**Diagnose JavaScript errors, monitor application performance, and troubleshoot issues from your browser directly within Claude Code.**

## Quick Start

### Prerequisites

- **Browser**: Chrome, Brave, or Edge (Manifest V3 compatible)
- **Runtime**: Node.js 18+ and Bun
- **Browser Logger**: Must be installed and running (see setup below)

### 1. Setup Browser Logger Backend

The plugin requires the Browser Logger backend service running locally.

```bash
# Clone or navigate to your browser-logger project
cd /path/to/browser-logger

# Install dependencies
bun install

# Start the backend server
bun run logger start
```

You should see:
```
✓ Logger started on http://localhost:3001
[Server] Listening on http://localhost:3001
```

To verify it's running:
```bash
curl http://localhost:3001/health
# Returns: {"status":"ok"}
```

### 2. Load the Browser Extension

1. Open your browser and navigate to the extensions page:
   - **Chrome/Edge**: `chrome://extensions/`
   - **Brave**: `brave://extensions/`

2. Enable **"Developer mode"** (toggle in top right corner)

3. Click **"Load unpacked"**

4. Navigate to your browser-logger project and select the `extension/` folder

5. You should see "Console & Network Logger" appear in your extensions list

### 3. Install the Plugin in Claude Code

**Add Cory Feign's plugin marketplace** (includes this and future plugins):

```bash
/plugin marketplace add https://coryfeign.com/tools/cc/plugins.git
```

Then browse and install:
```bash
/plugin
```

**Or install directly from the plugin repository:**
```bash
/plugin install cfeign/browser-logger-plugin
```

## Usage

### Quick Commands

Once installed, use these slash commands:

- `/errors` - Show last 10 errors with stack traces
- `/logs` - Show last 20 console logs
- `/network` - Show last 10 network requests
- `/slow` - Show network requests slower than 1000ms
- `/failed` - Show failed requests (4xx/5xx status codes)
- `/recent` - Show last 10 console logs
- `/search` - Search logs by keyword (customize in command)

### Manual Queries

For more advanced queries, use the built-in skill:

```bash
# From your browser-logger project directory:
bun run query -- "SELECT timestamp, level, message, url FROM console_logs WHERE level='error' ORDER BY timestamp DESC LIMIT 20"
```

### Example Workflows

**Debug an authentication error:**
```bash
/errors
# Find the error in your app's logs, then use Claude to help understand it
```

**Monitor API performance:**
```bash
/slow
# Check which API endpoints are taking >1 second
```

**Search for specific issues:**
```bash
bun run query -- "SELECT * FROM console_logs WHERE message LIKE '%null reference%' ORDER BY timestamp DESC"
```

**View logs by app:**
```bash
bun run query -- "SELECT app_name, COUNT(*) FROM console_logs GROUP BY app_name"
```

## How It Works

### Architecture

```
Your Web App (localhost:3000, etc.)
          ↓
   Browser Extension (Manifest V3)
   - Captures console.log, console.error, etc.
   - Monitors network requests
   - Auto-detects app name
          ↓
   Browser Logger Backend (localhost:3001)
   - HTTP API: POST /api/logs, POST /api/network
   - SQLite Database: console-logs.db
          ↓
   Claude Code Plugin
   - Queries console_logs and network_logs tables
   - Provides slash commands and diagnostics skill
```

### Data Flow

1. **Console Capture**: Extension injects a script that wraps console methods
2. **Batching**: Logs are batched (max 20 or 200ms interval) for efficiency
3. **Transmission**: Batches sent to `http://localhost:3001/api/logs`
4. **Storage**: SQLite database stores logs with 5-minute rolling retention
5. **Querying**: Plugin queries database via `bun run query` command

### Key Features

- **100% Local**: No cloud services, all data stays on your machine
- **Real-time**: Logs captured as they happen
- **Auto-detection**: Apps are tagged by port, domain, or `window.__APP_NAME__`
- **Persistent**: Logs survive page reloads and tab refreshes
- **Filtered**: Noise (React DevTools, HMR, tracking) automatically filtered

## Configuration

### Backend Configuration

Edit environment variables before starting the server:

```bash
# .env file in browser-logger project root
LOG_RETENTION_MINUTES=10          # Keep logs for 10 minutes (default: 5)
CLEANUP_INTERVAL_SECONDS=60       # Cleanup every minute (default: 30)
PORT=3002                          # Use custom port (default: 3001)
```

Or set when starting:
```bash
LOG_RETENTION_MINUTES=10 bun run logger start
```

### Extension Configuration

**Set custom app name** (recommended for multiple apps):

In your app's main HTML or JavaScript file:
```html
<script>
  window.__APP_NAME__ = 'my-app';
</script>
```

Or use a meta tag:
```html
<meta name="app-name" content="my-app">
```

Or via Vite (if using Vite):
```javascript
window.__APP_NAME__ = import.meta.env.VITE_APP_NAME;
```

## Troubleshooting

### Backend Issues

**Backend won't start:**
```bash
# Check if port 3001 is in use
lsof -i :3001

# Check logs
bun run logger logs

# Try a different port
PORT=3002 bun run logger start
```

**"Server not responding" error:**
```bash
# Check if server is running
bun run logger status

# Restart it
bun run logger restart

# Verify health check
curl http://localhost:3001/health
```

### Extension Issues

**Extension not loading:**
1. Go to `chrome://extensions/`
2. Make sure "Developer mode" is ON (top right toggle)
3. Click the reload icon (↻) on the "Console & Network Logger" extension
4. Refresh the web page you're testing

**No logs appearing:**
1. Verify backend is running: `bun run logger status`
2. Verify extension is loaded: Check `chrome://extensions/`
3. Open DevTools (F12) on the page you're testing
4. Check the extension's service worker console for errors:
   - Right-click extension icon → "Inspect"
5. Try reloading the page: `Ctrl+R` or `Cmd+R`

**Only network logs, no console logs:**
- Make sure page scripts have loaded (wait a few seconds)
- Check that the extension icon shows it's active (should have blue badge)
- Try manually enabling from extension icon
- Check browser console (F12) for any errors

**Logs disappeared:**
- Logs auto-cleanup after 5 minutes (or configured retention time)
- To extend retention, set `LOG_RETENTION_MINUTES=30` before starting
- Or query them before they expire: `/errors`

### Database Issues

**Database is too large:**
```bash
# Check size
ls -lh /path/to/browser-logger/console-logs.db

# Manual cleanup (older than 1 hour)
bun run query -- "DELETE FROM console_logs WHERE created_at < datetime('now', '-1 hour')"

# Reset entire database
rm console-logs.db && bun run logger restart
```

**Getting "database locked" errors:**
- This usually means the server crashed or is hung
- Stop the server: `bun run logger stop`
- Restart it: `bun run logger start`

## Browser Support

| Browser | Support | Notes |
|---------|---------|-------|
| Chrome 88+ | ✅ | Manifest V3 supported |
| Edge 88+ | ✅ | Chromium-based |
| Brave | ✅ | Privacy-friendly |
| Firefox | ❌ | No Manifest V3 support |
| Safari | ❌ | Different extension format |

## Performance

The extension is designed for zero overhead:

- **<1ms per console call** - Minimal impact on app performance
- **Batched transmission** - Groups logs before sending to reduce network calls
- **Filtered noise** - React DevTools, HMR, tracking domains filtered automatically
- **Circular buffers** - Old logs auto-removed to keep database small

**Verified**: No observable impact on page load time or runtime performance.

## API Reference

The plugin uses the Browser Logger backend API. Common endpoints:

```bash
# Get recent console logs
curl "http://localhost:3001/api/logs?limit=20"

# Get errors only
curl "http://localhost:3001/api/logs?level=error&limit=10"

# Get logs from specific app
curl "http://localhost:3001/api/logs?app_name=myapp&limit=20"

# Get network logs
curl "http://localhost:3001/api/network?limit=10"

# Search logs
curl "http://localhost:3001/api/logs/search?q=timeout&limit=20"
```

## Contributing

Found a bug or want to improve the plugin? Contributions welcome!

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## License

MIT License - See LICENSE file for details

## Support

- 📖 [Browser Logger Docs](https://github.com/cfeign/browser-logger)
- 🐛 [Report Issues](https://github.com/cfeign/browser-logger-plugin/issues)
- 💬 [Discussions](https://github.com/cfeign/browser-logger-plugin/discussions)
- 🌐 [Website](https://coryfeign.com)

---

**Built for developers who want complete visibility into their browser's behavior. No cloud, no telemetry, 100% local.**
