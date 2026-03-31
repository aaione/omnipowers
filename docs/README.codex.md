# Omnipowers for Codex

Guide for using Omnipowers with OpenAI Codex via native skill discovery.

## Quick Install

Tell Codex:

```
Fetch and follow instructions from https://raw.githubusercontent.com/aaione/omnipowers/refs/heads/main/.codex/INSTALL.md
```

## Manual Installation

### Prerequisites

- OpenAI Codex CLI
- Git

### Steps

1. Clone the repo:
   ```bash
   git clone https://github.com/aaione/omnipowers.git ~/.codex/omnipowers
   ```

2. Create the skills symlink:
   ```bash
   mkdir -p ~/.agents/skills
   ln -s ~/.codex/omnipowers/skills ~/.agents/skills/omnipowers
   ```

3. Restart Codex.

4. **For subagent skills** (optional): Skills like `dispatching-parallel-agents` and `subagent-driven-development` require Codex's multi-agent feature. Add to your Codex config:
   ```toml
   [features]
   multi_agent = true
   ```

### Windows

Use a junction instead of a symlink (works without Developer Mode):

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.agents\skills"
cmd /c mklink /J "$env:USERPROFILE\.agents\skills\omnipowers" "$env:USERPROFILE\.codex\omnipowers\skills"
```

### Legacy CLI Tool (Deprecated)

The older CLI-based approach using `omnipowers-codex` commands (`bootstrap`, `use-skill`, `find-skills`) is still available but no longer recommended. The native skill discovery method above is simpler and more reliable.

If you need the legacy CLI tool for compatibility:

**Location:** `~/.codex/omnipowers/.codex/omnipowers-codex`

The CLI uses the shared `skills-core` module (`~/.codex/omnipowers/lib/skills-core.js`, ES module format) for skill discovery and parsing.

## How It Works

Codex has native skill discovery — it scans `~/.agents/skills/` at startup, parses SKILL.md frontmatter, and loads skills on demand. Omnipowers skills are made visible through a single symlink:

```
~/.agents/skills/omnipowers/ → ~/.codex/omnipowers/skills/
```

The `using-omnipowers` skill is discovered automatically and enforces skill usage discipline — no additional configuration needed.

## Usage

Skills are discovered automatically. Codex activates them when:
- You mention a skill by name (e.g., "use brainstorming")
- The task matches a skill's description
- The `using-omnipowers` skill directs Codex to use one

### Personal Skills

Create your own skills in `~/.agents/skills/`:

```bash
mkdir -p ~/.agents/skills/my-skill
```

Create `~/.agents/skills/my-skill/SKILL.md`:

```markdown
---
name: my-skill
description: Use when [condition] - [what it does]
---

# My Skill

[Your skill content here]
```

The `description` field is how Codex decides when to activate a skill automatically — write it as a clear trigger condition.

## Updating

```bash
cd ~/.codex/omnipowers && git pull
```

Skills update instantly through the symlink.

## Uninstalling

```bash
rm ~/.agents/skills/omnipowers
```

**Windows (PowerShell):**
```powershell
Remove-Item "$env:USERPROFILE\.agents\skills\omnipowers"
```

Optionally delete the clone: `rm -rf ~/.codex/omnipowers` (Windows: `Remove-Item -Recurse -Force "$env:USERPROFILE\.codex\omnipowers"`).

## Troubleshooting

### Skills not showing up

1. Verify the symlink: `ls -la ~/.agents/skills/omnipowers`
2. Check skills exist: `ls ~/.codex/omnipowers/skills`
3. Restart Codex — skills are discovered at startup

### Windows junction issues

Junctions normally work without special permissions. If creation fails, try running PowerShell as administrator.

## Getting Help

- Report issues: https://github.com/aaione/omnipowers/issues
- Main documentation: https://github.com/aaione/omnipowers
