---
name: bump-pi-dependencies
description: Upgrade Pi coding agent dependencies to the latest stable release across this pi-extensions monorepo's subprojects. Use for bulk Pi dependency bumps, lockfile refreshes, Pi API migrations, or checking extension compatibility with a new Pi release. Prefer lockfile-only upgrades, minimal backward compatibility, and strict typechecking against real installed Pi declarations.
---

# Bump Pi dependencies

Upgrade all subprojects unless the user narrows the scope. This updates project
dependencies, not the globally installed Pi CLI. Follow the monorepo and local
`AGENTS.md` instructions, preserve unrelated work, and do not commit or push unless
asked.

## Dependency and compatibility policy

- **Prefer lockfile-only updates.** A new Pi release alone does not justify
  changing manifest ranges or raising the minimum supported runtime version.
  Decide per subproject and dependency.
- **Prefer no backward-compatibility code.** A tiny, obvious, type-safe adjustment
  is acceptable—roughly one or two lines is a guideline, not a hard limit. Avoid
  compatibility layers, duplicate paths, or maintenance-heavy fallbacks.
- If an actual breaking change requires nontrivial compatibility code, raise the
  relevant manifest minimum and migrate cleanly instead. Keep peer constraints
  and documented minimums consistent. Use the first release providing the needed
  API when established; otherwise use the validated target and explain why.
- If an existing range cannot resolve the target, adjust it as necessary rather
  than forcing an invalid lockfile. Distinguish updating development dependencies
  from raising the supported runtime minimum. Validation tooling changes are
  also allowed without implying a higher runtime minimum.
- Keep unrelated dependency upgrades and refactors out of scope.

## Review before upgrading

1. Discover the current subprojects and their actual Pi dependencies from the
   checkout rather than a hardcoded list. Inspect manifests, lockfiles, validation
   setup, and existing failures. Record declared ranges separately from resolved
   versions.
2. Resolve the latest stable release from the registry at execution time, using
   the package names actually declared (currently the `@earendil-works` scope).
   Record exact targets for the run. Check related Pi package compatibility and
   development/CI runtime requirements; do not assume all packages share a version.
3. **Read https://pi.dev/news and follow the individual release-note links**
   covering each project's old locked version through the target. Read the full
   relevant posts, not just summaries, and review breaking changes and migration
   guidance. Supplement missing releases with upstream changelogs; consult the
   target release's docs, types, and implementation as needed.
4. Identify which changes affect each extension's actual code and behavior.
   Typechecking cannot detect every semantic change. Do not assume a newly found
   mismatch is a new upstream break without evidence.

## Upgrade each subproject

- Run dependency commands inside the correct subproject; each submodule is an
  independent repository with its own tooling and lockfile.
- Use its package manager to update the relevant dependencies and lockfile,
  applying the policy above. Verify the target was actually reached: a successful
  update command can still leave an older, range-constrained resolution.
- Make necessary API migrations and add focused regression tests for changed
  behavior. If retaining an older minimum after changing API usage, verify that
  the changed path still works there without leaving the final installation
  downgraded. Report uncertainty rather than claiming unverified support.

## Strict real-type validation is required

Every upgraded subproject must pass a trustworthy typecheck, even if its previous
setup was incomplete.

- Use project-local TypeScript with `strict: true`, without overrides weakening
  strictness. Check all shipped entry points and production source files, not
  merely tests or a subset. Make missing validation reproducible in the project.
- Resolve Pi imports against the target packages' **real installed declarations**.
  Remove or isolate handwritten SDK substitutes, ambient shims, and aliases that
  mask those declarations. Verify actual resolution when uncertain.
- Runtime test mocks are fine, including required fake Pi subprocesses. They must
  not replace the production type contract. Check relevant fixtures and callbacks
  against upstream types; simply asserting that a mock has an SDK type does not
  validate it.
- Do not obtain green checks through broad casts, `any`, suppression comments,
  weaker compiler settings, or excluding failing production code. Fix the actual
  mismatch; disclose unavoidable test-harness boundary assertions.
- Also run the upgrade check with `skipLibCheck: false` to expose declaration
  problems. This is separate from application strictness: skipping library
  internals does not itself make imported SDK types fake. Investigate failures
  and report any necessary exemption rather than claiming a clean full check.

## Completion criteria

- Run strict typechecking and the project's tests and applicable lint/build checks
  against a reproducible installation of the final lockfile. Exercise changed
  runtime contracts where feasible; disclose untested interactive behavior.
- Review manifests, lockfiles, code changes, and installed versions together.
  Confirm intended targets, valid dependency relationships, and no unrelated churn.
- Do not claim the bulk upgrade is complete if any project was silently skipped
  or failed verification. If registry access, release notes, or upstream type
  problems block validation, state that explicitly.
- If commits are requested, follow the repository's submodule-first commit and
  parent-gitlink workflow. Otherwise leave the changes uncommitted.

## Output

Report **every subproject in scope**, including unchanged, skipped, or blocked
packages, **one by one—not in a Markdown table**. Use a heading for each package
with concise bullets covering:

- Completion status and change category.
- Pi version(s) before → after.
- Changes made or still needed.
- Strict real-type check and test/other check results.

Use clear change categories:

- **Lockfile only:** dependency resolutions changed; no manifest or code changes.
- **Manifest + lockfile:** dependency ranges changed; no code migration needed.
- **Code migration:** production code needed changes to accommodate Pi's API or
  behavior. Indicate whether manifest ranges also changed.
- **Validation changes:** tests, fixtures, or typecheck tooling needed updates;
  distinguish these from production API migrations. May accompany other categories.
- **No changes:** already current and verified.

Keep completion status separate from change category: completed, blocked, or
skipped. Do not describe proposed fixes as completed work or unrun checks as passes.

Within each package's entry, briefly explain anything beyond a lockfile bump:
what changed upstream, the code or validation updates made (or still needed), why
any manifest/minimum version changed, and any retained compatibility adjustment.
Cite relevant release-note URLs and disclose blockers or unverified behavior.
Finish with counts of completed, blocked, and skipped packages.
