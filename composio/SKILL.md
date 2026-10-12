---
name: composio
description: Integrates Hermes and AI agents with Composio's 250+ tools and SaaS platforms (GitHub, Gmail, Slack, Linear, Notion, Twitter/X, Discord, Google Calendar). Manages secure OAuth sessions, tool execution schemas, and multi-app automation workflows. Use when connecting agents to external services, triggering third-party APIs, or orchestrating cross-app tasks.
---

# 🔌 Composio Tooling & SaaS Integration

Composio enables autonomous agents to securely interact with over 250 enterprise and developer services without managing manual API keys or brittle scrapers.

---

## 🎯 Supported Tool Ecosystem
- **Development**: GitHub, GitLab, Jira, Linear, Sentry, Vercel.
- **Communication**: Slack, Discord, Gmail, Outlook, Telegram, Twilio.
- **Productivity**: Notion, Google Sheets, Google Drive, Airtable, Coda, Asana.
- **Social & Media**: Twitter/X, LinkedIn, Reddit, YouTube.

---

## 🔒 Security & Auth Architecture
1. **OAuth 2.0 Abstraction**: User authenticates once through Composio's managed auth dashboard.
2. **Zero Plaintext Secrets**: Agents never read or store raw API tokens in memory logs.
3. **Scoped Actions**: Restrict agent permissions (e.g., read-only email search vs. send email).

---

## 🛠️ Usage Patterns

- **Read Action**:
  > *"Use Composio to search my Gmail for unread invoices from Stripe and summarize amounts."*
- **Write Action**:
  > *"Create a Linear ticket in project CORE with title 'Fix CORS on auth endpoint' and assign to me."*
- **Multi-App Workflow**:
  > *"When a GitHub release tag is pushed, send an announcement to Slack #announcements and create a Notion release note."*
