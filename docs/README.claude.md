# SentinelXPrime for Claude Code

SentinelXPrime supports two Claude Code install routes:

- **Route A — Plugin**: ships `.claude-plugin/plugin.json` + `.claude-plugin/marketplace.json` + a SessionStart bootstrap hook. Requires `/plugin` support in the Claude Code surface.
- **Route B — Plain Skill Auto-Discovery**: installs skill directories under `~/.claude/skills/` (user) or `.claude/skills/` (project). Works in headless/SDK and restricted environments. No SessionStart hook.

Full reference: [`.claude-plugin/INSTALL.md`](../.claude-plugin/INSTALL.md).

## Quick Install

Follow [`.claude-plugin/INSTALL.md`](../.claude-plugin/INSTALL.md).

## What Ships (Route A)

- [`.claude-plugin/plugin.json`](../.claude-plugin/plugin.json)
- [`.claude-plugin/marketplace.json`](../.claude-plugin/marketplace.json)
- [`hooks/hooks.json`](../hooks/hooks.json)
- [`hooks/session-start`](../hooks/session-start)

## Route A — Plugin Install

1. Clone the repository:

   ```bash
   git clone https://github.com/alicankiraz1/SentinelXPrime.git ~/.claude/sentinelxprime
   ```

2. Load the plugin from the repository root using your Claude Code plugin workflow.

3. Start a fresh session.

## Route B — Plain Skill Auto-Discovery

Claude Code auto-discovers skills from `~/.claude/skills/<name>/SKILL.md` (personal) and `<project>/.claude/skills/<name>/SKILL.md` (project).

### User-Scoped Install

```bash
git clone https://github.com/alicankiraz1/SentinelXPrime.git ~/.claude/sentinelxprime
mkdir -p ~/.claude/skills
for name in sentinelx-prime sentinelx-plan-gap sentinelx-review-gate sentinelx-test-rig using-sentinelx shared; do
  ln -sfn "$HOME/.claude/sentinelxprime/skills/$name" "$HOME/.claude/skills/$name"
done
```

### Project-Scoped Install

```bash
git clone https://github.com/alicankiraz1/SentinelXPrime.git .vendor/sentinelxprime
mkdir -p .claude/skills
for name in sentinelx-prime sentinelx-plan-gap sentinelx-review-gate sentinelx-test-rig using-sentinelx shared; do
  ln -sfn "$PWD/.vendor/sentinelxprime/skills/$name" "$PWD/.claude/skills/$name"
done
```

## Verify

- Confirm the plugin route's root contains `.claude-plugin/plugin.json`, or that `~/.claude/skills/` contains the linked skill directories for Route B.
- Ask: `Use $sentinelx-prime while we plan this admin auth change.`
- Route A only: the SessionStart hook should inject brief SentinelXPrime bootstrap context.
- Record the result in [`validation/release-readiness.md`](validation/release-readiness.md) before calling the Claude Code surface release-ready.
- Run `node scripts/check-release-readiness.mjs` before making an external release-ready or handoff claim.

## Marketplace Notes

This repository is manifest-complete for self-hosted or development plugin flows because it includes a manifest and marketplace descriptor. It does not claim an official public marketplace listing or evidence-backed public release readiness without recorded runtime smoke results.

## Updating

```bash
cd ~/.claude/sentinelxprime && git pull
```

Restart the Claude Code session after updating.

## Troubleshooting

- `/plugin` reports an error or is unavailable: switch to Route B (plain skill auto-discovery).
- Skills not appearing in `/` menu after Route B install: confirm symlinks resolve and that `SKILL.md` is present inside each target directory; start a fresh session.
- Hook context missing in Claude Code: only Route A injects SessionStart context. Route B intentionally skips the bootstrap hook.
