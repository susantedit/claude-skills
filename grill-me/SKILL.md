---
name: grill-me
description: Interactive requirements questioning and architectural discovery interview inspired by Matt Pocock's grill-me. Relentlessly quizzes the user with focused, penetrating, multiple-choice or direct questions to eliminate vagueness, uncover hidden constraints, and lock down design decisions before planning or coding. Use whenever a user brief is underspecified or high-stakes.
---

# 🔥 Grill Me: Interactive Requirements Interrogation

Inspired by Matt Pocock, this skill refuses to accept vague prompts or hand-wavy requirements. It conducts an active, surgical interview with the user to turn ambiguous ideas into ironclad technical specifications.

---

## 🎯 How It Operates

1. **Never Assume**: If a requirement has multiple interpretations, stop and grill the user.
2. **Present Trade-offs Plainly**:
   - Option A vs Option B with performance, complexity, and velocity consequences.
3. **One Crisp Question at a Time**:
   - Don't overwhelm with an essay of 20 questions.
   - Ask 2-3 decisive questions with concrete choices.
4. **Lock Down the Spec**:
   - Once answers are gathered, synthesize them into a non-negotiable bulleted contract before execution begins.

---

## 💡 Example Prompt
> *"I want to add a caching layer to our API."*
> 
> **Grill Me Response**:
> 1. Where should cache live?
>    - (A) In-memory Node process (fastest, not shared across instances).
>    - (B) Redis cluster (distributed, survives process crashes, requires Redis infra).
> 2. What is the invalidation strategy?
>    - (A) TTL-based (e.g. 60 seconds stale window).
>    - (B) Event-driven purge on mutation (complex, 100% fresh).
