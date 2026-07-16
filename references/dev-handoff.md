# Handoff to the development AI

In a refactor with this skill, the planning assistant (whichever AI runs this skill: Claude, ChatGPT, etc.) does the design and the documentation; the code is written by a development AI (Claude Code, Codex, etc.) in the real environment, because there it can run the playground, the linters and the tests. This reference describes which documents to produce, which rules to impose and which prompts to deliver. The two role tags below are: **[Planner]** = the planning assistant, **[Dev AI]** = the development AI.

## Catalog of documents to generate (and who creates each)

All in `refactoring/docs/`. The exact set depends on the project; this is a reference set (taken from a real project). Two authors are involved, so each document is tagged:

- **[Planner]** — written by the planning assistant during Phases 1-6, before the handoff. These are the specs the development AI reads but does not author.
- **[Dev AI]** — created by the development AI during the build, as it works.
- **[Planner -> Dev AI]** — seeded by the planning assistant (the contract / what must be preserved) and completed/maintained by the development AI as it implements.

Catalog:

- `CODE-MAP.md` — **[Planner]** map of the existing code: functions, interactions, data and risk zones (Phase 1).
- `DATA-MODEL.md` — **[Planner]** tables, options, metadata: exact names and, per the compatibility mode, what is preserved or migrated (Phases 1-2).
- `ARCHITECTURE.md` — **[Planner]** new architecture, layers, patterns, decisions and trade-offs, including the replacement strategy decided with the user (full rewrite vs incremental/strangler), which the build plan must follow (Phase 3).
- `SECURITY.md` — **[Planner]** the platform's security checklist the development AI must follow (Phase 4).
- `LIFECYCLE.md` — **[Planner]** idempotent install, update, deactivate and uninstall with the optional cleanup, Danger Zone (Phase 5). In mode B it also specifies the data migration as a first-class deliverable: idempotent, resumable, batched, with dry-run, a progress record, a defined abort/rollback path, and (on WordPress) a WP-CLI command for big sites.
- `TESTING.md` — **[Planner -> Dev AI]** test strategy defined by the planning assistant (Phase 6); the development AI implements it and keeps the doc updated. Covers the full stack: unit/integration tests (PHPUnit + WP test suite for WP/Woo; the equivalent for other platforms), static analysis (PHPStan/Psalm), coding standards (PHPCS with WordPress/WooCommerce sniffs), the golden tests for the locked contracts, and the performance baseline and budget (baseline captured in sprint 0 from the reference version; every sprint close compares against it) — all green in blocking CI, with their config files (`phpunit.xml.dist`, `phpstan.neon`, `phpcs.xml`) and a CI workflow.
- `API.md` / `REST-API.md` — **[Planner -> Dev AI]** the contract of the public interfaces is fixed by the planning assistant (in mode A/B, byte for byte); the development AI completes the full reference of the new code.
- `PLAN.md` or `PROGRESS.md` — **[Dev AI]** the build plan split into short sprints, which the development AI creates and marks off. Living state document.
- `HOOKS.md` — **[Dev AI]** exposed actions and filters, documented as each one is created.
- `CLASSES.md` — **[Dev AI]** classes and responsibilities.
- `USAGE.md` — **[Dev AI]** usage for the end developer.
- project-specific abstraction documents, e.g. `subscriptions-abstraction.md` — **[Dev AI]**, following the planned architecture.
- `CONTINUE-PHASE-N.md` — **[Dev AI]** a self-contained prompt per sprint to resume in a fresh chat without losing context.
- `changelog.txt`, and on WordPress also `readme.txt` — **[Dev AI]** release/convention files in the platform's required plain-text format (the exception to the .md-only rule).

The rule: in the end, just with these documents the developer documentation page must be generatable. That is why the development AI documents each public surface the moment it creates it.

## Mandatory rules block (include verbatim in the handoff documentation)

