# Memos Skill

A CLI skill for interacting with [Memos](https://github.com/usememos/memos) API. Designed for AI agents (Claude Code, OpenClaw, etc.) to create, read, delete, and list memos.

## Features

- Create / Get / Delete / List memos via command line
- Support content from direct argument, file (`--file`), or stdin (`-`)
- UTF-8 first — handles Chinese, emoji, and special characters correctly on Windows/macOS/Linux
- Auto-appends `#AI-Agent` tag to created memos
- JSON output with structured error reporting

## Quick Start

### 1. Install dependency

```bash
pip install requests
```

### 2. Configure

Create a `.env` file in the skill directory:

```env
MEMOS_URL=https://your-memos-instance.com
MEMOS_TOKEN=memos_pat_xxxxx
```

Or set environment variables directly.

### 3. Use

```bash
# Create a memo
python memos.py create "Hello world" "PUBLIC"

# Create from file (recommended for long or non-ASCII content)
python memos.py create --file content.md "PUBLIC"

# Create from stdin (pipe-friendly)
cat content.md | python memos.py create - "PUBLIC"

# Get a memo
python memos.py get <memo_id>

# Delete a memo
python memos.py delete <memo_id>

# List memos
python memos.py list 50
```

All commands return JSON on stdout. Errors go to stderr as JSON.

## Commands

| Command | Description |
|---------|-------------|
| `create <content\|-\|--file path> [visibility]` | Create a memo. Visibility: `PUBLIC` (default), `PRIVATE`, `PROTECTED` |
| `get <id>` | Retrieve a memo by ID |
| `delete <id> [force]` | Delete a memo |
| `list [pageSize]` | List memos (default 20, max 1000) |

## File Structure

```
memos-skill/
├── memos.py       # Main script — CLI + Python API
├── SKILL.md       # Agent-facing skill documentation
├── .env.example   # Environment variable template
├── .gitignore
└── README.md
```

## Python API

You can also use it as a Python module:

```python
from memos import create, get, delete, list_memos

# Create
result = create("My memo content", "PUBLIC")

# Get
memo = get("memos/ABC123")

# Delete
delete("ABC123", force=True)

# List
memos = list_memos(pageSize=50)
```

## Error Handling

All errors are returned as JSON with structured fields:

```json
{
  "error": "Memos API Error",
  "message": "HTTP 404: Not Found - Memo not found",
  "status_code": 404
}
```

## License

MIT
