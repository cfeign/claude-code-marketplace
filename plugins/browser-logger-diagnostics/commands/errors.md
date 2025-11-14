---
description: Display recent console errors
allowed-tools: Bash(cd:*), Bash(bun:*)
---

!`cd /Users/yroc/scripts/browser-logger && bun run query -- "SELECT timestamp, level, message, stack_trace, url, app_name FROM console_logs WHERE level='error' ORDER BY timestamp DESC LIMIT 10"`
