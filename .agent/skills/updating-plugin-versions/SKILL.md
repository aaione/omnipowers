---
name: updating-plugin-versions
description: A skill for updating Claude Code Plugin versions. Automatically increments the patch version when the user says "update patch version", the major version when the user says "update major version", and the minor version when the user says "update minor version". This skill automatically finds all files that need version updates and synchronizes them.
---

# Plugin Version Update Skill

This skill manages Claude Code Plugin version updates, following Semantic Versioning (SemVer) specification.

## Version Number Format

The version number format is `MAJOR.MINOR.PATCH`, e.g., `2.27.0`:

- **MAJOR**: Incompatible API changes or major feature refactoring
- **MINOR**: Backward-compatible new feature additions
- **PATCH**: Backward-compatible bug fixes

## Trigger Conditions

This skill is automatically triggered when the user mentions the following keywords:

| User Command | Version Update Type | Example Change |
|--------------|---------------------|----------------|
| "update patch version" / "patch" / "fix version" | PATCH +1 | 2.27.0 → 2.27.1 |
| "update minor version" / "minor" / "feature version" | MINOR +1, PATCH reset to 0 | 2.27.0 → 2.28.0 |
| "update major version" / "major" / "breaking version" | MAJOR +1, MINOR/PATCH reset to 0 | 2.27.0 → 3.0.0 |

## Files to Update

According to the Claude Code Plugin official specification, version information is stored in the following locations:

### 1. Plugin Configuration File (plugin.json)

**Path**: `.claude-plugin/plugin.json` (root of current project)

```json
{
  "name": "plugin-name",
  "version": "2.27.0",  // ← Update here
  "description": "..."
}
```

### 2. Marketplace Configuration File (marketplace.json) - Optional

**Path**: `.claude-plugin/marketplace.json` (if it exists)

```json
{
  "plugins": [
    {
      "name": "plugin-name",
      "version": "2.27.0",  // ← Update here
      ...
    }
  ]
}
```

### 3. Changelog (CHANGELOG.md / RELEASE-NOTES.md) - Optional

**Path**: `CHANGELOG.md` or `RELEASE-NOTES.md` (root of current project)

Add a new version entry at the top of the file:

```markdown
## [X.Y.Z] - YYYY-MM-DD

### Added
- New feature description

### Changed
- Change description

### Fixed
- Fix description
```

### 4. Package Configuration (package.json) - Optional

**Path**: `package.json` (if it exists and has a version field)

```json
{
  "name": "plugin-name",
  "version": "2.27.0",  // ← Update here
  ...
}
```

### 5. Skills Metadata (skills/*/metadata.json) - Optional

**Path**: `skills/*/metadata.json` (if they exist)

```json
{
  "version": "1.0.0",  // ← May need update if plugin version changes
  ...
}
```

## Execution Flow

### Step 1: Detect Project Structure

First, identify the project structure by looking for version files:

```bash
# Find plugin.json
find . -name "plugin.json" -path "*/.claude-plugin/*" | head -1

# Check for marketplace.json
if [ -f .claude-plugin/marketplace.json ]; then
  echo "marketplace.json found"
fi

# Check for changelog
if [ -f CHANGELOG.md ]; then
  echo="CHANGELOG.md found"
elif [ -f RELEASE-NOTES.md ]; then
  echo="RELEASE-NOTES.md found"
fi

# Check for package.json
if [ -f package.json ]; then
  echo "package.json found"
fi
```

### Step 2: Read Current Version

Read the current version number from the detected `plugin.json`:

```bash
# Get plugin name from plugin.json
PLUGIN_NAME=$(cat .claude-plugin/plugin.json | jq -r '.name')

# Get current version
CURRENT_VERSION=$(cat .claude-plugin/plugin.json | jq -r '.version')
echo "Current version: $CURRENT_VERSION"
```

### Step 3: Calculate New Version

Calculate the new version number based on the user's command type:

```bash
# Parse version numbers
MAJOR=$(echo $CURRENT_VERSION | cut -d. -f1)
MINOR=$(echo $CURRENT_VERSION | cut -d. -f2)
PATCH=$(echo $CURRENT_VERSION | cut -d. -f3)

# Calculate new version based on type
case "$VERSION_TYPE" in
  "patch")
    NEW_VERSION="${MAJOR}.${MINOR}.$((PATCH + 1))"
    ;;
  "minor")
    NEW_VERSION="${MAJOR}.$((MINOR + 1)).0"
    ;;
  "major")
    NEW_VERSION="$((MAJOR + 1)).0.0"
    ;;
esac

echo "New version: $NEW_VERSION"
```

```

Current version: MAJOR.MINOR.PATCH

Update patch version:  → MAJOR.MINOR.(PATCH+1)
Update minor version:  → MAJOR.(MINOR+1).0
Update major version:  → (MAJOR+1).0.0
```

### Step 4: Update All Version Files

**The following files must be updated synchronously:**

1. ✅ `.claude-plugin/plugin.json` - `version` field (required)
2. ✅ `.claude-plugin/marketplace.json` - corresponding plugin's `version` field (if exists)
3. ✅ `CHANGELOG.md` or `RELEASE-NOTES.md` - Add new version entry (if exists)
4. ✅ `package.json` - `version` field (if exists)
5. ✅ `skills/*/metadata.json` - Update if plugin version changes are relevant (if exists)

**Update commands:**

