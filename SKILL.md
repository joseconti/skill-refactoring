---
name: refactor
description: >-
  Safely refactor EXISTING software (plugins, addons, extensions, libraries,
  apps or services), modernizing the code without breaking stored data or public
  contracts. Use it whenever the user wants to refactor, rewrite, modernize, clean
  up, restructure or harden code that already exists, or says things like "we need
  to refactor this plugin", "modernize this code", "rewrite this with a good
  structure", "clean up the technical debt", "reorganize this addon", "hacer un
  refactoring" or "refactorizar este plugin". Its mandatory first step is to study
  all the code and build a map of functions and interactions so nothing ever
  breaks. Do NOT use it for brand-new projects from scratch (that is what the keel
  skill is for); this skill assumes there is already code to start from.
metadata:
  version: 1.1.0
  author: José Conti
  homepage: https://plugins.joseconti.com
---

# Refactor — safe refactoring of existing software

This skill guides the refactoring of software that ALREADY exists: modernizing and restructuring the code without breaking what users or integrations already depend on. It is not for new projects (that is what `keel` is for); here you always start from real code.

The core idea: before changing anything, you must understand what the code does and what must not be touched. Almost all the risk in a refactor is breaking, without noticing, something that something or someone depended on. The phases below exist to make that risk visible and controllable.

## Version

This skill is **Refactor v1.1.0**. If the user asks which version of this skill they have or are running (for example "what version is this skill", "which Refactor version do I have", "qué versión tiene este skill"), state it plainly from the `version` field in the frontmatter: "You're using Refactor v1.1.0." The frontmatter `version` is the source of truth; keep it, this line, and `CHANGELOG.md` in sync. Only change the version number on the user's explicit instruction — never bump it on your own initiative.

## Update check (do this first, only if you can)

At the very start of a run, before Phase 1, check whether a newer version has been published — but only if you have web access. Just check and notify; do NOT try to update or install anything yourself. Repository: https://github.com/joseconti/skill-refactoring

1. Fetch the published version and compare it with this skill's frontmatter version (1.1.0). Read the `version:` field from `https://raw.githubusercontent.com/joseconti/skill-refactoring/main/SKILL.md` (or the top entry of `https://raw.githubusercontent.com/joseconti/skill-refactoring/main/CHANGELOG.md`).
2. If the published version is newer, simply tell the user there is a new version available (name it), briefly note what changed, and point them to the repository to download/update it if they want. Do not download, replace or install anything — leave updating entirely in the user's hands.
3. If you cannot access the web, or the fetch fails, skip this silently and continue with the refactor. Never block the work because the update check failed.
4. Do this at most once per session; do not nag.

## Principles that govern every phase

- Never invent or interpret silently. Anything undefined is asked to the user before continuing. It is better to stop and ask than to guess and break.
- Specs are a strong starting point, not dogma. The only things locked 100% are the sacred contracts: persisted data and public interfaces per the chosen compatibility mode, and security. Everything else (architecture, structure, naming) is open to improvement. The development AI is the real implementation specialist: if it sees a better approach, it should propose it (not implement it unilaterally), explain it to the user, and offer to discuss it with the planning assistant. Proposing improvements and asking are encouraged; silently deviating is not.
- Separate the untouchable from the free. Persisted data and public interfaces are a contract; internal names of classes, files and functions can be changed freely.
- Security is per platform and not optional. The platform's official standard is consulted from the start, not at the end.
- Document as you go, not as a final step.
- All process and project documentation ALWAYS in Markdown (`.md`), in any phase. The only exceptions are platform-convention release files the development AI must ship in a fixed format — e.g. WordPress `readme.txt` and `changelog.txt` — which follow the platform's required format, not `.md`.
- Reuse the internal API before writing new code. Duplication is a defect, just like a security hole.
- Close each phase before moving to the next. Do not slide into the next phase with open gaps in the current one.

## Who does what (the full cycle)

Two roles are involved. To avoid confusing the person, name them by role, not by product:

- **The planning assistant (you)** — whichever AI is running this skill (Claude, ChatGPT, or any chat assistant). It does the thinking, design and documentation, and the final review. Refer to it as "the planning assistant" or simply "I/you"; never assume a specific product name when talking to the person.
- **The development AI** — the tool that writes and tests the code in the real environment (Claude Code, Codex, etc.).