1. Sprint 0, before writing any new code: set up the test environment, run the REFERENCE (old) version in it, and capture from it the golden fixtures and the performance baseline that `TESTING.md` defines (in mode C there are no golden fixtures to capture — the performance baseline still applies), recording their provenance (reference version, date). Only then start building, and verify each part in that environment before considering it done.
   - IF the project is WordPress/WooCommerce: create a WordPress Playground and test in it.
   - IF it is any other platform (a library, an app, a service, etc.): use that platform's normal local test setup (a dev server, a container, a test harness, etc.). WordPress Playground does NOT apply here.

   Never block or stop because "you cannot create the Playground" — that is WordPress-specific and simply does not apply outside WP/Woo. If you are unsure how to test, ask the user instead of stopping.

   Golden fixtures are never generated — or regenerated — from the new code. If a fixture needs to change, that is a contract change: it requires the user's explicit approval and the corresponding spec update, never a silent re-capture.
2. If you need a premium plugin, do NOT search for it or invent it: ask the person to place it in `refactoring/docs/plugins` and wait. If you extend that plugin or add functionality to it, study it first and document its real hooks and filters; use only hook names that genuinely exist in its code, never invented ones.
3. Before writing code, study all the material, create a PLAN in a document and follow it. Split it into small phases/sprints so the context never overflows. At the close of each phase/sprint, generate a `CONTINUE-PHASE-N.md`: a self-contained prompt the person pastes into a fresh chat to start the next phase without dragging old context. Everything essential to continue goes in that file.
4. Document everything you create, as you create it, in `refactoring/docs/`.
5. SACRED: always follow the platform's styles, security, escaping and sanitization. Not a single hole.
6. NEVER invent. If something is unclear or unknown, ALWAYS ask before implementing. Assuming a solution and implementing it without confirming is forbidden.
7. Propose improvements, do not silently deviate. The specs are a strong starting point, not dogma — except the sacred contracts (persisted data and public interfaces per the compatibility mode, and security), which are non-negotiable. You are the implementation specialist: if you see a better approach for the architecture, structure or naming, tell the user, explain how you would improve it and why, and ask if they want to discuss it with the planning assistant. If they say yes, generate a prompt (template D) the user will paste to the planning assistant. Do not implement the change until it is agreed.
8. Maximum extensibility: expose hooks (actions and filters) at every reasonable point. Every relevant string passes through a filter, every important decision fires a hook before/after, and every query and response is filterable, so third parties extend without forking. Document each hook in `HOOKS.md` the moment you create it.
9. Write and keep the automated test stack GREEN before declaring anything done: unit/integration tests (PHPUnit + WP test suite for WP/Woo; the equivalent for other platforms), static analysis (PHPStan/Psalm), coding standards (PHPCS with the WordPress/WooCommerce sniffs for WP), the golden tests for the locked contracts, and the performance comparison against the sprint-0 baseline, within the budget `TESTING.md` sets. Run them in blocking CI. Never declare a sprint complete with any check red, and never report a check as green without including its raw output (the actual command/CI output, not a summary) in the sprint close.
10. ALWAYS create repository and package hygiene files so nothing that should not reach the repo reaches it, and nothing that should not ship gets packaged:
    - `.gitignore` (exclude vendor/build/local/dev artifacts from the repo).
    - `.gitattributes` with `export-ignore` for dev-only files (tests, CI config, docs, the `refactoring/` folder), so the distributed package/archive stays clean. `.gitattributes` works with any Git, but if the user's host or packaging system uses a different exclusion mechanism (e.g. `.distignore` for WordPress.org, `.npmignore`, Composer archive excludes), create that one instead — ASK the user which they use if it is not clear.
    - `changelog.txt` — always create it and keep it updated.

## Files the planning assistant provides vs files the dev AI must create

The handoff must spell this out, so the development AI knows exactly what it is given and what it owns. Include it in the documentation and in the initial prompt.

Files the planning assistant provides (read-only inputs — read them, do not rewrite their decisions):
- `CODE-MAP.md`, `DATA-MODEL.md`, `ARCHITECTURE.md`, `SECURITY.md`, `LIFECYCLE.md`, the test strategy in `TESTING.md`, and the public-interface contract in `API.md`/`REST-API.md` (in mode A/B this contract is byte-for-byte and must not change). These are the specs. Treat them as the source of truth.

