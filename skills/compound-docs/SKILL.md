---
name: compound
description: Capture solved problems as categorized documentation with YAML frontmatter for fast lookup
allowed-tools:
  - Read # Parse conversation context
  - Write # Create resolution docs
  - Shell # Create directories
  - Grep # Search existing docs
  - Glob # Find existing docs
preconditions:
  - Problem has been solved (not in-progress)
  - Solution has been verified working
---

# Compound Skill

**Purpose:** Automatically document solved problems to build searchable institutional knowledge with category-based organization (enum-validated problem types).

## Overview

This skill captures problem solutions immediately after confirmation, creating structured documentation that serves as a searchable knowledge base for future sessions.

**Organization:** Single-file architecture - each problem documented as one markdown file in its category directory (e.g., `docs/solutions/testing/race-condition-playwright-20250129.md`). Files use YAML frontmatter for metadata and searchability.

**Unified structure requirement:** Whether it's a bug, an operational issue, or a newly implemented important feature, any documentation generated using `/omnipowers:compound` must adhere to the following: the field constraints defined in `schema.yaml` and the section structure of `assets/resolution-template.md`, and must be stored under `docs/solutions/[directory mapped in category_mapping]/[sanitized-title]-[YYYYMMDD].md`.

---

## Task Decomposition

This skill decomposes the documentation task into logical subtasks:

| Subtask | Responsibility | Output |
|---------|---------------|--------|
| **Context Analysis** | Extract problem type, symptoms, tech stack from conversation | YAML frontmatter |
| **Solution Extraction** | Identify root cause, gather code examples | Solution content |
| **Related Docs Search** | Search `docs/solutions/` for similar issues | Cross-references |
| **Prevention Planning** | Develop prevention strategies and best practices | Prevention section |
| **Category Classification** | Determine category and generate filename | File path |
| **Documentation Assembly** | Combine all parts, validate, write file | Final document |

Each subtask corresponds to information gathering in the 7-Step Process below.

---

<critical_sequence name="documentation-capture" enforce_order="strict">

## 7-Step Process

<step number="1" required="true">
### Step 1: Detect Confirmation

**Auto-invoke after phrases:**

- "that worked"
- "it's fixed"
- "working now"
- "problem solved"
- "that did it"

**OR manual:** `/omnipowers:compound` command

**Non-trivial problems only:**

- Multiple investigation attempts needed
- Tricky debugging that took time
- Non-obvious solution
- Future sessions would benefit

**Skip documentation for:**

- Simple typos
- Obvious syntax errors
- Trivial fixes immediately corrected

**Feature / best-practices work:**

- In the "Planning / Design / Evaluation" phase: If discussing how to implement a new feature, architecture, or best practice (e.g., "Implement Agent List Page", "Full Implementation Guide"), prioritizing using `/omnipowers:brainstorm` (Requirements & Plan) and `/omnipowers:e2e` (Test Design & Execution). Do not generate a compound doc immediately.
- When an important new feature is implemented and verified: You **should** use `/omnipowers:compound` to deposit this work, but treat it as a solved devex/logic/integration problem—describing "what was missing/painful" and "how this implementation becomes the standard", strictly following the `resolution-template.md` structure.
</step>

<step number="2" required="true" depends_on="1">
### Step 2: Gather Context

Extract from conversation history:

**Required information:**

- **Title**: Clear, concise problem description
- **Symptom**: Observable error/behavior (exact error messages)
- **Investigation attempts**: What didn't work and why
- **Root cause**: Technical explanation of actual problem
- **Solution**: What fixed it (code/config changes)
- **Prevention**: How to avoid in future

**Environment details:**

- Tech stack versions
- OS/platform
- File/line references

**BLOCKING REQUIREMENT:** If critical context is missing (title, exact error, or resolution steps), ask user and WAIT for response before proceeding to Step 3:

