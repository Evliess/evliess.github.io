---
layout: index
title: "claude playbook"
category: ai
date: 2026-03-21 15:17:55
---

# Claude Code CLI 高级工作流：操作说明书

## 📋 目录

1. [环境准备与初始化](#环境准备与初始化)
2. [核心工作流操作指南](#核心工作流操作指南)
3. [多项目管理操作步骤](#多项目管理操作步骤)
4. [最佳实践实施指南](#最佳实践实施指南)
5. [故障排除与维护](#故障排除与维护)
6. [快速参考手册](#快速参考手册)

---

## 环境准备与初始化

### 步骤1：检查系统环境

打开终端，运行以下命令检查基础环境：

```bash
# 检查操作系统
uname -s

# 检查是否安装Git
git --version

# 检查是否安装Claude Code
claude --version

# 如果没有安装Claude Code，根据系统选择安装命令：
# Windows:
irm https://claude.ai/install.ps1 | iex

# macOS/Linux:
# 访问 https://claude.ai/download 下载安装
```

### 步骤2：创建基础目录结构

```bash
# 创建Claude工作流主目录
mkdir -p ~/.claude/{commands,scripts,templates}

# 设置权限
chmod 750 ~/.claude/scripts
chmod 644 ~/.claude/commands/*.md 2>/dev/null || true

# 验证目录创建
ls -la ~/.claude/
```

### 步骤3：安装必要工具（可选但推荐）

```bash
# 安装jq（JSON处理工具）
# macOS:
brew install jq

# Ubuntu/Debian:
sudo apt-get install jq

# Windows (使用choco):
choco install jq

# 安装tree（目录树显示）
# macOS:
brew install tree

# Ubuntu/Debian:
sudo apt-get install tree

# Windows:
choco install tree
```

### 步骤4：配置全局环境

创建全局配置文件 `~/.claude/CLAUDE.md`：

```bash
cat > ~/.claude/CLAUDE.md << 'EOF'
# 全局开发规范

## 文档规范
1. 需求文档编号：三位数字前缀 (001-, 002-, ...)
2. 需求文档结构：必须包含9个标准章节
3. 目录索引：每个docs子目录必须有README.md

## 自定义规则示例
- 所有API端点必须有Swagger文档
- 数据库迁移必须包含回滚脚本
- 错误码必须使用统一规范

## 项目继承规则
每个项目的CLAUDE.md应包含：
*继承全局规范: ~/.claude/CLAUDE.md*
EOF

echo "✅ 全局配置创建完成"
```

### 步骤5：下载并安装工作流脚本

```bash
# 创建临时目录
TEMP_DIR=$(mktemp -d)
cd $TEMP_DIR

# 下载核心脚本（假设有在线仓库）
# 如果没有在线仓库，手动创建以下文件：
# 1. compatibility.sh - 兼容性检查
# 2. checkpoint.sh - 检查点脚本
# 3. recap.sh - 恢复脚本
# 4. init-project.sh - 项目初始化
# 5. postmortem.sh - 问题复盘
# 6. ui-helpers.sh - UI辅助函数

# 复制到目标位置
cp *.sh ~/.claude/scripts/

# 设置执行权限
chmod +x ~/.claude/scripts/*.sh

# 清理临时目录
cd ~
rm -rf $TEMP_DIR

echo "✅ 脚本安装完成"
```

---

## 核心工作流操作指南

### 场景1：开始新项目

#### 操作步骤：

1. **创建项目目录并初始化**
   ```bash
   # 方法A：交互式创建
   ~/.claude/scripts/init-project.sh

   # 方法B：命令行参数创建
   ~/.claude/scripts/init-project.sh --name "my-app" --path "./projects/my-app"
   ```

2. **查看生成的项目结构**
   ```bash
   cd ./projects/my-app

   # 查看目录结构
   if command -v tree &> /dev/null; then
       tree -I '.git|.claude|node_modules' --dirsfirst
   else
       find . -type f -name "*.md" | sed 's|^\./||' | sort
   fi
   ```

3. **验证项目配置**
   ```bash
   # 检查必需文件
   ls -la CLAUDE.md docs/progress.md docs/memory/

   # 查看初始进度
   cat docs/progress.md
   ```

#### 预期输出：
```
✅ 项目初始化完成!
📁 项目结构:
my-app/
├── CLAUDE.md
├── docs/
│   ├── progress.md
│   ├── memory/
│   ├── requirements/
│   ├── features/
│   └── postmortem/
└── .gitignore
```

### 场景2：日常开发工作流

#### 操作步骤：

1. **开始工作前：恢复上下文**
   ```bash
   # 进入项目目录
   cd ~/projects/my-app

   # 执行recap恢复工作状态
   ~/.claude/scripts/recap.sh

   # 或使用短命令（如果配置了alias）
   # claude /recap
   ```

2. **工作中：定期保存进展**
   ```bash
   # 手动保存重要决策
   ~/.claude/scripts/checkpoint.sh

   # 查看保存的日志
   tail -5 docs/memory/$(date +%Y-%m-%d).md
   ```

3. **完成工作单元后：更新进度**
   ```bash
   # 编辑进度文件
   echo "## 完成的工作 @ $(date '+%H:%M:%S')" >> docs/progress.md
   echo "- 实现了用户登录API" >> docs/progress.md
   echo "- 添加了JWT验证中间件" >> docs/progress.md
   echo "" >> docs/progress.md

   # 保存检查点
   ~/.claude/scripts/checkpoint.sh
   ```

#### 检查点内容示例：
```markdown
---
checkpoint: 2026-03-21 14:30:22
project: my-app
---

## 工作摘要
- 时间: 2026-03-21 14:30:22
- 位置: /home/user/projects/my-app
- 用户: developer

## 最近活动
- 修改: auth.controller.js
- 修改: user.service.js
- 修改: progress.md
```

### 场景3：遇到问题时的问题复盘

#### 操作步骤：

1. **启动问题复盘**
   ```bash
   # 方法A：交互式复盘
   ~/.claude/scripts/postmortem.sh

   # 方法B：直接提供信息
   ~/.claude/scripts/postmortem.sh --title "数据库连接超时" --desc "生产环境数据库连接频繁超时"
   ```

2. **填写复盘文档**
   ```bash
   # 查看生成的复盘文档
   latest_postmortem=$(ls -t docs/postmortem/*.md | head -1)
   echo "复盘文档: $latest_postmortem"

   # 编辑文档填写详细信息
   # 可以使用vim、nano或其他编辑器
   vim "$latest_postmortem"
   ```

3. **执行预防措施**
   ```bash
   # 根据复盘文档中的"预防措施"章节
   # 执行相应的改进措施

   # 示例：更新项目配置
   echo "# 从复盘中学到的教训" >> CLAUDE.md
   echo "1. 数据库连接必须设置合理的超时时间" >> CLAUDE.md
   echo "2. 生产环境配置需要独立验证" >> CLAUDE.md
   ```

#### 复盘文档结构：
```markdown
# 问题复盘: [问题标题]
## 元数据
**时间**: 2026-03-21 14:30:22
**状态**: ✅ 已解决
**严重性**: ⭐⭐ 中
**分类**: [bug, 性能, 安全, 文档, 流程, 其他]

## 问题描述
[详细描述问题]

## 根本原因分析
### 表面原因
1. [原因1]
2. [原因2]

### 预防措施
- [ ] 短期措施 (1周内)
- [ ] 中期措施 (1月内)
- [ ] 长期措施 (3月内)
```

---

## 多项目管理操作步骤

### 步骤1：设置指挥室项目

```bash
# 创建指挥室目录
mkdir -p ~/claude-command-center
cd ~/claude-command-center

# 初始化指挥室项目
~/.claude/scripts/init-project.sh --name "Command Center"

# 创建项目注册表
cat > projects.json << 'EOF'
{
  "projects": [
    {
      "name": "my-app",
      "path": "/home/user/projects/my-app",
      "status": "active",
      "last_updated": "$(date -I)"
    },
    {
      "name": "api-gateway",
      "path": "/home/user/projects/api-gateway",
      "status": "planning",
      "last_updated": "$(date -I)"
    }
  ]
}
EOF
```

### 步骤2：添加项目管理脚本

```bash
# 创建项目管理脚本
cat > ~/.claude/scripts/manage-projects.sh << 'EOF'
#!/bin/bash
# 项目管理工具

COMMAND_CENTER="$HOME/claude-command-center"
PROJECTS_FILE="$COMMAND_CENTER/projects.json"

add_project() {
    read -p "项目名称: " name
    read -p "项目路径: " path

    # 标准化路径
    path=$(realpath "$path" 2>/dev/null || echo "$path")

    # 更新JSON文件
    if command -v jq &> /dev/null; then
        jq ".projects += [{\"name\": \"$name\", \"path\": \"$path\", \"status\": \"active\", \"last_updated\": \"$(date -I)\"}]" \
            "$PROJECTS_FILE" > tmp.json && mv tmp.json "$PROJECTS_FILE"
    else
        echo "请安装jq工具或手动编辑 $PROJECTS_FILE"
    fi
}

list_projects() {
    echo "📋 监控中的项目:"
    echo ""

    if command -v jq &> /dev/null; then
        jq -r '.projects[] | "  \(.name) (\(.status)): \(.path)"' "$PROJECTS_FILE"
    else
        grep -E '"name"|"path"|"status"' "$PROJECTS_FILE" | \
            sed 's/.*"name": "\([^"]*\)".*/  名称: \1/; s/.*"path": "\([^"]*\)".*/  路径: \1/; s/.*"status": "\([^"]*\)".*/  状态: \1/' | \
            paste -d ' ' - - -
    fi
}

# 主函数
case "${1:-}" in
    add) add_project ;;
    list) list_projects ;;
    *) echo "用法: $0 [add|list]" ;;
esac
EOF

chmod +x ~/.claude/scripts/manage-projects.sh
```

### 步骤3：使用全局视图

```bash
# 查看所有项目状态
~/.claude/scripts/recap.sh --global

# 或使用项目管理工具
~/.claude/scripts/manage-projects.sh list

# 添加新项目
~/.claude/scripts/manage-projects.sh add
```

### 步骤4：跨项目协作示例

```bash
# 在项目A中工作
cd ~/projects/project-a
# 完成重要架构决策
echo "## 架构决策: 使用GraphQL替代REST" >> docs/progress.md
~/.claude/scripts/checkpoint.sh

# 在项目B中参考项目A的决策
cd ~/projects/project-b
# 查看项目A的最新进展
cat ~/projects/project-a/docs/progress.md | tail -10

# 应用到项目B
echo "## 参考项目A的架构决策" >> docs/progress.md
echo "- 考虑使用GraphQL进行数据查询" >> docs/progress.md
~/.claude/scripts/checkpoint.sh
```

---

## 最佳实践实施指南

### 实践1：记忆系统使用最佳实践

#### ✅ 正确做法：

1. **频繁保存小进展**
   ```bash
   # 每完成一个小任务就保存
   ~/.claude/scripts/checkpoint.sh

   # 或者设置定时保存
   */30 * * * * ~/.claude/scripts/checkpoint.sh >/dev/null 2>&1
   ```

2. **使用有意义的checkpoint消息**
   ```bash
   # 在progress.md中添加具体描述
   echo "## 完成的功能 @ $(date '+%H:%M:%S')" >> docs/progress.md
   echo "- 实现了用户登录API，支持JWT令牌" >> docs/progress.md
   echo "- 添加了密码加密和验证逻辑" >> docs/progress.md
   echo "- 编写了单元测试，覆盖率85%" >> docs/progress.md
   ```

3. **结构化记忆文件**
   ```bash
   # 每日memory文件结构示例
   cat > docs/memory/template.md << 'EOF'
   ---
   date: YYYY-MM-DD
   project: [项目名]
   user: [用户名]
   ---

   ## 上午工作 (09:00-12:00)
   - [ ] 任务1
   - [ ] 任务2

   ## 下午工作 (13:00-18:00)
   - [ ] 任务3
   - [ ] 任务4

   ## 重要决策
   1. [决策内容]

   ## 遇到的问题
   - [问题描述]

   ## 明日计划
   - [ ] 计划任务
   EOF
   ```

#### ❌ 错误做法：
- 一天只保存一次检查点
- 使用模糊的描述："做了一些工作"
- 不记录决策背景和原因

### 实践2：项目结构最佳实践

#### 标准项目结构：

```bash
# 创建标准项目结构
~/projects/standard-project/
├── CLAUDE.md                    # 项目特定配置
├── docs/
│   ├── progress.md              # 保持简洁，只当前状态
│   ├── memory/                  # 按日期组织的详细日志
│   │   ├── 2026-03-20.md
│   │   └── 2026-03-21.md
│   ├── requirements/            # 需求文档
│   │   ├── 001-用户认证.md
│   │   └── 002-支付系统.md
│   ├── features/               # 功能设计
│   │   └── auth/
│   │       ├── design.md
│   │       └── api-spec.md
│   └── postmortem/             # 问题复盘
│       └── 20260321_auth-bug.md
└── src/                        # 源代码
```

#### 实施步骤：

1. **初始化标准结构**
   ```bash
   # 使用初始化脚本
   ~/.claude/scripts/init-project.sh --name "standard-project" --path "./projects/standard-project"
   ```

2. **维护文档索引**
   ```bash
   # 更新需求文档索引
   cat > docs/requirements/README.md << 'EOF'
   # 需求文档索引

   | 编号 | 标题 | 状态 | 负责人 | 创建日期 | 更新日期 |
   |------|------|------|--------|----------|----------|
   | 001 | 用户认证 | 进行中 | 张三 | 2026-03-20 | 2026-03-21 |
   | 002 | 支付系统 | 待开始 | 李四 | 2026-03-21 | 2026-03-21 |

   ## 文档规范
   1. 需求文档使用三位数字编号: 001-, 002-, ...
   2. 每份需求文档必须包含9个标准章节
   3. 需求变更必须更新此索引表
   EOF
   ```

3. **定期整理和归档**
   ```bash
   # 每月初整理上个月的memory文件
   # 创建归档脚本
   cat > ~/.claude/scripts/archive-memory.sh << 'EOF'
   #!/bin/bash
   # 归档上个月的memory文件

   PROJECT_DIR="${1:-.}"
   MEMORY_DIR="$PROJECT_DIR/docs/memory"
   ARCHIVE_DIR="$PROJECT_DIR/docs/archive"

   mkdir -p "$ARCHIVE_DIR"

   # 归档上个月的文件
   LAST_MONTH=$(date -d "last month" +%Y-%m)
   find "$MEMORY_DIR" -name "${LAST_MONTH}-*.md" -exec mv {} "$ARCHIVE_DIR/" \;

   # 压缩3个月前的归档文件
   find "$ARCHIVE_DIR" -name "*.md" -mtime +90 -exec gzip {} \;

   echo "✅ 归档完成: $LAST_MONTH 的文件已移动到 $ARCHIVE_DIR/"
   EOF

   chmod +x ~/.claude/scripts/archive-memory.sh
   ```

### 实践3：团队协作最佳实践

#### 步骤1：共享全局配置

```bash
# 创建团队配置仓库
git clone git@github.com:your-team/claude-config.git ~/.claude-team

# 链接到个人配置
ln -sf ~/.claude-team/CLAUDE.md ~/.claude/CLAUDE.md
ln -sf ~/.claude-team/CLAUDE-TEMPLATE.md ~/.claude/CLAUDE-TEMPLATE.md

# 同步团队脚本
cp -r ~/.claude-team/scripts/* ~/.claude/scripts/
chmod +x ~/.claude/scripts/*.sh
```

#### 步骤2：建立团队协作流程

```bash
# 创建团队协作脚本
cat > ~/.claude/scripts/team-sync.sh << 'EOF'
#!/bin/bash
# 团队工作同步脚本

# 同步memory文件到团队仓库
sync_memories() {
    local project_dir="$1"
    local team_repo="$2"

    cd "$project_dir"

    # 检查是否是git仓库
    if git rev-parse --git-dir > /dev/null 2>&1; then
        # 添加memory文件
        git add docs/memory/*.md docs/progress.md

        # 检查是否有变化
        if ! git diff --cached --quiet; then
            git commit -m "chore: sync work memory $(date '+%Y-%m-%d %H:%M')"
            git push
            echo "✅ Memory同步完成"
        else
            echo "ℹ️  没有需要同步的更改"
        fi
    fi
}

# 同步多个项目
sync_all_projects() {
    local command_center="$HOME/claude-command-center"
    local projects_file="$command_center/projects.json"

    if [ ! -f "$projects_file" ]; then
        echo "❌ 未找到projects.json"
        return 1
    fi

    # 使用jq解析项目路径
    if command -v jq &> /dev/null; then
        jq -r '.projects[].path' "$projects_file" | while read project_path; do
            if [ -d "$project_path" ]; then
                echo "同步项目: $(basename "$project_path")"
                sync_memories "$project_path"
                echo ""
            fi
        done
    fi
}

case "${1:-}" in
    all) sync_all_projects ;;
    *) sync_memories "." "${2:-}" ;;
esac
EOF

chmod +x ~/.claude/scripts/team-sync.sh
```

#### 步骤3：定期团队复盘会议

```bash
# 创建周度复盘脚本
cat > ~/.claude/scripts/weekly-review.sh << 'EOF'
#!/bin/bash
# 周度工作复盘

generate_weekly_report() {
    local week_start=$(date -d "last monday" +%Y-%m-%d)
    local week_end=$(date -d "this sunday" +%Y-%m-%d)

    echo "# 工作周报 ($week_start ~ $week_end)"
    echo ""
    echo "## 📊 数据概览"
    echo ""

    # 统计checkpoint数量
    checkpoint_count=$(find docs/memory -name "*.md" -newermt "$week_start" ! -newermt "$week_end" | wc -l)
    echo "- **Checkpoint次数**: $checkpoint_count"

    # 统计问题复盘数量
    postmortem_count=$(find docs/postmortem -name "*.md" -newermt "$week_start" ! -newermt "$week_end" | wc -l)
    echo "- **问题复盘数量**: $postmortem_count"

    # 统计文档编写
    doc_count=$(find docs/requirements docs/features -name "*.md" -newermt "$week_start" ! -newermt "$week_end" | wc -l)
    echo "- **文档编写数量**: $doc_count"

    echo ""
    echo "## 🎯 完成的工作"
    echo ""

    # 提取本周progress.md中的完成事项
    if [ -f "docs/progress.md" ]; then
        grep -A 2 "## 完成的工作" docs/progress.md | tail -10
    fi

    echo ""
    echo "## 🐛 遇到的问题"
    echo ""

    # 列出本周的复盘问题
    find docs/postmortem -name "*.md" -newermt "$week_start" ! -newermt "$week_end" | while read file; do
        title=$(head -1 "$file" | sed 's/# 问题复盘: //')
        echo "- $title"
    done

    echo ""
    echo "## 📅 下周计划"
    echo ""
    echo "- [ ] 计划任务1"
    echo "- [ ] 计划任务2"
}

# 生成报告
generate_weekly_report > "weekly-report-$(date +%Y%m%d).md"
echo "✅ 周报已生成: weekly-report-$(date +%Y%m%d).md"
EOF

chmod +x ~/.claude/scripts/weekly-review.sh
```

### 实践4：性能优化最佳实践

#### 优化1：定期清理旧文件

```bash
# 创建清理脚本
cat > ~/.claude/scripts/cleanup.sh << 'EOF'
#!/bin/bash
# 工作流清理维护

KEEP_DAYS=${1:-30}  # 默认保留30天

echo "🧹 开始清理工作..."
echo "保留最近 $KEEP_DAYS 天的文件"
echo ""

# 1. 清理旧memory文件
echo "清理memory文件..."
find docs/memory -name "*.md" -mtime +$KEEP_DAYS -delete
echo "✅ 清理完成"

# 2. 压缩30-90天的文件
echo "压缩归档文件..."
find docs/memory -name "*.md" -mtime +30 -mtime -90 -exec gzip {} \;
echo "✅ 压缩完成"

# 3. 清理临时锁文件
echo "清理锁文件..."
find docs/memory -name "*.lock" -mmin +60 -delete 2>/dev/null || true
echo "✅ 锁文件清理完成"

# 4. 清理postmortem备份
echo "清理复盘备份..."
find docs/postmortem -name "*.backup.*" -mtime +7 -delete
echo "✅ 备份清理完成"

echo ""
echo "🎉 所有清理任务完成"
EOF

chmod +x ~/.claude/scripts/cleanup.sh

# 设置定时清理（每周日凌晨2点）
(crontab -l 2>/dev/null; echo "0 2 * * 0 ~/.claude/scripts/cleanup.sh >> ~/.claude/logs/cleanup.log 2>&1") | crontab -
```

#### 优化2：启用增量保存

```bash
# 配置增量保存
cat > ~/.claude/auto-checkpoint.conf << 'EOF'
# 自动保存配置
interval_minutes=30
max_conversation_turns=50
enable_linear_sync=false

# 性能优化选项
enable_incremental_save=true
max_memory_file_size_kb=1024
skip_directories="node_modules,.git,temp,build,dist"
max_file_size_mb=10

# 通知设置
enable_notifications=true
notification_method="desktop"
EOF

echo "✅ 性能优化配置已更新"
```

#### 优化3：创建内存索引

```bash
# 创建memory索引脚本
cat > ~/.claude/scripts/create-index.sh << 'EOF'
#!/bin/bash
# 创建memory文件索引

INDEX_FILE="docs/memory/INDEX.md"

echo "# Memory文件索引" > "$INDEX_FILE"
echo "最后更新: $(date)" >> "$INDEX_FILE"
echo "" >> "$INDEX_FILE"
echo "| 日期 | 文件 | 摘要 | 大小 |" >> "$INDEX_FILE"
echo "|------|------|------|------|" >> "$INDEX_FILE"

# 遍历所有memory文件
find docs/memory -name "*.md" -type f | sort | while read file; do
    filename=$(basename "$file")
    filedate="${filename%.md}"

    # 提取摘要（第一行非元数据的内容）
    summary=$(grep -v "^---" "$file" | grep -v "^$" | head -1 | sed 's/^## //' | cut -c1-50)

    # 获取文件大小
    size=$(stat -f%z "$file" 2>/dev/null || stat -c%s "$file" 2>/dev/null)
    size_kb=$((size / 1024))

    echo "| $filedate | $filename | $summary... | ${size_kb}KB |" >> "$INDEX_FILE"
done

echo "" >> "$INDEX_FILE"
echo "## 搜索提示" >> "$INDEX_FILE"
echo "- 按日期搜索: grep '2026-03-*' docs/memory/*.md" >> "$INDEX_FILE"
echo "- 按关键词搜索: grep -r '关键词' docs/memory/" >> "$INDEX_FILE"
echo "- 查看最新文件: tail -20 docs/memory/\$(date +%Y-%m-%d).md" >> "$INDEX_FILE"

echo "✅ 索引创建完成: $INDEX_FILE"
EOF

chmod +x ~/.claude/scripts/create-index.sh

# 每月初更新索引
(crontab -l 2>/dev/null; echo "0 0 1 * * ~/.claude/scripts/create-index.sh >> ~/.claude/logs/index.log 2>&1") | crontab -
```

---

## 故障排除与维护

### 常见问题解决

#### 问题1：命令找不到或权限错误

**症状**:
```bash
bash: /command/not/found 或 Permission denied
```

**解决步骤**:
```bash
# 1. 检查脚本权限
chmod +x ~/.claude/scripts/*.sh

# 2. 添加到PATH
echo 'export PATH="$PATH:$HOME/.claude/scripts"' >> ~/.bashrc
echo 'export PATH="$PATH:$HOME/.claude/scripts"' >> ~/.zshrc  # 如果使用zsh
source ~/.bashrc  # 或 source ~/.zshrc

# 3. 使用完整路径
~/.claude/scripts/checkpoint.sh

# 4. 检查文件编码（Windows特别需要）
dos2unix ~/.claude/scripts/*.sh 2>/dev/null || echo "跳过dos2unix"
```

#### 问题2：memory文件不更新

**症状**: checkpoint运行成功但文件无变化

**解决步骤**:
```bash
# 1. 检查文件权限
ls -la docs/memory/
chmod 644 docs/memory/*.md

# 2. 检查磁盘空间
df -h .

# 3. 检查锁文件
rm -f docs/memory/.checkpoint.lock

# 4. 启用调试模式
DEBUG=true ~/.claude/scripts/checkpoint.sh

# 5. 检查目录是否存在
mkdir -p docs/memory
```

#### 问题3：脚本执行错误

**症状**: 脚本执行时报语法错误

**解决步骤**:
```bash
# 1. 检查bash版本
bash --version

# 2. 验证脚本语法
bash -n ~/.claude/scripts/checkpoint.sh

# 3. 跨平台兼容性检查
# 检查行结束符
file ~/.claude/scripts/*.sh

# 如果是Windows，转换行结束符
sed -i 's/\r$//' ~/.claude/scripts/*.sh

# 4. 检查依赖工具
which git which date which find
```

### 定期维护任务

#### 每周维护清单：

```bash
# 创建周维护脚本
cat > ~/.claude/scripts/weekly-maintenance.sh << 'EOF'
#!/bin/bash
# 周度系统维护

echo "🛠️  开始周度维护..."
echo ""

# 1. 验证系统完整性
echo "1. 验证系统完整性..."
~/.claude/scripts/validate.sh
echo ""

# 2. 清理旧文件
echo "2. 清理旧文件..."
~/.claude/scripts/cleanup.sh 30
echo ""

# 3. 更新索引
echo "3. 更新索引..."
~/.claude/scripts/create-index.sh
echo ""

# 4. 备份配置
echo "4. 备份配置..."
BACKUP_DIR="$HOME/.claude-backups"
mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/claude-config-$(date +%Y%m%d).tar.gz" \
    ~/.claude/CLAUDE.md \
    ~/.claude/CLAUDE-TEMPLATE.md \
    ~/.claude/scripts/ \
    ~/.claude/auto-checkpoint.conf \
    2>/dev/null
echo "✅ 备份完成: $BACKUP_DIR/claude-config-$(date +%Y%m%d).tar.gz"
echo ""

# 5. 检查磁盘使用
echo "5. 磁盘使用情况..."
du -sh ~/.claude/ ~/.claude-backups/ 2>/dev/null || true
echo ""

echo "🎉 周度维护完成"
EOF

chmod +x ~/.claude/scripts/weekly-maintenance.sh

# 每周一早上8点执行
(crontab -l 2>/dev/null; echo "0 8 * * 1 ~/.claude/scripts/weekly-maintenance.sh >> ~/.claude/logs/maintenance.log 2>&1") | crontab -
```

#### 紧急修复脚本：

```bash
# 创建紧急修复脚本
cat > ~/.claude/scripts/emergency-fix.sh << 'EOF'
#!/bin/bash
# 紧急修复工具

echo "🚨 紧急修复模式"
echo ""

case "${1:-}" in
    reset-locks)
        echo "清除所有锁文件..."
        find . -name "*.lock" -type f -delete 2>/dev/null
        echo "✅ 锁文件已清除"
        ;;

    repair-progress)
        echo "修复progress.md文件..."
        if [ -f "docs/progress.md" ]; then
            cp docs/progress.md "docs/progress.md.backup.$(date +%Y%m%d_%H%M%S)"
            echo "# 项目进度快照 (修复版)" > docs/progress.md
            echo "**修复时间**: $(date)" >> docs/progress.md
            echo "**状态**: 修复完成" >> docs/progress.md
            echo "" >> docs/progress.md
            echo "> 原始文件已备份为 .backup 文件" >> docs/progress.md
            echo "✅ progress.md 已修复"
        else
            echo "⚠️  progress.md 不存在，创建新文件"
            echo "# 项目进度快照" > docs/progress.md
            echo "**创建时间**: $(date)" >> docs/progress.md
        fi
        ;;

    check-environment)
        echo "检查环境..."
        echo "1. Git: $(git --version 2>/dev/null || echo '未安装')"
        echo "2. Claude: $(claude --version 2>/dev/null || echo '未安装')"
        echo "3. 当前目录: $(pwd)"
        echo "4. 用户: $(whoami)"
        echo "5. 日期: $(date)"
        ;;

    *)
        echo "可用命令:"
        echo "  reset-locks     - 清除所有锁文件"
        echo "  repair-progress - 修复损坏的progress.md"
        echo "  check-environment - 检查系统环境"
        ;;
esac
EOF

chmod +x ~/.claude/scripts/emergency-fix.sh
```

---

## 快速参考手册

### 命令速查表

| 命令 | 功能 | 使用示例 |
|------|------|----------|
| `checkpoint.sh` | 保存工作进展 | `~/.claude/scripts/checkpoint.sh` |
| `recap.sh` | 恢复工作上下文 | `~/.claude/scripts/recap.sh` |
| `recap.sh --global` | 查看全局状态 | `~/.claude/scripts/recap.sh --global` |
| `init-project.sh` | 初始化新项目 | `~/.claude/scripts/init-project.sh --name "项目名"` |
| `postmortem.sh` | 问题复盘 | `~/.claude/scripts/postmortem.sh --title "问题标题"` |
| `cleanup.sh` | 清理旧文件 | `~/.claude/scripts/cleanup.sh 30` |
| `weekly-review.sh` | 生成周报 | `~/.claude/scripts/weekly-review.sh` |
| `emergency-fix.sh` | 紧急修复 | `~/.claude/scripts/emergency-fix.sh reset-locks` |

### 配置文件说明

#### `~/.claude/CLAUDE.md`
```markdown
# 全局开发规范
# 包含团队统一的开发规范和标准
```

#### `~/.claude/auto-checkpoint.conf`
```ini
# 自动保存配置
interval_minutes=30          # 检查点间隔（分钟）
max_conversation_turns=50    # 最大对话轮数
enable_linear_sync=false     # 是否启用Linear同步
enable_incremental_save=true # 增量保存
max_memory_file_size_kb=1024 # memory文件最大大小
```

#### `~/claude-command-center/projects.json`
```json
{
  "projects": [
    {
      "name": "项目名",
      "path": "/项目/路径",
      "status": "active",
      "last_updated": "2026-03-21"
    }
  ]
}
```

### 文件结构参考

```
~/.claude/                    # 全局配置目录
├── CLAUDE.md                # 全局规范
├── CLAUDE-TEMPLATE.md       # 项目模板
├── commands/                # 命令定义
├── scripts/                 # 脚本文件
│   ├── checkpoint.sh        # 检查点脚本
│   ├── recap.sh            # 恢复脚本
│   ├── init-project.sh     # 项目初始化
│   ├── postmortem.sh       # 问题复盘
│   ├── cleanup.sh          # 清理脚本
│   └── emergency-fix.sh    # 紧急修复
└── auto-checkpoint.conf    # 自动保存配置

~/projects/                  # 项目目录
├── my-app/                 # 项目A
│   ├── CLAUDE.md           # 项目配置
│   ├── docs/               # 文档目录
│   └── src/                # 源代码
└── api-gateway/            # 项目B

~/claude-command-center/    # 指挥室目录
├── CLAUDE.md              # 指挥室配置
├── projects.json          # 项目注册表
└── docs/                  # 全局文档
```

### 日常工作检查清单

#### 开始工作时：
- [ ] 进入项目目录：`cd ~/projects/项目名`
- [ ] 恢复上下文：`~/.claude/scripts/recap.sh`
- [ ] 查看今日计划：`tail -10 docs/progress.md`

#### 工作中：
- [ ] 每30分钟自动保存（如果配置了自动保存）
- [ ] 完成重要任务后手动保存：`~/.claude/scripts/checkpoint.sh`
- [ ] 遇到问题及时记录：`~/.claude/scripts/postmortem.sh`

#### 结束工作时：
- [ ] 更新进度文件：记录完成的工作
- [ ] 保存最终检查点：`~/.claude/scripts/checkpoint.sh`
- [ ] 清理临时文件：`rm -f docs/memory/.checkpoint.lock`

#### 每周五：
- [ ] 运行周度复盘：`~/.claude/scripts/weekly-review.sh`
- [ ] 清理旧文件：`~/.claude/scripts/cleanup.sh`
- [ ] 备份配置：`tar -czf backup.tar.gz ~/.claude/`

### 故障排除流程图

```
问题发生
    ↓
检查症状 → 命令错误 → 检查权限和PATH
    ↓
memory不更新 → 检查锁文件和磁盘空间
    ↓
脚本错误 → 检查语法和依赖
    ↓
使用紧急修复 → ~/.claude/scripts/emergency-fix.sh
    ↓
问题解决 → 记录到postmortem
```

---

## 总结与下一步

### 已实现的核心功能

✅ **三层记忆系统**：解决上下文丢失问题
✅ **多项目管理**：通过指挥室统一管理
✅ **知识沉淀**：通过复盘机制积累经验
✅ **团队协作**：共享配置和记忆文件
✅ **自动化维护**：定期清理和备份

### 推荐的学习路径

1. **第1周**：掌握基础工作流（checkpoint、recap）
2. **第2周**：学习多项目管理（指挥室设置）
3. **第3周**：实施最佳实践（结构化记忆、团队协作）
4. **第4周**：掌握高级功能（自动化、性能优化）

### 获取帮助

- **文档**：本文档的"故障排除与维护"章节
- **脚本**：使用 `~/.claude/scripts/help.sh`（如果已创建）
- **紧急情况**：运行 `~/.claude/scripts/emergency-fix.sh`

### 持续改进

这个操作说明书是一个活的文档，随着你的使用会不断完善：

1. **记录使用体验**：遇到问题及时记录到postmortem
2. **分享最佳实践**：与团队成员分享你的改进
3. **定期回顾**：每月回顾一次工作流效果
4. **贡献改进**：将你的改进提交到团队配置库

---

> 💡 **最后提示**：这个工作流的核心价值不在于工具本身，而在于**形成持续学习和改进的习惯**。每次checkpoint、每次postmortem都在让你的工作方式变得更高效、更可持续。

**开始你的Claude Code CLI高效工作之旅吧！**

---
**文档版本**: 1.0
**最后更新**: 2026-03-21
**作者**: Claude Code CLI 工作流社区
**许可证**: MIT - 自由使用和修改