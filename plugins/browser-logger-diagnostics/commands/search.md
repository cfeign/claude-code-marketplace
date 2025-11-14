---
description: Search console logs by message content or pattern
allowed-tools: Bash(cd:*), Bash(bun:*)
---

!`cd /Users/yroc/scripts/browser-logger && bun run query -- "SELECT timestamp, level, message, app_name FROM console_logs WHERE message LIKE '%timeout%' ORDER BY timestamp DESC LIMIT 20"`
