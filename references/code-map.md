# Code map structure (`refactoring/docs/CODE-MAP.md`)

The code map is the living inventory of the existing software. It is created in Phase 1 by reading all the code and kept up to date throughout the refactor. Its purpose: before refactoring any piece, you know exactly how it works and what depends on it, so nothing breaks.

## Build it mechanically first, then curate

Reading alone does not guarantee completeness — on a large codebase it guarantees the opposite. Build the raw inventories (persisted data, public surfaces) with systematic searches over the whole tree (grep/ripgrep, or an AST tool when available), and only then read the code to curate and annotate what the sweep found. The mechanical output is the completeness baseline: every hit ends up either documented in the map or explicitly discarded with a reason. A persisted item or public surface that the sweep finds and the map lacks is a defect of the map.

Typical sweeps — adapt to the platform:

- WordPress/WooCommerce, persisted data: `get_option|add_option|update_option|delete_option`, `get_post_meta|add_post_meta|update_post_meta|delete_post_meta`, `get_user_meta|update_user_meta|add_user_meta`, `get_term_meta|update_term_meta`, `get_transient|set_transient|delete_transient`, `wp_cache_get|wp_cache_set|wp_cache_delete`, `$wpdb->` (custom tables and direct queries), `dbDelta`, file writes (`file_put_contents`, `wp_upload_dir`, `WP_Filesystem`), cron (`wp_schedule_event|wp_schedule_single_event`), user session/usermeta tokens.
- WordPress/WooCommerce, public surface: `do_action|apply_filters` (hooks the software EXPOSES), `add_action|add_filter` (third-party hooks it CONSUMES), `register_rest_route`, `add_shortcode`, `add_rewrite_rule`, `wp_ajax_`, `register_block_type`, `WP_CLI::add_command`.
- Any other platform, same idea: storage/ORM calls, config and environment reads, cache clients, route/endpoint registrations, event emitters/listeners, CLI registrations, scheduled jobs.

On large codebases this is also how the mapping scales: run the sweeps once over the whole tree, then read and map module by module in affordable chunks — the sweep guarantees nothing is missed while the reading progresses.

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
- Performance observations from reading: queries inside loops, work repeated on every request that could be cached, N+1 patterns, heavy operations. These feed the Phase 6 performance baseline and budget.

## Maintenance
When refactoring a piece: read its entry here first, refactor, and update the entry (new location, new internal name, same behavior guarantees). The map is also the project's memory across conversations.
