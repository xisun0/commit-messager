# commit-messager

Claude Code plugin and Codex skill for drafting focused Git commit messages from repository diffs.

It uses Angular-style subjects:

```text
type(scope): short summary
```

and can add structured body sections when useful:

```text
[aim]
<why this change is needed>

[approach]
- <implementation choice or changed area>

[attention]
- <risk, migration note, validation gap, or reviewer note>

codex resume <SESSION_ID>
Refs: #<ISSUE_ID>
```

## Claude Code

Install from GitHub:

```text
/plugin marketplace add xisun0/commit-messager
/plugin install commit-messager@commit-messager
/reload-plugins
```

Invoke:

```text
/commit-messager:commit-messager
```

## Codex

Install from GitHub:

```text
$skill-installer https://github.com/xisun0/commit-messager/tree/main/skills/commit-messager
```

Restart Codex to pick up the skill.

Invoke:

```text
Use $commit-messager to draft a commit message for the current changes.
```

For local development, symlink the skill instead:

```bash
ln -s "$(pwd)/skills/commit-messager" ~/.codex/skills/commit-messager
```

## Project Scopes

For consistent scopes within a project, create this file in that project's root:

```text
GIT_HYGIENE.md
```

Define the project's preferred commit scopes there. If it is missing, commit-messager falls back to its bundled `git_hygiene.md`.

## Optional Codex Hook

For automatic Git context at Codex session start, add the hook helper to `~/.codex/config.toml`:

```toml
[hooks]
SessionStart = [
  { hooks = [
    { type = "command", command = "/absolute/path/to/commit-messager/skills/commit-messager/scripts/session_start_commit_context.py", async = false, timeoutSec = 5 }
  ] }
]
```

The hook only injects context. It does not modify files or run commits.