```
I need a few details to document this properly:

1. What was the exact error message or symptom?
2. What technologies/frameworks were involved?
3. What was the root cause?

[Continue after user provides details]
```
</step>

<step number="3" required="false" depends_on="2">
### Step 3: Check Existing Docs

Search `docs/solutions/` for similar issues:

```bash
# Search by error message keywords
rg "exact error phrase" docs/solutions/

# List existing categories
ls docs/solutions/
```

**IF similar issue found:**

THEN present decision options:

```
Found similar issue: docs/solutions/[path]

What's next?
1. Create new doc with cross-reference (recommended)
2. Update existing doc (only if same root cause)
3. Skip documentation

Choose (1-3): _
```

WAIT for user response, then execute chosen action.

**ELSE** (no similar issue found):

Proceed directly to Step 4 (no user interaction needed).
</step>

<step number="4" required="true" depends_on="2">
### Step 4: Generate Filename

Format: `[sanitized-symptom]-[YYYYMMDD].md`

**Sanitization rules:**

- Lowercase
- Replace spaces with hyphens
- Remove special characters except hyphens
- Truncate to reasonable length (< 80 chars)

**Examples:**

- `race-condition-playwright-tests-20250129.md`
- `docker-port-binding-conflict-20250129.md`
- `api-timeout-rate-limiting-20250129.md`
</step>

<step number="5" required="true" depends_on="4" blocking="true">
### Step 5: Validate YAML Schema

**CRITICAL:** All docs require validated YAML frontmatter with enum validation.

<validation_gate name="yaml-schema" blocking="true">

**Validate against schema:**
Load `schema.yaml` and classify the problem against the enum values defined in [yaml-schema.md](./references/yaml-schema.md). Ensure all required fields are present and match allowed values exactly.

**BLOCK if validation fails:**

```
❌ YAML validation failed

Errors:
- problem_type: must be one of schema enums, got "compilation_error"
- severity: must be one of [critical, high, medium, low], got "invalid"
- symptoms: must be array with 1-5 items, got string

Please provide corrected values.
```

**GATE ENFORCEMENT:** Do NOT proceed to Step 6 (Create Documentation) until YAML frontmatter passes all validation rules defined in `schema.yaml`.

</validation_gate>
</step>

<step number="6" required="true" depends_on="5">
### Step 6: Create Documentation

**Determine category from problem_type:** Use the category mapping defined in `schema.yaml`.

**Category mapping:**
- `bug` → `docs/solutions/bugs/`
- `performance` → `docs/solutions/performance/`
- `config` → `docs/solutions/config/`
- `test` → `docs/solutions/testing/`
- `security` → `docs/solutions/security/`
- `integration` → `docs/solutions/integration/`
- `ui` → `docs/solutions/ui/`
- `logic` → `docs/solutions/logic/`
- `build` → `docs/solutions/build/`
- `database` → `docs/solutions/database/`
- `devex` → `docs/solutions/devex/`

**Create documentation file:**

```bash
PROBLEM_TYPE="[from validated YAML]"
CATEGORY="[mapped from problem_type]"
FILENAME="[generated-filename].md"
DOC_PATH="docs/solutions/${CATEGORY}/${FILENAME}"

# Create directory if needed
mkdir -p "docs/solutions/${CATEGORY}"

# Write documentation using template from assets/resolution-template.md
# (Content populated with Step 2 context and validated YAML frontmatter)
```

**Result:**
- Single file in category directory
- Enum validation ensures consistent categorization

**Create documentation:** Populate the structure from `assets/resolution-template.md` with context gathered in Step 2 and validated YAML frontmatter from Step 5.
</step>

<step number="7" required="false" depends_on="6">
### Step 7: Cross-Reference & Critical Pattern Detection

If similar issues found in Step 3:

**Update existing doc:**

```bash
# Add Related Issues link to similar doc
echo "- See also: [$FILENAME]($REAL_FILE)" >> [similar-doc.md]
```

