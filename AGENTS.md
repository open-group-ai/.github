# AGENTS.md — Open Group (organization)

This file is the global rule set for every repository in the `open-group-ai` organization (Open Group / Grupo Open). Each repo also has its own `AGENTS.md` for local startup, commands, and owners. The repo file wins on local paths and commands; this file wins on invariants.

Keep this file short. Put long guides in the owning repo and leave a pointer here.

Ports: the fleet port registry is [`PORTS.md`](PORTS.md) in this repo. Claim a port block there (PR) before binding it on any shared host.

OpenClaw Controller / NSVX lives here (`openclaw`, `openclaw-nsvx`, `nsvx-web`, `grow-ops`, and related apps). Follow each repo’s own AGENTS.md (OpenClaw’s root file must stay under 20,000 characters).

## What every Open Group agent does

1. Initialize before writing code: read `PROGRESS.md` or `.agents/harness/PROGRESS.md`, `DECISIONS.md`, `feature_list.json` if present, and `git log --oneline -5`. Record the exact HEAD sha. Run `./init.sh` or `.agents/harness/init.sh` when present.
2. Work on one feature at a time (WIP=1).
3. Do not mark a feature done because code was added. Done means verification evidence is recorded.
4. Leave a clean handoff: update progress, decisions, verification notes, and the feature list; delete leftovers (debug logs, temp files, commented-out code, stray TODOs).
5. If init verification fails, fix the baseline before any new feature work.

## Definition of done

Feature complete = end-to-end verification passed, not “code is written.”

1. Unit tests pass.
2. Integration tests pass.
3. End-to-end flow verification passes.

Do not start level 2 if level 1 failed. Do not start level 3 if level 2 failed.

## Diagnostic loop

A failure is a harness defect, not “the model being dumb again.” Name the layer, fix it, and do not fail that way again:

- task_specification
- context_provision
- execution_environment
- verification_feedback
- state_management

Append one JSON line per task to `.agents/harness/diagnostics/log.jsonl` (or `diagnostics/log.jsonl`): task, ok, layer (if failed), detail, fix.

## Golden rules

- Prefer the shared utility package over hand-rolled helpers.
- Do not YOLO-guess data structures. Validate boundaries or use typed SDKs.
- Prefer durable repo files over chat summaries.
- The implementer does not grade itself.
- UI: minimal, visuals over text, common words (show, hide, edit, save, list, progress). Official logos, not generic icons, when a brand exists.
- No Cursor, no CloudAgent, no Origin. Code lands through the Group Open pipeline (Master Grow: Codex, Muse, Claude) as PRs onto `develop`.
- Agent-native by default (AgentOS directive). Do not lift `clone_session` or owner sessions into plugins or the gateway.
- OpenClaw DEV gateway is `:18790`. Do not touch `:18789`.
- Never write model names, versions or effort levels into service/unit descriptions, timer names, UI labels or prose lists. Routing lives only in the engine config (`engines.json`, `config.json`); anything else goes stale.
  - Exception (owner directive, Miguel, 26 Nov 2026 12:27 Asia/Jerusalem): the required fleet models may be named in docs and prose. UI/design uses Claude Opus 5.5 medium; coding uses Codex GPT Sol 6.2 medium; all in-app inference uses GPT 6.1 Terra low only. Never use Haiku or Luna, including fallbacks, dev and tests. If the required model is unavailable, fail closed with no substitution. Runtime routing still lives only in engine config.
- Do not mix Open Group / Grupo Open product surfaces with Universe branding. Follow each app’s product rules (including adult vs SFW separation).

## CI, branches, and repository hygiene

- `develop` is the primary working branch unless a repository explicitly uses a single primary branch (`main` is production). Production deployment is permitted: finalized work → `develop` → `main` → incremental production deploy → verify (owner policy 2026-11-17, `/etc/grupo-open/PRODUCTION-DEPLOY-POLICY.md`). Never force-push; delete remote branches only after they are merged and obsolete.
- Never use GitHub-hosted runner labels (`ubuntu-latest`, `macos-latest`, `windows-latest`, `macos-15`) or Git LFS.
- Linux CI jobs run on `[self-hosted, Linux, X64, grow-ci, open-group-super-cluster]` and place caches under `${{ runner.temp }}`.
- Run minimal incremental checks on diffs, never full test suites during deployment.
- Never commit `node_modules/`, `vendor/`, `__pycache__/`, build outputs, screenshot/evidence dumps, or pure source-grep tests.

## Loops

- `/goal` = one big task, runs until an independent checker says it is done (or max turns).
- `/loop` = one small action, repeats on an interval.
- Grow Ideas/Jobs = goal-based. Grow Timers = time-based. PR/CI hooks = event-driven.

## Cleanup

Before ending a session: startup path still works, verification still runs, progress is honest, no half-finished step is undocumented, next session can run without a manual repair.

## Deployment operating rule (Miguel, 18 Nov 2026)

"A requested application fix includes implementation, immediate development publication, focused validation, production deployment, and verification of the live result. Repair deployment infrastructure as part of the task. Do not stop at development or request renewed approval for routine releases already covered by the user’s authorization. Use sequential integration and minimal tests covering the complete release impact. Never mark a production fix complete without production evidence."

"Fixed in dev" is intermediate; a production fix is done only when the live revision matches the release and the changed workflow works on the public URL (mobile and desktop). Repair a missing publisher, runner, credential reference or host key as part of the task (repeatable provisioning; host keys verified from an authenticated source and pinned, never by asking Miguel, never by disabling verification). Explicit per-request `[merge-only]` / `[no deploy]` markers, auth, secrets, health checks and pre-migration backups stay. Canonical: `/etc/grupo-open/PRODUCTION-DEPLOY-POLICY.md` (`open-group-ai/openbot-master` `policies/`).
