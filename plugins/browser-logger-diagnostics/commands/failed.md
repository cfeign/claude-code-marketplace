---
description: Display HTTP requests that failed (4xx/5xx status codes)
allowed-tools: Bash(cd:*), Bash(bun:*)
---

!`cd /Users/yroc/scripts/browser-logger && bun run query -- "SELECT timestamp, url, method, status_code, error_text, app_name FROM network_logs WHERE status_code >= 400 ORDER BY timestamp DESC LIMIT 10"`