**Update new doc:**
Already includes cross-reference from Step 6.

**Update patterns if applicable:**

If this represents a common pattern (3+ similar issues):

```bash
# Add to docs/solutions/patterns/common-solutions.md
cat >> docs/solutions/patterns/common-solutions.md << 'EOF'

## [Pattern Name]

**Common symptom:** [Description]
**Root cause:** [Technical explanation]
**Solution pattern:** [General approach]

**Examples:**
- [Link to doc 1]
- [Link to doc 2]
- [Link to doc 3]
EOF
```

**Critical Pattern Detection (Optional Proactive Suggestion):**

If this issue has automatic indicators suggesting it might be critical:
- Severity: `critical` in YAML
- Affects multiple components or foundational systems
- Non-obvious solution that must be followed every time

Then in the decision menu, add a note:
```
💡 This might be worth adding to Required Reading (Option 2)
```

But **NEVER auto-promote**. User decides via decision menu (Option 2).

**Template for critical pattern addition:**

When user selects Option 2 (Add to Required Reading), use the template from `assets/critical-pattern-template.md` to structure the pattern entry. Number it sequentially based on existing patterns in `docs/solutions/patterns/critical-patterns.md`.
</step>

</critical_sequence>

---

<decision_gate name="post-documentation" wait_for_user="true">

## Decision Menu After Capture

After successful documentation, present options and WAIT for user response:

```
✓ Solution documented

File created:
- docs/solutions/[category]/[filename].md

What's next?
1. Continue workflow (recommended)
2. Add to Required Reading - Promote to critical patterns (critical-patterns.md)
3. Link related issues - Connect to similar problems
4. Add to existing skill - Link from a related skill
5. Create new skill - Extract into new learning skill
6. View documentation - See what was captured
7. Other
```

**Handle responses:**

**Option 1: Continue workflow**
- Return to calling skill/workflow
- Documentation is complete

**Option 2: Add to Required Reading** ⭐ PRIMARY PATH FOR CRITICAL PATTERNS
User selects this when:
- System made this mistake multiple times across different components
- Solution is non-obvious but must be followed every time
- Foundational requirement (framework, threading, async patterns, etc.)

