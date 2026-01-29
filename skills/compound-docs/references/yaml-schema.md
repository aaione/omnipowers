# YAML Frontmatter Schema

**See `skills/compound-docs/schema.yaml` for the complete schema specification.**

## Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `title` | string | Clear, concise problem title |
| `date` | string | ISO 8601 date (YYYY-MM-DD) |
| `problem_type` | enum | Category of the problem |
| `severity` | enum | Impact level |
| `tech_stack` | array | Technologies involved |
| `symptoms` | array | Observable symptoms (1-5 items) |
| `tags` | array | Searchable keywords |
| `search_terms` | array | Exact error messages or phrases for search |

## Enum Values

### problem_type

| Value | Description | Category Directory |
|-------|-------------|-------------------|
| `bug` | Code defects, unexpected behavior | `docs/solutions/bugs/` |
| `performance` | Slow execution, memory issues, N+1 queries | `docs/solutions/performance/` |
| `config` | Configuration, environment, setup issues | `docs/solutions/config/` |
| `test` | Test failures, flaky tests, test infrastructure | `docs/solutions/testing/` |
| `security` | Security vulnerabilities, auth issues | `docs/solutions/security/` |
| `integration` | Third-party API, external service issues | `docs/solutions/integration/` |
| `ui` | Visual bugs, layout issues, CSS problems | `docs/solutions/ui/` |
| `logic` | Business logic errors, algorithm bugs | `docs/solutions/logic/` |
| `build` | Build, compilation, bundler errors | `docs/solutions/build/` |
| `database` | Migration, query, schema problems | `docs/solutions/database/` |
| `devex` | Developer experience: workflow, tooling, setup | `docs/solutions/devex/` |

### severity

| Value | Description |
|-------|-------------|
| `critical` | System down, data loss, security breach |
| `high` | Major feature broken, significant user impact |
| `medium` | Feature partially broken, workaround exists |
| `low` | Minor issue, cosmetic, edge case |

### root_cause (optional)

| Value | Description |
|-------|-------------|
| `race_condition` | Timing/async race condition |
| `missing_await` | Missing async/await |
| `config_error` | Configuration or environment issue |
| `logic_error` | Algorithm/business logic bug |
| `memory_leak` | Memory leak or excessive allocation |
| `network_issue` | Network connectivity or timeout |
| `dependency_conflict` | Package/dependency version conflict |
| `missing_dependency` | Missing required dependency |
| `permission_issue` | File/network permission denied |
| `state_mutation` | Unexpected state mutation |
| `type_error` | Type mismatch or coercion issue |
| `null_reference` | Null/undefined reference |
| `resource_exhaustion` | CPU/memory/disk exhaustion |
| `encoding_issue` | Character encoding problem |
| `api_breaking_change` | External API changed |

### resolution_type (optional)

| Value | Description |
|-------|-------------|
| `code_fix` | Fixed by changing source code |
| `config_change` | Fixed by changing configuration |
| `dependency_update` | Fixed by updating dependency |
| `environment_setup` | Fixed by environment configuration |
| `test_fix` | Fixed by correcting tests |
| `workaround` | Temporary workaround applied |
| `documentation` | Clarified documentation |

## Validation Rules

1. All required fields must be present
2. Enum fields must match allowed values exactly (case-sensitive, lowercase)
3. `tech_stack` must be YAML array with 1+ items
4. `symptoms` must be YAML array with 1-5 items
5. `tags` must be YAML array with 1+ items (lowercase, hyphen-separated)
6. `search_terms` must be YAML array with 1+ items (exact phrases for grep)
7. `date` must match YYYY-MM-DD format

## Example

```yaml
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
```

## Category Mapping Summary

Based on `problem_type`, documentation is filed in:

```
docs/solutions/
├── bugs/           # bug
├── performance/    # performance
├── config/         # config
├── testing/        # test
├── security/       # security
├── integration/    # integration
├── ui/             # ui
├── logic/          # logic
├── build/          # build
├── database/       # database
├── devex/          # devex
└── patterns/       # Common patterns (3+ similar issues)
```
