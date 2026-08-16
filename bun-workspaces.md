# bun workspaces

Root `package.json` owns workspaces. Packages do not pin each other with
`file:` if a workspace name exists — use `"workspace:*"`.

```json
{
  "workspaces": ["packages/*", "projects/*"]
}
```

## Install

- Always `bun install` at the repo root. A `bun install` inside a package
  creates a nested lockfile and you will spend an afternoon on phantom
  versions.
- One `bun.lock`. Commit it.
- `bunx <tool>` for one-shots (`oxlint`, `tsc`). Don't add a root script
  that only wraps a single bunx call.

## Scripts

- Filter: `bun run --filter @scope/pkg test`
- A package script should be runnable from that package directory *and*
  via filter. Hardcoded `../../node_modules/.bin` paths will break.

## Types / lint

Scoped checks only. Never bare `bunx tsc --noEmit` on the whole
monorepo unless that is the job.

```bash
bunx oxlint packages/foo/src/bar.ts
bunx tsc --noEmit -p packages/foo
```

## Gotchas

- bun's `exports` resolution is strict. If a package has `exports`, a
  deep import that isn't listed will fail in bun and pass in Node.
  List the path or stop deep-importing.
- `bun test` is not vitest. Don't assume `vi.mock` exists. Pick one
  runner per package and keep it.
- Optional deps that call `postinstall` (native addons) fail in CI if
  the optional is actually required. Make it a real dependency.
