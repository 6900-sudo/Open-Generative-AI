# GBrain Integration Guide

## Overview

GBrain is a persistent knowledge brain for AI agents. This project has been configured with GBrain to provide:

- **Cross-session memory** — Claude Code remembers what you tell it across sessions
- **Keyword-based retrieval** — Search through captured notes and memories
- **Persistent facts** — Automatic capture of important decisions and learnings
- **Graph-based knowledge** — Entity relationships and connections

## Setup Status

✅ **Initialized**: PGLite local brain at `~/.gbrain/brain.pglite`
✅ **Memory writeback**: Enabled (salient mode) — captures important decisions automatically
⏳ **Embeddings**: Not yet configured (optional but recommended for semantic search)

## How to Use

### 1. Enable MCP in Claude Code

Add gbrain to Claude Code's MCP servers:

```bash
claude mcp add gbrain -- gbrain serve --surface verbs
```

Then restart Claude Code. You'll have access to the 7-verb memory protocol:
- `remember` — save a fact for future sessions
- `recall` — retrieve remembered facts by topic
- `entity` — query information about a specific person/company/project
- `synthesize` — get a summary with citations and gaps
- `forget` — remove a memory
- `context_pack` — get a summary of context for this brain
- `delta` — get what's changed since last recall

### 2. Quick Test

In a Claude Code session:

```
remember: "My preferred Python style is list comprehensions over loops"
```

Then restart Claude Code and ask:

```
recall: "my Python style"
```

It will remember across sessions — the answer comes from gbrain, not chat history.

### 3. Capturing Knowledge

**Manually capture a note:**
```bash
gbrain capture "Built the API response caching layer. Key insight: Redis TTL management is the bottleneck, not the cache hit rate."
```

**Capture from a file:**
```bash
gbrain capture --file ./notes/2026-09-12-meeting.md
```

**Via stdin (from scripts/pipes):**
```bash
echo "Completed the performance audit" | gbrain capture --stdin
```

### 4. Querying Your Brain

**Keyword search (fast, works now):**
```bash
gbrain search "API performance"
```

**Synthesis with gaps (recommended for decisions):**
```bash
gbrain think "What's our current strategy on caching?"
```

Returns a synthesized answer with sources and an honest note on what the brain doesn't know yet.

## Architecture

### Two Engines, One Contract

- **PGLite** (current): Embedded Postgres via WASM, zero-config, local-only
- **Postgres + Supabase**: For team deployments (100+ users, 10K+ pages)

### Storage Location

All brain data lives in `~/.gbrain/`:
- `brain.pglite` — Postgres database (WASM-based)
- `config.json` — Brain configuration
- `brain-repo/` — Git repo with markdown pages (optional)

### Memory Writeback

When enabled (salient mode), Claude Code automatically captures:
- Direct statements ("I decided to refactor X")
- Commitments ("I'll ship this by Friday")
- Insights ("Learned that Y requires Z")

Excludes: greetings, questions, tool output, code snippets

## Advanced Setup

### Enable Semantic Search (Recommended)

Requires an embedding API key. Pick one:

**Option 1: Voyage (recommended, $0.04/M tokens)**
```bash
export VOYAGE_API_KEY="pa-..."
gbrain init --force --embedding-model voyage:voyage-4 --path ~/.gbrain/brain.pglite
```

**Option 2: OpenAI**
```bash
export OPENAI_API_KEY="sk-..."
gbrain init --force --embedding-model openai:text-embedding-3-small --path ~/.gbrain/brain.pglite
```

After setup:
```bash
gbrain sync   # re-embed existing pages
```

### Team Brain (Multi-User)

Upgrade to Postgres + Supabase for team access with role-scoped privacy:

```bash
gbrain init --supabase
# Guides you through Supabase setup
# Adds OAuth scoping so team members only see allowed pages
```

See [company-brain tutorial](https://github.com/garrytan/gbrain/blob/master/docs/tutorials/company-brain.md) for full setup.

### Bulk Import

Import existing notes:

```bash
gbrain import ~/Notes ~/Documents  --no-embed
```

After setting up embeddings:

```bash
gbrain sync
```

## CLI Cheat Sheet

```bash
# Memory
gbrain remember "fact here" --entity people/alice --provenance meeting
gbrain recall people/alice
gbrain forget slug/inbox/2026-09-12-xyz

# Query
gbrain search "what did Alice say about pricing?"
gbrain think "should we use Redis for caching?"

# Capture
gbrain capture "note here"
gbrain capture --file ./notes.md
echo "from pipe" | gbrain capture --stdin

# Admin
gbrain doctor              # Health check
gbrain config show         # View current config
gbrain search modes        # Show search cost matrix
gbrain schema active       # What page types are available
gbrain sources list        # Manage knowledge sources
```

## FAQ

**Q: Does this send data to external servers?**
A: No. PGLite runs locally. Only API keys you explicitly set (embedding providers) connect externally.

**Q: Can I switch to a team brain later?**
A: Yes. Start with PGLite, graduate to Supabase when you're ready. Pages migrate automatically.

**Q: What if I forget to enable MCP?**
A: The brain still works via CLI (`gbrain search`, `gbrain remember`), but Claude Code won't have memory access. Re-run `claude mcp add gbrain -- gbrain serve --surface verbs`.

**Q: Can multiple agents share one brain?**
A: Yes (with OAuth scoping). Agents on the same PGLite brain see all pages. With Supabase, you can scope per agent.

**Q: Does GBrain work in Claude Code cloud sessions?**
A: Yes. The cloud sandbox has disk space for PGLite. Queries might be slightly slower than on your laptop.

## Next Steps

1. ✅ **Restart Claude Code** — MCP wiring loads on startup
2. **Test memory** — tell Claude Code something, restart, ask it back
3. **Enable embeddings** (optional) — for semantic search
4. **Import notes** (optional) — bulk-load your knowledge
5. **Tune search mode** — `gbrain config set search.mode balanced` if desired (costs more, better results)

## Resources

- **Docs**: https://github.com/garrytan/gbrain
- **Protocol**: `docs/protocol/MEMORY_VERBS_v1.md` (in gbrain repo)
- **Troubleshooting**: `gbrain doctor` catches most issues
- **Contrib Guide**: https://github.com/garrytan/gbrain/blob/master/CONTRIBUTING.md

---

**Health Check:**
```bash
gbrain doctor
gbrain search "test"  # Should show "No results" (keyword-only mode, expected)
```

Happy remembering! 🧠
