# @Intent
- Container boundary => separate prebuilt runtime packaging from source-building local development packaging.
- Runtime image => keep production-style container lean and depend on an already-built `filebrowser` binary.
- Dev compose path => provide a self-contained Docker build that compiles frontend assets and Go binary inside container toolchains.
- State layout => mount file content, config, and database through stable container paths instead of baking mutable data into images.
- Dev file-root policy => local-development browsing must target a dedicated host `data/` subtree instead of the repository root.
# @Invariants
- Runtime `Dockerfile` => copies a prebuilt `filebrowser` binary and must not assume host source compilation during image build.
- Source-building container path => must build frontend `dist` before Go compile so embedded assets match the current source tree.
- Runtime binary => any binary copied into the BusyBox runtime image must be Linux-compatible and runnable without missing libc dependencies.
- Runtime paths => `/srv`, `/config`, and `/database` remain the canonical mounted paths for browsed files, config, and Bolt data.
- Dev host mounts => compose-backed local development maps `[project_root]/data` to `/srv` and must not expose the repository root as user content.
- Init flow => container startup must preserve `/config/settings.json` auto-seeding through existing init scripts.
- Health behavior => runtime healthcheck continues to probe `/health` on configured address and port.
- Dev compose UX => one `docker compose up --build` path should be sufficient on hosts without local Go installation.
- Dev persistence => local-development compose should persist browsed files, config, and database outside the image in ignored or placeholder-backed host paths.
- Packaging split => dev compose must not weaken or replace the existing lean runtime image contract.
# @Perf_Scale
- Build cost => source-building dev images pay Node dependency install and Go compile cost on rebuilds.
- Runtime cost => final runtime stage should stay slim and avoid shipping build toolchains.
- Asset freshness => rebuilding the dev image is required when frontend or embedded backend assets change.
- Build context => large local `data/` contents must stay out of Docker build context to avoid slow rebuilds.
# @Checklist
- Container workflow change => update compose file, Docker build file, and this capsule together.
- Runtime image edit => preserve init scripts, runtime paths, and healthcheck behavior.
- Dev compose edit => validate `docker compose config` and at least one build path before merge.
- New mounted state path => add it to `.gitignore` when it is generated locally and to `.dockerignore` when builds do not need it.
- Dev file-root edit => keep a tracked placeholder for `data/` if the compose path expects the directory to exist in fresh clones.
- Build pipeline change => ensure frontend asset generation still precedes Go binary embedding.
