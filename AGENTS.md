# pi-extensions

Monorepo for my Pi coding agent extensions and other pi-related projects. Each
top-level directory is a git submodule pointing at its own GitHub repo; these
are the real dev checkouts. The extensions are also symlinked from
`~/.pi/agent/extensions/` so Pi loads them. `pi-tau-web-server` is not a pi
extension — it is a pi-related project built on top of Pi, so it is not
symlinked there. `pi-multi-agent-skill` is a skill, not an extension: its
`skills/multi-agent` directory is symlinked as `~/.pi/agent/skills/multi-agent`.
`CLAUDE.md` is a symlink to this file.

## Submodules

Format: submodule | GitHub repo | has AGENTS.md. Before working on a
submodule, you MUST read its local AGENTS.md if it has one.

- pi-plan-mode | github.com/milanglacier/pi-plan-mode | yes
- pi-session-model | github.com/milanglacier/pi-session-model | no
- pi-dynamic-workflow | github.com/milanglacier/pi-dynamic-workflow | yes
- pi-gemini-patch | github.com/milanglacier/pi-gemini-patch | no
- pi-regenerate-last-response | github.com/milanglacier/pi-regenerate-last-response | no
- pi-background-tasks | github.com/milanglacier/pi-background-tasks | no
- pi-minimal-permission-system | github.com/milanglacier/pi-minimal-permission-system | yes
- pi-theme-switcher | github.com/milanglacier/pi-theme-switcher | no
- pi-subagents | github.com/milanglacier/pi-subagents | yes
- pi-tau-web-server | github.com/milanglacier/pi-tau-web-server | yes (not an extension)
- pi-multi-agent-skill | github.com/milanglacier/pi-multi-agent-skill | no (a skill)

## Commit messages

Conventional Commits are mandatory for every commit in this monorepo, including
submodule gitlink bumps. Use `<type>: <subject>` with a lowercase type
(`feat:`, `fix:`, `chore:`, ...). Never omit the type prefix, even for a trivial
bump — write `chore: bump pi-plan-mode`, not `bump pi-plan-mode`.

## Agent rules

- Read the submodule's local AGENTS.md first (if it has one, see list above);
  it overrides this file for work in that submodule.
- Submodules are independent repos: edit/test inside the submodule, commit there
  first (all submodules use `main`), then update the gitlink in this monorepo
  (`git add <submodule>` + commit, with a Conventional Commit message as above).
  Don't mix submodules in one commit.
- Record only commits from a submodule's `main` branch in parent gitlinks,
  never in-progress feature-branch commits.
- When bumping dependencies in a submodule checked out on a working branch
  other than `main`, use a separate worktree based on `main`. Preserve the
  original checkout's branch, files, and uncommitted work. Take the parent
  gitlink from the `main` worktree, not the original working branch.
- Keep the temporary worktree until the user asks to merge the bump to `main`.
  After merging and recording the `main` commit in the parent gitlink, remove
  that worktree as part of the merge cleanup. Verify it is clean first; never
  force-remove a worktree containing uncommitted changes.
- Don't commit a submodule with a dirty working tree unless intentional.
- Run a project's own commands (`npm run typecheck`, `npm test`) with the
  submodule as cwd; each pins its own node_modules.
