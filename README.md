# Memos Skill

一个用于与 [Memos](https://github.com/usememos/memos) API 交互的 CLI 技能。为 AI Agent（Claude Code、OpenClaw 等）设计，支持创建、读取、删除和列出备忘录。

## 特性

- 命令行操作：创建 / 获取 / 删除 / 列出备忘录
- 多种内容输入方式：直接传参、文件读取（`--file`）、标准输入（`-`）
- UTF-8 优先 — 正确处理中文、emoji、特殊字符，兼容 Windows/macOS/Linux
- 创建备忘录时自动追加 `#AI-Agent` 标签
- JSON 格式输出，结构化错误报告

## 快速开始

### 1. 安装依赖

```bash
pip install requests
```

### 2. 配置

在技能目录下创建 `.env` 文件：

```env
MEMOS_URL=https://your-memos-instance.com
MEMOS_TOKEN=memos_pat_xxxxx
```

也可以直接设置环境变量。

### 3. 使用

```bash
# 创建备忘录
python memos.py create "Hello world" "PUBLIC"

# 从文件创建（推荐长内容和非 ASCII 内容使用）
python memos.py create --file content.md "PUBLIC"

# 从标准输入创建（管道友好）
cat content.md | python memos.py create - "PUBLIC"

# 获取备忘录
python memos.py get <memo_id>

# 删除备忘录
python memos.py delete <memo_id>

# 列出备忘录
python memos.py list 50
```

所有命令成功时输出 JSON 到 stdout，错误时输出 JSON 到 stderr。

## 命令一览

| 命令 | 说明 |
|------|------|
| `create <content\|-\|--file path> [visibility]` | 创建备忘录。可见性：`PUBLIC`（默认）、`PRIVATE`、`PROTECTED` |
| `get <id>` | 按 ID 获取备忘录 |
| `delete <id> [force]` | 删除备忘录 |
| `list [pageSize]` | 列出备忘录（默认 20，最大 1000） |

## 文件结构

```
memos-skill/
├── memos.py       # 主脚本 — CLI + Python API
├── SKILL.md       # Agent 面向的技能文档
├── env.example    # 环境变量模板
├── .gitignore
└── README.md
```

## Python API

也可以作为 Python 模块使用：

```python
from memos import create, get, delete, list_memos

# 创建
result = create("备忘录内容", "PUBLIC")

# 获取
memo = get("memos/ABC123")

# 删除
delete("ABC123", force=True)

# 列出
memos = list_memos(pageSize=50)
```

## 错误处理

所有错误以 JSON 格式返回，包含结构化字段：

```json
{
  "error": "Memos API Error",
  "message": "HTTP 404: Not Found - Memo not found",
  "status_code": 404
}
```

## License

MIT
