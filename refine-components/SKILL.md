---
name: refine-components
description: Premium UI component refinement skill utilizing top-tier UI libraries and Playwright visual verification.
---

# 🎨 Refine Components Skill

This skill provides proper, step-by-step guidance for designing, building, and refining high-end UI components. When activated, you must elevate standard components into premium, production-ready interfaces using top-tier design libraries and rigorous visual testing.

## 📚 Curated Libraries & Resources

You must utilize the following premium UI libraries to achieve the best possible output. Choose the right library based on the component's specific needs:

- **Foundational Components**: [shadcn/ui](https://ui.shadcn.com/), [Ant Design](https://ant.design/)
- **Animations & Micro-interactions**: [Magic UI](https://magicui.design/docs/components), [React Bits](https://reactbits.dev/)
- **Advanced Blocks & Layouts**: [MVP Blocks](https://blocks.mvp-subha.me/), [Kibo UI](https://www.kibo-ui.com/), [Skiper UI](https://skiper-ui.com/)
- **Specialty & Thematic**: [shadcnspace](https://shadcnspace.com/), [Origin UI](https://www.originui-ng.com/), [Shsf UI](https://www.shsfui.com/), [TweakCN](https://tweakcn.com/)

---

## 🛠️ The Refinement Workflow

Follow this step-by-step guidance strictly when refining UI components:

### 1. Requirements & Intent Analysis
- Analyze the core purpose of the component and the user's request.
- Identify the target aesthetic (e.g., clean, minimalist, playful, high-tech).
- Select the best library combinations from the list above.

### 2. Premium Design Execution
- **Reject Generic Defaults**: Do not use standard browser styling or flat, uninspired layouts. The result must WOW the user.
- **Typography & Spacing**: Enforce strict typographic hierarchy (e.g., using Inter or Outfit). Use generous, mathematically consistent spacing (e.g., an 8px grid system).
- **Color & Depth**: Utilize sophisticated color palettes, subtle gradients, glassmorphism, and precise shadow layers to create depth. 
- **Motion**: Add purposeful, smooth micro-animations on hover, active, and focus states (e.g., using Framer Motion or Magic UI).

### 3. High-Quality Implementation
- Write clean, modern, and maintainable code.
- Ensure the component is fully accessible (proper ARIA attributes, keyboard navigation, focus management).
- Ensure the component is entirely responsive across mobile, tablet, and desktop viewports.

### 4. Visual Verification (MANDATORY)
- **You MUST use the `/playwright-cli` skill (or Playwright tools)** to view the component in a real browser environment.
- **Inspect**: Check for layout shifts, overflow issues, and exact pixel alignment.
- **Interact**: Use Playwright to simulate user interactions (clicks, hovers, inputs) to ensure states and animations trigger correctly.
- **Iterate**: If the visual output is not perfect, iterate on the code and re-test. Do not mark the task complete until the browser output is verified to be flawless.

## 🛑 Strict Anti-Slop Rules

- **NEVER skip visual verification**: You must look at what you built using Playwright before claiming it is done.
- **NEVER reinvent the wheel poorly**: If a library provides a perfect, accessible base (like shadcn/ui), use it and refine it rather than writing a fragile custom component from scratch.
- **ALWAYS prioritize the "feel"**: A component isn't just about how it looks, but how it responds to the user. Micro-interactions matter.
