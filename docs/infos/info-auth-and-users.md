# @Intent
- Auth boundary => authenticate requests through pluggable backends and issue JWT sessions for the SPA and API.
- User boundary => persist identity, scope, permissions, UI preferences, commands, and rules in one record.
- Policy layering => combine auth backend output, global settings, and user record to derive effective request capability.
- Session contract => token payload gives the frontend immediate user state while the server remains final authority.
# @Invariants
- `auth.Auther` => every backend authenticates the request and declares whether a dedicated login page is required.
- Supported auth modes => JSON, proxy, hook, and noauth all resolve through `Settings.AuthMethod`.
- Login => successful auth signs HS256 tokens with embedded user snapshot and configured expiration.
- Renewal hints => authenticated responses set `X-Renew-Token=true` when expiry is near or the user was updated after token issue.
- Admin gating => admin-only handlers rely on `Perm.Admin`; UI route guards are advisory only.
- Signup => self-registration is disabled unless enabled and must never grant admin or execute rights by default.
- User hydration => `User.Clean` attaches a scoped `BasePathFs` rooted at server root plus user scope before request use.
- Unique-admin guard => delete paths must reject removing the final admin account.
- Sensitive self-updates => JSON auth requires current password for username, password, scope, or permission-class changes.
- Frontend auth state => client stores JWT in cookie and localStorage and derives idle logout from token expiry unless proxy logout forbids it.
# @Perf_Scale
- Auth freshness => authenticated requests refetch user data so permission and profile edits apply without process restart.
- Update tracking => `LastUpdate` is in-memory session metadata used for renewal hints, not durable audit state.
- Session size => JWT carries only a user snapshot, not full settings or filesystem data.
# @Checklist
- New auth backend => implement `Auther`, backend serialization, and boot metadata for `LoginPage` and auth mode.
- User field change => update storage clean/save paths, JWT projection, and frontend user typing together.
- Permission change => review signup defaults, server handlers, and router guards in one change set.
- Password policy change => update validation rules, common-password checks, and quick-setup behavior together.
- Auth UX change => verify noauth, proxy logout, and standard login flows separately.
