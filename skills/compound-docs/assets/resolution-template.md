---
title: [Clear Problem Title]
date: [YYYY-MM-DD]
problem_type: [bug|performance|config|test|security|integration|ui|logic|build|database|devex]
severity: [critical|high|medium|low]
tech_stack: [technology1, technology2]
symptoms:
  - [Observable symptom 1 - specific error message or behavior]
  - [Observable symptom 2 - what user actually saw/experienced]
tags: [keyword1, keyword2]
search_terms:
  - [exact error message or phrase 1]
  - [exact error message or phrase 2]
component: [Optional: specific component affected]
root_cause: [Optional: race_condition|missing_await|config_error|logic_error|memory_leak|network_issue|dependency_conflict|missing_dependency|permission_issue|state_mutation|type_error|null_reference|resource_exhaustion|encoding_issue|api_breaking_change]
resolution_type: [Optional: code_fix|config_change|dependency_update|environment_setup|test_fix|workaround|documentation]
---

# [Clear Problem Title]

## Problem

[1-2 sentence clear description of the issue and what the user experienced]

**Error message** (if applicable):
```
[Exact error message, copy-pasted from output]
```

## Environment

- **Tech Stack**: [e.g., Node.js 20, Playwright, TypeScript]
- **Affected Component**: [e.g., "E2E test suite", "API endpoint", "Build pipeline"]
- **Date**: [YYYY-MM-DD when this was solved]

## Symptoms

- [Observable symptom 1 - what the user saw/experienced]
- [Observable symptom 2 - error messages, visual issues, unexpected behavior]

## What Didn't Work

**Attempted Solution 1:** [Description of what was tried]
- **Why it failed:** [Technical reason this didn't solve the problem]

**Attempted Solution 2:** [Description of second attempt]
- **Why it failed:** [Technical reason]

[If nothing else was attempted first, write:]
**Direct solution:** The problem was identified and fixed on the first attempt.

## Root Cause

[Technical explanation of:]
1. What was the ROOT CAUSE of the problem?
2. What underlying mechanism failed?
3. Why did this happen? (API misuse, race condition, configuration error, etc.)

## Solution

[The actual fix that worked - provide specific details]

**Code changes** (if applicable):
```[language]
// Before (broken):
[Show the problematic code]

// After (fixed):
[Show the corrected code with explanation]
```

**Configuration changes** (if applicable):
```[format]
# Before:
[Show the problematic config]

# After:
[Show the corrected config]
```

**Commands run** (if applicable):
```bash
# Steps taken to fix:
[Commands or actions]
```

## Prevention

[How to avoid this problem in future development:]
- [Specific coding practice, check, or pattern to follow]
- [What to watch out for]
- [How to catch this early]

## Related Issues

[If any similar problems exist in docs/solutions/, link to them:]
- See also: [another-related-issue.md](../category/another-related-issue.md)

[If no related issues, write:]
No related issues documented yet.
