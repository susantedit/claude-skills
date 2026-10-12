---
name: nova-youtube-agent
description: Comprehensive YouTube video intelligence and automated research agent inspired by sharbelxyz/nova-youtube-agent and ZeroPointRepo/youtube. Downloads transcripts, extracts key timestamps, synthesizes architectural diagrams, clips key moments, and exports structured obsidian-compatible summaries. Use when extracting knowledge from YouTube videos, webinars, conference talks, or dev tutorials.
---

# 📺 Nova YouTube Intelligence Agent

Nova transforms video content into high-density, searchable engineering knowledge without watching hours of footage manually.

---

## 🎯 Extraction Pipeline
1. **Audio/Subtitle Ingestion**: Fetches accurate official subtitles or generates whisper transcripts with millisecond timestamps.
2. **Key Moment Detection**: Flags section boundaries, slide changes, live coding demos, and key architectural assertions.
3. **Synthesis to Second Brain**:
   - Compiles notes into `06_Sources/[[Video_Title]].md` in the Obsidian vault.
   - Extracts exact terminal commands, GitHub repos, and tools mentioned in the video.
   - Creates bi-directional links (`[[...]]`) to existing concept notes.

---

## 💻 Example Usage
> *"Nova: Extract the full transcript and architectural takeaways from `https://www.youtube.com/watch?v=5WQk9WiWcYQ` and file into my Obsidian second brain."*
