---
name: git-guardrails-claude-code
description: 设置 Claude Code 钩子，在危险 git 命令（push、reset --hard、clean、branch -D 等）执行前拦截并阻止它们。当用户想防止破坏性 git 操作、添加 git 安全钩子、或在 Claude Code 中阻止 git push/reset 时使用。
---

# 设置 Git 护栏（Setup Git Guardrails）

设置一个 PreToolUse 钩子，在 Claude 执行危险 git 命令之前拦截并阻止它们。

## 会被阻止的命令

- `git push`（含 `--force` 在内的所有变体）
- `git reset --hard`
- `git clean -f` / `git clean -fd`
- `git branch -D`
- `git checkout .` / `git restore .`

被阻止时，Claude 会看到一条消息，告诉它没有权限执行这些命令。

## 步骤

### 1. 询问范围

问用户：只为**本项目**安装（`.claude/settings.json`），还是**所有项目**（`~/.claude/settings.json`）？

### 2. 复制钩子脚本

自带脚本位于：[scripts/block-dangerous-git.sh](scripts/block-dangerous-git.sh)

按范围复制到目标位置：

- **项目**：`.claude/hooks/block-dangerous-git.sh`
- **全局**：`~/.claude/hooks/block-dangerous-git.sh`

用 `chmod +x` 加上执行权限。

### 3. 把钩子加进 settings

加进相应的 settings 文件：

**项目**（`.claude/settings.json`）：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

**全局**（`~/.claude/settings.json`）：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

如果 settings 文件已存在，把钩子合并进现有的 `hooks.PreToolUse` 数组。不要覆盖其他设置。

### 4. 询问定制

问用户是否想在阻止清单中增加或删除某些模式。相应地编辑复制过去的脚本。

### 5. 验证

跑一个快速测试：

```bash
echo '{"tool_input":{"command":"git push origin main"}}' | <path-to-script>
```

它应以退出码 2 退出，并向 stderr 打印一条 BLOCKED 消息。
