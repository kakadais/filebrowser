# @Intent
- Share boundary => create, list, and revoke user-owned hash links for files and directories.
- Public boundary => expose shared content without normal login while reusing owner rules and scoped filesystem access.
- Access hardening => support optional expiry, password hash, and token-based download continuation.
- Admin oversight => admins can enumerate all shares while regular users see only their own links.
# @Invariants
- Share capability => creation and management require both `Perm.Share` and `Perm.Download`.
- Ownership => every share stores creator `UserID`; delete rights belong only to the owner or an admin.
- Expiry => expired links are deleted lazily on `All`, `FindByUserID`, `Gets`, and `GetByHash` reads and then behave as missing.
- Password protection => share passwords are bcrypt-hashed; protected shares also mint a random token for authenticated download continuation.
- Public resolution => public handlers load the owning user, scope to the shared base path, and then resolve optional relative paths inside that subtree.
- Rule reuse => public file resolution still passes through the same `rules.Checker` and hidden-dotfile policy as authenticated access.
- Shared root naming => directory share JSON replaces the synthetic root name with the final segment of the original shared path.
- Public transfer => public JSON shares and public downloads reuse `FileInfo`, raw file serving, and raw archive generation.
- Backward compatibility => public download URLs may carry an extra filename segment for legacy browser save behavior.
# @Perf_Scale
- Public lookup path => each request performs share lookup, optional password check, owner lookup, and filesystem stat or listing work.
- Lazy cleanup => stale-share pruning cost is paid on share read paths instead of background workers.
- Large shared dirs => archive generation and directory listing costs match authenticated paths because public access reuses the same primitives.
# @Checklist
- Share schema change => update storage backends, public resolver, and admin or user listing endpoints together.
- New share auth flow => validate passwordless, passworded, token-based download, and expired-link behavior separately.
- Public payload change => keep share responses compatible with frontend share and `FileInfo` consumers.
- File lifecycle change => review delete, move, and stale-link behavior for shared paths.
- Admin share change => keep owner-only delete policy explicit even when admins can list everything.
