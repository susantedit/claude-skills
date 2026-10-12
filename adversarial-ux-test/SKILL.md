---
name: adversarial-ux-test
description: Stress-tests user interfaces against chaotic user behaviors, invalid input patterns, network latency, and viewport extremities using Playwright and automated fuzzing. Uncovers race conditions, broken error states, visual overflow regressions, and accessibility dead-ends. Use before shipping web apps to ensure resilient user experience.
---

# 🥊 Adversarial UX Testing

Adversarial UX Test systematically attacks your frontend like an impatient, chaotic, and malicious user to break edge cases before real users do.

---

## 🎯 Test Vectors & Attack Scenarios

1. **Rage Clicking & Double Submits**:
   - Rapid-fire clicking on buttons, forms, and toggles before API responses resolve.
   - Verifies idempotent submission handling and disabled button states.
2. **Extreme Input Fuzzing**:
   - Zero-length strings, 5,000-character unicode strings, emojis, RTL characters, script tags (`<script>`).
   - Checks layout overflow, text clipping, and XSS sanitization.
3. **Flaky Network & Abort Scenarios**:
   - Simulates 3G throttled latency, 500 server errors, and aborted requests mid-flight.
   - Verifies whether error toasts appear or if the UI gets stuck in infinite loading spinners.
4. **Extreme Viewport Geometries**:
   - Tests on 320px mobile screens, ultra-wide 3440px monitors, and extreme browser zoom (200%-300%).