The work moves back and forth between these two hands:

1. **The planning assistant (you)** runs Phases 1-7: study the code, decide compatibility, design the architecture, write ALL the handoff documentation (including the security, lifecycle and testing specs) and prepare the handoff. You do NOT write the software's code.
2. **The development AI** builds and tests in the real environment following that documentation, with its own sprint plan (the release itself is closed later, in Phase 10, after the review approves). This happens outside this skill and is not part of its phases.
3. **Back to the planning assistant (Phases 8-10)**: when the development AI finishes, it returns a prompt with all the documentation; you do the full review (Phase 8), optionally research improvements (Phase 9), and coordinate the release closure (Phase 10). The review may trigger another correction sprint.

While the development AI works, the person pastes into this same chat whatever that AI reports, so you keep the thread without losing context.

CRITICAL RULE: this SKILL is for the planning assistant only. The development AI must NOT see the SKILL or these phases: it only receives the documentation in `refactoring/docs/`, written exclusively as instructions for it. If you hand it the whole skill, it gets confused. Keep the "planning" work separate from the "development" work.

## How to run the skill

Work the phases in order. Each phase carries its objective, the artifact it produces and the questions worth asking the user so you do not advance blindly. Announce each phase in one line, do the work, and confirm closure before continuing. If the user resumes mid-way, first read the artifacts already produced (especially the code map and the contract inventory) and continue from where things stand; never reinterpret decisions already made.

For the detail of the key artifacts, read the references when you reach them:
- Code map structure: `references/code-map.md`
- How to lock contracts with golden tests: `references/golden-tests.md`
- Handoff document catalog, mandatory rules and prompt templates: `references/dev-handoff.md`

---

## Phase 1 — Map the existing code (FIRST STEP, before touching anything)

Objective: study ALL the code and produce a document that explains what functions exist, what each one is for and how they interact. This map is the permanent reference: every time a piece is to be refactored, its entry is consulted to know how it works and what depends on it, so nothing is ever broken by carelessness.

Precondition: you must have direct access to the software's source code directory — mounted/connected, so you can read it and create the `refactoring/docs/` folder and files inside it. If you do not have that access, ask the user to grant access to (connect/mount) the software's directory; do not ask for a zip upload, because you need to read the tree and write files into it.

Artifact: `refactoring/docs/CODE-MAP.md`, inside a `refactoring/docs/` folder created in the software's main directory (see `references/code-map.md` for the exact structure). The `refactoring/` subfolder is used so that everything this process generates stays clearly separated from the software's code and can be removed easily later without mixing with anything. It is a living document: when refactoring a function its entry is read first and updated afterwards. All artifacts of this process (map, contract inventory, plans) live in `refactoring/docs/`.

A key part of this phase: locate and document absolutely EVERYTHING the software creates or persists: all options, metadata (order, product, user, etc.), tables, columns, cache keys, transients and files. This data inventory is the raw material for the compatibility mode decided in Phase 2; if something is missing here, a later migration or deletion will be incomplete.

Do not build these inventories by reading alone: extract them mechanically first. Run systematic searches (grep/ripgrep or an AST tool) over the whole tree for the platform's persistence and extension APIs, and treat that raw output as the completeness baseline the map must account for — the sweep guarantees nothing is missed; reading provides the semantics. Concrete sweep recipes per platform are in `references/code-map.md`. The same applies to the public-surface inventory (hooks, filters, endpoints, CLI). While reading, also record performance-relevant observations (queries inside loops, work repeated on every request that could be cached, N+1 patterns, heavy operations) as risk-zone entries: they feed the performance baseline and budget of Phase 6.

If the software extends or integrates with other plugins, study those plugins too and document the real hooks and filters they use. This avoids breaking them or inventing hook names by accident when adding functionality, a frequent mistake. If you do not have that plugin's code yet, ask the user to place it in `refactoring/docs/plugins` (in the connected directory) before mapping those integration points — do not guess the hook names.

Do not start designing or rewriting until this map exists and has been reviewed. It is the foundation for the compatibility decision (Phase 2) and the test safety net (Phase 6).

