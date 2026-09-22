# pi-extensions

Monorepo for my Pi coding agent extensions and other pi-related projects. Each
top-level directory is a git submodule pointing at its own GitHub repo; these
are the real dev checkouts. The extensions are also symlinked from
`~/.pi/agent/extensions/` so Pi loads them. `pi-tau-web-server` is not a pi
extension — it is a pi-related project built on top of Pi, so it is not
symlinked there. `CLAUDE.md` is a symlink to this file.

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
- pi-tau-web-server | github.com/milanglacier/pi-tau-web-server | yes (not an extension)

## Agent rules

- Read the submodule's local AGENTS.md first (if it has one, see list above);
  it overrides this file for work in that submodule.
- Submodules are independent repos: edit/test inside the submodule, commit there
  first (all submodules use `main`), then update the gitlink in this monorepo
  (`git add <submodule>` + commit). Don't mix submodules in one commit.
- Don't commit a submodule with a dirty working tree unless intentional.
- Run a project's own commands (`npm run typecheck`, `npm test`) with the
  submodule as cwd; each pins its own node_modules.
