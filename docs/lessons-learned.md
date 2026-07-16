# Lessons learned — Refactor skill

Recorded mistakes are never repeated.

- **L-1** (2026-07-16): README's phase list had drifted from SKILL.md (Phase 10 was missing). Whenever SKILL.md's phases or rules change, the same edit must check README's "What it does" list, INSTALL.md's package tree, and CHANGELOG.md — coherence across files is part of the change, not a separate task.
- **L-2** (2026-07-16): The mandatory rules exist in TWO places by design (SKILL.md Phase 7, rules 1-11, and dev-handoff.md's rules block + templates). Any rule change must be mirrored in both, or the planner and the development AI receive different contracts. Same for capture sequencing: it is stated in Phase 6, Phase 7 rule 1, "What the development AI does", golden-tests.md and dev-handoff.md — all five must agree.
- **L-3** (2026-07-16): git operations on the repo from the Cowork device bridge can hit a stale `.git/index.lock` that the sandbox cannot unlink ("Operation not permitted"). Do not fight it: write files through the bridge and run git (commit, tag, push) from the Mac directly.
