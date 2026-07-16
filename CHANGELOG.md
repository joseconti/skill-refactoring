# Changelog — Refactor skill

All notable changes to this skill are documented here. The version in `SKILL.md` frontmatter is the source of truth.

## 1.1.0

- Sprint 0 hardened: the golden fixtures and the performance baseline are captured from the REFERENCE version, in the real test environment, before any new code exists; their provenance (version, date) is recorded. Fixtures are never generated or regenerated from the new code — a fixture change is a contract change requiring explicit user approval. (Phases 6-7, `golden-tests.md`, `dev-handoff.md`.)
- Performance joins the safety net: performance-relevant observations recorded in the code map (Phase 1), a baseline captured in sprint 0 and a performance budget in `TESTING.md` (Phase 6), the comparison kept green as part of the test stack (rule 9), and the extensibility layer explicitly designed within the budget (Phase 3). A cleaner-but-slower refactor is a failed refactor unless the user accepts the trade-off.
- Review with evidence (Phase 8): raw check outputs instead of summaries, a contract-coverage table (every `DATA-MODEL.md`/`API.md` item mapped to code and test), fixtures' provenance, and a dedicated reviewer subagent by default when the environment supports one. The dev AI's closure prompt now returns this evidence package (rule 11, template C).
- Mode-B migrations at scale (Phase 5, `LIFECYCLE.md`): idempotent, resumable, batched, dry-run, progress record and abort/rollback path are now mandatory spec items; WP-CLI command on WordPress for big sites; expected data volumes asked explicitly.
- Replacement strategy is an explicit Phase 3 decision made with the user: full rewrite vs incremental (strangler) coexistence, recorded in `ARCHITECTURE.md` and followed by the dev AI's plan.
- Code map built mechanically first (Phase 1, `code-map.md`): concrete grep/AST sweep recipes per platform; the mechanical inventory is the completeness baseline the map must account for — a sweep hit missing from the map is a defect of the map.
- README: Phase 10 (release) added to the phase list (it was missing) and the phase summaries aligned with the above.
- Handoff read list fixed: the seeded `API.md`/`REST-API.md` contract (byte-for-byte in modes A/B) is now explicitly among the files the development AI must READ (Phase 7 and template A) — it was only in the create/complete list.
- Mode-C carve-out made explicit everywhere sprint 0 is mentioned (no golden fixtures to capture in mode C; the performance baseline always applies).
- Hygiene: the Keel workflow state (`docs/`) is export-ignored so it never ships, and INSTALL.md's package tree now includes `CHANGELOG.md`.

## 1.0.0

- First release.
- Full refactoring workflow for existing software (plugins, addons, libraries, apps, services), role-neutral (planning assistant + development AI).
- Phases: 1) map the existing code, 2) intent and compatibility mode (A/B/C), 3) architecture, 4) security spec, 5) lifecycle and uninstall spec, 6) test safety net spec (golden master + PHPUnit/PHPStan/PHPCS), 7) handoff to the development AI, 8) review and correction loop, 9) optional improvement research with its own build+review cycle, 10) release and publish.
- References: `code-map.md`, `golden-tests.md`, `dev-handoff.md`.
