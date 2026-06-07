# AGENTS.md — instructions for AI agents

You are working with **pideploy**, a CLI that deploys git repos to a self-hosted
Docker host (Raspberry Pi): on `git push`, a per-repo self-hosted GitHub Actions
runner builds the repo's Docker image and runs it via docker compose; apps appear
in Portainer and are exposed over Tailscale HTTPS.

## Read this first
Run **`pideploy --agent`** for the authoritative operating manual (architecture,
every command and flag, the deploy lifecycle, an end-to-end quickstart, and
troubleshooting). For any single command's options run **`pideploy help <command>`**.
Do not guess flags — confirm with `help`.

To install the companion Claude Code skill:
`pideploy --skill > .claude/skills/pideploy/SKILL.md`

## Output contract (rely on this)
- Data → **stdout** (`key=value`/rows, or JSON with `--json`). Parse stdout.
- Progress / warnings / errors → **stderr** (`pideploy:` / `warn:` / `error:`;
  in `--json` mode errors are `{"error":"...","code":N}` on stderr).
- Exit codes: **0** ok · **1** runtime error · **2** usage error. Always check them.
- The CLI never prompts. Prefer `--json` when parsing programmatically.

## Rules
- ALWAYS `pideploy doctor --json` first; ensure `"ok":true`. If not, `pideploy setup`, re-check.
- The repo MUST have a GitHub remote before `init` (`gh repo create --source=. --private --push`).
- The app MUST listen on the `--port` you pass, and compose maps `127.0.0.1:<port>:<port>`.
- NEVER add a `pull_request` trigger to the generated workflow on a public repo.
- NEVER commit secrets; use GitHub Actions secrets or Portainer env (`init` gitignores `.env`).

## Standard workflow
1. `pideploy doctor --json`
2. `cd <repo>`  (ensure a GitHub remote)
3. `pideploy init --port <P> --json`
4. `pideploy status --json`   (runner active? stack up?)
5. `pideploy serve <P>`        (stdout is the https URL)
Redeploy after changes: `git push` (or `pideploy deploy`).

---
This file is a pointer. The single source of truth is **`pideploy --agent`**.
