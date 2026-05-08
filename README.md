# commit-messager

A Codex skill repository for drafting Angular-style Git commit messages from repository diffs.

The skill follows this commit subject template:

```text
type(scope): short summary
```

## Repository Layout

```text
skills/
└── commit-messager/
    ├── SKILL.md
    ├── agents/
    │   └── openai.yaml
    └── scripts/
        ├── collect_commit_context.py
        └── session_start_commit_context.py
```

`skills/commit-messager/` is the actual skill folder. Keeping the skill under `skills/` makes the repository easier to use as a skill source and leaves the repo root for project documentation and release files.

## Install

Copy or symlink the skill folder into Codex's skill directory:

```bash
ln -s "$(pwd)/skills/commit-messager" ~/.codex/skills/commit-messager
```

Then invoke it with:

```text
Use $commit-messager to draft a commit message for the current changes.
```

## Optional Hook

The skill includes a Codex `SessionStart` hook helper:

```text
skills/commit-messager/scripts/session_start_commit_context.py
```

Add it to `~/.codex/config.toml` if you want Codex sessions to automatically receive lightweight Git status context when the current repository has uncommitted changes.
