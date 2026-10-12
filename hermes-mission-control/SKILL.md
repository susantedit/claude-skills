---
name: hermes-mission-control
description: NEXORA-style Mission Control dashboard and orchestration hub inspired by sharbelxyz/hermes-agent-mission-control. Manages multiple autonomous Hermes instances, tracks active tasks, displays agent team health, monitors token consumption, and routes events across Telegram topics. Use when operating multi-agent crews, monitoring autonomous background jobs, or centralizing agent operations.
---

# 🚀 Hermes Mission Control (NEXORA Hub)

Hermes Mission Control is the centralized command deck for managing, observing, and coordinating multiple autonomous agent instances across servers and tasks.

---

## 🎛️ Dashboard Modules
1. **Agent Fleet Overview**:
   - Status: Active, Idle, Blocked, Waiting on User.
   - Resource Usage: Tokens consumed, cost tracking, model selection.
2. **Task Kanban**:
   - Backlog, In Progress, Review Gate, Done.
   - Links to Obsidian Second Brain project notes.
3. **Telegram Notification Bridge**:
   - Maps each agent to a dedicated Telegram forum topic.
   - Filters alerts: Only ping user when human action or approval is required.
4. **Log & Audit Stream**:
   - Live stream of tool calls, shell executions, and verification results.
