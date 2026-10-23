# pi-extensions

A monorepo for the collection of [pi](https://pi.dev) extensions and other
pi-related projects I maintain. Each project lives in its own submodule with
its own GitHub repository. Extensions can be installed independently via
`pi install npm:<package>` or `pi install github:milanglacier/<repo>`.

## Pi-related projects

Projects that are not pi extensions, but are built on top of pi and maintained
here.

### [pi-tau-web-server](https://github.com/milanglacier/pi-tau-web-server)

Tau is a browser workspace for pi: a standalone Node.js server that spawns and
manages multiple live `pi --mode rpc` sessions in parallel. Each in-page tab is
its own pi session, sessions survive browser reloads while the server runs, and
extension UI dialogs are forwarded to the browser. It also adds a session
history browser, session tree/branching, and multi-device access. It is
distributed on npm — install it with `npm install --global pi-tau-web-server`
and run `pi-tau-web-server` — so it is not loaded as a pi extension.

## Skills

### [pi-multi-agent-skill](https://github.com/milanglacier/pi-multi-agent-skill)

A minimal, lightweight alternative to [pi-subagents](#pi-subagents): a skill
plus a CLI, with no extension and no new tools. Run agents in parallel or in
the background, then wait for, steer, stop, or resume them. Each agent gets the
same extensions, MCP servers, skills, and settings as your interactive pi.

Because it is only a skill and a CLI, any harness or coding agent that has a
shell can use it to orchestrate pi, not only pi itself. The agent reads about
subcommands and options only when it needs them, so the skill costs little
context. Install by symlinking `skills/multi-agent` into
`~/.pi/agent/skills/`.

## Extensions

### [pi-request-user-input](https://github.com/milanglacier/pi-request-user-input)

Adds the `request_user_input` tool. The model can ask one to three short
questions, each open-ended or with multiple-choice options, and the answers come
back as a readable summary plus structured tool details.

### [pi-dynamic-workflow](https://github.com/milanglacier/pi-dynamic-workflow)

Lets the LLM author a small JavaScript orchestration script on the fly and fan
it out into isolated pi subagent subprocesses via `/workflow <task>`. Scripts
compose `agent(...)` calls with `parallel()` / `pipeline()`, and failed
subagents yield `null` instead of rejecting the batch, making it great for
audits, batch transformations, and multi-perspective research.

### [pi-background-tasks](https://github.com/milanglacier/pi-background-tasks)

Reactive background shell tasks for pi. It adds the `bg_task` / `bg_status`
tools, a `/bg` slash command, and a multi-pane dashboard (`Ctrl+Shift+B`) for
spawning, watching, and stopping tracked background processes, including
persistent log files and follow-up wakeups when a watched task emits output or
exits.

### [pi-minimal-permission-system](https://github.com/milanglacier/pi-minimal-permission-system)

A lightweight permission layer for the built-in `read`, `edit`, `write`, and
`bash` tools (MCP and extension tools are intentionally out of scope). It keeps
things minimal with just global and project-local policies, while still
allowing per-tool control such as read-only access to sensitive paths.

### [pi-session-model](https://github.com/milanglacier/pi-session-model)

Adds a `/session-model` slash command that switches the active model and
optional thinking level for the current pi session only, without touching
`~/.pi/agent/settings.json` or other global defaults.

### [pi-regenerate-last-response](https://github.com/milanglacier/pi-regenerate-last-response)

Adds `/regenerate` (shorthand `/reg`) to regenerate the last agent response by
branching back to the preceding user message and re-triggering the agent.

### [pi-theme-switcher](https://github.com/milanglacier/pi-theme-switcher)

Automatically switches the terminal theme between dark and light based on
environment variables (`PI_AGENT_THEME`, `THEME_MODE`) or time of day, with a
configurable night window and periodic polling to handle transitions during
long TUI sessions.

### [pi-gemini-patch](https://github.com/milanglacier/pi-gemini-patch)

Patches outgoing Google/Gemini provider requests so all Gemini safety
categories use `BLOCK_NONE`, correctly merging the settings into the request's
`config` object (OpenRouter Gemini models are excluded). There is no toggle —
if the extension is loaded, the patch applies to matching requests.

### [pi-subagents](https://github.com/milanglacier/pi-subagents)


**NOTE**: I am currently experimenting with the `pi-multi-agent-skill`—a
lightweight, pure skill-and-CLI combination that exposes no tools—as a
potential replacement for this extension.

A fork of [tintinweb/pi-subagents](https://github.com/tintinweb/pi-subagents)
maintained here: Claude Code-style autonomous sub-agents for pi. Spawn
specialized agents in isolated sessions, each with its own tools, system
prompt, model, and thinking level; run them in the background, steer them
mid-run, resume finished sessions, define custom agent types, and orchestrate
many at once with deterministic JavaScript workflows.

## Obsolete extensions

### ~~[pi-plan-mode](https://github.com/milanglacier/pi-plan-mode)~~

Structured planning mode for pi: `/plan` injected a planning prompt and exposed
planning tools directly on the main conversation. No longer maintained and
removed from this monorepo. [pi-request-user-input](#pi-request-user-input),
which was extracted from it, replaced it because I no longer use plan mode. I
still let the agent draft a plan before implementing, but a dedicated plan mode
is no longer needed.

## Development

Each top-level directory is a git submodule pointing at its own repository.
Work inside the submodule, run its own commands (`npm run typecheck`,
`npm test`) there, then update the gitlink in this monorepo.