Questions for the user:

- Is there already a map, diagram or documentation of the code, or do we start from scratch?
- What level of detail do you want: a per-module summary or an entry per function?
- Which parts work well and must be kept, and which ones hurt and motivate the refactor?
- What does the code depend on today (other plugins, libraries, external services)?
- Are there subtle behaviors or "quirks" that look like bugs but that something depends on?

---

## Phase 2 — User intent and compatibility mode

Objective: understand what the user wants from the refactor and, above all, explicitly decide the compatibility mode. This decision conditions everything else (architecture, migration, uninstall, tests), so do not advance without having it in writing.

First ask what they want to achieve with the refactor (modernize, decouple, clean debt, prepare an extension...). Then set the compatibility mode by choosing one of these three paths:

- **A) Full backward compatibility.** Keep all names (options, metas, endpoints, tables) as they are. The change is transparent: nothing external notices it and all data keeps working. Only internal names of classes, files and functions change.
- **B) Rename with migration.** Options/metas/tables/endpoints are renamed to something clean, but a migration is written that moves everything existing to the new names. Transparent to the end user, but with the cost and risk of the migration. On real installed bases the migration is an engineering piece in its own right — batched, resumable, with dry-run and rollback (specified in Phase 5) — so factor that cost into the decision.
- **C) No backward compatibility.** All names are changed without migration. It must be made VERY clear to the user that this means losing all existing data (licenses, settings, metadata). Only valid if the user explicitly accepts it.

Lean on the Phase 1 data inventory so the scope of the migration or deletion is complete. Then WRITE the frozen contract to files (do not leave it only in memory):

- `refactoring/docs/DATA-MODEL.md` — every persisted item (tables, columns, options, metas, cache keys, transients, files) with its exact name and, per the mode, whether it is preserved, migrated or dropped.
- `refactoring/docs/API.md` and/or `REST-API.md` — the public interfaces consumed by third parties (endpoints, hooks, filters, response formats, CLI). In mode A or B this is the exact contract, byte for byte, including error formats, serialization and salts.

In mode A or B, treat these two files as the sacred contract and also save the key constraints to memory, so they govern future conversations too. These are the most critical artifacts of the whole refactor: if they are incomplete, something will break.

Questions for the user:

- What do you want to achieve with this refactor?
- Mode A, B or C? (If unsure, explain the trade-off of each.)
- In mode A or B: which interfaces do third parties consume (endpoints/API, hooks, filters, response formats, CLI) and are there serialization formats or salts that cannot be changed?
- In mode C: do you confirm you accept losing all existing data?

---

## Phase 3 — Architecture

Objective: design the software's modern architecture, aligned with the Phase 2 compatibility mode, and write it down in `refactoring/docs/ARCHITECTURE.md` as part of the documentation the development AI will later execute.

Steps:

1. Decide the replacement strategy explicitly, with the user: **full rewrite** (the new code replaces the old in one release, protected by the safety net) or **incremental / strangler** (old and new coexist; modules are replaced one at a time and the refactor can ship in stages). Codebase size, installed base, risk tolerance and release cadence decide: the bigger the codebase and the more users in production, the stronger the case for incremental. Record the choice and its reasons in `ARCHITECTURE.md`; the development AI's plan must follow it.
2. Design the new structure and abstractions, consistent with the Phase 2 compatibility mode.
3. Decide the level of modernization (language version, autoload, dependencies, typing).
4. Abstract variability behind interfaces (interchangeable providers/engines) so the core is not coupled to one implementation.
5. Define the extension points (hooks, filters, events, API) with maximum extensibility: every relevant string filterable, a hook before/after every important decision, every query and response filterable — and note in `ARCHITECTURE.md` that this layer has a runtime cost, so it is designed within the Phase 6 performance budget, not against it.
6. Collect platform requirements (in WooCommerce: HPOS, blocks, multisite).

Questions for the user:

- Full rewrite or incremental (strangler) replacement? Could/should the refactor ship in stages?
- What level of modernization are you after?
- Is there variability worth abstracting behind an interface?
- Specific platform requirements (HPOS, blocks, multisite)?
- Which Git host and packaging target do you use (GitHub, GitLab, Bitbucket, WordPress.org SVN...)? This decides whether `.gitattributes` export-ignore is enough or a different exclusion file is needed (`.distignore`, `.npmignore`, etc.).
- Which development AI will you work with (Claude Code, Codex...)? (To tune the Phase 7 prompt.)

