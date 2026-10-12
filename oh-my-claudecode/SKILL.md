---
name: oh-my-claudecode
description: Multi-agent coordination framework and CLI customization layer inspired by Yeachan-Heo/oh-my-claudecode. Extends AI developer agents with specialized team roles, automated git workflows, prompt templates, and autonomous subagent delegation. Use when scaling from single-prompt tasks to multi-agent team workflows, managing long-running tasks, or organizing terminal development workflows.
---

# ⚡ Oh My Claude Code: Multi-Agent Team Framework

Oh My Claude Code equips developer agents with a structured framework for team orchestration, specialized engineering roles, and terminal execution hygiene.

---

## 👥 Preconfigured Team Roles
- **Lead Architect**: Owns system design, boundary interfaces, and data models.
- **Implementer (SWE)**: Writes clean, atomic code adhering strictly to specification.
- **Quality Assurance**: Writes unit, integration, and fuzz tests; challenges assumptions.
- **Security Auditor**: Checks for credential leaks, OWASP risks, and unsafe dependencies.

---

## 🛠️ Key Protocols
1. **Spec-First Gate**: Never touch code until the Architect approves the plan.
2. **Subagent Delegation**: Delegate independent tasks to parallel workers with zero shared mutable state.
3. **Continuous Verification**: Every code block must be validated by test suites or terminal output before reporting success.
