# Usage Saver (Claude Code plugin)

Cuts wasted tokens in Claude Code without lowering answer quality: fewer re-reads, smaller tool output, and cheap models for routine side work. Judgment, design, debugging and anything you read stay on the model you chose.

## What it adds
| Piece | What it does |
|---|---|
| Session rules (SessionStart hook) | Loads the lean-usage rules into every session, including after `/clear` and compaction. |
| `usage-saver:lean-scout` (Haiku, read-only) | Broad searches, log/doc triage, "where is X / what uses Y". Returns a short answer with `path:line` evidence. |
| `usage-saver:lean-runner` (Sonnet) | Fully specified mechanical work: run tests/builds, apply an exact change across files, reformat. |
| `usage-saver:usage-saver` skill | Model-choice table, the "handover" summary for starting a fresh chat, and answer-shape rules. |

## Install (each person, once)
```
/plugin marketplace add <org>/usage-saver-plugin
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
    "usage-saver-marketplace": { "source": { "source": "github", "repo": "<org>/usage-saver-plugin" } }
  },
  "enabledPlugins": { "usage-saver@usage-saver-marketplace": true }
}
```

## If you installed the standalone kit before
Run `bash ~/.claude/usage-saver/uninstall.sh` first. Otherwise you get the rules twice (the kit's `CLAUDE.md` block plus this plugin's hook) and two copies of each helper.

## Remove
`/plugin uninstall usage-saver@usage-saver-marketplace`
