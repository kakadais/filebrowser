# @Intent
- Product boundary => ship one Go service with embedded Vue SPA for authenticated file browsing, editing, sharing, and admin flows.
- Runtime composition => `cmd` owns process bootstrap; `http` owns request surface; domain packages own stateful policies.
- Persistence split => filesystem stores file content while Bolt-backed metadata stores users, auth config, settings, and shares.
- Config contract => backend injects runtime flags into `window.FileBrowser`; frontend must treat those keys as authoritative boot data.
- Security posture => auth, rules, and permissions are enforced server-side; frontend only mirrors capability hints.
# @Invariants
- `main.go` => delegates execution to Cobra root command; startup branching must stay centralized in `cmd`.
- `storage.Storage` => aggregates `Users`, `Share`, `Auth`, and `Settings`; handlers depend on the aggregate instead of raw backends.
- `http.NewHandler` => mounts `/health`, `/static`, SPA fallback, and `/api/*` subrouters from one mux root.
- Common handler wrapper => reloads settings per request, applies global headers, and builds request-scoped `data` with runner + store references.
- Base URL handling => server trims trailing slash and strips prefixes at router/static boundaries instead of duplicating prefixed route tables.
- Embedded assets => frontend `dist` is served from embedded FS; runtime HTML is templated instead of rebuilt on startup.
- Cross-layer contract => backend JSON field names and frontend type/constants must evolve together for boot data and API payloads.
# @Perf_Scale
- Deployment baseline => single-process embedded service is the primary model; metadata persistence assumes local Bolt/Storm latency.
- Request freshness => per-request settings loads trade storage reads for zero-restart config propagation.
- Static delivery => gzip-compressed JS assets are preferred when clients advertise gzip support.
# @Checklist
- Startup flow change => update `cmd`, `http`, and the architecture capsule in one change set.
- New subsystem => add a parent boundary capsule before adding leaf capsules or scattered code comments.
- Boot payload change => update static HTML injection, frontend constants, and client types together.
- Route prefix change => validate SPA fallback, `/static`, `/api`, and share/public links with non-empty `BaseURL`.
- Backend swap => preserve `storage.Storage` aggregate semantics or document the new aggregate boundary first.
