# Contributing to LivingScene

The single source of truth for **how we build LivingScene** — branching, commits, pull requests,
CI, and releases — across every repo in the org. Written for a solo founder today, but every rule
is chosen so that adding a collaborator later is a *settings toggle*, not a restructure.

**The one principle:** `main` is always releasable. Rigidity lives in automation (a protected
branch + CI + conventions), **not** in a complicated branch layout.

## The repos

| Repo | What | Deploys |
|---|---|---|
| [`LivingScene-hub`](https://github.com/LivingScene/LivingScene-hub) | Internal console — hub.livingscene.ai (Next.js) | Vercel — preview per PR, prod on merge to `main` |
| [`LivingScene-marketing`](https://github.com/LivingScene/LivingScene-marketing) | Public site — livingscene.ai (Next.js) | Vercel — preview per PR, prod on merge to `main` |
| [`LivingScene-client`](https://github.com/LivingScene/LivingScene-client) | Desktop app (Electron monorepo) | GitHub Releases on a `v*` tag → `electron-updater` |

Repo-specific setup, scripts, and release steps live in each repo's **README**. This document is
the shared *process*; the README is the local *detail*.

## TL;DR — the loop

```sh
git switch -c feat/short-name          # branch off main, one concern
# ...work...
git commit -m "feat: add X"            # Conventional Commit
git push -u origin feat/short-name
gh pr create                           # open a PR into main
# CI goes green → squash-merge → branch auto-deletes
```

## Branching — trunk-based

- **`main` is the only long-lived branch.** It's protected and always green.
- Do **all** work on short-lived branches off `main`. Never commit to `main` directly.
- Keep branches **small and short** — one concern, merged within a day or two. Long-lived branches
  drift and cause painful merges.

Branch names: `<type>/<short-kebab-summary>`, where `<type>` matches a commit type below —
`feat/audience-reactions`, `fix/login-redirect`, `chore/bump-next-16`.

## Commits & PR titles — Conventional Commits

Every commit **and every PR title** follows [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(optional-scope): <summary>
```

| Type | Use for | Version bump |
|---|---|---|
| `feat` | a new capability | minor |
| `fix` | a bug fix | patch |
| `perf` | a performance improvement | patch |
| `refactor` | internal change, no behavior change | — |
| `docs` | documentation only | — |
| `test` | tests only | — |
| `build` / `ci` | build system or CI | — |
| `chore` | everything else (deps, tooling) | — |

Add `!` after the type or a `BREAKING CHANGE:` footer for breaking changes (major bump). Scope is
optional; in the client monorepo use it for the workspace (`feat(full):`, `fix(lean):`).

**Why the PR title matters:** we **squash-merge**, so GitHub uses the *PR title* as the final
commit on `main`. A CI check (`PR title`) rejects titles that don't follow the format — that one
line drives the release notes.

## Pull requests

1. Push your branch and open a PR into `main`.
2. Fill in the PR template (it's short).
3. CI must pass — every repo runs at least:
   - **`PR title`** — Conventional Commit format.
   - **CI** — lint + typecheck + build (web) · lint + typecheck + test (client).
   Web repos also get a **Vercel Preview** deployment to click through.
4. **Squash-merge** (the repo default); the branch auto-deletes.
5. Solo today, so you self-merge once checks are green. When a collaborator joins, flip on
   "require 1 approval" — CODEOWNERS already routes the request.

Keep PRs reviewable: small, focused, with a one-line "how I tested this."

## Releases

- **Web (hub, marketing):** continuous — merging to `main` ships to production via Vercel. No tags,
  no version bumps.
- **Desktop (client):** tag-based. `pnpm --filter livingscene release:patch|minor|major` bumps the
  app version and pushes a `v*` tag; the release workflow builds, signs, and publishes the
  installers to GitHub Releases. Details in the client README.

## Issues

Work is **issue-first**, even solo — it's the paper trail and the substrate a teammate steps into.
File via the templates and label with `type:*`, `area:*`, `priority:*`.

---

## When we become a team (what flips on — no restructure)

| Need | Change |
|---|---|
| Code review | Turn on "require 1 approval" in branch protection |
| Review routing | CODEOWNERS (already in place) auto-requests the owner |
| Access control | Add people to GitHub **teams** with scoped permissions |
| Onboarding | This file |
