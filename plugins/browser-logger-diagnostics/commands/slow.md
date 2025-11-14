---
description: Display slow HTTP requests (over a threshold duration)
allowed-tools: Bash(cd:*), Bash(bun:*)
---

!`cd /Users/yroc/scripts/browser-logger && bun run query -- "SELECT timestamp, url, method, total_time, status_code, app_name FROM network_logs WHERE total_time > 1000 ORDER BY total_time DESC LIMIT 10"`
