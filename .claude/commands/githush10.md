---
description: Push code, wire up GitHub Pages via Actions, write a professional README, update repo About/live-site link, and run a secrets scan
---

Run the following steps in order for this repository. It is a static site deployed to GitHub Pages (repo: `edkhng-ai/Edmund`, live URL: `https://edkhng-ai.github.io/Edmund/`). Use PowerShell for git/gh commands (refresh `$env:Path` from Machine+User scope first if `git`/`gh` aren't recognized, since PATH doesn't persist between tool calls in this environment).

## 0. Security scan FIRST (before anything is staged or pushed)

Secrets are hard to un-push once they hit a public remote, so this runs before step 1, not after, even though it's listed last in the original ask.

- Grep the working tree (staged + unstaged + untracked) for likely secrets: API keys, access/secret tokens, private keys (`-----BEGIN...PRIVATE KEY-----`), `.env` files, connection strings with embedded credentials, AWS/GCP/Azure key patterns, hardcoded passwords, and anything matching common high-entropy token shapes.
- Check `.gitignore` exists and excludes typical sensitive paths (`.env`, `*.pem`, `*.key`, `node_modules/`, credential files) — create/update it if missing.
- If anything suspicious is found: STOP, do not commit/push, report the exact file/line to the user, and wait for their decision (remove it, .gitignore it, or confirm it's a false positive) before proceeding to step 1.
- If clean, say so briefly and continue.

## 1. Push/update code to GitHub

- `git status` to see what changed.
- Stage relevant changes (`git add -A`, but review the list — don't blindly add files the scan flagged).
- Commit with a descriptive message summarizing what actually changed (not a generic "update").
- `git push`. If push hangs waiting on browser auth, that's expected — GitHub's credential flow can take a minute.

## 2. Create/update GitHub Pages deployment via GitHub Actions

- Check for `.github/workflows/deploy-pages.yml`. If missing, create it; if present, verify it still uses current action versions (`actions/checkout`, `actions/configure-pages`, `actions/upload-pages-artifact`, `actions/deploy-pages`) triggered on `push` to `main` plus `workflow_dispatch`.
- Commit/push if changed.
- Reminder to surface to the user: Pages' "Source" must be set to **GitHub Actions** once in Settings → Pages — this can't be done via git, only via the GitHub UI or `gh api` (see step 4 for `gh` auth notes).

## 3. Write/update a professional README.md

- Cover: project description, **live site link** (`https://edkhng-ai.github.io/Edmund/`), features, getting started (no build step — open `index.html` directly), project structure, deployment (mention the Actions workflow), and customization notes (CSS custom properties at `:root`).
- **Encoding gotcha**: on this machine, overwriting an existing file with the Write tool can silently preserve that file's prior encoding (this repo's README was once corrupted to UTF-16). After writing, verify with:
  `[System.IO.File]::ReadAllBytes(path)[0..3]` — first bytes should be ASCII (e.g. `23 20 ...` for `# `), not `FF FE` or alternating-null. If it's wrong, write the corrected content to the scratchpad dir first, then `Copy-Item -Force` it over the repo file to force clean UTF-8.
- Commit/push.

## 4. Update GitHub repo "About" (description + website link)

- This is repo metadata, not a file — it needs the GitHub API, via `gh repo edit --description "..." --homepage "https://edkhng-ai.github.io/Edmund/"`.
- Check `gh --version` / `gh auth status` first. If `gh` isn't installed, `winget install --id GitHub.cli -e` (note: this may need a UAC prompt approved interactively — if the install silently cancels, tell the user to approve the elevation prompt). If installed but not authenticated, `gh auth login` requires an interactive browser/device-code step the user must complete themselves.
- If `gh` auth truly can't be completed non-interactively, fall back to telling the user the exact About-section fields to paste in manually (description text + `https://edkhng-ai.github.io/Edmund/` as the website) rather than blocking the whole command on this step.

## 5. Final confirmation

Report back concisely: what was committed/pushed, whether Pages/About were updated automatically or need a manual click, and confirm the security scan came back clean (or what was found/fixed).
