# ctrlback

My global working rules for AI coding agents. The goal is to work with AI in a more structured way: I am the architect and decide what to do, the AI is the hands and executes. The rules cover session types (exploration vs task), pace, scope, verification, token economy, git/system safety and communication style.

They are designed to work in three places:

- Claude Code, locally
- Cowork (cloud)
- Claude Code (cloud)

## Files

- `rules.md`: the rules. Single source of truth.
- `CLAUDE.md`: notes for working on this repo, including why the layout is what it is and what was verified about each environment.

## How to use

### Claude Code, local

Clone the repo and symlink the rules into the user-level rules folder, which Claude Code loads automatically:

```sh
git clone https://github.com/EdoaLive/ctrlback.git
mkdir -p ~/.claude/rules
ln -s "$(pwd)/ctrlback/rules.md" ~/.claude/rules/rules.md
```

Edit `rules.md` in the clone, never the symlink target elsewhere. Do not also create a `~/.claude/CLAUDE.md` with the same rules: both would load and cost tokens twice.

### Cowork (cloud) and Claude Code (cloud)

These environments do not load the user's `~/.claude/`. Instead, the preferences tell Claude to fetch the rules at the start of every session:

```
https://raw.githubusercontent.com/EdoaLive/ctrlback/main/rules.md
```

Requirements:

- The repo must be public (a private repo needs a token).
- Cowork cloud: add `raw.githubusercontent.com` to the allowed hosts in claude.ai Settings > Capabilities.
- Claude Code cloud: works with the default Trusted network level.
- Fetch with `curl`, not a summarizing web fetch tool, so the text is verbatim.

The exact text to put in the preferences is still to be defined.
