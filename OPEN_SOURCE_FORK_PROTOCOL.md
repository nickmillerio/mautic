# Open Source Fork Protocol

## Remotes
- **`origin`** — the user's personal fork. All commits go here.
- **`upstream`** — the original source repo. Never push here.

**Rule:** Never commit directly to `origin/main`. Always use feature branches.

---

## Initial Setup

```bash
git clone <fork-repo-url> project-folder
cd project-folder
git remote add upstream https://github.com/ORIGINAL-OWNER/project.git
```

Pin dependencies immediately after cloning — do not let them drift:
```bash
npm install          # generates package-lock.json
pip install -r requirements.txt
```

---

## Syncing with Upstream

```bash
git fetch upstream
# Review dependency files (package.json, requirements.txt) for version bumps before proceeding
git stash                        # stash any uncommitted changes first
git checkout main
git pull --rebase origin main    # sync fork with itself
git rebase upstream/main         # apply upstream on top
git stash pop                    # restore stashed changes if needed
```

**On conflicts:** Keep `ours` for custom logic. Accept `theirs` only for critical upstream fixes.

---

## Decision Matrix

| Scenario | Action |
|---|---|
| Minor upstream bug fixes | Sync via rebase |
| Major upstream API change | Do not sync blindly — audit breaking changes, test locally before rebasing |
| Preparing to open a PR upstream | Stop rebasing; preserve commit hashes |
| Active private development | Sync frequently via `fetch upstream` |

---

## Safety Rules

- **Never** run `git pull upstream main` with uncommitted changes on `main`.
- **Always** audit dependency version bumps after upstream syncs. A major bump (e.g., `v1` → `v2`) may break API compatibility — pin to the last working version until the upgrade is validated.
- **Never** push broken code or unresolved conflicts to a PR.

---

## Contributing Back Upstream

```bash
git checkout -b fix/issue-123
# make changes and commits
git push origin fix/issue-123
# open PR from fix/issue-123 → upstream/main (or upstream/dev per repo policy)
```

---

## Response Guidelines

| User situation | Response |
|---|---|
| "Will upstream updates break my code?" | No — forking is an independent copy. Updates don't affect it unless you sync manually. |
| User wants to pull upstream changes | Run `git fetch upstream` first. Audit dependency changes before rebasing. |
| User has merge/rebase conflicts | Identify origin: custom logic → keep `ours`. Library/dependency change → install new version, re-test, then accept `theirs`. |
