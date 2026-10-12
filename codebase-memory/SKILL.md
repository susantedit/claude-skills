---
name: codebase-memory
description: Structural codebase memory and architecture knowledge graph inspired by DeusData/codebase-memory-mcp. Indexes symbol hierarchies, call graphs, type dependencies, and architectural decisions across coding sessions. Use when navigating large codebases, preserving architectural context across restarts, or tracing cross-file dependencies.
---

# 🧠 Codebase Memory (MCP Architecture & Symbol Graph)

Codebase Memory provides structural, AST-level persistence of codebase architecture, relationships, and decisions across AI coding sessions.

---

## 🎯 When to Use
- Working in repositories with more than 10 files where full context reads waste tokens.
- Tracing multi-step dependency flows (e.g., API router → Controller → Service → Repository → DB model).
- Retaining architectural constraints and bug histories across agent sessions.

---

## 🏗️ The Memory Indexing Model

1. **Entity Graph**:
   - Symbols: Classes, interfaces, functions, exported constants.
   - Files: Module boundaries, package declarations, file locations.
2. **Relationship Edges**:
   - `CALLS`: Function A calls Function B.
   - `IMPLEMENTS`: Class A implements Interface B.
   - `IMPORTS`: File A imports from File B.
   - `MUTATES`: Function A modifies State B.
3. **Architectural Decisions**:
   - Why a specific library was chosen over alternatives.
   - Known race conditions or performance boundaries.

---

## 📋 Querying & Updating Pattern

- **Recall Context**:
  > *"Query codebase memory for all endpoints touching user billing and their authentication middleware."*
- **Update Memory**:
  > *"Record that the database pool size was restricted to 10 connections due to serverless connection limits."*
