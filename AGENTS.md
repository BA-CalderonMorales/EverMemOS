# AGENTS.md - EverMemOS

## Quick Reference

- **Purpose**: Long-term memory system for AI agents
- **Health**: `curl -s http://localhost:1995/health | jq .`
- **Session**: `~/.config/evermem-secrets/evermem-session.sh`

## Memory Types

| Type | Description | Example |
|------|-------------|---------|
| `preference` | User likes/dislikes | "Prefers Rust over Go" |
| `project` | Repo/project specifics | "Project uses PostgreSQL" |
| `environment` | Deployment/config info | "Deploy target is AWS" |
| `instruction` | Explicit directives | "Always run tests first" |
| `team` | Team conventions | "Team uses conventional commits" |
| `constraint` | Hard requirements | "Must support Python 3.9" |
| `tool` | Tool-specific config | "Uses uv for Python deps" |

## API Examples

**Store memory:**
```bash
curl -X POST http://localhost:1995/api/v1/memories \
  -H "Content-Type: application/json" \
  -d '{"message_id": "msg_123", "sender": "agent", "content": "User prefers minimal explanations"}'
```

**Search memories:**
```bash
curl -X GET http://localhost:1995/api/v1/memories/search \
  -H "Content-Type: application/json" \
  -d '{"query": "user preferences", "retrieve_method": "hybrid"}'
```

## Security Boundaries

**DO NOT store:**
- API keys or secrets
- Passwords or tokens
- Personal identifiable information (PII)

**OK to store:**
- Coding preferences and style
- Project architecture decisions
- Workflow patterns
- Tool preferences

## Working Rules

- Stop and explain before major architectural changes
- One change per commit, commit before starting next
- Do not bundle unrelated work into the same commit
