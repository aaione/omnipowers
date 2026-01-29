---
title: Playwright Tests Flaky Due to Race Conditions
date: 2025-01-29
problem_type: test
severity: medium
tech_stack: [playwright, typescript, node]
symptoms:
  - "Timeout 30000ms exceeded"
  - "Element not visible after click"
tags: [race-condition, flaky-test, e2e, timeout]
search_terms:
  - "Timeout 30000ms exceeded"
  - "waiting for selector"
  - "playwright timeout"
component: E2E test suite
root_cause: race_condition
resolution_type: code_fix
---

# Playwright Tests Flaky Due to Race Conditions

## Problem

E2E tests fail randomly with timeout errors. Tests pass locally but fail intermittently in CI, or vice versa.

**Error message:**
```
TimeoutError: Timeout 30000ms exceeded.
  waiting for selector ".submit-button"
```

## Environment

- **Tech Stack**: Playwright, TypeScript, Node.js
- **Affected Component**: E2E test suite
- **Date**: 2025-01-29

## Symptoms

- Tests pass on some runs, fail on others (flaky)
- Timeout errors when clicking or interacting with elements
- Elements appear to exist but interactions fail

## What Didn't Work

**Attempted Solution 1:** Increased timeout to 60 seconds
- **Why it failed:** Just delayed the failure; the underlying race condition remained

**Attempted Solution 2:** Added `page.waitForTimeout(2000)` before click
- **Why it failed:** Arbitrary delays are unreliable and slow down tests unnecessarily

## Root Cause

Playwright executes commands faster than the browser can render or stabilize elements. The element may exist in the DOM but:
1. Not yet visible (CSS transition in progress)
2. Not yet interactive (JavaScript not attached)
3. Covered by another element (modal, overlay)

Clicking without waiting for the element to be truly ready causes race conditions.

## Solution

Use `waitForSelector` with explicit `state: 'visible'` before any interaction.

**Code changes:**
```typescript
// Before (broken):
await page.click('.submit-button');

// After (fixed):
await page.waitForSelector('.submit-button', { state: 'visible' });
await page.click('.submit-button');
```

**For more robust waiting:**
```typescript
// Wait for element to be visible AND stable
await page.waitForSelector('.submit-button', { state: 'visible' });
await page.waitForLoadState('networkidle'); // Optional: wait for network
await page.click('.submit-button');
```

## Prevention

- Always use explicit waits before interactions, especially after navigation or state changes
- Prefer `state: 'visible'` over default selector wait
- Use `waitForLoadState('networkidle')` after page loads or AJAX requests
- Consider using Playwright's `locator` API with auto-waiting: `page.locator('.submit-button').click()`

## Related Issues

No related issues documented yet.