Action:
1. Extract pattern from the documentation
2. Format as ❌ WRONG vs ✅ CORRECT with code examples
3. Add to `docs/solutions/patterns/critical-patterns.md` (Create file if it doesn't exist)
4. Add cross-reference back to this doc
5. Confirm: "✓ Added to Required Reading. All subagents will see this pattern before code generation."

**Option 3: Link related issues**
- Prompt: "Which doc to link? (provide filename or describe)"
- Search `docs/solutions/` for the doc
- Add cross-reference to both docs
- Confirm: "✓ Cross-reference added"

**Option 4: Add to existing skill**
User selects this when the documented solution relates to an existing learning skill:
1. Prompt: "Which skill? (e.g., writing-plans, brainstorming)"
2. Determine which reference file to update
3. Add link and brief description to appropriate section
4. Confirm: "✓ Added to [skill-name] skill"

**Option 5: Create new skill**
User selects this when the solution represents the start of a new learning domain:
1. Prompt: "What should the new skill be called? (e.g., docker-debugging, api-testing)"
2. **Invoke `writing-skills` skill** to create the new skill structure.
3. Add the current solution as the first example in the new skill.
4. Confirm: "✓ Created new [skill-name] skill with this solution as first example"

**Option 6: View documentation**
- Display the created documentation
- Present decision menu again

**Option 7: Other**
- Ask what they'd like to do

</decision_gate>

---

<integration_protocol>

## Integration Points

**Invoked by:**
- `/omnipowers:compound` command (primary interface)
- Manual invocation in conversation after solution confirmed
- Can be triggered by detecting confirmation phrases like "that worked", "it's fixed", etc.

**Invokes:**
- `writing-skills` (optional, for Option 5)

**Handoff expectations:**
All context needed for documentation should be present in conversation history before invocation.

</integration_protocol>

---

<success_criteria>

## Success Criteria

Documentation is successful when ALL of the following are true:

- ✅ YAML frontmatter validated (all required fields, correct formats)
- ✅ File created in `docs/solutions/[category]/[filename].md`
- ✅ Enum values match `schema.yaml` exactly
- ✅ Code examples included in solution section
- ✅ Cross-references added if related issues found
- ✅ User presented with decision menu and action confirmed

</success_criteria>

---

## Error Handling

**Missing context:**
- Ask user for missing details
- Don't proceed until critical info provided

**YAML validation failure:**
- Show specific errors
- Present retry with corrected values
- BLOCK until valid

**Similar issue ambiguity:**
- Present multiple matches
- Let user choose: new doc, update existing, or link as duplicate

---

## Execution Guidelines

**MUST do:**
- Validate YAML frontmatter (BLOCK if invalid per Step 5 validation gate)
- Extract exact error messages from conversation
- Include code examples in solution section
- Create directories before writing files (`mkdir -p`)
- Ask user and WAIT if critical context missing
- Map `problem_type` to the `category_mapping` from `schema.yaml` and write files ONLY under `docs/solutions/[mapped-category]/[filename].md` (e.g., `devex` → `docs/solutions/devex/`).

**MUST NOT do:**
- Skip YAML validation (validation gate is blocking)
- Use vague descriptions (not searchable)
- Omit code examples or cross-references
- Create new top-level directories under `docs/solutions/` that are not in `category_mapping`.
- Use generic requirements in `symptoms`.

---

## Example Scenario

**User:** "That worked! The flaky Playwright test is fixed."

**Skill activates:**

1. **Detect confirmation:** "That worked!" triggers auto-invoke
2. **Gather context:**
   - Title: Playwright Tests Flaky Due to Race Conditions
   - Symptom: Tests timing out randomly, "Timeout 30000ms exceeded"
   - Failed attempts: Increased timeout (didn't help), added sleep (unreliable)
   - Solution: Added explicit `waitForSelector` with `state: 'visible'`
   - Root cause: Element exists but not yet interactive when clicked
3. **Check existing:** No similar issue found
4. **Generate filename:** `race-condition-playwright-tests-20250129.md`
5. **Validate YAML:**
   ```yaml
   title: Playwright Tests Flaky Due to Race Conditions
   date: 2025-01-29
   problem_type: test
   severity: medium
   tech_stack: [playwright, typescript, node]
   symptoms:
     - "Timeout 30000ms exceeded"
     - "Element not visible after click"
   tags: [race-condition, flaky-test, e2e]
   search_terms:
     - "Timeout 30000ms exceeded"
     - "waiting for selector"
   ```
   ✅ Valid
6. **Create documentation:**
   - `docs/solutions/testing/race-condition-playwright-tests-20250129.md`
7. **Cross-reference:** None needed (no similar issues)

**Output:**

```
✓ Solution documented

File created:
- docs/solutions/testing/race-condition-playwright-tests-20250129.md

What's next?
1. Continue workflow (recommended)
2. Add to Required Reading - Promote to critical patterns (critical-patterns.md)
3. Link related issues - Connect to similar problems
4. Add to existing skill - Link from a related skill
5. Create new skill - Extract into new learning skill
6. View documentation - See what was captured
7. Other
```

---

## The Compounding Philosophy

**Why "compound"?** Each documented solution compounds your team's knowledge.

1. First time you solve "Playwright race condition" → Research (30 min)
2. Document the solution → `docs/solutions/testing/race-condition-playwright.md` (5 min)
3. Next time similar issue occurs → Quick lookup (2 min)
4. Knowledge compounds → Team gets smarter

**Each unit of engineering work should make subsequent units of work easier—not harder.**
