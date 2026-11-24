# Schema notes (team-maintained)

Durable facts about our data sources, so nobody pays to rediscover them. Add to this file
by pull request when you learn something that will hold for months. Keep each line short.
Live tools win over this file: if a playbook or freshness tool says otherwise, trust the tool
and fix this file. Last reviewed 2026-11-24.

## ES data connector (data lake on AWS Athena + Social Studio + MinuteMedia + syndication)
- Playbooks (`data_playbook`): `choosing_a_data_source`, `newsletter_subscriber_behavior`.
  Fetch the relevant one before identity or multi-table joins: naive ID joins (e.g.
  `user_profiles.ppid = es-readers.ppid`) undercount 2-15x.
- `datalake_query`: read-only Athena; default `max_rows` 50 (up to 2000); auto-appends LIMIT;
  blocks huge unpartitioned scans; costs scanned bytes against a daily budget. Last resort,
  after `datalake_search` and the purpose-built tools.
- Databases: dbt_big_tables, dbt_marts, es-data-lake, dbt_enrich_marts, dbt_enrich_big_tables,
  minutemedia, esstudio, esstudio_unified, dbt_audit_staging, marts.
- For cross-database joins use the fully qualified, double-quoted names from `datalake_join_path`.

### Routing (from the `choosing_a_data_source` playbook; re-fetch it when numbers look wrong)
- Social traffic (Facebook, Instagram, Threads, Reddit) -> `social_*` tools only. The lake's
  GA4 tables and `daily_traffic_by_source` undercount tagged platforms badly (top-20 truncation).
- Traffic to the site vs performance on the platform are different questions with no join key.
  Never add or divide them.
- Syndication (partner accepted/blocked/published) -> `syndication_*`; the only source with today.
  Pageviews from a partner -> `query_articles` / traffic tools.
- Exact pageviews or money -> MinuteMedia (`daily_revenue_by_source`, `newsletter_revenue`).
- Freshness: `social_data_freshness`, `traffic_latest_available_date` (lake),
  `revenue_latest_date` (MinuteMedia). The lake usually runs a day ahead of Social Studio.
- Two sources disagree: Social Studio wins for social traffic; check the lake for a partial day
  before calling a dip real. Never average.
- No X/Twitter, YouTube, LinkedIn or Pinterest data here: say so.
- `traffic_daily.newUsers` in the social tools is empty (as of 2026-09-14): don't report new users.

## beehiiv connector
- `get_post_stats_batch`: up to 5 post IDs per call; per-post summary.
- `get_post_stats`: full single-post detail (bounce, suppression, split test). Use only when needed.
- Newsletter questions over many posts or long ranges: try the ES connector's `newsletter_*`
  tools or the lake's beehiiv tables (e.g. `es-data-lake.beehiiv_raw_base`) before paging the API.

## Add below: tables, join keys and gotchas you've confirmed
