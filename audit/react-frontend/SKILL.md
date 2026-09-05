---
name: react-frontend-audit
description: |
  Production audit checklist for React/Next.js frontends. Use this skill whenever the project
  has react, next, vue, svelte, or angular in package.json, or build tools like vite or webpack.
  Also trigger when the user says "review my React app", "check my Next.js project", "frontend
  audit", "memory leak in component", "bundle size too large", "UI is slow", "infinite rerender",
  "useEffect not working", or "state management review". When activated, immediately scan
  using the grep patterns below — do NOT wait to be asked.
---

# React / Frontend Production Audit

## Output Format
Report findings as a severity table:
| # | Severity | File | Issue | Production Impact | Fix |
|---|----------|------|-------|-------------------|-----|

After the table, list CRITICAL fixes first with exact code changes.
Ask: "Want me to apply these fixes? I'll start with CRITICAL."
After applying CRITICAL fixes, re-scan before moving to HIGH severity.

## CRITICAL Checks

### 1. Memory Leaks — Uncleared Subscriptions/Timers
**What to grep:** `setInterval|setTimeout|addEventListener|subscribe` without cleanup in `useEffect` return
**Check:** Does every `useEffect` with subscriptions/timers return a cleanup function?
**Risk:** Each navigation/remount leaks listeners. After 50 navigations → browser tab crashes.
**Fix:** Return cleanup: `return () => { clearInterval(id); unsubscribe(); removeEventListener(); }`

### 2. Memory Leaks — State Updates After Unmount
**What to grep:** `setState` or state setter inside `async` functions without abort/cancel
**Check:** If component unmounts while fetch is in-flight, does it still call setState?
**Risk:** "Can't perform state update on unmounted component" → memory leak accumulation.
**Fix:** Use `AbortController` with fetch, or check mounted ref, or use TanStack Query (handles this).

### 3. SSE/WebSocket Connections Not Closed
**What to grep:** `new EventSource|new WebSocket|socket.connect` — check for `.close()` in cleanup
**Check:** Are real-time connections closed on unmount?
**Risk:** Each navigation opens new connection without closing old one. 50 tabs = 50 connections.
**Fix:** Close in useEffect cleanup: `return () => { eventSource.close(); socket.disconnect(); }`

### 4. Infinite Re-render Loops
**What to grep:** `useEffect` with missing or wrong dependency arrays
**Check:** Are there effects that set state which triggers the same effect again?
**Risk:** Browser tab freezes, 100% CPU, user has to force-close.
**Fix:** Audit dependency arrays. Use `useMemo`/`useCallback` for object/function deps.

## HIGH Checks

### 5. No Error Boundaries
**What to grep:** `ErrorBoundary` or `componentDidCatch` or `react-error-boundary`
**Check:** If a component throws, does the entire app crash (white screen)?
**Risk:** One broken component = entire app unusable. User sees blank page.
**Fix:** Wrap route-level components in `<ErrorBoundary>` with fallback UI.

### 6. Bundle Size — No Code Splitting
**What to grep:** `React.lazy|dynamic(` or route-level `import()`
**Check:** Is the entire app in one bundle? What's the main bundle size?
**Risk:** 2MB+ initial bundle = 5-10s load on 3G. Users bounce.
**Fix:** `React.lazy()` + `Suspense` for route-level splitting. Dynamic imports for heavy libs.

### 7. API Calls Without Loading/Error States
**What to grep:** `fetch|axios` in components — check for loading and error state handling
**Check:** What does the user see while data loads? What if the API returns 500?
**Risk:** Blank screen during load. Silent failures. User thinks app is broken.
**Fix:** Show skeleton/spinner during load, error message on failure, retry button.

### 8. No Request Deduplication/Caching
**What to grep:** `useEffect` with `fetch` inside — same endpoint called from multiple components
**Check:** If 3 components need the same data, does it fetch 3 times?
**Risk:** Unnecessary API load, inconsistent data between components, race conditions.
**Fix:** Use TanStack Query, SWR, or Zustand with fetch logic centralized.

### 9. Uncontrolled Re-renders (Performance)
**What to grep:** Components receiving new object/array references on every render
**Check:** Are parent components passing `style={{}}` or `onClick={() => {}}` inline?
**Risk:** Child components re-render on every parent render even if data hasn't changed.
**Fix:** `useMemo` for computed values, `useCallback` for handlers, `React.memo` for pure components.

### 10. Missing Accessibility (a11y)
**What to grep:** `<div onClick` without `role`, `<img` without `alt`, `<button` without accessible name
**Check:** Can the app be navigated with keyboard only? Are screen readers supported?
**Risk:** Excludes users with disabilities. Legal liability in some jurisdictions.
**Fix:** Use semantic HTML (`<button>` not `<div onClick>`), add `aria-label`, ensure focus management.

## MEDIUM Checks

### 11. Environment Variables Exposed
**What to grep:** `VITE_|NEXT_PUBLIC_|REACT_APP_` — what secrets are in client-side env vars?
**Check:** Are API keys, internal URLs, or admin endpoints exposed in client bundle?
**Risk:** Anyone can view-source and extract your API keys.
**Fix:** Only expose what the client genuinely needs. Keep secrets server-side.

### 12. No Optimistic Updates for Mutations
**What to grep:** POST/PUT/DELETE calls that wait for response before updating UI
**Check:** Does the UI feel sluggish after actions (create, delete, toggle)?
**Fix:** Update UI immediately, revert on error. TanStack Query `onMutate` pattern.

### 13. Missing Form Validation (Client-Side)
**What to grep:** `<form` or form submission handlers — check for validation before submit
**Check:** Can users submit empty/invalid forms? Is validation only server-side?
**Fix:** Use `react-hook-form` + `zod` for client-side validation with good UX.

### 14. No Loading Skeleton / Layout Shift
**What to grep:** Components that show nothing then suddenly render content
**Check:** Does content "jump" when data loads? Is there Cumulative Layout Shift?
**Fix:** Use skeleton components that match final layout dimensions.

### 15. Stale Closures in Event Handlers
**What to grep:** Event handlers or callbacks referencing state that may be stale
**Check:** Do `setTimeout` callbacks or event listeners capture old state values?
**Fix:** Use `useRef` for latest value, or functional state updates `setState(prev => ...)`.
