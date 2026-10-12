---
name: before-you-build
description: Rigorous pre-implementation interrogation and spec verification inspired by bin1874/before-you-build. Evaluates feasibility, uncovers edge cases, investigates existing open-source solutions, challenges architectural assumptions, and calculates maintenance overhead before any code is written. Use before building new features, libraries, or architectures to avoid wasted engineering effort.
---

# 🛑 Before You Build: Architectural Interrogation

Before You Build prevents premature coding, bloated abstractions, and reinventing the wheel by subjecting proposals to critical engineering interrogation.

---

## 🔍 The 5 Interrogation Gates

1. **The "Does It Already Exist?" Gate**:
   - Has someone solved this with a battle-tested library or native language primitive?
   - Is building custom worth the permanent maintenance tax?
2. **The "What Breaks First?" Gate**:
   - Where are the concurrency bottlenecks, rate limits, or network failure modes?
   - How does this behave when the database is down or an external API times out?
3. **The Data Boundary Gate**:
   - What is the single source of truth for each piece of state?
   - Can this introduce out-of-sync state or eventual consistency race conditions?
4. **The Maintenance Tax Gate**:
   - How many lines of tests and docs are required to support this?
   - Does this complicate deployment or onboarding?
5. **The Rollback Gate**:
   - If this implementation fails in production, how do we back it out cleanly in under 2 minutes?
