# Refactor — safe refactoring of existing software

A skill for **safely refactoring software that already exists** (plugins, addons, extensions, libraries, apps or services): modernizing and restructuring the code without breaking stored data or public contracts.

It is **not** for new projects from scratch. It assumes there is already real code to start from, and its whole purpose is to make a refactor succeed without breaking anything that users or integrations depend on.

## What it does

It drives a refactor through a clear, ordered set of phases:

1. **Map the existing code** (mandatory first step) — study all the code and produce a living map of functions, interactions, data (options, metas, tables) and risk zones, so nothing is ever broken by accident.
2. **User intent and compatibility mode** — decide explicitly between full backward compatibility, rename with migration, or no compatibility (with data loss).
3. **Architecture** — design a modern, extensible architecture aligned with the chosen compatibility mode.
4. **Security** (spec) — the platform's security checklist: validate, sanitize, escape, prepared queries, capability + nonce, path traversal protection.
5. **Lifecycle and uninstall** (spec) — idempotent install, update and optional full-deletion "Danger Zone".
6. **Test safety net** (spec) — golden-master tests that lock public contracts byte for byte.
7. **Handoff to a development AI** — hand all the documentation to a development AI (Claude Code, Codex...) with strict, unbreakable rules.
8. **Back to review** — review the delivery and run a correction loop until everything is resolved.
9. **Improvement research** (optional) — research similar software and discuss worthwhile improvements, honestly.

A central principle runs through every phase: **never invent — if something is unclear, ask.** This skill is deliberately built so that a refactor succeeds and does not break things.

## Author

Developed by **José Conti**.

- Web: [plugins.joseconti.com](https://plugins.joseconti.com)
- X (Twitter): [@josecontic](https://x.com/josecontic)
- GitHub: [github.com/joseconti](https://github.com/joseconti)

## Repository

[github.com/joseconti/skill-refactoring](https://github.com/joseconti/skill-refactoring)

See [INSTALL.md](INSTALL.md) for installation.
