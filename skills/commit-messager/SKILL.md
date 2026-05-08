---
name: commit-messager
description: "Draft high-quality Angular-style Git commit messages from repository changes using the template type(scope): short summary. Use when Codex needs to inspect staged or unstaged diffs, summarize the intent and impact of code changes, propose commit messages, split changes into multiple commits, or refine an existing commit message before committing."
---

# Commit Messager

## Workflow

1. Inspect repository state before drafting:
   - Run `git status --short`.
   - Prefer staged changes with `git diff --cached --stat` and `git diff --cached`.
   - If nothing is staged, inspect unstaged changes with `git diff --stat` and `git diff`.
   - Check recent style with `git log --oneline -n 8` when the repository has history.
2. Do not stage, unstage, commit, amend, or reset changes unless the user explicitly asks.
3. Identify whether changes form one commit or several coherent commits.
4. Draft message candidates using the Angular-style template `type(scope): short summary`.
5. Keep the subject line imperative, specific, and under 72 characters when practical.
6. Add a body only when it clarifies non-obvious intent, behavior changes, migration notes, risk, or testing.
7. Mention test commands only if they were actually run or are visible in the provided context. Do not invent verification.

## Helper Script

Use `scripts/collect_commit_context.py` to gather a compact summary before drafting:

```bash
python3 scripts/collect_commit_context.py
```

Resolve the script path relative to this skill directory, but run it from the target Git repository. The script reports status, staged and unstaged stats, untracked files, recent commits, and file-level diff names. Read full diffs with `git diff` or `git diff --cached` when the summary is not enough to understand intent.

## Optional Codex Hook

Use `scripts/session_start_commit_context.py` as a Codex `SessionStart` hook when the user wants commit-message context injected automatically at session startup or resume.

Add a command hook like this to the user's Codex config, replacing the path with this skill's installed location:

```toml
[hooks]
SessionStart = [
  { hooks = [
    { type = "command", command = "/absolute/path/to/commit-messager/scripts/session_start_commit_context.py", async = false, timeoutSec = 5 }
  ] }
]
```

The hook reads the Codex hook payload from stdin, checks the payload `cwd`, and emits `additionalContext` only when that directory is a Git worktree with uncommitted changes. It injects a compact status summary and a reminder to use `$commit-messager`; it does not stage, commit, amend, or modify files.

## Output Shape

For a single commit, provide:

```text
type(scope): short summary

<optional body>
```

For multiple possible commits, provide each message under a short heading naming the change group. Include the files or change theme that belong in each commit.

When the user asks for alternatives, give 2-4 candidates with different emphasis rather than tiny wording variations.

## Judgment Rules

- Always include both `type` and `scope` in the subject unless the user explicitly asks for another format.
- Prefer `fix` for bug fixes, `feat` for user-visible capability, `refactor` for behavior-preserving restructuring, `test` for test-only changes, `docs` for documentation-only changes, `chore` for tooling or maintenance, and `style` for formatting-only changes.
- Choose a short lowercase scope from the changed area, package, feature, command, script, or document group, such as `auth`, `api`, `ui`, `hooks`, `scripts`, `docs`, or `commit-message`.
- Use `chore(repo)` only when no more specific scope is defensible.
- Avoid vague subjects such as `update files`, `fix bug`, `misc changes`, or `refactor code` unless the diff truly gives no more information.
- Do not include ticket IDs, co-author trailers, breaking-change trailers, or generated-by lines unless the repository already uses them or the user asks.
- If the diff is too large or ambiguous, state what is unclear and draft the safest message from observable changes.
- If staged and unstaged changes differ, clearly say which set the message covers.
