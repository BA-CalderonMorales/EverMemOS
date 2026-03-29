# Agent Context for EverMemOS

## What This Is

EverMemOS is a long-term memory system for AI agents. This local instance remembers:
- User preferences ("I prefer minimal explanations")
- Project context ("This repo uses Rust")
- Environment details ("Deploy target is AWS us-east-1")
- Team constraints and patterns

## Quick Start for Agents

**Check if memory service is running:**
```bash
curl -s http://localhost:1995/health | jq .
```

**Store a memory:**
```bash
curl -X POST http://localhost:1995/api/v1/memories \
  -H "Content-Type: application/json" \
  -d '{
    "message_id": "msg_'"$(date +%s)"'",
    "sender": "<AGENT_ID>",
    "content": "User prefers minimal explanations by default",
    "create_time": "'"$(date -Iseconds)"'"
  }'
```

**Recall relevant memories:**
```bash
curl -X GET http://localhost:1995/api/v1/memories/search \
  -H "Content-Type: application/json" \
  -d '{
    "query": "What are the user preferences?",
    "retrieve_method": "hybrid"
  }'
```

## Security Boundaries

**DO NOT store in EverMemOS:**
- API keys or secrets
- Passwords or tokens
- Personal identifiable information (PII)

**OK to store:**
- Coding preferences and style
- Project architecture decisions
- Workflow patterns
- Tool preferences

## Agent Identification

Use these headers for attribution:
- `X-Agent-ID`: Your identifier (e.g., `kimi-cli`, `claude-code`, `copilot`)
- `X-Session-ID`: Unique session identifier

## Troubleshooting

**Service not running?**
```bash
~/.config/evermem-secrets/evermem-session.sh
```

**Need to edit secrets?**
```bash
export SOPS_AGE_KEY_FILE=~/.config/evermem-secrets/identity.txt
sops ~/.config/evermem-secrets/secrets.enc.yaml
```

**Clear all memories (dangerous):**
```bash
curl -X DELETE http://localhost:1995/api/v1/memories/clear
```

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

## Infrastructure Dependencies

⚠️ **Security Issue Identified**: Infrastructure components use outdated versions.

| Component | Current | Latest | Age |
|-----------|---------|--------|-----|
| Elasticsearch | 8.11.0 | 8.17.0 | 2 years |
| Milvus | v2.5.2 | v2.5.10 | 1 year |
| MinIO | 2023-03-20 | 2025-02 | 3 years |
| etcd | v3.5.5 | v3.5.19 | 4 years |

**Action Required**:
1. Test `docker-compose.updated.yaml` for compatibility
2. Run `trivy config docker-compose.yaml` to scan for CVEs
3. Update to newer versions incrementally

See `docker-compose.updated.yaml` for proposed updates.

## Integration with Other Agents

Multiple agents can share this memory instance. Each agent:
1. Reads existing context on session start
2. Stores relevant facts discovered during conversation
3. Queries for relevant memories before responding

The memory system is **conversational** — it extracts facts from natural dialogue. You don't need special syntax; just normal conversation gets parsed for extractable facts.
