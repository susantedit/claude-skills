---
name: hermes-watchers
description: Scheduled autonomous background watchers and daemon monitors for Hermes Agent. Periodically polls repositories, APIs, web endpoints, database tables, or RSS feeds, filters out false alarms, and triggers alerts only when critical threshold conditions are met. Use when setting up automated surveillance, uptime checks, price tracking, release monitoring, or security drift detection.
---

# 👁️ Hermes Autonomous Watchers

Hermes Watchers are background daemon monitors designed to operate persistently with zero user intervention, alerting humans only when something genuinely demands action.

---

## 🎯 The High-Signal Principle
- **No Heartbeat Noise**: Never send "everything is normal" messages unless explicitly asked.
- **State Hashing & Diffing**: Store the last known state hash in the Second Brain vault (`01_Daily/`). If diff == 0, exit silently.
- **Threshold Filters**: Only trigger when a metric crosses the user's defined anomaly boundary.

---

## 🛠️ Typical Watcher Targets
1. **GitHub Watcher**: Checks for new release tags, critical CVE security advisories, or unmerged PRs stale for > 48 hours.
2. **Uptime & API Watcher**: Probes HTTP endpoints, tracking status codes and p95 latency.
3. **Database Watcher**: Flags deadlocks, slow queries, or unprocessed queue backlogs.
