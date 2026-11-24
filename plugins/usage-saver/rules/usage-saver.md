## Usage saver
Cut waste, never quality. Judgment, design, debugging, reviews and anything the user reads stay with the main model.
- Read narrowly: search first, then read only the lines you need. Don't re-read a file you already have or just edited.
- Keep tool output small: filter with grep/head/tail and quiet flags; never dump whole logs, lockfiles or minified files.
- Send independent tool calls together in one message. Don't poll or sleep-loop; run long commands in the background.
- Route side work with fixed rules:
  - `usage-saver:lean-scout` (Haiku): broad searches, log/doc triage, "where is X / what uses Y" that would take 4+ reads or large output. Ask for a short answer with path:line evidence.
  - `usage-saver:lean-runner` (Sonnet): fully specified mechanical work (run tests/builds and report failures, apply an exact change across files, reformat). Give exact steps.
  - Everything else, anything unclear, and any task of 3 tool calls or fewer stays with the main model.
- Before relying on a delegated result that matters, check the cited lines yourself.
- Unrelated new task in a long session: suggest /clear. Long task continuing: suggest /compact with a one-line focus.
- Answer first; no restating the question, no recap of unchanged work. Never drop facts, steps or caveats to save tokens.

### Data analysis (beehiiv, the data lake, any data connector)
Every row that lands in the chat is re-sent on every later message. Bring back answers, not raw data.
- Purpose-built tools before raw SQL; a data source's playbook before multi-table joins; a search tool before listing or describing tables.
- Aggregate at the source: filter, GROUP BY, select only the columns needed, bound every query by date, keep `max_rows` small, count before pulling rows.
- Batch calls (e.g. beehiiv `get_post_stats_batch`, 5 posts per call) instead of one call per item. Never re-run a query whose result you already have.
- Pulls that return many rows or wide JSON go to `usage-saver:data-runner`, which returns a compact table and saves full results to CSV; analyse saved CSVs with Python, never by reading them into the chat.
- "Which table/field/tool has X" goes to `usage-saver:data-scout`.
- Quality: check the source's freshness before quoting dates; give date range, timezone and source with every number; when two sources disagree, never average, follow the `usage-saver:data-analysis` skill.
