# Installing SentinelXPrime for Claude Code

Two install routes are supported. Pick the one that matches your Claude Code surface.

## Prerequisites

- Git
- A working `claude` CLI or Claude Code IDE/desktop session (Route A only)

## Choose Your Route

Run:

```bash
claude --version
```

- If `/plugin` is available in your Claude Code surface (CLI, desktop app, IDE extensions) → use **Route A: Plugin Install**.
- If `/plugin` is unavailable (headless/SDK, restricted environments, older builds) → use **Route B: Plain Skill Auto-Discovery**.

You can also run both routes side by side — Claude Code resolves skills from `.claude/skills/` even when a plugin is loaded.

## Route A — Plugin Install

1. Clone the repository:

   ```bash
   git clone https://github.com/alicankiraz1/SentinelXPrime.git ~/.claude/sentinelxprime
   ```

2. Load the plugin from the repository root using your Claude Code plugin workflow (`/plugin` in interactive surfaces).

3. Start a fresh session.

The plugin ships:

- `.claude-plugin/plugin.json`
- `.claude-plugin/marketplace.json`
- `hooks/hooks.json`
- `hooks/session-start`

## Route B — Plain Skill Auto-Discovery

Claude Code auto-discovers skills from `~/.claude/skills/<name>/SKILL.md` (personal) and `<project>/.claude/skills/<name>/SKILL.md` (project). No `/plugin` command required.

Trade-offs vs Route A:

- Skills are invoked as `/skill-name`, not `plugin:skill-name`.
- The plugin's SessionStart bootstrap hook does **not** run.
- Works in headless/SDK and restricted environments.

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

### Windows

Use junctions instead of symlinks:

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\skills" | Out-Null
$skills = @("sentinelx-prime", "sentinelx-plan-gap", "sentinelx-review-gate", "sentinelx-test-rig", "using-sentinelx", "shared")
foreach ($name in $skills) {
  cmd /c mklink /J "$env:USERPROFILE\.claude\skills\$name" "$env:USERPROFILE\.claude\sentinelxprime\skills\$name" | Out-Null
}
```

## Verify

```bash
ls -la ~/.claude/skills/sentinelx-prime
```

Open a fresh Claude Code session and ask:

```text
Use $sentinelx-prime while we plan this admin auth change.
```

## Updating

```bash
cd ~/.claude/sentinelxprime && git pull
```

Restart the Claude Code session after updating.

## Uninstall

```bash
for name in sentinelx-prime sentinelx-plan-gap sentinelx-review-gate sentinelx-test-rig using-sentinelx shared; do
  rm -f "$HOME/.claude/skills/$name"
done
```
