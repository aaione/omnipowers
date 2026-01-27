# Omnipowers

[English](README.md) | 简体中文

Omnipowers 是一个完整的软件开发工作流，专为你的编码代理构建，基于一组可组合的"技能"（skills）和确保代理正确使用它们的初始指令。

## 工作原理

从启动编码代理的那一刻开始。当它检测到你在构建某样东西时，它*不会*直接跳转到编写代码。相反，它会退一步，询问你真正想要实现什么。

一旦从对话中梳理出规格说明，它会以足够简短的块向你展示，让你能够真正阅读和理解。

在你批准设计后，代理会制定一个清晰的实现计划，即使是热情但缺乏品味、缺乏判断力、缺乏项目背景且厌恶测试的初级工程师也能遵循。它强调真正的红/绿 TDD、YAGNI（你不需要它）和 DRY 原则。

接下来，当你说"开始"时，它会启动*子代理驱动开发*流程，让代理处理每个工程任务，检查和审查他们的工作，并继续前进。Claude 能够连续数小时自主工作而不偏离你制定的计划，这并不罕见。

还有很多其他功能，但这是系统的核心。由于技能会自动触发，你不需要做任何特殊操作。你的编码代理只需拥有 Omnipowers。

## 安装

**注意：** 安装方式因平台而异。Claude Code 有内置插件系统。Codex 和 OpenCode 需要手动设置。

### Claude Code（通过插件市场）

在 Claude Code 中，首先注册市场：

```bash
/plugin marketplace add aaione/omnipowers-marketplace
```

然后从此市场安装插件：

```bash
/plugin install omnipowers@omnipowers-marketplace
```

### 验证安装

检查命令是否出现：

```bash
/help
```

```
# 应该看到：
# /omnipowers:brainstorm - 头脑风暴式设计完善
# /omnipowers:write-plan - 创建实现计划
# /omnipowers:execute-plan - 批量执行计划
```

### Codex

告诉 Codex：

```
从 https://raw.githubusercontent.com/aaione/omnipowers/refs/heads/main/.codex/INSTALL.md 获取并遵循说明
```

**详细文档：** [docs/README.codex.md](docs/README.codex.md)

### OpenCode

告诉 OpenCode：

```
从 https://raw.githubusercontent.com/aaione/omnipowers/refs/heads/main/.opencode/INSTALL.md 获取并遵循说明
```

**详细文档：** [docs/README.opencode.md](docs/README.opencode.md)

## 基本工作流程

1. **brainstorming（头脑风暴）** - 在编写代码之前激活。通过问题完善粗略的想法，探索替代方案，分段展示设计以供验证。保存设计文档。

2. **using-git-worktrees** - 在设计批准后激活。在新分支上创建隔离的工作区，运行项目设置，验证干净的测试基线。

3. **writing-plans** - 使用批准的设计激活。将工作分解为小任务（每个 2-5 分钟）。每个任务都有确切的文件路径、完整的代码、验证步骤。

4. **subagent-driven-development 或 executing-plans** - 使用计划激活。为每个任务分派新的子代理，进行两阶段审查（规格合规性，然后代码质量），或通过人工检查点批量执行。

5. **test-driven-development** - 在实现期间激活。强制执行 RED-GREEN-REFACTOR：编写失败的测试，看着它失败，编写最少的代码，看着它通过，提交。删除在测试之前编写的代码。

6. **requesting-code-review** - 在任务之间激活。对照计划进行审查，按严重程度报告问题。关键问题会阻止进度。

7. **finishing-a-development-branch** - 在任务完成时激活。验证测试，展示选项（合并/PR/保留/丢弃），清理工作树。

**代理在任何任务之前都会检查相关技能。** 这是强制性工作流程，而不是建议。

## 内容概览

### 技能库

**测试**
- **test-driven-development** - RED-GREEN-REFACTOR 循环（包括测试反模式参考）

**调试**
- **systematic-debugging** - 4 阶段根因过程（包括根因追踪、纵深防御、基于条件的等待技术）
- **verification-before-completion** - 确保真正修复

**协作**
- **brainstorming** - 苏格拉底式设计完善
- **writing-plans** - 详细的实现计划
- **executing-plans** - 带检查点的批量执行
- **dispatching-parallel-agents** - 并发子代理工作流
- **requesting-code-review** - 预审查检查清单
- **receiving-code-review** - 回应反馈
- **using-git-worktrees** - 并行开发分支
- **finishing-a-development-branch** - 合并/PR 决策工作流
- **subagent-driven-development** - 通过两阶段审查快速迭代（规格合规性，然后代码质量）

**元技能**
- **writing-skills** - 按照最佳实践创建新技能（包括测试方法）
- **using-omnipowers** - 技能系统介绍

## 理念

- **测试驱动开发** - 始终先编写测试
- **系统化而非临时** - 流程胜过猜测
- **降低复杂性** - 简单作为主要目标
- **证据胜过声明** - 在声明成功之前进行验证

## 贡献

技能直接存储在此仓库中。要贡献：

1. Fork 仓库
2. 为你的技能创建分支
3. 按照 `writing-skills` 技能创建和测试新技能
4. 提交 PR

完整指南请参阅 `skills/writing-skills/SKILL.md`。

## 更新

当你更新插件时，技能会自动更新：

```bash
/plugin update omnipowers
```

## 许可证

MIT License - 详见 LICENSE 文件

## 支持

- **问题反馈**: https://github.com/aaione/omnipowers/issues
- **插件市场**: https://github.com/aaione/omnipowers-marketplace
