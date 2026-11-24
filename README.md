# Usage Saver (Claude Code plugin)

Cuts wasted tokens in Claude Code without lowering answer quality: fewer re-reads, smaller tool output, compact data pulls, and cheap models for routine side work. Judgment, design, debugging and anything you read stay on the model you chose.

## What it adds
| Piece | What it does |
|---|---|
| Session rules (SessionStart hook) | Loads the lean-usage rules, including the data-analysis rules, into every session, also after `/clear` and compaction. |
| `usage-saver:lean-scout` (Haiku, read-only) | Broad searches, log/doc triage, "where is X / what uses Y". Returns a short answer with `path:line` evidence. |
| `usage-saver:lean-runner` (Sonnet) | Fully specified mechanical work: run tests/builds, apply an exact change across files, reformat. |
| `usage-saver:data-scout` (Haiku, read-only) | "Which tool / table / column has X", data freshness. Uses your connectors' read-only tools; returns exact names and gotchas. |
| `usage-saver:data-runner` (Sonnet) | Runs a fully specified beehiiv or data-lake pull, keeps raw rows out of your chat, saves the full result to `~/.cache/usage-saver/data/` as CSV, and returns a summary table. |
| `usage-saver:usage-saver` skill | Model-choice table, the "handover" summary for starting a fresh chat, and answer-shape rules. |
| `usage-saver:data-analysis` skill | Lean, accurate data workflow: route by subject, check freshness, aggregate at the source, batch, delegate big pulls, report with date range and source. Ships `schema-notes.md`, the team's shared notes on our sources. |

## Data analysis: why it saves the most here
Every row a query returns is re-sent with every later message in the chat. The data rules keep results small at the source (aggregate in SQL, date-bound, small `max_rows`, batch beehiiv calls), send big pulls to `data-runner` so your main chat only sees a summary, and stop each session rediscovering schemas by reading `schema-notes.md` and the data connector's playbooks first. Accuracy rules come with it: freshness checked before any date range, and date range, timezone and source stated with every number.

The data helpers use whatever connectors you have connected. They are told to use read-only tools only, and your normal permission prompts still apply to anything else.

**Grow the shared notes:** when you confirm a join key, gotcha or "this table answers that", add a line to `plugins/usage-saver/skills/data-analysis/schema-notes.md` by pull request. Everyone gets it on their next plugin update.

## Install (each person, once)
```
/plugin marketplace add essentiallysports-tech/usage-saver-plugin
/plugin install usage-saver@usage-saver-marketplace
```
Then restart Claude Code (or run `/reload-plugins`). Try a local copy first with `/plugin marketplace add /path/to/usage-saver-plugin`.

## Optional: earlier auto-compaction
Plugins cannot set environment variables, so this one is a single line in `~/.claude/settings.json` (or in your org's managed settings to roll it out to everyone):
```json
{ "env": { "CLAUDE_AUTOCOMPACT_PCT_OVERRIDE": "50" } }
```
It compacts at 50% of the context window instead of the default, which keeps long sessions cheaper.

## Roll out to the whole org
Add the marketplace in managed settings so it installs for everyone:
```json
{
  "extraKnownMarketplaces": {
    "usage-saver-marketplace": { "source": { "source": "github", "repo": "essentiallysports-tech/usage-saver-plugin" } }
  },
  "enabledPlugins": { "usage-saver@usage-saver-marketplace": true }
}
```

## If you installed the standalone kit before
Run `bash ~/.claude/usage-saver/uninstall.sh` first. Otherwise you get the rules twice (the kit's `CLAUDE.md` block plus this plugin's hook) and two copies of each helper.

## Update
`/plugin marketplace update usage-saver-marketplace`, then restart Claude Code. The version is in `plugins/usage-saver/.claude-plugin/plugin.json`; bump it with every change so installs pick it up.

## Remove
`/plugin uninstall usage-saver@usage-saver-marketplace`
