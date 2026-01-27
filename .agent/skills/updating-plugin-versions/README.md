# Claude Code Plugin 版本更新技能

通用的 Claude Code Plugin 版本管理技能，遵循语义化版本规范 (SemVer)。

## 功能特性

自动管理 Claude Code Plugin 的版本更新，支持：

- **PATCH 更新**：错误修复 (2.27.0 → 2.27.1)
- **MINOR 更新**：新功能添加 (2.27.0 → 2.28.0)
- **MAJOR 更新**：破坏性变更 (2.27.0 → 3.0.0)

## 触发方式

使用以下任一命令即可触发：

| 用户命令 | 版本类型 |
|----------|----------|
| `update patch version` / `patch` / `fix version` | PATCH |
| `update minor version` / `minor` / `feature version` | MINOR |
| `update major version` / `major` / `breaking version` | MAJOR |

## 自动更新的文件

技能会自动检测并更新以下文件（如果存在）：

1. ✅ `.claude-plugin/plugin.json` - 插件版本（必需）
2. ✅ `.claude-plugin/marketplace.json` - 市场配置（可选）
3. ✅ `CHANGELOG.md` / `RELEASE-NOTES.md` - 变更日志（可选）
4. ✅ `package.json` - NPM 包配置（可选）
5. ✅ `skills/*/metadata.json` - 技能元数据（可选）

## 使用示例

### 场景 1：修复 Bug

```
用户: update patch version

执行:
1. 读取当前版本: 4.1.1
2. 计算新版本: 4.1.2
3. 更新所有版本文件
4. 在 CHANGELOG.md 添加 "### Fixed" 条目
5. 验证所有文件
```

### 场景 2：添加新功能

```
用户: update minor version

执行:
1. 读取当前版本: 4.1.1
2. 计算新版本: 4.2.0
3. 更新所有版本文件
4. 在 CHANGELOG.md 添加 "### Added" 条目
5. 验证所有文件
```

### 场景 3：破坏性变更

```
用户: update major version

执行:
1. 读取当前版本: 4.1.1
2. 计算新版本: 5.0.0
3. 更新所有版本文件
4. 在 CHANGELOG.md 添加 "### Breaking Changes" 条目
5. 验证所有文件
```

## 通用性设计

此技能设计为可在任何 Claude Code Plugin 项目中使用：

- ✅ 自动检测项目结构
- ✅ 优雅处理缺失的可选文件
- ✅ 适配不同的 CHANGELOG 格式
- ✅ 支持多种配置文件格式

## 版本规范

遵循 [Semantic Versioning 2.0.0](https://semver.org/)：

- **MAJOR**: 不兼容的 API 变更
- **MINOR**: 向后兼容的新功能
- **PATCH**: 向后兼容的问题修复

## 相关资源

- [Claude Code Plugin 文档](https://docs.claude.com/en/docs/claude-code/plugins)
- [语义化版本规范](https://semver.org/)
- [Keep a Changelog](https://keepachangelog.com/)
