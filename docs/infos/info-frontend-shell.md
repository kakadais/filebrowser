# @Intent
- Frontend boundary => render the SPA shell for login, file browsing, public share browsing, settings, and error routes.
- State model => Pinia stores coordinate auth, file selection, layout chrome, uploads, clipboard, and route-driven UI state.
- API adapter => `frontend/src/api/*` wraps backend endpoints and normalizes `BaseURL`-prefixed URLs for views and components.
- Runtime config => backend-injected `window.FileBrowser` values are the only supported source for auth, branding, and feature toggles.
# @Invariants
- First navigation => router initializes auth on first resolve via JWT renew or noauth login before protected-route checks.
- Protected routes => `/files` and `/settings` require auth; `/settings/global`, `/settings/users`, and user detail require admin.
- Login redirect => authenticated visits to `/login` must redirect to `/files/`.
- Catch-all routing => unmatched client paths rewrite to `/files/...`; explicit `/403`, `/404`, and `/500` remain dedicated routes.
- File view selection => directories render listing, text-like resources render editor, and other file types render preview; CSV defaults to preview unless `edit=true`.
- Request cancelation => file view aborts stale fetches on route change or unmount and treats aborts as non-fatal state transitions.
- JWT lifecycle => token parse updates cookie, localStorage, auth store, locale, and logout timer from token expiry.
- Upload switching => client chooses tus only for supported blob uploads with server tus settings; all other creation paths use `/api/resources`.
- Boot contract => frontend constants and TS types must stay aligned with `http/static.go` injected keys and backend JSON payload shapes.
# @Perf_Scale
- Code loading => editor and preview are async-loaded to reduce initial bundle cost for listing-centric sessions.
- Retry behavior => tus uploads use bounded exponential backoff based on server-provided retry count.
- UI continuity => file store rebinds selection by item URL after refresh to reduce unnecessary reselection churn.
# @Checklist
- New boot key => update backend template injection, frontend constants, and relevant TS declarations together.
- New route => define title key, auth or admin guard, and layout or error behavior in the same change set.
- API payload change => update frontend types, adapters, and view assumptions before merging.
- Auth flow change => verify JSON auth, proxy auth, noauth, and custom logout interactions.
- Upload UX change => keep frontend strategy switching aligned with backend resource and tus semantics.
