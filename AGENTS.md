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

- pi-request-user-input | github.com/milanglacier/pi-request-user-input | yes
- pi-session-model | github.com/milanglacier/pi-session-model | no
- pi-dynamic-workflow | github.com/milanglacier/pi-dynamic-workflow | yes
- pi-gemini-patch | github.com/milanglacier/pi-gemini-patch | no
- pi-regenerate-last-response | github.com/milanglacier/pi-regenerate-last-response | no
- pi-background-tasks | github.com/milanglacier/pi-background-tasks | no
- pi-minimal-permission-system | github.com/milanglacier/pi-minimal-permission-system | yes
- pi-theme-switcher | github.com/milanglacier/pi-theme-switcher | no
- pi-subagents | github.com/milanglacier/pi-subagents | yes
- pi-tau-web-server | github.com/milanglacier/pi-tau-web-server | yes (not an extension)
- pi-multi-agent-skill | github.com/milanglacier/pi-multi-agent-skill | yes (a skill)

## Commit messages

Conventional Commits are mandatory for every commit in this monorepo, including
submodule gitlink bumps. Use `<type>: <subject>` with a lowercase type
(`feat:`, `fix:`, `chore:`, ...). Never omit the type prefix, even for a trivial
bump — write `chore: bump pi-request-user-input`, not `bump pi-request-user-input`.

## Changelogs

Every submodule keeps a `CHANGELOG.md` at its root, listed in `package.json`
`files`, in pi's changelog convention. Pi shows new entries to users after a
package update.

- Record each user-facing change under `## [Unreleased]` at the top, in the
  commit that makes it. Group entries as `### Breaking Changes` (with the
  migration step), `### Added`, `### Changed`, `### Fixed`, or `### Removed`.
- Keep entries to one or two short sentences written for users. Skip docs,
  tests, CI, refactors, dependency bumps, and trivial fixes, but do record
  changes to the supported Pi range.
- Link issues and PRs: `([#12](url))`, or `([#12](url) by [@user](url))` for
  external contributions.
- On release, rename `[Unreleased]` to `## [x.y.z] - YYYY-MM-DD`. The version
  must equal `package.json` `version`, because Pi matches headings against it.
  Released sections are immutable.

## Tests

Add a test only when it protects behavior that users rely on, or a contract that
other code depends on. Every test must be worth its maintenance and run time.

- Write a regression test only for a bug that shipped, meaning it reached a
  released version. Bugs in features still under development have not shipped,
  including mistakes made and fixed during the same task. For those, fix the
  bug and move on. If the feature's behavior needs coverage, test the final
  behavior, not the mistake made along the way, and do not call it a regression
  test.
- Do not write tests for test tooling: benchmarks, fixtures, test harnesses, or
  debugging scripts. To check them, run them once and report the result.
- Do not lock incidental details into tests, such as fixture hashes, exact item
  counts, or generated sizes, unless that value is a real contract.
- If you are unsure whether a test is worth keeping, ask instead of adding it.

## README

Like changelog entries, the README is written for people who install and use
the project. It covers what the project does, how to install, configure, and
run it, the available commands and flags, and how to read their output.

- Keep implementation details out of the README: how something works inside,
  internal names, JSON field names, test or benchmark setup, fixture contents
  and counts, and investigation results. Put those in code comments, plans,
  review notes, or commit messages.
- Edit the README only when a change affects what a user does or sees. Do not
  update it as a side effect of debugging, investigating, or measuring
  performance.

## Code review

When asked to review a change, run the style review in its own subagent that
follows the `review-style` skill and checks only code and writing style. Keep
logic reviews, which cover correctness, behavior, performance, security, and
test coverage, out of that subagent; split them across as many reviewers as the
change needs.

Merge all results into one review and remove duplicates. Keep the style
reviewer's `[S1]`–`[S3]` tags so style findings stay apart from the logic
findings' `[P0]`–`[P3]` tags. If subagents are not available, do the style
review as a separate pass after the logic review.

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
