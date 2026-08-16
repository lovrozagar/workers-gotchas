# Cloudflare Workers

Bindings live on `env`, not `process.env`. A Worker that reads
`process.env.FOO` is reading the build machine, not the isolate.

## D1

- One logical DB, many replicas. Writes go to primary. A read-after-write
  on a different colo can see stale data unless you use a session bookmark
  (`meta.after` / Sessions API).
- No connection pool. Each query is an RPC. Batch with `db.batch([...])`
  instead of `await` in a loop.
- `max_rows` / `D1_ERROR` on huge result sets — page it. Don't `SELECT *`
  a 100k row table into a Worker.
- Migrations are files. Apply with `wrangler d1 migrations apply`. Do not
  "fix" a failed migration by editing an already-applied file.

## wrangler

- `wrangler.jsonc` over toml. Bindings in config, secrets via
  `wrangler secret put`. Never commit `.dev.vars` if it has real keys.
- `wrangler dev` is not production: local D1 is SQLite on disk, no Smart
  Placement, no same cache topology.
- Custom domains: hostname route on the Worker + DNS. `workers.dev` is
  fine for staging, not for a product.

## Isolates

- CPU time is the limit that bites, not wall clock. Tight JSON parse loops
  and huge `Response` buffering die first.
- No shared heap across requests. "Module-level cache" is per-isolate and
  can vanish between requests. Durable state → D1 / KV / DO / R2.
- `waitUntil` for work after the response. Don't block TTFB on analytics.

## R2

- S3-compatible enough. No egress to Workers on the same account.
- Conditional writes (`If-Match`) if two Workers can put the same key.
- Public buckets via custom domain + cache rules, not by making the
  Worker stream every object.
