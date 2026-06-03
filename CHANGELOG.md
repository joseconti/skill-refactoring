# Changelog — Refactor skill

All notable changes to this skill are documented here. The version in `SKILL.md` frontmatter is the source of truth.

## 1.0.0

- First release.
- Full refactoring workflow for existing software (plugins, addons, libraries, apps, services), role-neutral (planning assistant + development AI).
- Phases: 1) map the existing code, 2) intent and compatibility mode (A/B/C), 3) architecture, 4) security spec, 5) lifecycle and uninstall spec, 6) test safety net spec (golden master + PHPUnit/PHPStan/PHPCS), 7) handoff to the development AI, 8) review and correction loop, 9) optional improvement research with its own build+review cycle, 10) release and publish.
- References: `code-map.md`, `golden-tests.md`, `dev-handoff.md`.
