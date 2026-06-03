# Installing the Refactor skill

Repository: [github.com/joseconti/skill-refactoring](https://github.com/joseconti/skill-refactoring)

This is a Claude skill (the `.skill` format installs in Claude apps such as Cowork and Code). The planning workflow itself is written in role-neutral terms, so the same process can be followed by any chat assistant; only the install mechanics below are Claude-specific.

## What's in the package

```
skill-refactoring/
├── SKILL.md              # The skill itself (read by Claude)
├── README.md             # What it is and who made it
├── INSTALL.md            # This file
└── references/
    ├── code-map.md       # Structure of the code map (Phase 1)
    ├── golden-tests.md   # How to lock contracts with golden tests (Phase 6)
    └── dev-handoff.md    # Document catalog, mandatory rules and prompt templates (Phase 7)
```

## Option A — Install from the packaged `.skill` file (Claude Cowork)

1. Get the packaged `refactor.skill` file (or package it yourself, see below).
2. In Claude, open the file and click **Save skill** to install it.
3. The skill triggers on its own when you ask to refactor existing software. You can also invoke it explicitly.

## Option B — Install from the repository folder (Claude Code)

1. Clone the repo:
   ```bash
   git clone https://github.com/joseconti/skill-refactoring.git
   ```
2. Copy the folder into your skills directory (for example `~/.claude/skills/refactor/`), keeping `SKILL.md` at its root and the `references/` folder beside it.
3. Restart Claude Code if it was running. The skill will be available.

## Packaging it yourself

From the skill-creator skill directory:

```bash
python -m scripts.package_skill <path-to-skill-refactoring> <output-dir>
```

This produces a `.skill` file you can install via **Save skill**.

## How to use it

Just ask, in plain language, to refactor something that already exists, e.g.:

- "We need to refactor this plugin without breaking its endpoints."
- "Modernize this addon's code and clean up the technical debt."

The skill starts by mapping all the existing code before changing anything, and walks you through the full process up to the final review. For brand-new projects from scratch, use the `keel` skill instead.

---

Developed by **José Conti** — [plugins.joseconti.com](https://plugins.joseconti.com) · [@josecontic](https://x.com/josecontic) · [github.com/joseconti](https://github.com/joseconti)
