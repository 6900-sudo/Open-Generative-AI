# GBrain Quick Start

GBrain is now integrated with this project. This guide gets you using it in 5 minutes.

## What You Have

✅ Local brain initialized at `~/.gbrain/brain.pglite`
✅ Memory writeback enabled (auto-captures decisions)
✅ MCP configuration ready (`.claude/mcp/gbrain.json`)
✅ 7-verb memory protocol loaded

## Step 1: Restart Claude Code

The MCP wiring loads at startup. Restart Claude Code (desktop or CLI) after the integration is complete.

## Step 2: Test Memory Across Sessions

**In your first Claude Code session:**

Tell Claude Code to remember something:
```
remember: "I'm working on the API caching layer for Open-Generative-AI"
```

Close Claude Code completely.

**In a new session (clear chat history, no context window):**

Ask it back:
```
recall: "what am I working on?"
```

**Expected result:** Claude Code knows the answer even though chat history was cleared. The brain persisted it across sessions.

## Step 3: Capture Knowledge

As you work, capture insights:

```bash
# From terminal
gbrain capture "Redis TTL tuning is the bottleneck, not cache hit rate. Reduces lookup time from 50ms to 2ms."

# Or pipe from script
echo "Implemented batch API response caching" | gbrain capture --stdin

# Or from a file
gbrain capture --file ./session-notes.md
```

## Step 4: Query What You Know

Ask the brain questions:

```bash
# Keyword search (fast)
gbrain search "caching strategy"

# Synthesis with gaps (for decisions)
gbrain think "What's our current approach to API performance?"
```

## The 7 Memory Verbs

When Claude Code is connected, use these tools:

| Verb | Use Case | Example |
|------|----------|---------|
| `remember` | Save a fact for future sessions | "remember: I use async/await in this project" |
| `recall` | Retrieve facts by topic | "recall: our API performance strategy" |
| `entity` | Query about a specific person/company | "entity: people/alice" |
| `synthesize` | Get an answer with sources and gaps | "synthesize: what do we know about the user auth system?" |
| `forget` | Remove a memory | "forget: the old caching approach" |
| `context_pack` | Get a summary of all context | "context_pack: summarize everything about this project" |
| `delta` | What changed since last recall | "delta: show me what's new about the API" |

## CLI Cheat Sheet

```bash
# Capture & retrieve
gbrain capture "My note"
gbrain search "topic"
gbrain think "analytical question?"

# View what you have
gbrain doctor                          # Health check
gbrain config show                     # Current settings
gbrain sources list                    # Knowledge sources

# Memory management (no encoding yet, keyword only)
gbrain remember "fact" --entity people/alice
gbrain recall people/alice
gbrain forget inbox/2026-09-12-xyz123
```

## Troubleshooting

**Brain not working in Claude Code?**
- Restart Claude Code — MCP loads at startup
- Check: `gbrain doctor`
- Verify: `claude mcp list` should show `gbrain` with ✓

**Search returns no results?**
- Normal — keyword-only mode until embeddings configured
- To enable semantic search: set `VOYAGE_API_KEY` and run `gbrain init --force --embedding-model voyage:voyage-4`

**Can't remember things across sessions?**
- Make sure you're using `remember` tool, not just telling Claude Code
- Verify: `gbrain recall <entity>` should return it

## Next: Enable Semantic Search (Optional)

For richer queries, enable embeddings:

```bash
export VOYAGE_API_KEY="pa-..."  # Get from https://www.voyageai.com
gbrain init --force --embedding-model voyage:voyage-4
gbrain sync  # Re-embeds existing pages
```

Then queries like "Who from our portfolio is working on RAG?" actually work.

## Next: Team Brain (Optional)

To share brain across team members with role-scoped access:

1. Follow [company-brain tutorial](https://github.com/garrytan/gbrain/blob/master/docs/tutorials/company-brain.md)
2. Upgrade from PGLite to Supabase
3. Set up OAuth scoping

## Resources

- **Full docs**: `docs/GBRAIN_INTEGRATION.md`
- **GBrain repo**: https://github.com/garrytan/gbrain
- **Health check**: `gbrain doctor`
- **Config**: `~/.gbrain/config.json`

---

**Status:** 🟢 Ready to use. You have cross-session memory + keyword search working now.

**Quick test:**
```bash
gbrain search "Open-Generative-AI"
# Should find the project overview we captured
```

Happy remembering! 🧠
