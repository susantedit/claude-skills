---
name: skillclaw
description: Automated AI agent skill discovery, ingestion, validation, and adapter framework inspired by AMAP-ML/SkillClaw. Discovers new skills across GitHub and registries, evaluates code safety, standardizes YAML frontmatter, and dynamically adapts tools into Antigravity and Hermes compatible formats. Use when discovering new capabilities, upgrading skill catalogs, or converting third-party MCP tools into native skills.
---

# 🦅 SkillClaw: Automated Skill Discovery & Adaptation

SkillClaw automates the discovery, extraction, safety vetting, and installation of external AI agent skills and MCP servers.

---

## 🎯 The Ingestion Pipeline

```mermaid
graph TD
    A[Discovered Repo / Tool URL] --> B[SkillClaw Safety Vetting & AST Scan]
    B --> C{Safe & Clean?}
    C -- No --> D[Reject & Quarantine with Risk Report]
    C -- Yes --> E[Extract Workflow & Generate SKILL.md Frontmatter]
    E --> F[Test in Sandboxed Session]
    F --> G[Install to Local & Global Antigravity Config]
```

---

## 🔒 Safety Verification Gates
- Checks for credential leaks, malicious eval statements, and unauthorized telemetry.
- Ensures all instructions adhere to RFC 2119 standards (`MUST`, `SHOULD`, `MAY`).
- Enforces explicit user confirmation before any tool installs binary dependencies.
