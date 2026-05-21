# SentinelXPrime

> Stage-aware security skills for Codex and Claude Code.

[![Support Surface](https://img.shields.io/badge/Support-Codex%20%7C%20Claude%20Code-111111?style=flat)](#installation)
[![Validation](https://img.shields.io/badge/Validation-static%20%2B%20evals-2ea043?style=flat)](#validation-and-release)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow?style=flat)](LICENSE)
[![Stacks](https://img.shields.io/badge/Stacks-ASP.NET%20Core%20%7C%20Spring%20%7C%20Node%20%7C%20Python%20%7C%20Go%20%7C%20Rails%20%7C%20Laravel%20%7C%20Rust-1f6feb?style=flat)](#supported-stacks)

SentinelXPrime helps teams catch missing security requirements during planning, surface scoped concerns during risky implementation work, offer opt-in review help after coding, and propose a practical security check plan before release.

The suite is advisory-first. It improves signal and consistency, but it does not certify a repository as secure, fully reviewed, or production-ready.

## How It Works

1. Planning stage: `sentinelx-plan-gap`
2. Risky implementation stage: `sentinelx-prime`
3. Post-implementation review stage: `sentinelx-review-gate`
4. Pre-release hardening stage: `sentinelx-test-rig`

Use `using-sentinelx` as the lightweight bootstrap skill when a session needs quick orientation to the suite.

## Supported Stacks

- `.NET / ASP.NET Core`
- `Java / Spring`
- `Node / TypeScript`
- `Python`
- `Go`
- `Ruby on Rails`
- `PHP / Laravel`
- `Rust`

If the stack is unclear, SentinelXPrime falls back to common web-security guidance and says that the stack inference is uncertain.

Crypto-sensitive discussions should cross-check [`skills/shared/crypto-guidance.md`](skills/shared/crypto-guidance.md).

## Stage Decision Aid

- If the code is done and the next question is "is this implementation safe enough?", use `sentinelx-review-gate`.
- If the next step is release or handoff hardening, use `sentinelx-test-rig`.
- If the stage evidence is weak or contradictory, stay in `uncertain` mode and keep the guidance advisory until the stage becomes clearer.

## Installation

| Platform | Status | Entry Point |
| --- | --- | --- |
| Codex | Supported | [`.codex/INSTALL.md`](.codex/INSTALL.md) and [`docs/README.codex.md`](docs/README.codex.md) |
| Claude Code | Supported | [`.claude-plugin/plugin.json`](.claude-plugin/plugin.json) and [`docs/README.claude.md`](docs/README.claude.md) |

Supported means the repository ships a documented install surface that exists in this repo.

Current release or handoff claims for Codex and Claude Code should be backed by recorded smoke evidence in [`docs/validation/release-readiness.md`](docs/validation/release-readiness.md).
Use `node scripts/check-release-readiness.mjs` or the `Release Claim Readiness` workflow before making an external release-ready or handoff claim.

## Repository Status

- Packaging, installation docs, and validator coverage are in good shape for private collaborator review.
- `bash scripts/static-validation.sh` is expected to pass in a Node.js 22 + Ruby environment.
- `node scripts/check-release-readiness.mjs` still fails because Claude Code does not yet have a fresh authenticated runtime `pass` row in the readiness matrix.
- Until that evidence exists, describe the repo as validated for private review, not as fully release-ready or handoff-ready.

Latest updates in this snapshot:

- Share-facing root reports are now English-only and canonicalized as `cross-validation-report-2026-04-04.md` and `phased-remediation-plan-2026-04-04.md`.
- The release-readiness claim gate now accepts only the canonical readiness matrix and enforces current-pass freshness checks.
- Doc validators now cover root markdown files, reject repository-escape local links, and keep the markdown inventory contract centralized.
- Legacy-name validation and related regression coverage were hardened to fail on actionable lint instead of uncaught stack traces.

## Verify Installation

Start a fresh session and try one of these prompts:

- `Use $sentinelx-prime while we plan this new ASP.NET Core feature.`
- `Use $sentinelx-plan-gap to review this Node/TypeScript API design for missing security requirements.`
- `Use $sentinelx-review-gate to run a focused security review on the completed auth changes.`
- `Use $sentinelx-test-rig to propose a stack-aware security check plan for this release handoff.`

More examples live in [`docs/examples/example-prompts.md`](docs/examples/example-prompts.md).

## What's Inside

| Skill | Purpose |
| --- | --- |
| `using-sentinelx` | Lightweight bootstrap and orientation skill |
| `sentinelx-prime` | Orchestrator for stage-aware security guidance |
| `sentinelx-plan-gap` | Planning-stage security gap analysis |
| `sentinelx-review-gate` | Opt-in post-implementation security review |
| `sentinelx-test-rig` | Opt-in security test/check planning before release |
| `shared/*` | Common threat references, finding schema, and stack profiles |

## Philosophy / Safety Model

- advisory-first by default
- no false assurance
- no silent installs
- no hidden mutation
- read-only active analysis only after explicit user consent
- substantial outputs separate reviewed areas, unreviewed areas, assumptions, and tools run

## Updating

- Codex: update the clone used by your install doc, then restart Codex
- Claude Code: update the plugin clone and restart the session
- See [CHANGELOG.md](CHANGELOG.md) for notable repository changes.

## Troubleshooting

- Skills not showing up: confirm the platform-specific install doc was followed exactly and start a fresh session.
- Hook context missing in Claude Code: verify [`hooks/hooks.json`](hooks/hooks.json) and [`hooks/session-start`](hooks/session-start) are present in the plugin root.
- Release packaging issues: build from the SentinelXPrime repo root only, not from a wrapper workspace or nested copy.

## Validation And Release

Validation prerequisites:

- Node.js 22
- Ruby available for `scripts/static-validation.sh`
- `codex` CLI on `PATH` for live eval runs
- readable Codex auth at `$CODEX_HOME/auth.json` or `~/.codex/auth.json` for live eval runs
- `unzip` plus either `zip` or `ditto` for release packaging and archive verification

Recommended local verification:

```bash
bash scripts/static-validation.sh
node evals/run-sentinelx-prime.mjs --manifest-json
node evals/run-sentinelx-prime.mjs --preflight-only
node evals/run-sentinelx-prime.mjs
node evals/run-sentinelx-prime.mjs --promote-artifacts
bash tests/hooks/test-session-start.sh
node scripts/check-doc-links.mjs
node scripts/check-legacy-names.mjs
```

Public release flow:

```bash
bash scripts/package-release.sh
SENTINELX_PRIME_FORCE_NO_RSYNC=1 bash scripts/package-release.sh SentinelXPrime-fallback
node scripts/verify-release-archive.mjs dist/SentinelXPrime.zip
node scripts/verify-release-archive.mjs dist/SentinelXPrime-fallback.zip
node scripts/check-release-readiness.mjs
```

Build release archives only from a clean SentinelXPrime repo root. Do not package from wrapper workspaces, nested source trees, or Finder/manual zips.

Treat `scripts/package-release.sh` as the canonical release archive flow. Finder/manual zip output is not a supported release artifact.

## License

MIT. See [LICENSE](LICENSE).
