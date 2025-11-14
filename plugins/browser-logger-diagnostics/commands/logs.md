---
description: Display the most recent console logs from browser-logger
allowed-tools: Bash(cd:*), Bash(bun:*)
---

!`cd /Users/yroc/scripts/browser-logger && bun run query -- "SELECT timestamp, level, message, url, app_name FROM console_logs ORDER BY timestamp DESC LIMIT 20"`
