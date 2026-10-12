---
name: defuddle
description: Web-to-markdown distillation and clutter-removal skill inspired by kepano/obsidian-skills. Converts complex HTML web pages, articles, and documentation into clean, semantic markdown formatted for Obsidian vaults and second brains. Strips navigation bars, ads, cookie banners, tracking scripts, and styling garbage while preserving tables, headings, code blocks, images, and source citations.
---

# 🧹 Defuddle: Clean Web-to-Markdown Distiller

Defuddle cleans noisy web pages into pure, distraction-free Markdown, optimized for **Obsidian vaults**, **Hermes Second Brains**, and context-conscious LLM agents.

---

## 🎯 When to Use
- Archiving web research into local Markdown files.
- Reading web documentation without wasting token windows on headers, footers, sidebars, or CSS classes.
- Preparing clippings for atomic note synthesis in the Second Brain.

---

## 📋 Distillation Principles
1. **Preserve High-Value Content**:
   - Article title, author, date, and canonical source URL.
   - Core headings (`h1` through `h4` hierarchy).
   - Code snippets with appropriate language tags.
   - Data tables, markdown lists, blockquotes, and meaningful diagrams.
2. **Aggressively Strip Clutter**:
   - Navigation bars, breadcrumbs, search bars, and pagination widgets.
   - Cookie consent notices, newsletter popups, subscription paywall blurbs.
   - Comment sections, related article recommendation carousels.
   - Raw JavaScript tags, inline CSS, SVG base64 bloat, tracking pixels.
3. **Format for Obsidian**:
   - Add standardized YAML frontmatter (`title`, `source`, `clipped_at`, `tags: [source, web-clipping]`).
   - Standardize internal references where applicable.

---

## 💻 Output Format Standard

```markdown
---
title: "Article or Documentation Title"
source: "https://example.com/article"
clipped_at: YYYY-MM-DDTHH:mm:ss
author: "Author Name"
tags:
  - source/web
  - topic/subtopic
---

# Article Title

> Clipped from [example.com/article](https://example.com/article) on YYYY-MM-DD.

## Section 1: Core Concept
...distilled text...

```bash
# clean code snippet
```
```
