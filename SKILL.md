---
name: compactat
description: Change the auto-compaction context threshold. Usage: /compactat 50
argument-hint: <percentage 1-99>
---

The user wants to set `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` to `$ARGUMENTS`%.

1. Pick the right shell rc file. Default to `~/.zshrc` (the macOS default since Catalina). If `$SHELL` ends in `bash`, use `~/.bashrc` (or `~/.bash_profile` if that's what exists) instead. Set `RC` to that path.

2. Set the variable idempotently — **replace the line if it already exists, otherwise append it.** A bare `sed` replace silently does nothing on a fresh machine where the line isn't present yet, so check first:

   ```bash
   RC="$HOME/.zshrc"   # or ~/.bashrc for bash users
   touch "$RC"
   if grep -q '^export CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=' "$RC"; then
     sed -i '' 's/^export CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=.*/export CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=$ARGUMENTS/' "$RC"
   else
     printf '\nexport CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=%s\n' "$ARGUMENTS" >> "$RC"
   fi
   ```

   (On Linux, GNU `sed` needs `sed -i` without the `''` argument — use `sed -i 's/.../'` there.)

3. Confirm the change by reading back the line: `grep CLAUDE_AUTOCOMPACT_PCT_OVERRIDE "$RC"`.

4. Tell the user: "Compaction threshold set to $ARGUMENTS%. Open a new terminal (or `source` your shell rc) and restart Claude Code for it to take effect."
