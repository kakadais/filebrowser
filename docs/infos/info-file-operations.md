# @Intent
- Resource boundary => `/api/resources` owns file metadata reads and authenticated CRUD over scoped user filesystems.
- Access model => server-side rule checking and permission bits gate every path read, expansion, and mutation.
- Transfer model => downloads, previews, resumable uploads, and search extend the same scoped filesystem contract.
- Hook model => mutating operations may trigger before and after command hooks through `runner.Runner`.
# @Invariants
- Rule evaluation => dotfile hiding denies early; then global rules and user rules evaluate in order with the last match winning.
- File introspection => `files.NewFileInfo` rejects disallowed paths before stat or expansion work.
- Directory reads => expanded directories return listing metadata plus sorted children using the caller's saved sorting.
- Mutations => POST, PUT, PATCH, and DELETE must enforce `Create`, `Modify`, `Rename`, or `Delete` semantics before touching storage.
- Root safety => root path cannot be deleted, moved, or overwritten.
- Overwrite semantics => existing targets require explicit `override=true`; conflict paths return `409` when overwrite is not allowed.
- Cleanup semantics => file deletes purge share records and preview cache; renames purge preview cache before move.
- Upload modes => simple POST writes whole bodies; tus paths append with offset checks and finalize via upload cache completion.
- Download semantics => raw downloads require `Perm.Download`; directory downloads archive only rule-allowed descendants.
- Preview semantics => previews fall back to raw file delivery when resizing is disabled or format support is missing.
- Search => `/api/search` streams newline-delimited JSON results and heartbeat lines while walking the scoped filesystem.
- Command execution => websocket command runner requires server exec enabled, user `Perm.Execute`, and per-user command allowlisting.
# @Perf_Scale
- Listing cost => expanded directory responses materialize full child lists in memory before sorting and JSON rendering.
- Preview cache => resized previews are cached by real path, modtime, and size key when file cache is enabled.
- Tus recovery => upload offsets live in cache, so resumable upload durability depends on cache choice.
# @Checklist
- New file action => update permissions, hooks, share cleanup impact, and frontend API calls together.
- Rule change => validate hidden-dotfile behavior, global rules, and user rules with conflicting matches.
- Upload change => test both plain POST and tus resumable paths.
- Download change => verify archive generation for single file, single dir, and multi-select requests.
- Search or exec change => preserve streaming behavior and cancellation safety.
