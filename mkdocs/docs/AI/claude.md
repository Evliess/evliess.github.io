---
layout: index
title: "Claude Code"
category: ai
date: 2026-03-15 15:17:55
---

# Claude 
## Install on Windows

```bash
irm https://claude.ai/install.ps1 | iex
```
## Setup Environment

```bash
export ANTHROPIC_BASE_URL=https://api.hunyuan.cloud.tencent.com/anthropic
export ANTHROPIC_AUTH_TOKEN=token
export ANTHROPIC_MODEL=deepseek-chat
export ANTHROPIC_SMALL_FAST_MODEL=deepseek-chat
```

## Useful Command

```bash
/init
/plan
/install-github-app
claude mcp add --transport http figma-dev-mode-mcp-server http://127.0.0.1:3845/mcp
claude mcp add playwright npx @playwright/mcp@latest
```

## Useful Claude Code Commands (English/Chinese)

### Basic CLI Commands

| Command | English Description | 中文描述 | Usage Example |
|---------|-------------------|---------|---------------|
| `/help` | Show help information about Claude Code | 显示 Claude Code 帮助信息 | `claude /help` |
| `/clear` | Clear the conversation history | 清除对话历史 | `claude /clear` |
| `/fast` | Toggle fast mode for faster output | 切换快速模式以获得更快输出 | `claude /fast` |
| `/init` | Initialize a new project or session | 初始化新项目或会话 | `claude /init` |
| `/plan` | Enter plan mode for implementation planning | 进入计划模式进行实施规划 | `claude /plan` |

### Development Workflow Commands

| Command | English Description | 中文描述 | Usage Example |
|---------|-------------------|---------|---------------|
| `/commit` | Create a git commit with staged changes | 创建包含暂存更改的 git 提交 | `claude /commit -m "Fix bug"` |
| `/review-pr` | Review a GitHub pull request | 审查 GitHub 拉取请求 | `claude /review-pr 123` |
| `/babysit-prs` | Monitor PRs and notify of changes | 监控 PR 并通知更改 | `claude /babysit-prs` |
| `/install-github-app` | Install GitHub app for repository access | 安装 GitHub 应用以访问仓库 | `claude /install-github-app` |

### Code Quality & Analysis

| Command | English Description | 中文描述 | Usage Example |
|---------|-------------------|---------|---------------|
| `/simplify` | Review changed code for reuse, quality, and efficiency | 审查更改的代码以提高重用性、质量和效率 | `claude /simplify` |
| `/loop` | Run a prompt or command on recurring interval | 按 recurring 间隔运行提示或命令 | `claude /loop 5m /babysit-prs` |

### MCP (Model Context Protocol) Commands

| Command | English Description | 中文描述 | Usage Example |
|---------|-------------------|---------|---------------|
| `claude mcp list` | List all installed MCP servers | 列出所有安装的 MCP 服务器 | `claude mcp list` |
| `claude mcp add` | Add a new MCP server | 添加新的 MCP 服务器 | `claude mcp add playwright npx @playwright/mcp@latest` |
| `claude mcp remove` | Remove an MCP server | 移除 MCP 服务器 | `claude mcp remove playwright` |

### Environment & Configuration

| Command | English Description | 中文描述 | Usage Example |
|---------|-------------------|---------|---------------|
| `claude config` | Manage Claude Code configuration | 管理 Claude Code 配置 | `claude config get model` |
| `claude auth` | Manage authentication settings | 管理认证设置 | `claude auth status` |

## Usage Examples

### English Examples:
1. **Initialize a project**: `claude /init` - Starts a new coding session with project setup
2. **Plan a feature implementation**: `claude /plan` - Enter plan mode to design implementation steps
3. **Commit changes**: `claude /commit -m "Add user authentication"` - Stage and commit all changes
4. **Review PR #45**: `claude /review-pr 45` - Analyze pull request and provide feedback
5. **Monitor PRs every 10 minutes**: `claude /loop 10m /babysit-prs` - Auto-check PR status
6. **Add Playwright MCP**: `claude mcp add playwright npx @playwright/mcp@latest` - Enable browser automation

### 中文示例：
1. **初始化项目**：`claude /init` - 启动新的编码会话并设置项目
2. **计划功能实现**：`claude /plan` - 进入计划模式设计实施步骤
3. **提交更改**：`claude /commit -m "添加用户认证"` - 暂存并提交所有更改
4. **审查 PR #45**：`claude /review-pr 45` - 分析拉取请求并提供反馈
5. **每10分钟监控PR**：`claude /loop 10m /babysit-prs` - 自动检查PR状态
6. **添加Playwright MCP**：`claude mcp add playwright npx @playwright/mcp@latest` - 启用浏览器自动化

## Tips for Developers

### English:
- Use `/plan` before starting complex implementations to get user approval
- `/babysit-prs` is great for waiting on CI/CD pipelines or reviewer feedback
- MCP servers extend Claude's capabilities (browser automation, database access, etc.)
- `/simplify` automatically improves code quality after changes

### 中文：
- 开始复杂实现前使用 `/plan` 获取用户批准
- `/babysit-prs` 非常适合等待 CI/CD 管道或审查者反馈
- MCP 服务器扩展了 Claude 的能力（浏览器自动化、数据库访问等）
- `/simplify` 在更改后自动提高代码质量

## Current Available Skills:
- **simplify**: Review changed code for reuse, quality, and efficiency (审查更改的代码以提高重用性、质量和效率)
- **loop**: Run a prompt or command on recurring interval (按间隔重复运行提示或命令)
- **claude-api**: Build apps with the Claude API or Anthropic SDK (使用 Claude API 或 Anthropic SDK 构建应用)

## Integrate with Figma

```bash
claude mcp add --scope user --transport http figma https://mcp.figma.com/mcp
/mcp list
/plugin install figma@claude-plugins-official
claude mcp add context7 --scope user -- npx @upstash/context7-mcp 
claude mcp remove
```