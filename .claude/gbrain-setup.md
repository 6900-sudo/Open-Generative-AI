# GBrain Setup for Open-Generative-AI

## What Was Installed

This project now has GBrain integrated for persistent agent memory.

### Installation Summary

| Component | Status | Details |
|-----------|--------|---------|
| **Brain Engine** | ✅ PGLite | Local Postgres (WASM), zero-config at `~/.gbrain/brain.pglite` |
| **Memory Verbs** | ✅ Enabled | 7-verb protocol: remember, recall, entity, synthesize, forget, context_pack, delta |
| **Memory Writeback** | ✅ Enabled | Ambient capture mode (salient) — automatically saves important decisions |
| **MCP Integration** | ✅ Ready | Stdio MCP server configured in `.claude/mcp/gbrain.json` |
| **Search** | ✅ Keyword-only | Vector embeddings optional (requires API key) |
| **Team Brain** | ⏳ Optional | Can upgrade to Supabase multi-user later |

### Files Added

```
├── .claude/mcp/gbrain.json                 # MCP configuration
├── docs/GBRAIN_INTEGRATION.md              # Full integration guide
├── docs/GBRAIN_QUICKSTART.md               # 5-minute quick start
└── ~/.gbrain/                              # Brain home (user's directory)
    ├── brain.pglite                        # PGLite database
    ├── config.json                         # Brain configuration
    └── brain-repo/                         # Optional markdown pages dir
```

### What It Does

**Cross-Session Memory**
- Tell Claude Code to remember something in one session
- Restart Claude Code
- Ask it back → it knows the answer (from brain, not chat history)

**Automatic Capture**
- Important decisions, commitments, and insights are automatically saved
- Skips: greetings, questions, tool output, code snippets

**Knowledge Retrieval**
- `gbrain search "topic"` — fast keyword search
- `gbrain think "question?"` — synthesized answer with gaps
- CLI, MCP, or Claude Code tools — your choice

## How to Use

### Quick Start (5 minutes)

```bash
# 1. Restart Claude Code — MCP loads at startup

# 2. In Claude Code, tell it to remember something:
remember: "I'm refactoring the API response caching"

# 3. Restart Claude Code (clear chat, no context)

# 4. Ask it back:
recall: "what am I working on?"
# → It knows! (from brain, not chat history)

# 5. Capture a note:
gbrain capture "Redis TTL tuning reduced lookup time 50ms → 2ms"

# 6. Search what you know:
gbrain search "caching strategy"
```

### Claude Code Tools Available

When connected via MCP, Claude Code has access to:

- **`remember(fact, entity, provenance)`** — save a memory
- **`recall(entity)`** — retrieve by topic/entity
- **`synthesize(question)`** — get answer with sources + gaps
- **`entity(slug)`** — query specific page
- **`forget(slug)`** — remove memory
- **`context_pack()`** — summary of everything
- **`delta(since_slug)`** — what changed since last recall

### CLI (No MCP)

```bash
gbrain capture "note here"
gbrain search "query"
gbrain think "analytical question?"
gbrain remember "fact" --entity people/alice
gbrain recall people/alice
gbrain doctor  # health check
```

## Configuration

### Current Settings

```bash
gbrain config show
```

Key settings:
- `memory.auto_writeback: salient` — automatic capture enabled
- `search.mode: conservative` — keyword-only (embeddings optional)
- Engine: PGLite (local, zero-cost)

### Enable Semantic Search (Optional)

Requires an embedding API key:

```bash
# Option 1: Voyage (recommended)
export VOYAGE_API_KEY="pa-..."
gbrain init --force --embedding-model voyage:voyage-4

# Option 2: OpenAI
export OPENAI_API_KEY="sk-..."
gbrain init --force --embedding-model openai:text-embedding-3-small

# Sync after setup
gbrain sync
```

Then queries like "Who is working on RAG?" actually work (semantic search).

### Upgrade to Team Brain (Optional)

When ready for multi-user:

```bash
# Follow company-brain tutorial: https://github.com/garrytan/gbrain/blob/master/docs/tutorials/company-brain.md
# Upgrades to Supabase with OAuth role-scoping
```

## Architecture

### Trust Boundary

- **Local only**: PGLite runs locally, all data stays on machine
- **API calls**: Only if you set API keys (embeddings, synthesis)
- **No defaults**: Nothing phones home unless explicitly configured

### Storage

```
~/.gbrain/
├── brain.pglite        # Postgres database (WASM)
├── config.json         # Configuration
├── brain-repo/         # Optional git repo with markdown pages
└── audit/              # Audit logs
```

Delete `~/.gbrain/` to reset completely.

### Data Model

Pages have:
- **Type**: person, company, project, meeting, note, etc.
- **Content**: markdown body + frontmatter
- **Timeline**: captures + edits + synthesis events
- **Graph edges**: auto-linked references (works_at, attended, etc.)
- **Visibility**: shared by default; `visibility: private` for local-only

## Testing

### Verify Installation

```bash
# Health check
gbrain doctor

# List what's in brain
gbrain search "Open-Generative-AI"

# Check MCP is ready
claude mcp list  # Should show gbrain with ✓
```

### Test Cross-Session Memory

**Session 1:**
```bash
# In Claude Code
remember: "Testing cross-session memory on Open-Generative-AI"
```

**Session 2** (restart Claude Code, new session):
```bash
# In Claude Code
recall: "What was I testing?"
# Expected: "Testing cross-session memory on Open-Generative-AI"
```

## Troubleshooting

### Brain not working in Claude Code?

1. Restart Claude Code — MCP loads at startup
2. Check: `gbrain doctor`
3. Verify: `claude mcp list` should show `gbrain`

### Search returns no results?

- Normal in keyword-only mode
- Add API key for embeddings: `export VOYAGE_API_KEY=... && gbrain init --force --embedding-model voyage:voyage-4`

### Memory not persisting?

- Verify using `gbrain recall <entity>` from CLI
- If CLI works but Claude Code doesn't: MCP connection issue (restart Claude Code)

### Brain file corrupted?

```bash
gbrain pglite-repair --dry-run  # Check
gbrain pglite-repair --yes      # Fix in place
```

## Next Steps

1. **✅ Setup complete** — you have working brain right now
2. **Restart Claude Code** — MCP wiring loads at startup
3. **Test memory** — follow "Quick Start" above
4. **Enable embeddings** (optional) — `export VOYAGE_API_KEY=... && gbrain init --force ...`
5. **Explore skills** (optional) — `gbrain advisor` lists 73 available skills
6. **Team setup** (optional) — follow company-brain tutorial for multi-user

## Resources

- **Quick Start**: `docs/GBRAIN_QUICKSTART.md`
- **Full Integration Guide**: `docs/GBRAIN_INTEGRATION.md`
- **GBrain Docs**: https://github.com/garrytan/gbrain
- **Protocol**: https://github.com/garrytan/gbrain/blob/master/docs/protocol/MEMORY_VERBS_v1.md
- **Troubleshooting**: `gbrain doctor`

## Stats

```
Brain Home:     ~/.gbrain/
Engine:         PGLite (Postgres 17 via WASM)
Pages:          1 (project overview)
Embeddings:     Not configured (keyword-only search)
Memory Verbs:   7 (recall, remember, synthesize, entity, forget, context_pack, delta)
Skills:         73 available
Cost:           $0/month (with embeddings: ~$40/mo @ 10K queries)
```

---

**Status: 🟢 Ready to use**

Last updated: 2026-09-12
Setup by: Claude Code Integration
