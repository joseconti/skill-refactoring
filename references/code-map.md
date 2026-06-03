# Code map structure (`refactoring/docs/CODE-MAP.md`)

The code map is the living inventory of the existing software. It is created in Phase 1 by reading all the code and kept up to date throughout the refactor. Its purpose: before refactoring any piece, you know exactly how it works and what depends on it, so nothing breaks.

## Minimum content

### 1. Summary and entry points
- What the software is and what it does in one sentence.
- Entry point(s): main file, bootstrap, how it loads.
- Folder structure and what each part is for.

### 2. Inventory of functions, methods and classes
For each class and each relevant function or method:
- Name and location (file and approximate line).
- One-line purpose.
- Inputs (parameters) and output (what it returns).
- Side effects: writes to DB, fires hooks, sends emails, touches files, calls external services.

For large inventories, organize by module or by class and use a table per section.

### 3. Interaction map (who calls whom)
- Incoming calls (who uses this piece) and outgoing (who it calls).
- Flow of the main operations end to end (for example: license creation, activation, renewal), following the data's path.
- Data passed between pieces.

### 4. Dependencies and coupling
- External dependencies (other plugins, libraries, services, APIs).
- Strong coupling points the refactor wants to break or abstract.

### 5. Contact points with the untouchable contract
- Which functions read or write persisted data (tables, options, metas).
- Which functions expose public interfaces (endpoints, hooks, filters).
- These are the highest-risk pieces: they link to the Phase 2 inventory and must be covered by the Phase 6 golden tests.

### 6. Risk zones
- Subtle behaviors or "quirks" that look like bugs but that something depends on.
- Code whose users are unclear (a candidate to investigate before touching).
- Concrete technical debt and why it hurts.

## Maintenance
When refactoring a piece: read its entry here first, refactor, and update the entry (new location, new internal name, same behavior guarantees). The map is also the project's memory across conversations.
