# GitHub

## Contributions

A commit counts if:

- author email is on the GitHub account
- repo is not a fork
- commit is on the default branch (or `gh-pages`)
- you own the repo, or you opened a PR/issue there

Private commits only show with **Include private contributions** on.

Author date is what the graph uses, not committer date. Force-pushing
rewrites SHAs; GitHub may keep the old events until the repo is
**deleted**, not just reset to one commit.

A new private repo's first push is what actually indexes old dates.
Pushing more history onto an already-indexed repo often does nothing
for years it already skipped.

## `gh`

```bash
gh auth status
gh api user --jq .login
gh repo view OWNER/REPO --json name,isPrivate,createdAt
```

`delete_repo` is a separate scope. `repo` can force-push; it cannot
delete. `gh auth refresh -h github.com -s delete_repo` if you need it.

## Achievements

Earned on public, owned repos. Merging your own PR with no review is
YOLO. `Co-authored-by: Name <id+login@users.noreply.github.com>` on a
merged PR is Pair Extraordinaire. Tiers on Pull Shark / Galaxy Brain /
Starstruck are just more of the same action.
