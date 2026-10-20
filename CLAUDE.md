# claude-config

- `rules.md` holds my global AI working rules. Edit it here: `~/.claude/rules/rules.md` is only a symlink to it.
- Do not recreate `~/.claude/CLAUDE.md` or put other copies of the rules in `~/.claude/`: both load, so every session pays the tokens twice. That is why the old `~/.claude/CLAUDE.md` was deleted.
- Why this layout (checked against the Claude Code and OpenCode docs, 2026-10-14):
  - Real file in a dedicated folder: the `~/.claude` folder cannot be opened from the UI, and a folder can be versioned and shared with other agents.
  - Symlink into `~/.claude/rules/` rather than an `@` import from `~/.claude/CLAUDE.md`: `rules/` loads automatically, supports symlinks, and user-level files need no import approval. An import would add an indirection for no benefit.
  - OpenCode needs its own entry point: it has no global `rules/` folder, does not fall back to `CLAUDE.md`, and its `instructions` config field was not resolved in v2.
  - Known caveat: in Cowork desktop sessions, a symlinked rules file pointing outside the working directory is skipped.
- OpenCode is not installed. When it is, symlink `rules.md` to `~/.config/opencode/AGENTS.md` and rewrite the two Claude Code-specific references in the rules (the clickable question tool, memory).

## Rules in Cowork and Claude Code cloud (exploration, 2026-10-15)

- Goal: make `rules.md` apply outside local Claude Code. Current layout (symlink in `~/.claude/rules/`) only works locally.
- Verified from docs (via search summaries, not verbatim):
  - Cloud sessions load the repo's `CLAUDE.md` and `.claude/rules/`, not the user's `~/.claude/`.
  - Cowork has Global instructions in app settings; no source found saying it reads `CLAUDE.md` or `rules/`.
  - Setup scripts are documented only for Claude Code cloud environments.
- Tested by me:
  - `/context` does not exist in Cowork cloud.
  - Cowork cloud: `curl` to `raw.githubusercontent.com` works only after adding the host in claude.ai Settings > Capabilities.
  - Claude Code cloud: works with no changes. Default network level is Trusted, and `raw.githubusercontent.com` is in the default allowed list (cloud-environments docs). It breaks only if the environment is set to None or to Custom without the default list.
- Chosen direction: put a one-line instruction in the preferences of each environment: at session start, fetch `rules.md` from a raw GitHub URL with `curl` and follow it. Single source of truth, no sync. Do not use WebFetch (it summarizes, not verbatim).
- Raw URL: `https://raw.githubusercontent.com/<owner>/<repo>/<branch>/<path>` (github.com URL with `blob/` removed). Source: community discussions, not official docs.
- Known limits: the instruction is soft (not enforced like a hook); CDN cache can serve stale content after a commit; a private repo needs a token.
- Done: repo is public (`EdoaLive/ctrlback`), pushed to `main`; raw URL returns 200. Preference text is in `README.md`.
- Tested: the preference text works in Claude Code cloud and in Cowork cloud.
- Out of scope for now: Cowork desktop app.