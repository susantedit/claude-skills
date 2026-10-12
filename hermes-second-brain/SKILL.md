---
name: hermes-second-brain
description: Andrej Karpathy's "Second Brain" Obsidian memory architecture for Hermes and AI coding agents. Manages persistent long-term memory across token compaction, bi-directional wiki-linking ([[topic]]), atomic notes, Maps of Content (MOCs), Defuddle web distillation, and automated vault updates. Use whenever organizing knowledge, managing agent memory, persisting insights into Obsidian, or structuring complex domain information.
---

# 🧠 Hermes Second Brain (Obsidian + LLM Wiki Architecture)

Inspired by **Andrej Karpathy's "Second Brain" concept**, this skill transforms AI agent memory from fragile conversational context into an external, durable, bi-directionally linked knowledge graph stored in local Markdown files (an Obsidian vault).

---

## 🎯 The Core Philosophy: RAM vs. Disk

- **Conversational Context is RAM**: Fast, volatile, expensive, and subject to abrupt loss through compaction or token window limits.
- **The Obsidian Vault is Disk**: Cheap, permanent, version-controlled with Git, easily inspected by humans, and structured with atomic notes and `[[wiki-links]]`.

Whenever Hermes or any coding agent completes a task, discovers a recurring pattern, learns user preferences, or extracts research, it **never leaves it solely in chat history**. It compiles and commits it to the Second Brain vault.

---

## 📂 Vault Hierarchy & Directory Standard

An optimal Hermes second brain vault follows this standard structure:

```text
vault/
├── 00_Meta/
│   ├── MOC_Index.md             # Master Map of Content (table of contents)
│   ├── Agent_Identity.md        # Hermes identity, role, and capabilities
│   └── System_Vocabulary.md     # Standardized glossary of project terms
├── 01_Daily/
│   └── YYYY-MM-DD.md            # Daily work log, active context, raw notes
├── 02_Concepts/
│   ├── [[Reverse_Engineering]].md
│   ├── [[Memory_Compaction]].md
│   └── [[LLM_Orchestration]].md
├── 03_Projects/
│   ├── [[Project_Alpha]].md     # Goals, status, active tasks, blockers
│   └── [[Hermes_Goldmine]].md
├── 04_Entities/
│   ├── [[People]]/              # Collaborators, mentors, authors
│   └── [[Tools_and_Services]]/  # Tools, APIs, platforms (Composio, etc.)
├── 05_Playbooks/
│   ├── [[APK_Reversing_Workflow]].md
│   └── [[SEO_Audit_Protocol]].md
└── 06_Sources/
    └── [[Defuddle_Clipped_Articles]]/ # Distilled web clippings & transcripts
```

---

## 📝 Atomic Note Template & Frontmatter Standard

Every note created in the Second Brain must adhere to strict atomic note principles:
1. **Single Concept Focus**: One note addresses one clear idea, entity, or workflow.
2. **Bi-directional Links**: Use `[[Target Note Title]]` for semantic cross-referencing.
3. **YAML Frontmatter**: Standardized metadata enabling algorithmic search and agent querying.

```markdown
---
title: "Note Title"
created: YYYY-MM-DDTHH:mm:ss
updated: YYYY-MM-DDTHH:mm:ss
type: concept | project | entity | playbook | daily | source
tags:
  - knowledge
  - domain/subdomain
aliases:
  - Alternate Name
status: active | evergreen | archived
confidence: high | medium | hypothesis
---

# Note Title

## Summary
A concise 2-3 sentence overview of this atomic concept.

## Key Insights & Principles
- Core takeaway 1 linked to [[Related Concept]]
- Core takeaway 2 with factual evidence

## Connections & Relations
- Parent MOC: [[00_Meta/MOC_Index]]
- Related: [[Other Atomic Note]], [[Specific Tool]]

## Proven Commands / Snippets
```bash
# Executable commands tested and verified
```

## Changelog / Memory Trail
- YYYY-MM-DD: Initial synthesis by Hermes Agent.
```

---

## 🔄 The 4-Stage Second Brain Lifecycle

### Stage 1: Capture (Defuddle & Intake)
- When consuming web pages, videos, or documents, strip all marketing filler, nav bars, scripts, and noise.
- Extract purely semantic markdown into `06_Sources/`.
- Preserve source URLs and attribution.

### Stage 2: Distill (Atomic Note Synthesis)
- Convert raw clippings into bite-sized atomic notes in `02_Concepts/` or `04_Entities/`.
- Generate explicit bi-directional links (`[[...]]`) rather than vague text.
- Formulate concrete rules, code snippets, and gotchas.

### Stage 3: Connect (Map of Content Weaving)
- Update parent MOCs (`00_Meta/MOC_Index.md` or domain MOCs like `Concepts_MOC.md`).
- Ensure no orphan notes exist; every note must be reachable via graph traversal.

### Stage 4: Retrieve & Act (Memory Recall)
- Before beginning new projects or tasks, query the vault:
  ```powershell
  # Search relevant notes in vault
  grep -rn "pattern" ./vault/
  ```
- Load the most tightly coupled atomic notes into agent working memory.
- Avoid dumping whole directories into context; pull only the exact relevant atomic notes.

---

## ⚡ Handling Context Compaction & Token Eviction

When working on long multi-hour tasks where conversational context approaches the token limit:

1. **Pre-Compaction Flush**:
   - Write current state, open loops, architectural decisions, and next commands into `01_Daily/YYYY-MM-DD.md`.
2. **Context Reset / Resume**:
   - Re-initialize agent context with:
     > *"Read `01_Daily/YYYY-MM-DD.md` and `03_Projects/<Current_Project>.md` to resume state."*
3. **Zero Knowledge Loss**:
   - The agent picks up immediately without hallucinations, repeated work, or lost context.

---

## 🛠️ Essential Automation Workflows

### 1. Generating a Daily Note
Hermes logs major accomplishments, commands run, and discovered bugs to `01_Daily/YYYY-MM-DD.md` with timestamps.

### 2. Updating a Project Note
When code is committed or tasks are completed, Hermes updates the project status, checking off completed items and logging next steps.

### 3. Extracting a Reusable Playbook
When an engineering sequence succeeds (e.g. debugging a stubborn CORS issue or setting up an MCP server), Hermes generalizes it into a playbook in `05_Playbooks/` for instant reuse.