```bash
# Update plugin.json
jq ".version = \"$NEW_VERSION\"" .claude-plugin/plugin.json > tmp.json && mv tmp.json .claude-plugin/plugin.json

# Update marketplace.json (if exists)
if [ -f .claude-plugin/marketplace.json ]; then
  # Update version for the plugin in marketplace.json
  jq "(.plugins[] | select(.name == \"$PLUGIN_NAME\" | .version = \"$NEW_VERSION\")) | .plugins" .claude-plugin/marketplace.json > tmp.json && mv tmp.json .claude-plugin/marketplace.json
fi

# Update package.json (if exists)
if [ -f package.json ]; then
  jq ".version = \"$NEW_VERSION\"" package.json > tmp.json && mv tmp.json package.json
fi
```

### Step 5: Update Changelog

Insert a new version entry at the top of the changelog file:

**For CHANGELOG.md:**

```bash
# Get current date
TODAY=$(date +%Y-%m-%d)

# Determine category based on version type
case "$VERSION_TYPE" in
  "patch")
    CATEGORY="### Fixed"
    ;;
  "minor")
    CATEGORY="### Added"
    ;;
  "major")
    CATEGORY="### Changed\n\n### Breaking Changes"
    ;;
esac

# Insert new version entry at the top (after any header section)
sed -i.bak "/^## /i\\
## [$NEW_VERSION] - $TODAY\\
\\
$ CATEGORY\\
- \\
" CHANGELOG.md && rm CHANGELOG.md.bak
```

**For RELEASE-NOTES.md:**

```bash
# For RELEASE-NOTES.md, preserve any fork notes at the top
# Insert after the links section or as the first version entry
sed -i.bak "/^## /i\\
## $NEW_VERSION - $TODAY\\
\\
$ CATEGORY\\
- \\
" RELEASE-NOTES.md && rm RELEASE-NOTES.md.bak
```

### Step 6: Verify

Ensure version numbers are consistent across all files:

```bash
# Verify plugin.json
echo "plugin.json: $(cat .claude-plugin/plugin.json | jq -r '.version')"

# Verify marketplace.json (if exists)
if [ -f .claude-plugin/marketplace.json ]; then
  echo "marketplace.json: $(cat .claude-plugin/marketplace.json | jq -r ".plugins[] | select(.name == \"$PLUGIN_NAME\") | .version")"
fi

# Verify package.json (if exists)
if [ -f package.json ]; then
  echo "package.json: $(cat package.json | jq -r '.version')"
fi

# Verify JSON syntax
cat .claude-plugin/plugin.json | jq . > /dev/null && echo "✅ plugin.json is valid"
if [ -f .claude-plugin/marketplace.json ]; then
  cat .claude-plugin/marketplace.json | jq . > /dev/null && echo "✅ marketplace.json is valid"
fi
if [ -f package.json ]; then
  cat package.json | jq . > /dev/null && echo "✅ package.json is valid"
fi
```

## Example Operations

### Example 1: User Input: "update patch version"

**Execution Steps:**

1. Detect project structure → Found `.claude-plugin/plugin.json`
2. Read current version: `2.27.0`
3. Calculate new version: `2.27.1`
4. Update `plugin.json`: `"version": "2.27.1"`
5. Update `marketplace.json` (if exists): corresponding plugin `"version": "2.27.1"`
6. Update `package.json` (if exists): `"version": "2.27.1"`
7. Update `CHANGELOG.md`: Add `## [2.27.1] - 2025-01-27` entry with `### Fixed` category
8. Verify all files
9. Output confirmation message

### Example 2: User Input: "update minor version"

**Execution Steps:**

1. Detect project structure → Found `.claude-plugin/plugin.json`, `CHANGELOG.md`
2. Read current version: `2.27.0`
3. Calculate new version: `2.28.0`
4. Update `plugin.json`: `"version": "2.28.0"`
5. Update `marketplace.json` (if exists): corresponding plugin `"version": "2.28.0"`
6. Update `package.json` (if exists): `"version": "2.28.0"`
7. Update `CHANGELOG.md`: Add `## [2.28.0] - 2025-01-27` entry with `### Added` category
8. Verify all files
9. Output confirmation message

### Example 3: User Input: "update major version"

**Execution Steps:**

1. Detect project structure → Found `.claude-plugin/plugin.json`, `CHANGELOG.md`
2. Read current version: `2.27.0`
3. Calculate new version: `3.0.0`
4. Update `plugin.json`: `"version": "3.0.0"`
5. Update `marketplace.json` (if exists): corresponding plugin `"version": "3.0.0"`
6. Update `package.json` (if exists): `"version": "3.0.0"`
7. Update `CHANGELOG.md`: Add `## [3.0.0] - 2025-01-27` entry with `### Changed` and `### Breaking Changes` categories
8. Verify all files
9. Output confirmation message

## Notes

1. **Universal Compatibility**: This skill works with any Claude Code plugin project structure
2. **Version Consistency**: Version numbers must be exactly the same across all files
3. **JSON Format Validation**: Always verify JSON file format after updates
4. **CHANGELOG Format**: Follow [Keep a Changelog](https://keepachangelog.com/) specification
5. **Semantic Versioning**: Strictly follow [SemVer](https://semver.org/) specification
6. **Optional Files**: The skill gracefully handles missing optional files (marketplace.json, package.json, etc.)

## Related Resources

- [Claude Code Plugin Documentation](https://docs.claude.com/en/docs/claude-code/plugins)
- [Plugin Reference](https://docs.claude.com/en/docs/claude-code/plugins-reference)
- [Semantic Versioning](https://semver.org/)
- [Keep a Changelog](https://keepachangelog.com/)
