---
description: Display recent HTTP requests captured from the browser
allowed-tools: Bash(cd:*), Bash(bun:*)
---

!`cd /Users/yroc/scripts/browser-logger && bun run query -- "SELECT timestamp, url, method, status_code, total_time, app_name FROM network_logs ORDER BY timestamp DESC LIMIT 10"`
