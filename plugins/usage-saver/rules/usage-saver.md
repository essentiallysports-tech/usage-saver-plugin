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
