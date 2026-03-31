# Changelog

## [5.0.6] - 2026-03-31

### Added

- Synced all improvements from superpowers v5.0.6 upstream
- Added brainstorming Visual Companion (browser-based mockup system)
- Added HARD-GATE design approval flow to brainstorming
- Added spec self-review and user review gate to brainstorming
- Added spec document reviewer and plan document reviewer prompts
- Added DOT format flow diagrams to brainstorming and SDD skills
- Added Model Selection guidance to subagent-driven-development
- Added Implementer Status handling (DONE/DONE_WITH_CONCERNS/BLOCKED/NEEDS_CONTEXT)
- added Scope Check, File Structure, No Placeholders, and Self-Review sections to writing-plans
- Added Code Organization section to implementer prompt
- Added "When You're in Over Your Head" escalation guidance to implementer prompt
- Added using-git-worktrees as REQUIRED sub-skill for SDD
- Added executing-plans and subagent-driven-development as REQUIRED sub-skills for worktrees
- Simplified executing-plans flow (removed batch model, sequential execution)
- Added code organization checks to code quality reviewer
- Added context isolation principle to dispatching-parallel-agents
- Updated reviewer context isolation in requesting-code-review
- Added checkbox syntax (`- [ ]`) to plan task steps
- Added Gemini CLI integration (GEMINI.md, gemini-extension.json)
- Added Cursor plugin support (.cursor-plugin/plugin.json)
- Added zero-dependency brainstorm server with WebSocket protocol
- Added Windows lifecycle tests for brainstorm server
- Added document review system integration test
- Deprecated brainstorm, execute-plan, and write-plan commands
- Added .github issue templates and PR template
- Added CODE_OF_CONDUCT.md
- Preserved omnipowers-unique extensions: compound-docs, react-best-practices, e2e-runner, updating-plugin-versions, lib/skills-core.js

### Changed

- Spec paths now use `docs/omnipowers/specs/`
- Plan paths now use `docs/omnipowers/plans/`
- Execution handoff now offers choice between Subagent-Driven (recommended) and Inline Execution
- Hooks updated to Claude Code 2.1.x compatible format
- Brainstorm server migrated to zero-dependency implementation (server.cjs)
- Updated .gitattributes and .gitignore
