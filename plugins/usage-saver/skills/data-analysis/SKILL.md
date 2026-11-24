---
name: data-analysis
description: Token-lean, accurate workflow for data analysis on EssentiallySports data - beehiiv newsletters (posts, opens, clicks, subscribers, revenue), the ES data lake on AWS (Athena SQL, GA4 traffic, MinuteMedia revenue, articles), social traffic and syndication. Use whenever the user asks for numbers, metrics, reports, trends, comparisons, charts or SQL from these sources, or asks why two numbers disagree.
---

# Data analysis, lean and correct

Every row or JSON blob that enters the conversation is re-sent with every later message,
so a careless 2,000-row pull costs far more than the query itself. Get the answer from the
source in its smallest correct form. Never trade accuracy for tokens: when a rule here would
make an answer less correct, skip the rule.

## Workflow
1. **Pin the question.** Subject (newsletter, site traffic, social, syndication, revenue),
   metric, date range, grain (day/week/post), and timezone. Ask one question if the date
   range or metric is genuinely ambiguous.
2. **Pick the source by subject, not by metric word.** Read `schema-notes.md` in this skill
   folder first; it holds the routing rules and gotchas the team has already learned. For a
   source with a playbook tool (the ES data connector's `data_playbook`), fetch the matching
   playbook before writing multi-table or identity joins. Check certified queries if your
   org has any.
3. **Check freshness** for that source and clamp the date range to what exists.
4. **Prefer purpose-built tools** (e.g. `newsletter_post_metrics`, `newsletter_revenue`,
   `social_traffic_summary`, `query_articles`) over raw SQL: they are cheaper, guarded and
   return compact results. Use raw SQL only when none fits.
5. **Write the smallest correct query:** only needed columns, date-bounded on the partition
   column, aggregated in SQL (GROUP BY, SUM, COUNT, window functions), ordered, with a small
   `max_rows`. Run a COUNT first if you don't know the size.
6. **Delegate big pulls.** If you still need many rows (for joins in Python, distributions,
   or a chart), hand the exact call to `usage-saver:data-runner`: it saves the CSV and
   returns a summary. Analyse that CSV with a Python script that prints only the result.
   Schema hunting ("which table has X") goes to `usage-saver:data-scout`.
7. **Report:** the answer first, then a small table; state date range, timezone, source,
   and any caveat (partial day, sampling, row cap). For a chart, chart the aggregated table,
   never raw rows.

## beehiiv specifics
- Batch stats: `get_post_stats_batch` takes up to 5 post IDs per call; use it over
  per-post `get_post_stats` unless you need bounce, suppression or split-test detail.
- List calls: filter by date/status and keep page size small; page only as far as needed.
- For history across many posts or long ranges, the data lake's beehiiv tables are usually
  cheaper than paging the beehiiv API; see `schema-notes.md`.

## Never
- Never `SELECT *`, never an unbounded date range, never `allow_full_scan` without the user's OK.
- Never average two sources that disagree or pick the convenient one; follow the routing rules.
- Never quote a date range you did not check against the source's freshness.
- Never call write tools (save, send, publish, schedule, delete) during analysis.
- Never drop a caveat, row or number the user needs to save tokens.

## Keep schema-notes.md growing
When you learn a durable fact (a join key, a gotcha, which table answers a common question),
tell the user it is worth adding to `schema-notes.md` in the plugin repo so the whole team
stops rediscovering it.
