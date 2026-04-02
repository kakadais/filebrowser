# @Intent
- Bootstrap boundary => own process startup, config precedence, quick setup, and listener selection.
- Settings split => instance settings and server settings persist separately and are loaded through `settings.Storage`.
- Runtime services => startup wires image service, upload cache, optional file cache, logging, and embedded asset FS before serving.
- First-run UX => missing database path triggers automatic bootstrap instead of a hard failure.
# @Invariants
- Config precedence => flags override env, env override config file, config file override database, database override defaults.
- Quick setup => missing database must persist auth backend, settings, server settings, and first admin user before HTTP startup.
- Quick setup defaults => JSON auth is default unless `--noauth`; generated admin inherits defaults and then forces `Perm.Admin=true`.
- `settings.Storage.Save` => rejects empty keys and normalizes locale, view mode, rules, shell, commands, and before/after hook maps.
- `settings.Storage.Get` => backfills user home base path, logout page, password minimum, tus defaults, and file modes.
- Listener exclusivity => socket mode cannot coexist with address, port, or TLS overrides; explicit address flags clear saved socket state.
- Root normalization => server root must resolve to an absolute path before user filesystems or handlers are constructed.
- Command execution toggle => server execution stays opt-in and any enabled path must retain explicit warning logs.
# @Perf_Scale
- Image processing => worker count must stay `>= 1`; resizing throughput scales with configured worker count.
- Upload coordination => in-memory upload cache is local default; Redis cache is the multi-instance path.
- File caching => disk cache is optional and disabled when `cacheDir` is empty.
# @Checklist
- New setting => update structs, normalization, persistence, and frontend boot payload if the SPA reads it.
- Flag migration => preserve compatibility shims or document exact removal timing before deletion.
- Bootstrap edits => test missing-DB bootstrap and existing-DB startup separately.
- Listener edits => validate unix socket, plain TCP, and TLS paths together.
- Default change => document security and migration impact before changing bootstrap defaults.
