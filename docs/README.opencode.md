# Omnipowers for OpenCode

Complete guide for using Omnipowers with [OpenCode.ai](https://opencode.ai).

## Installation

Add omnipowers to the `plugin` array in your `opencode.json` (global or project-level):

```json
{
  "plugin": ["omnipowers@git+https://github.com/aaione/omnipowers.git"]
}
```

Restart OpenCode. The plugin auto-installs via Bun and registers all skills automatically.

Verify by asking: "Tell me about your omnipowers"

### Migrating from the old symlink-based install

If you previously installed omnipowers using `git clone` and symlinks, remove the old setup:

```bash
# Remove old symlinks
rm -f ~/.config/opencode/plugins/omnipowers.js
rm -rf ~/.config/opencode/skills/omnipowers

# Optionally remove the cloned repo
rm -rf ~/.config/opencode/omnipowers

# Remove skills.paths from opencode.json if you added one for omnipowers
```

Then follow the installation steps above.

### Manual Installation (Alternative)

If the plugin-based install doesn't work for your setup, you can use the manual symlink approach:

#### macOS / Linux

```bash
# 1. Install Omnipowers (or update existing)
if [ -d ~/.config/opencode/omnipowers ]; then
  cd ~/.config/opencode/omnipowers && git pull
else
  git clone https://github.com/aaione/omnipowers.git ~/.config/opencode/omnipowers
fi

# 2. Create directories
mkdir -p ~/.config/opencode/plugins ~/.config/opencode/skills

# 3. Remove old symlinks/directories if they exist
rm -f ~/.config/opencode/plugins/omnipowers.js
rm -rf ~/.config/opencode/skills/omnipowers

# 4. Create symlinks
ln -s ~/.config/opencode/omnipowers/.opencode/plugins/omnipowers.js ~/.config/opencode/plugins/omnipowers.js
ln -s ~/.config/opencode/omnipowers/skills ~/.config/opencode/skills/omnipowers

# 5. Restart OpenCode
```

#### Windows

**Prerequisites:**
- Git installed
- Either **Developer Mode** enabled OR **Administrator privileges**
  - Windows 10: Settings > Update & Security > For developers
  - Windows 11: Settings > System > For developers

**PowerShell (run as Administrator, or with Developer Mode enabled):**

```powershell
# 1. Install Omnipowers
git clone https://github.com/aaione/omnipowers.git "$env:USERPROFILE\.config\opencode\omnipowers"

# 2. Create directories
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.config\opencode\plugins"
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.config\opencode\skills"

# 3. Remove existing links (safe for reinstalls)
Remove-Item "$env:USERPROFILE\.config\opencode\plugins\omnipowers.js" -Force -ErrorAction SilentlyContinue
Remove-Item "$env:USERPROFILE\.config\opencode\skills\omnipowers" -Force -ErrorAction SilentlyContinue

# 4. Create plugin symlink (requires Developer Mode or Admin)
New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.config\opencode\plugins\omnipowers.js" -Target "$env:USERPROFILE\.config\opencode\omnipowers\.opencode\plugins\omnipowers.js"

# 5. Create skills junction (works without special privileges)
New-Item -ItemType Junction -Path "$env:USERPROFILE\.config\opencode\skills\omnipowers" -Target "$env:USERPROFILE\.config\opencode\omnipowers\skills"

# 6. Restart OpenCode
```

## Usage

### Finding Skills

Use OpenCode's native `skill` tool to list all available skills:

```
use skill tool to list skills
```

### Loading a Skill

```
use skill tool to load omnipowers/brainstorming
```

### Personal Skills

Create your own skills in `~/.config/opencode/skills/`:

```bash
mkdir -p ~/.config/opencode/skills/my-skill
```

Create `~/.config/opencode/skills/my-skill/SKILL.md`:

```markdown
---
name: my-skill
description: Use when [condition] - [what it does]
---

# My Skill

[Your skill content here]
```

### Project Skills

Create project-specific skills in `.opencode/skills/` within your project.

**Skill Priority:** Project skills > Personal skills > Omnipowers skills

## Architecture

### Plugin Structure (Recommended Install)

When using the `plugin` array in `opencode.json`, the plugin does two things:

1. **Injects bootstrap context** via the `experimental.chat.system.transform` hook, adding omnipowers awareness to every conversation.
2. **Registers the skills directory** via the `config` hook, so OpenCode discovers all omnipowers skills without symlinks or manual config.

### Manual Install Architecture

When using symlinks, the components are:

- **Plugin:** `~/.config/opencode/omnipowers/.opencode/plugins/omnipowers.js` — injects bootstrap via `experimental.chat.system.transform` hook
- **Skills:** `~/.config/opencode/skills/omnipowers/` (symlink to `~/.config/opencode/omnipowers/skills/`)

### Tool Mapping

Skills written for Claude Code are automatically adapted for OpenCode:

- `TodoWrite` → `todowrite`
- `Task` with subagents → OpenCode's `@mention` system
- `Skill` tool → OpenCode's native `skill` tool
- File operations → Native OpenCode tools

## Updating

### Plugin Install

Omnipowers updates automatically when you restart OpenCode. The plugin is re-installed from the git repository on each launch.

To pin a specific version, use a branch or tag:

```json
{
  "plugin": ["omnipowers@git+https://github.com/aaione/omnipowers.git#v5.0.3"]
}
```

### Manual Install

```bash
cd ~/.config/opencode/omnipowers
git pull
```

Restart OpenCode to load the updates.

## Troubleshooting

### Plugin not loading

1. Check OpenCode logs: `opencode run --print-logs "hello" 2>&1 | grep -i omnipowers`
2. Verify the plugin line in your `opencode.json` is correct
3. Make sure you're running a recent version of OpenCode

### Skills not found

1. Use OpenCode's `skill` tool to list available skills
2. Check that the plugin is loading (see above)
3. Each skill needs a `SKILL.md` file with valid YAML frontmatter

### Bootstrap not appearing

1. Check OpenCode version supports `experimental.chat.system.transform` hook
2. Restart OpenCode after config changes

### Windows: Module not found error

If you see `Cannot find module` errors on Windows:
- **Cause:** Git Bash `ln -sf` copies files instead of creating symlinks
- **Fix:** Use `mklink /J` directory junctions instead (see Windows installation steps)

## Getting Help

- Report issues: https://github.com/aaione/omnipowers/issues
- Main documentation: https://github.com/aaione/omnipowers
- OpenCode docs: https://opencode.ai/docs/

## Testing

Verify your installation:

```bash
# Check plugin loads
opencode run --print-logs "hello" 2>&1 | grep -i omnipowers

# Check skills are discoverable
opencode run "use skill tool to list all skills" 2>&1 | grep -i omnipowers

# Check bootstrap injection
opencode run "what omnipowers do you have?"
```

The agent should mention having omnipowers and be able to list skills from `omnipowers/`.