---

## Phase 4 — Security (spec in docs)

Objective: establish the platform's security checklist before any code is written, and close along the way the holes the old code carries.

The planning assistant does not implement this: it writes it as a specification in the handoff documentation (e.g. `refactoring/docs/SECURITY.md`), so the development AI complies with it in every line. It is handoff content, not a task the planning assistant executes.

Before any code, consult the platform's official security guides (in WordPress, via the site's MCP if available). Distinguish three operations: validate (the data is what you expect), sanitize (clean it) and escape (prepare it for the output context). Usual rules: validate and sanitize on input; escape on output always late and per context (HTML, attribute, URL, JS), using `wp_kses` when controlled HTML must be allowed; never process the whole raw input; authorization with capability and nonce/CSRF together; database queries always prepared; path traversal protection in files and downloads.

Questions for the user:

- What user or network input does the software process? How are secrets or personal data handled?
- Are there privacy or regulatory requirements (e.g. GDPR)?
- What known holes does the old code carry that must be closed in the rewrite?

---

## Phase 5 — Lifecycle and uninstall (spec in docs)

Objective: define installation, update and uninstall, including the optional data cleanup.

Like Phase 4, this is written as a specification in the handoff documentation (e.g. `refactoring/docs/LIFECYCLE.md`) for the development AI to implement; the planning assistant defines it, does not code it.

The installer must be idempotent over the existing database (it must not overwrite or duplicate data). By default, uninstalling deletes nothing. If the user wants full deletion, it is offered as an opt-in option, visually distinct ("Danger Zone"), with a clear warning of permanent and irreversible deletion and, ideally, an extra confirmation. The deletion routine is mapped against the Phase 2 inventory to leave no trace.

In mode B, the data migration is a first-class deliverable of this spec, not a footnote of the installer. `LIFECYCLE.md` must require it to be: idempotent and resumable (it can be interrupted at any point and re-run without corrupting anything), batched (it never loads or rewrites an unbounded set of rows in one pass — metas on a large store run to millions), with a dry-run mode that reports what would change without changing anything, a progress record, and a defined abort/rollback path (what state an installation is in if the migration stops halfway, and how it gets back). On WordPress, offer a WP-CLI command besides the admin trigger, for big sites. Ask for expected data volumes so the development AI sizes the batches for the largest real installation, not for the dev sandbox.

Questions for the user:

- What should happen on install, update and deactivate? Is data kept by default?
- In mode B: roughly how much data would the migration move on the largest real installation (rows of options/metas/tables)? Does it need WP-CLI support?
- Do you want a full-deletion option on uninstall? If so, with extra confirmation?
- What exactly should that cleanup delete?
- Do we need to handle multisite, multiple environments or version-to-version migrations?

---

## Phase 6 — Test safety net (spec in docs)

Objective: guarantee that critical behavior does not change during the rewrite.

This is also a handoff spec: the planning assistant defines the test strategy in the handoff documentation (e.g. `refactoring/docs/TESTING.md`) and the development AI implements and runs it in the real environment.

The most powerful approach for a refactor is the "golden master" strategy: capture the reference version's responses as versioned fixtures and compare them byte for byte against the new code, in blocking CI. That way, breaking a contract raises an alarm immediately. See `references/golden-tests.md`.

Sequencing is part of the guarantee: the fixtures (and the performance baseline below) are captured from the REFERENCE version, running in the real test environment, before a single line of new code exists. The handoff imposes this as the development AI's sprint 0, with the fixtures' provenance recorded (reference version, date). Fixtures captured — or regenerated — after the rewrite compare the rewrite against itself and protect nothing.

This applies to modes A and B, where there is a contract to preserve. In mode C (no backward compatibility) there is nothing old to keep, so the golden master of the old contract does NOT apply; the tests focus on the new behavior instead. Do not waste effort capturing fixtures of something that is being intentionally dropped.

The full test stack the spec must require (platform-appropriate, all green in blocking CI):