Files the dev AI must create and how to use each:
- `PLAN.md` / `PROGRESS.md` — create it first from the specs, split into short sprints; keep it updated as the living state, marking off what's done.
- `HOOKS.md` — add an entry the moment you create each action/filter.
- `CLASSES.md` — document each class and its responsibility as you build it.
- `USAGE.md` — fill in usage examples that actually run, for the end developer.
- `API.md` / `REST-API.md` — complete the full reference of the new code on top of the contract the planning assistant fixed (in mode A/B the contract is byte-for-byte and must not change).
- `CONTINUE-PHASE-N.md` — create one at the close of every sprint: a self-contained prompt to resume in a fresh chat.
- any project-specific abstraction doc the architecture calls for.

The goal: at the end, the developer documentation page can be generated from these files alone.

## Prompt templates

### A) Initial prompt for the development AI
Hand the person a prompt like this to give to Code/Codex:

> Load the software's main directory `<name/path>`. First READ the spec files I provide in `refactoring/docs/` — `CODE-MAP.md`, `DATA-MODEL.md`, `ARCHITECTURE.md`, `SECURITY.md`, `LIFECYCLE.md`, `TESTING.md`, and the seeded contract in `API.md`/`REST-API.md` (byte-for-byte in modes A/B) — and the mandatory rules block; treat them as the source of truth and do not rewrite their decisions. Then CREATE your own `PLAN.md` (split into short sprints, following the replacement strategy `ARCHITECTURE.md` records) and follow it. Your sprint 0, before any new code: set up the test environment (WordPress Playground only if this is WP/Woo; otherwise the equivalent local setup for this platform — never block because "the Playground can't be created"), run the REFERENCE version in it, and capture the golden fixtures (modes A/B; none in mode C) and the performance baseline `TESTING.md` defines, recording their provenance. As you build, create and keep updated `HOOKS.md`, `CLASSES.md`, `USAGE.md`, `API.md`/`REST-API.md` and a `CONTINUE-PHASE-N.md` at the end of each sprint. Comply with the sacred rules (security, escaping, sanitization, platform styles) and NEVER invent: if something is unclear, ask. At the end of each sprint, summarize the state and include the raw output of every check in the stack.

### B) Continuity (warn the person)
- Paste here, in this same chat, whatever Code/Codex reports: the process continues in this conversation and switching chats loses the context.
- If the chat with Code/Codex grows too long, ask me to generate a `CONTINUE-PHASE-N.md`: a self-contained prompt to resume in a fresh chat without losing anything essential.

### C) Closure and review (instruction for the development AI)
The documentation must order Code/Codex that, when finished:
> Explicitly declare that the work is done. Generate a return prompt for the planning assistant that includes: an index of all the documentation created; a summary of the final state; and the evidence package for the review — the raw output (command or CI) of every check in the stack (unit/integration, static analysis, coding standards, golden tests, performance versus the sprint-0 baseline), a contract-coverage table mapping every item of `DATA-MODEL.md` and `API.md`/`REST-API.md` to where the new code handles it and which test covers it, and the provenance of the golden fixtures (reference version and capture date, from sprint 0). If there is a playground or anything testable locally, explain to the person step by step how to test it.

### D) Improvement proposal back to the planning assistant
When the development AI spots a better approach and the user wants to discuss it, the development AI generates a prompt like this for the user to paste to the planning assistant:
> While implementing, I found something that could be better. What I saw: `<observation>`. How I would improve it: `<proposal>`. Why: `<reasoning and trade-offs>`. It does not touch the sacred contracts (data/public interfaces/security). Please evaluate it honestly and tell me whether to proceed, adjust, or keep the original spec.

The planning assistant evaluates it honestly (per the honesty principle), decides with the user, and if accepted updates the relevant spec doc before the development AI implements it.
