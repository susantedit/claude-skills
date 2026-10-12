---
name: code-wiki
description: Automated developer wiki generator and architectural documentation compiler for Hermes and Obsidian. Analyzes codebases to generate interconnected markdown wikis with module boundaries, API schemas, data flows, and living READMEs. Use when onboarding to a project, documenting complex systems, or syncing repository architecture into an Obsidian Second Brain.
---

# 📚 Code Wiki: Automated Architectural Documentation

Code Wiki compiles source code into an interconnected, searchable developer wiki formatted with Obsidian `[[wiki-links]]` and Mermaid diagrams.

---

## 🏗️ Generated Wiki Structure
```text
wiki/
├── 00_Overview.md               # High-level architecture and system goals
├── 01_Architecture_Diagram.md   # Mermaid diagrams of subsystems and data flows
├── 02_API_Reference.md          # REST / GraphQL / RPC schemas and contracts
├── 03_Data_Models.md            # Database schemas, ORM models, relations
├── 04_Environment_Setup.md      # Docker, env vars, build pipelines
└── 05_Decision_Records/         # Architecture Decision Records (ADRs)
```

---

## ⚡ Key Standards
- Automatically generates Mermaid data-flow diagrams.
- Quotes exact file paths and line ranges (`[handler.go:L45-60](...)`).
- Persists directly into the Obsidian Second Brain under `03_Projects/`.