- **Unit and integration tests.** For WordPress/WooCommerce: PHPUnit with the WP test suite (and WooCommerce helpers). For other platforms, the equivalent (Jest/Vitest, Pytest, Go test, etc.).
- **Static analysis.** For PHP: PHPStan (or Psalm) at a defined level. For other languages, the equivalent type/lint analyzer.
- **Coding standards.** For WordPress: PHPCS with the WordPress and WooCommerce sniffs (WPCS). For other platforms, that ecosystem's linter/formatter.
- **Golden tests** for the locked public contracts (above).
- **Performance baseline and budget.** Captured in the same sprint 0, from the reference version in the real environment (alongside the golden fixtures in modes A/B; in mode C the baseline still applies even though fixtures do not): the platform's key metrics — for WordPress/WooCommerce, database queries per key operation, peak memory, and the time of the critical flows. The spec sets the budget (by default: no worse than the baseline beyond an agreed tolerance) and every sprint close compares against it. This matters because modernization has a cost profile of its own — abstraction layers and maximum extensibility are not free — and a refactor that is cleaner but meaningfully slower is a failed refactor unless the user explicitly accepts the trade-off.

Name the concrete tools per the project's platform, with their config files (e.g. `phpunit.xml.dist`, `phpstan.neon`, `phpcs.xml`) and a CI workflow that blocks merges if anything is red. State the minimum coverage or level expected.

Key decision of this phase: where the tests run. If verification requires a real environment with the platform live (WordPress + WooCommerce, etc.), the heavy build should be run by a tool that runs in that environment (e.g. the development AI), instead of producing code that cannot be tested. This is exactly why the build is handed off in Phase 7; no separate "brief" document is needed — the handoff documentation in `refactoring/docs/` (map, DATA-MODEL, API contract, the specs and the mandatory rules) is that brief.

Questions for the user:

- What behavior can never change and must be captured as a reference? (In mode C, none — skip the golden master.)
- Do we have a "golden" version to compare against (fixtures)?
- Which performance metrics matter for this software, and what tolerance versus the baseline is acceptable?
- What target levels/coverage do you want for the test stack (e.g. PHPStan level, minimum coverage)?
- Does validation run in a real environment or a sandbox? This decides who runs the build.

---

## Phase 7 — Handoff to the development AI

Objective: close the planning assistant's part. Gather all the documentation in `refactoring/docs/` (Phases 1-6) and hand the person the prompt they will give to the development AI (Code/Codex) to build. The planning assistant's work ends here until the review.

IMPORTANT: the development AI only receives the documentation in `refactoring/docs/`, written exclusively as instructions for it. NEVER hand it this SKILL or these phases. The document catalog, the mandatory rules block and the prompt templates are in `references/dev-handoff.md`; read it when you enter this phase.

Make the file split explicit to the development AI: which files you provide (the specs it must read but not rewrite: `CODE-MAP.md`, `DATA-MODEL.md`, `ARCHITECTURE.md`, `SECURITY.md`, `LIFECYCLE.md`, `TESTING.md`, plus the seeded `API.md`/`REST-API.md` contract — byte-for-byte in modes A/B) and which files it must create and how to use each (`PLAN.md`/`PROGRESS.md` as living state, `HOOKS.md`/`CLASSES.md`/`USAGE.md` documented as it builds, `API.md`/`REST-API.md` completing the contract, a `CONTINUE-PHASE-N.md` per sprint). The full list with usage is in `references/dev-handoff.md`.

The handoff documentation must impose these mandatory rules on the development AI:

1. Sprint 0, before any new code: set up the test environment with the REFERENCE (old) version and capture from it the golden fixtures and the performance baseline that `TESTING.md` defines (fixtures only in modes A/B — in mode C there are none; the performance baseline always), recording their provenance. Only then start building, verifying every part in that environment. IF it is WordPress/WooCommerce, create a WordPress Playground; IF it is any other platform, use that platform's normal local test setup (Playground does not apply). Never stop because the Playground "cannot be created" — it is WP-specific. If unsure how to test, ask instead of blocking.
2. If it needs a premium plugin, ask the person to place it in `refactoring/docs/plugins`; if it extends it, study it and document its real hooks, without inventing names.
3. Study everything first, create a PLAN and follow it, split into short phases/sprints. At the close of each sprint, generate a `CONTINUE-PHASE-N.md` to resume in a fresh chat without dragging old context.
4. Document everything it creates, so that in the end, just with those documents, the developer documentation page can be generated.
5. SACRED: always follow the platform's styles, security, escaping and sanitization.
6. NEVER invent: if something is unknown, ALWAYS ask before implementing.
7. Propose improvements, don't silently deviate. The specs are a starting point, not dogma (except the sacred contracts: data/public interfaces per the compatibility mode, and security, which are non-negotiable). If you, as the implementation specialist, see a better way, tell the user, explain how and why, and ask if they want to discuss it with the planning assistant. If they say yes, generate a prompt the user will paste to the planning assistant describing what you saw, how you'd improve it and why, so it can evaluate too. Do not implement the change until it is agreed.
8. Maximum extensibility: expose hooks (actions and filters) at every reasonable point and document them.
9. Keep the automated test stack green (PHPUnit/equivalent, PHPStan/Psalm, PHPCS, golden tests, performance versus the sprint-0 baseline) in blocking CI; never close a sprint with a check red, and never report a check as green without its raw output — sprint closes include the actual command/CI output, not a summary.
10. ALWAYS create hygiene files: `.gitignore` and `.gitattributes` (with `export-ignore` for dev-only files), so nothing unwanted reaches the repo or the package; if the user's host/packaging uses a different mechanism (`.distignore`, `.npmignore`, etc.), create that instead — ask if unclear. Always create and maintain `changelog.txt`.
11. When done, declare it and generate a return prompt for the planning assistant with the index of all the documentation, the final state, and the evidence package for the review (Phase 8): raw outputs of every check, the contract-coverage table, and the golden fixtures' provenance.

Handoff and continuity:

12. State that the work now passes to the development AI and that it must load the software's main directory.
13. Provide the exact prompt for Code/Codex (template A in the reference).
14. Tell the person to paste into this same chat whatever Code/Codex reports; switching chats loses the context.
15. If the conversation grows too long, generate a fresh self-contained prompt to continue in another chat.

---

## What the development AI does (outside this skill)

These are not phases of this skill, but it helps to know them for the later review: with the documentation in `refactoring/docs/`, the development AI first runs sprint 0 (the test environment with the reference version; the performance baseline — and, in modes A/B, the golden fixtures — captured from it, provenance recorded), then builds in layers (scaffolding and tests first, then data, replicated contracts, domain, integrations, admin and front), following the replacement strategy recorded in `ARCHITECTURE.md`, implements security/lifecycle/tests per the specs, and documents each public surface as it creates it. It sets up the hygiene files early, but the final release (changelog, packaging, version bump) is closed in Phase 10, after the review approves — never bumping the version without an explicit order. It works with its own sprint plan and, at the end of each one, leaves a `CONTINUE-PHASE-N.md`.

---

## Phase 8 — Back to the planning assistant: review and correction loop

Objective: review the delivery and resolve, in a loop with the development AI, whatever fails, until everything is correct.

On resuming:

1. First read the prompt/response the person pastes from Code/Codex and DECIDE: if the context still fits, continue in this same chat; if it is already too long and there is a risk of losing something, generate a fresh self-contained prompt so the person opens a new chat. This decision is the first thing on resuming.
2. Full review: read all the documentation in `refactoring/docs/`, check that the sacred rules were respected (data/contracts per the compatibility mode, security, that nothing was invented and no hooks were broken). You do not run the tests yourself (you are a chat assistant), so the standard of proof is raw evidence, never assurances. Require, and read, the evidence package: the actual outputs (CI run or command output) of the golden tests, the quality tools and the performance comparison against the sprint-0 baseline; the contract-coverage table (every `DATA-MODEL.md` and `API.md`/`REST-API.md` item mapped to where the new code handles it and which test covers it); and the fixtures' provenance (captured from the reference version in sprint 0). "All green" without the outputs is a claim, not evidence — ask for the missing piece before approving. And since whoever builds should not be the only one who verifies: if your environment supports subagents, delegate this review to a dedicated reviewer subagent by default; if it does not, tell the user this pass is worth running with fresh eyes.

