# Critical Pattern Template

Use this template when adding a pattern to `docs/solutions/patterns/critical-patterns.md`:

---

## [N]. [Pattern Name] (ALWAYS REQUIRED)

**Source:** [Link to original solution doc](../category/filename.md)
**Date Added:** YYYY-MM-DD
**Severity:** critical

### Context

[When does this pattern apply? What situation triggers this?]

### ❌ WRONG

```[language]
// Anti-pattern code example
[Show the problematic approach]
```

**Why it fails:** [Technical explanation of why this doesn't work]

### ✅ CORRECT

```[language]
// Correct code example
[Show the proper approach]
```

**Why it works:** [Technical explanation of why this is the right approach]

### Prevention Checklist

- [ ] [First thing to check before implementing]
- [ ] [Second verification step]
- [ ] [Third safeguard]

---

## Instructions

1. Replace `[N]` with the next pattern number
2. Replace `[Pattern Name]` with descriptive title
3. Fill in WRONG example with code that causes the problem
4. Fill in CORRECT example with the solution
5. Explain the technical reason in "Why it fails" and "Why it works"
6. Clarify when this pattern applies in "Context"
7. Link to the full troubleshooting doc where this was originally solved
8. Add prevention checklist items for code review
