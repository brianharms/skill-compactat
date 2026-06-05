# /compactat

> ## ⚠️ Before you start
>
> Your AI agent can install this skill (copy into `~/.claude/skills/compactat/`, then restart Claude Code to load it). A few things need **you** or your environment:
>
> - After it edits your shell rc, **open a new terminal (or restart Claude Code)** for the new threshold to take effect.


> Change Claude Code's auto-compaction context threshold with one command.

## What it does

Claude Code automatically compacts your conversation once the context window fills past a built-in threshold. `/compactat` lets you move that line. Want Claude to run longer before it summarizes and drops detail? Push the threshold up. Want it to compact earlier and stay lean? Pull it down. You type `/compactat 50` and the skill sets your auto-compaction trigger to 50% of the context window.

Under the hood it edits the `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` environment variable in your shell profile (`~/.zshrc`), so the new threshold persists across sessions.

## Install

Clone or download this repo, then copy the skill folder into your Claude Code skills directory so that `~/.claude/skills/compactat/SKILL.md` exists:

```bash
git clone https://github.com/brianharms/skill-compactat.git
mkdir -p ~/.claude/skills
cp -R skill-compactat ~/.claude/skills/compactat
```

Or, if you downloaded a ZIP, just place the unpacked `compactat` folder at `~/.claude/skills/compactat/`.

That's the whole install — there are no companion scripts to run. Once the folder is in place, invoke it inside Claude Code by typing:

```
/compactat
```

## Usage

Pass a percentage from 1 to 99. That number becomes the share of the context window that fills before auto-compaction kicks in.

```
/compactat 50
```

Claude will:

1. Rewrite the `export CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=...` line in your `~/.zshrc`.
2. Read the line back to confirm the change applied.
3. Tell you: `Compaction threshold set to 50%. Restart Claude Code for it to take effect.`

A few practical values:

- `/compactat 85` — compact late; squeeze maximum context out of long sessions.
- `/compactat 50` — a balanced midpoint.
- `/compactat 30` — compact early; keep the working context tight and fast.

The change is written to disk immediately, but Claude Code reads the variable at startup, so you'll need to restart Claude Code for the new threshold to take effect.

## Requirements / Dependencies

- **macOS.** The skill uses BSD `sed -i ''` syntax, which is macOS-specific. On Linux the in-place flag differs (`sed -i` with no argument), so the command would need adjusting.
- **Zsh.** It edits `~/.zshrc`. If you use a different shell, point the same edit at your shell's profile (e.g. `~/.bashrc`).
- **Claude Code CLI** with the `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` environment variable honored at startup. Your `~/.zshrc` should already contain an `export CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=...` line for the `sed` replacement to match; if it doesn't, add one first.

No external tools, MCP servers, or sibling skills are needed.

## For AI coding agents

If you're an agent working **on** this skill, here's the lay of the land.

**Repo layout:**

```
skill-compactat/
├── SKILL.md       # the entire skill — instructions Claude follows when /compactat is invoked
├── LICENSE        # MIT
├── .gitignore     # ignores OS cruft and runtime/state artifacts
└── README.md      # this file
```

**SKILL.md is the contract.** When a user types `/compactat`, Claude Code loads `SKILL.md` and follows its steps verbatim. There is no code, no `scripts/`, no `web/` or `swift/` — the behavior *is* the markdown. The frontmatter (`name`, `description`, `argument-hint`) controls how the skill is registered and triggered. `$ARGUMENTS` is the placeholder Claude Code substitutes with whatever the user typed after `/compactat`.

**Invariants — do not break these:**

- The variable name must stay exactly `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE`. That string is the Claude Code contract; renaming it silently disables the feature.
- The target file is `~/.zshrc` (use `~/`-relative paths in the instructions, never hardcoded absolute home paths).
- The `sed` command uses BSD in-place syntax (`sed -i ''`). If you add Linux support, branch on OS rather than replacing the macOS form — don't silently break the current platform.
- Keep the three-step flow intact: **edit → read back to confirm → report the result to the user with the restart reminder.** The read-back step is what makes the change verifiable; don't drop it.
- Validate or assume the argument is an integer in `1–99` (matching `argument-hint`). A bad value should not corrupt the profile.

**Testing changes:** copy the folder into `~/.claude/skills/compactat/`, start Claude Code, and run `/compactat 60`. Confirm the `~/.zshrc` line updated, the confirmation message printed, and a restarted session respects the new threshold. Because the edit touches your real shell profile, back up `~/.zshrc` before testing destructive changes.

## License

MIT © 2026 Brian Harms / Ritual Industries — [ritual.industries](https://ritual.industries)

Part of **vibekit**, a showcase of small tools and Claude Code skills that make coding with AI better.
