# @Intent
- Deployment bundle => support portable server delivery as a prebuilt Docker image tar plus image-only compose file.
- Host assumption => remote target may provide Docker and Compose only, without Go, Node, or source-build toolchains.
- Arch policy => deployments from arm64 workstations may target linux/amd64 server images explicitly.
- Runtime safety => server deployments default to authenticated mode and explicit listener binding.
# @Invariants
- Deployment image tag => uploaded image and compose file must agree on a stable local-only tag.
- Compose startup => production deployment uses `image:` only and must not require `build:` on the remote host.
- Persistent state => deployment directory keeps `data`, `config`, and `database` beside compose artifacts for in-place upgrades.
- Listener config => runtime command binds `--address=0.0.0.0` and `--port=80` unless an operator overrides the compose file.
- Publish port policy => host-to-container publish port may differ from container port `80`; the tracked bundle may pin a reverse-proxy-facing host port.
- Auth default => deployment compose must omit `--noauth`.
- Server path policy => uploaded bundle targets `~/deploy/{project-name}` and remains self-contained under that directory.
# @Perf_Scale
- Image transfer => tarball delivery trades registry setup for larger one-shot uploads.
- Re-deploy cost => repeated deployments can replace image tar and rerun compose without rebuilding on the server.
- None
# @Checklist
- New deployment artifact => update this capsule and the container runtime capsule together.
- Compose tag change => keep local build tag, saved tar name, and remote compose `image:` value aligned.
- Remote port change => verify target host port availability before exposing public listeners.
- Deployment bundle change => keep generated tarballs out of git and Docker build context.
- Server rollout => verify remote container health after `docker compose up -d`.
