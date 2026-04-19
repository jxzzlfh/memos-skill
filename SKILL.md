# SKILL.md for the Memos skill

## Purpose

This skill provides a simple interface to the Memos API (https://usememos.com/docs/api). It lets you create, read, delete, and list memos from any AI Agent (such as Claude Code, OpenClaw, etc.).

## How it works

- The skill is implemented in Python and uses the `requests` library.
- It automatically loads configuration from a `.env` file in the skill directory (if present).
- It requires two environment variables (set via `.env` file or system environment):
  - `MEMOS_URL` – **REQUIRED**. Base URL of your Memos instance (e.g., `https://demo.usememos.com`).
  - `MEMOS_TOKEN` – **REQUIRED**. Personal access token for authentication.
- If either `MEMOS_URL` or `MEMOS_TOKEN` is not set, the skill will abort with a clear error.

## Setup

Create a `.env` file in the skill directory:

```env
MEMOS_URL=https://your-memos-instance.com
MEMOS_TOKEN=memos_pat_xxxxx
```

Environment variables take precedence over `.env` file values if both are set.

## Commands

| Command                         | Description                                                                                                                                | Usage example                                      | cd  |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------- | --- |
| `create <content> [visibility]` | Create a new memo. `visibility` defaults to `PUBLIC` (options: PRIVATE, PROTECTED, PUBLIC). The tag `#AI-Agent` is appended automatically. | `python memos.py create "Hello world" "PUBLIC"` |
| `create --file <path> [vis]`    | Create a memo from a file. Recommended for long or non-ASCII content (avoids shell encoding issues).                                       | `python memos.py create --file content.md "PUBLIC"` |
| `create - [visibility]`         | Create a memo from stdin. Pipe-friendly.                                                                                                   | `type content.md \| python memos.py create - "PUBLIC"` |
| `get <id>`                      | Retrieve a memo by its ID. ID can be in format `memos/123` or just `123`.                                                                  | `python memos.py get 123`                       |
| `delete <id> [force]`           | Delete a memo. Optional `force` (true/false) will delete even if it has associated data.                                                   | `python memos.py delete 123 true`               |
| `list [pageSize]`               | List memos with pagination. `pageSize` defaults to 20 (max 1000).                                                                          | `python memos.py list 50`                       |

## Direct Python Usage

You can also run the commands directly with Python:

```bash
# Create a memo (short content)
python skills/memos/memos.py create "My memo content" "PUBLIC"

# Create a memo from a file (recommended for long or non-ASCII content)
python skills/memos/memos.py create --file content.md "PUBLIC"

# Create a memo from stdin (pipe-friendly)
echo "Hello world" | python skills/memos/memos.py create - "PUBLIC"

# Get a memo by ID
python skills/memos/memos.py get j9THXDmYtueosTTeHcC5NA

# Delete a memo
python skills/memos/memos.py delete j9THXDmYtueosTTeHcC5NA

# Delete with force
python skills/memos/memos.py delete j9THXDmYtueosTTeHcC5NA true

# List memos (default 20)
python skills/memos/memos.py list

# List with custom page size
python skills/memos/memos.py list 50
```

All commands return JSON output on success and JSON error messages on stderr when errors occur.

## Referencing Memos in your Agent

When you create or retrieve a memo, the API returns a memo object with a `uid` field. To reference a memo in your agent's output, format it as a markdown link:

```markdown
[memo description](https://demo.usememos.com/memos/{uid})
```

**Example:**
If the API returns `{"uid": "ABC123xyz", "name": "memos/ABC123xyz", ...}`, reference it as:

```markdown
Created [your memo](https://demo.usememos.com/memos/ABC123xyz)
```

The URL format is: `{MEMOS_URL}/memos/{uid}` where `uid` is extracted from the response.

## Error handling

The skill includes comprehensive error handling:

- **API Errors**: Returns detailed error messages including HTTP status codes and API response details
- **Network Errors**: Handles timeouts (30s default) and connection failures
- **Validation**: Validates parameters before making API calls (e.g., visibility options, pageSize range)
- **Exit codes**: Returns 0 on success, 1 on error when run from command line
- **Error output**: All errors are output as JSON to stderr for easy parsing

Example error responses:

```json
{
  "error": "Memos API Error",
  "message": "HTTP 404: Not Found - Memo not found",
  "status_code": 404
}
```

## Extending the skill

- Add new commands by following the pattern of existing ones.
- If the Memos API adds new endpoints, you can create corresponding functions.

---

**NOTE:** This skill is meant for personal use; never share your `MEMOS_TOKEN` publicly.