Depending on the review outcome, one of two branches:

**A) You see everything fine.** Ask the person to test the software (in the playground or locally) to see if they spot any problem, and to report back the result: whether they found an error or not.

**B) You detect something wrong.** Tell the person and create a prompt for Code/Codex describing what you detected, so it reviews and fixes it. When done, Code/Codex returns a prompt with what it did, and you review it again.

**Test-error loop.** If the person finds an error while testing: investigate where it may come from and create a prompt for Code/Codex to fix it. Code/Codex reads it, fixes it and returns a prompt explaining what it did; you review it. If it is fine, continue; if not, repeat. This goes on until everything is resolved.

When everything is resolved (review OK and tests with no errors), proceed to Phase 9 if the user wants the optional improvement research; otherwise go straight to Phase 10 (release).

Questions for the user:

- Has the development AI already declared completion and delivered the review prompt with the evidence package (raw outputs, contract-coverage table, fixtures' provenance)?
- Is there anything specific you want the review to focus on?

---

## Phase 9 — Improvement research (optional)

Objective: only if the person wants, research similar software to spot improvements and features worth adding. It is optional; if they do not want it, skip straight to Phase 10 (release). If it does surface improvements the user approves, those go back to the development AI as another build+review cycle before releasing.

Steps:

1. Search for similar plugins/addons/software (the competition and references in the sector).
2. Make a comparison against what the refactored software already has.
3. List what others have and this one does not.
4. Look at what people ask of this type of software and does not yet exist (opportunities): reviews, forums, feature requests, support.
5. Discuss with the person which of all that could be genuinely interesting.

Honesty above all: assess each idea sincerely and do not encourage building something not worth it. The person does not want to be pleased, they want to invest time and effort in things that are truly worthwhile. If an improvement makes no sense, say so with the reason and propose the better alternative (or none).

If the user approves one or more improvements, run the same cycle as the main refactor, scoped to the new features:

- **a. New specs.** Produce the new specs and documents for the development AI in `refactoring/docs/`: update or add `ARCHITECTURE.md`, `SECURITY.md`, `LIFECYCLE.md`, `TESTING.md`, and `DATA-MODEL.md`/`API.md` as needed for the new functionality. Respect the sacred contracts: new features must not break the existing data or public interfaces (per the compatibility mode).
- **b. New handoff.** Create a handoff prompt for Code/Codex (same form as Phase 7's template A) that points at the new/updated docs and tells it to EXTEND its plan with new phases/sprints, add the corresponding new documentation, and keep the test stack green.
- **c. Development.** The development AI builds the new features in additional sprints, documents them, and ends with a return prompt for the planning assistant (same as the closure rule).
- **d. Review loop.** You review it (the Phase 8 loop): if something is wrong, send a correction prompt; repeat until everything works.
- **e. Release.** When the new functionality is done and green, go to Phase 10 (release).

Questions for the user:

- Do you want me to research improvements against similar software, or do we close here?
- Are there specific competitors or references you want me to look at?
- If improvements come up: do you want to build them now (extra build+review cycle) before releasing, or release first and add them later?

---

## Phase 10 — Release and publish

Objective: the explicit closure once the work is approved (after Phase 8, and after Phase 9 if it ran). The development AI executes; the planning assistant coordinates and confirms.

Steps:

1. Confirm with the user that everything is approved and ready to release.
2. Instruct the development AI to finalize the release: update `changelog.txt` (and `readme.txt` on WordPress), confirm the hygiene files (`.gitignore`, `.gitattributes` or the host/packaging equivalent decided in Phase 3), and build the distributable package excluding everything that must not ship (tests, CI, `refactoring/`, dev config).
3. Version bump ONLY on the user's explicit order — never change the version number on your own initiative.
4. Tell the user the exact steps to publish to their Git host / packaging target, and how to verify the published package contains only what it should.

Questions for the user:

- Is everything approved and ready to release?
- What version number does this release get? (Required before bumping.)

---

## Directory hygiene (cross-cutting)

Keep the old code separate from the new so they do not mix; if convenient, suggest disconnecting the old folder from the session. Do not delete folders with unsaved work (plans, documentation) even if they are not in git.
