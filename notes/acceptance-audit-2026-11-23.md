# Acceptance audit — 2026-11-23

Scope: existing work in `lilaclilac09/Vibe-Coding-Task-Manager` against
[ACCEPTANCE.md](../ACCEPTANCE.md). Reference format: open-kernel-challenge PR #6,
`notes/acceptance-audit-2026-11-22.md` (read during the preceding repo audit).

## Failing items

| Item | Source rule quoted | Evidence | Fix status |
|---|---|---|---|
| README omits product status and next human action | §5: "Demo ≠ product"; "end with product status `LIVE` / `BLOCKED` / `LOCAL ONLY` + next human action in one line." | README contains only an AI Studio banner and generic link; no app source exists in the seven inspected commits. Homepage null; Pages HTTP 404. | Fixed: README ends with `Status: BLOCKED` and next human action. No product or deployment fix claimed. |

## Inspection and checks

- Read README, AGENTS.md, CLAUDE.md, `.cursor/rules/acceptance.mdc`, ACCEPTANCE.md,
  PR template, workflow and Python verifier. Repo agent rules point back to the
  acceptance standard; no additional product-specific instructions were found.
- `git rev-parse --is-shallow-repository` printed `false`. Read
  `git log --all --format=fuller --stat` for all seven commits and their patches
  (`git log --all -p -- README.md` and the policy/tooling paths). The initial
  commit contains only the banner README; the other six add acceptance tooling.
  Enumerating every historical tree found exactly these paths:

  ```text
  .cursor/rules/acceptance.mdc
  .github/pull_request_template.md
  .github/workflows/acceptance.yml
  ACCEPTANCE.md
  AGENTS.md
  CLAUDE.md
  README.md
  scripts/check_acceptance.py
  ```

- §2: no README/doc claim that this task-manager is verified, working or fixed
  was found. The AI Studio slogan is not evidence of a deployed task-manager.
  PR #1 cites command results and a pushed SHA; its historical external
  107-test run was not independently reproduced. Current checker result below
  is directly observed, not inferred from that PR's claim.
- §4: no app, UI entrypoint, package manifest or UI PR exists. The README's
  banner HTML is documentation, not a runnable task-manager. No browser was
  launched; 390×844 PNG and animated-WebP interaction proof are not applicable,
  and no proof files were created.
- §5 deployment: ran
  `gh api repos/lilaclilac09/Vibe-Coding-Task-Manager --jq '{homepage,default_branch,description}'`:

  ```json
  {"default_branch":"main","description":"Vibe Coding Task Manager","homepage":null}
  ```

  `gh api repos/lilaclilac09/Vibe-Coding-Task-Manager/pages` returned exit 1:

  ```text
  gh: Not Found (HTTP 404)
  {"message":"Not Found","documentation_url":"https://docs.github.com/rest/pages/pages#get-a-apiname-pages-site","status":"404"}
  ```

  No Vercel/Netlify URL was found across all historical tracked files. Tested
  the README's only non-image destination to disprove the possibility that it
  identifies this app:
  `curl -L --max-time 30 -sS -o /dev/null -w 'AI Studio link: HTTP %{http_code}; final URL: %{url_effective}\n' https://aistudio.google.com/apps`.
  Output (query parameters omitted):

  ```text
  AI Studio link: HTTP 200; final URL: https://accounts.google.com/v3/signin/identifier
  ```

  This is a Google sign-in destination, not proof that the task-manager is live.
  No app-specific deployment was available to fetch. Status is `BLOCKED`, not
  `LOCAL ONLY`, because no local app is present either. Unlinked deployments
  remain unknown.
- §5 merge ancestry: PR #1 is `MERGED`, merge SHA
  `fe29fdc5f68e0d49da9eabf13984b86e51ae423a` exists;
  `git merge-base --is-ancestor fe29fdc5f68e0d49da9eabf13984b86e51ae423a origin/main`
  returned exit 0.
- §6: README has no setup/run commands, referenced scripts or env-variable
  instructions. No install/build command or product test suite exists to run.
  Ran the documented Python verifier rather than inventing npm commands.
- §8: fetched all PRs using
  `gh pr list -R lilaclilac09/Vibe-Coding-Task-Manager --state all --json number,title,body,state`
  and passed each body through Python `subprocess.run` to
  `python scripts/check_acceptance.py`:

  ```text
  PR count: 1
  PR #1 (MERGED)
  ACCEPTANCE.md: evidence closer complete
  ```

  No historical missing-closer failure found; no merged body edited.
- §7/secrets: used `git rev-list --all`, `git ls-tree -r --name-only` and
  `git show <commit>:<path>` to scan all seven commits for `.env` paths,
  private-key markers, GitHub/AWS token patterns and long assigned
  secret/password/API-key/token values (values suppressed):

  ```text
  History commits scanned: 7
  Tracked .env paths: []
  Secret-pattern matches (values suppressed): []
  Vercel/Netlify deployment URLs: []
  Scope: heuristic scan, not a guarantee that no secrets exist.
  ```

## Product blocker (§7)

| field | value |
|---|---|
| blocked_on | Task-manager source and app-specific deployment URL are unavailable. |
| evidence | Historical trees contain only banner README and acceptance tooling; homepage null; Pages 404; generic AI Studio link redirects to Google sign-in. |
| impact | degraded audit coverage; runtime/production verification blocked, documentation audit can continue. |
| human_action | Provide the intended task-manager source or its app-specific deployment URL. |
| not_doing | No replacement app, invented setup commands, login bypass, or localhost-as-production claim. |

## Not covered by any rule, not changed

The banner has mismatched heading tags (`h1`/`h2`); no applicable rule mandates
fixing that documentation markup. No rule authorizes inventing task-manager
features or a new app scaffold; neither was added. Banner and slogan preserved.

## Evidence closer

```txt
root cause: The banner-only README omitted the required product-status line; no task-manager source exists in inspected history.
files changed: README.md; notes/acceptance-audit-2026-11-23.md.
checks run: All seven commits and all PRs inspected; PR #1 evidence checker complete; merge ancestry exit 0; deployment metadata, link fetch and secret-pattern outputs recorded above.
manual QA: Documentation-only change; no runnable UI or interaction exists to capture.
verification: Missing status reproduced and corrected; actual API/checker output recorded; no production app claim made.
remaining risks: Unlinked deployments and arbitrary secrets cannot be excluded; product runtime unavailable.
manual steps: Owner opens the audit PR; provide intended task-manager source or app-specific deployment URL.
safe to merge: yes
```
