---
name: agent-reach
description: Cross-platform web reach and social signal extraction skill inspired by Panniantong/Agent-Reach. Equips agents with automated strategies for discovering intelligence across platforms (GitHub, Twitter/X, Reddit, Product Hunt, YouTube, and developer forums). Use when monitoring trending topics, tracking competitive product releases, mining user feedback, or performing market recon.
---

# 🌐 Agent Reach: Cross-Platform Intelligence & Recon

This skill directs automated agents to crawl, monitor, and synthesize intelligence across public developer and creator platforms.

---

## 🎯 Target Platforms & Recon Patterns

1. **GitHub Ecosystem**:
   - Track trending repositories, new releases, and issue backlogs.
   - Filter for organic engagement (stars > threshold, active commits in last 30 days).
2. **Twitter/X & Social Feeds**:
   - Monitor keyword spikes, builder threads, and community discussions.
   - Extract primary links, demo videos, and sentiment.
3. **Reddit & Developer Communities (Hacker News, Indie Hackers)**:
   - Identify recurring complaints, unaddressed edge cases, and feature requests.
   - Scrape top-rated problem posts for product inspiration.
4. **Product Hunt & Launch Platforms**:
   - Analyze daily top launches, tag categories, and maker comments.
   - Benchmark feature sets against incumbent tools.

---

## 📊 Output Schema

Every scout run outputs structured intelligence ready for the Second Brain vault:

```markdown
### Recon Report: [Target Topic]
- **Timestamp**: YYYY-MM-DDTHH:mm:ss
- **Key Signals**:
  1. [Signal 1 with source link]
  2. [Signal 2 with source link]
- **Market Opportunity / Takeaway**:
  - Direct insight for product development or investment.
- **Recommended Next Action**:
  - Task delegated to Builder or Researcher agent.
```
