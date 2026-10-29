# REA workspace guidelines

## REA tooling and setup
- **REA status.** REA (`rea-agents`) is installed and registered globally as an MCP server (`rea`).
- **Skip setup checks.** Never rerun `rea setup` or doctor diagnostics before starting work.
- **Direct execution.** When asked to inspect, analyze, or reverse engineer any game, binary, library, or application, proceed directly to the target using REA's MCP tools (`open_binary`, `inspect_managed_artifact`, `analyze_javascript_application`).

## Storage and disk protection
- **Workspace targets.** Keep extracted files, decompiled pseudocode, unpacked archives, and test scripts inside `workspace/`.
- **Tools and engines.** Store standalone external reverse-engineering tools and engines (Ghidra, JADX jar, Kuna) under `tools/`.
- **Caches.** Write temporary analysis dumps and Evidence bundles to `cache/`.
- **Disk boundaries.** Keep heavy analysis dumps, extractions, and target binaries inside this repository. Never write heavy analysis dumps or game binaries to system drives or other drives.

# General guidelines

Coding standards, architecture rules, and agent workflow.

## Code guidelines
- **Self-documenting code.** Write code that reads clearly on its own. Use descriptive names and flat control flow (early returns over multi-layer nesting). Keep comments minimal and concise. Reserve them for *why*, non-obvious constraints, and maintained module invariants. A short local heading is fine when a file needs to explain an ownership, lifecycle, or persistence rule. Do not add commentary that merely narrates the code.
- **Performance first.** Avoid dynamic evaluations and allocations in hot paths. Cache aggressively.
- **Measure twice, cut once.** Prefer small, deliberate changes over broad refactors. Before writing new logic, search the codebase for an existing helper that already does the job. Prefer intuitive shared helpers over inlining repeated lines of code for identical operations. If a change duplicates logic across callsites, extract it into a shared helper instead.
- **Work in logical slices.** Prioritize small, precise code changes rather than big blocks to prevent tooling and scope failures, especially during large refactors. Be surgical!
- **YAGNI.** Do not add abstractions, features, or complexity without a clear need.

## Agent behavior
- Read every rule and referenced skill before starting work. Confirm adherence to the user.
- Keep responses concise and focused on the task.
- Follow existing code style and formatting for each directory and its associated files.
- For writing work, read `.agents/skills/unslop/SKILL.md` and follow it even if the harness does not auto-load always-active skills. This applies to docs, prompts, comments, and user-facing copy.

### Guardrails
- **No automated git commits.** Never execute git commit commands or automate commits unless explicitly instructed. The user handles all commits manually or through the commit pipeline.
- **Downstream safety.** When modifying shared helpers, configurations, command-line interfaces, or core abstractions, stop and verify that downstream callers and consumers remain unbroken. Do not rely on speculation.
- **Respect established structure.** Do not move, rename, or reorganize files, folders, or asset paths. Keep changes localized to the files being edited.
- **Preserve behavioral contracts.** Refactors must not alter existing functionality, return types, or output schemas unless there is an explicit functional win.
- **No test harness creation during implementation.** Do not create new test suites or test harnesses while implementation work is in progress. Manual runtime tests carry higher value because test harnesses written against incomplete code are fragile and waste implementation time.
- **No premature finalization.** Never declare work done, implemented, or complete until the user explicitly says so. Do not write completion summaries or treat a slice as finished on your own.

