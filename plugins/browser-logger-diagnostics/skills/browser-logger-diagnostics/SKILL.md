---
name: Browser Logger Diagnostics
description: Query and analyze browser console logs from the SQLite database to diagnose JavaScript errors, exceptions, and console messages captured by the browser extension. Use this when users ask for recent errors, want to search logs, or need to troubleshoot console issues across multiple applications.
---

# Browser Logger Diagnostics

This skill helps you quickly retrieve and analyze browser console logs from the local SQLite database. It's designed for diagnosing JavaScript errors, unhandled promise rejections, and console messages captured in real-time from any webpage.

## Key Information

- **Database location**: `console-logs.db` (SQLite via sql.js, stored in browser-logger project root)
- **Query tool**: `bun run query` command (run from browser-logger project directory)
- **Data retention**: 5-minute rolling window (auto-cleanup every 30 seconds)
- **Table**: `console_logs` with columns: `id`, `timestamp`, `level`, `message`, `stack_trace`, `url`, `app_name`, `created_at`

## Common Diagnostic Queries

### Show Recent Errors (Last 5 Minutes)
```bash
# Run from the browser-logger project directory
bun run query -- "SELECT timestamp, level, message, url FROM console_logs WHERE level='error' ORDER BY timestamp DESC LIMIT 20"
```

### Show All Recent Logs
```bash
bun run query -- "SELECT timestamp, level, message FROM console_logs ORDER BY timestamp DESC LIMIT 50"
```

### Count Errors by Level
```bash
bun run query -- "SELECT level, COUNT(*) as count FROM console_logs GROUP BY level ORDER BY count DESC"
```

### Search for Specific Error
```bash
bun run query -- "SELECT timestamp, level, message, stack_trace FROM console_logs WHERE message LIKE '%search_term%' ORDER BY timestamp DESC LIMIT 20"
```

### Get Errors with Stack Traces
```bash
bun run query -- "SELECT timestamp, message, stack_trace FROM console_logs WHERE level='error' AND stack_trace IS NOT NULL ORDER BY timestamp DESC LIMIT 10"
```

### Errors by App Name
```bash
bun run query -- "SELECT app_name, level, COUNT(*) as count FROM console_logs GROUP BY app_name, level ORDER BY app_name, COUNT(*) DESC"
```

### Clear Old Logs Manually
```bash
bun run query -- "DELETE FROM console_logs WHERE timestamp < datetime('now', '-5 minutes')"
```

## Usage Instructions

1. **When a user asks about recent errors**: Run the "Show Recent Errors" query and present the results with timestamps and messages
2. **When searching for specific issues**: Use the "Search for Specific Error" query with the relevant search term
3. **When analyzing error patterns**: Use the "Count Errors by Level" query to understand error distribution
4. **For debugging**: Include stack traces by using `stack_trace` column and showing the full error context
5. **When database seems full**: Run a manual cleanup query (though auto-cleanup runs every 30 seconds automatically)

## Important Notes

- All queries are **read-only** for diagnostics (except manual cleanup if needed)
- The browser extension must be loaded and the API server must be running (`bun run logger start`) to capture new logs
- App names are auto-tagged if configured via `window.__APP_NAME__`, meta tags, or port detection
- Use `LIMIT` clause to avoid overwhelming output for large result sets
- Stack traces can be very long; consider using `SUBSTR()` to truncate if needed
- Logs are stored locally in your project directory - no data is ever sent to external services
