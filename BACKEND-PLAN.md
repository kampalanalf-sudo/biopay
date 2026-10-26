# BioPay backend boundary and pilot checklist

## Current status

The UI is a static preview on a Vercel project. The selected stack is **Vercel + Supabase**. Vercel's plain `SUPABASE_URL` environment variable is configured for production, preview, and development, and the local UI has the public project URL in `BIOPAY_CONFIG.supabaseUrl`. This is URL configuration only: the app has no Supabase client or key configured, no schema/RLS changes or migrations applied by this rebuild, and no Vercel API function. The UI does not make Supabase requests.

The configured Supabase project is **Biopay** (`srtaysgfnopjcuosebbb`, `eu-west-1`), status **ACTIVE_HEALTHY**. Its migration history is empty. This rebuild has not applied schema/RLS changes or performed database writes. No account data, payment, passkey, camera measurement, or BLE device is fabricated or activated.

## Proposed trust boundaries

- **Vercel static frontend:** display and collect user intent only; never hold privileged keys or decide that a payment settled.
- **Vercel server-side functions:** authenticate and authorize requests; verify WebAuthn assertions; validate payment intents; handle signed provider callbacks; apply rate limits, idempotency, and audit logic. Keep privileged secrets in Vercel server-only environment variables.
- **Supabase:** managed Postgres and approved authentication/storage services. Enable Row Level Security on every table reachable by client APIs; grant only the minimum required privileges. Do not expose a service-role/secret key to the browser. Apply reviewed, version-controlled migrations only after the schema is approved.
- **Payment provider:** not selected. Use provider-neutral transaction states and a provider sandbox until formal approval.

The former inactive Supabase project `My Project` was deleted following the user's explicit choice after an irreversible-action warning. Do not attempt to restore it. The current active project is the user-supplied `Biopay` project above.

## Passkeys, not fingerprint capture

A website must not collect a phone's raw fingerprint image or template. WebAuthn/passkeys let the device's operating system authenticate locally (fingerprint, face, or device PIN, depending on device settings) and return a cryptographic assertion. BioPay should store a credential public key and metadata, not raw biometric data.

Use short-lived, server-generated, single-use challenges. Bind each challenge to the user/session, expected origin, relying-party ID, and intended operation; verify the assertion on the server; reject replay; and apply the chosen WebAuthn library's current counter/credential guidance. Require a fresh server-verified passkey assertion for sensitive payment approval. Do not imply that a successful device prompt alone means a transaction was approved or settled.

## Optional rPPG / eye research

Treat camera-derived measurements as a separate, opt-in research study, not as identity proof or a payment/fraud decision signal. Before implementing capture:

- Document the exact study purpose, participant notice, consent and withdrawal process, access, storage, retention, deletion, and incident response.
- Complete a privacy/security review with the pilot sponsors and qualified local counsel.
- Do not silently fall back to simulated readings; if real capture fails, show the actual failure and stop the measurement flow.
- Avoid storing raw frames. Minimize derived data, encrypt it in transit and at rest, strictly restrict access, and set a short, documented retention period.
- Keep camera access off by default and visibly active only after separate informed opt-in. It must never authorize, deny, rank, or risk-score a payment.

The current preview does not request camera access and does not generate synthetic biometric values.

## Payment-provider sequence

1. Select a licensed provider and use its official sandbox first.
2. Keep credentials in Vercel server-side environment variables or another managed secret store; never place them in browser code or repository history.
3. Create payment intents server-side; validate identity, merchant status, amount, currency, authorization, limits, and idempotency.
4. Verify signed provider callbacks server-side before changing ledger state. Never trust a browser redirect as proof of settlement.
5. Record immutable audit facts; reconcile provider reports; test duplicate callbacks, timeouts, reversals, and recovery.
6. Enable live credentials only after formal pilot approval, operational controls, reconciliation, incident response, and independent security review.

## Minimal API contract

- `GET /api/health` — health-only response; no personal data. The current UI's API check remains inactive because `BIOPAY_CONFIG.apiBaseUrl` is blank and no route exists.
- Preserve earlier route names only as migration notes, not as permission to recreate insecure behavior: `/api/login`, `/api/me`, `/api/users`, `/api/chat`, `/api/transfer`, `/api/vendors`, and `/api/devices` were present in the previous deployment. Reimplement only routes still required, with authorization, validation, auditing, rate limits, and tests.
- Prior WebAuthn route names were `POST /api/webauthn/register-options`, `POST /api/webauthn/register`, `POST /api/webauthn/login-options`, `POST /api/webauthn/login`, plus a credential-removal endpoint. Rebuild against a maintained library and test the exact production origin/RP ID; require single-use challenges, expiry, session binding, and replay protection.
- Payment endpoints should return provider-neutral transaction states. Only a verified callback and atomic ledger operation can transition a payment to settled.
- BLE registry events must authenticate the sender, validate device identifiers, avoid automatic account creation/linking, and record who authorized enrollment. The two-tone sound stays a client-side notification after an explicitly permitted discovery event.

## Recommended milestones

1. Design the minimal schema, access roles, audit model, and retention policy. Review SQL migrations and RLS policies before applying them.
2. Decide whether Supabase Auth is used for identity or the application API owns sessions; implement server-side passkey verification with tests.
3. Add merchant/device workflows with explicit authorization, audit logs, and newest-discovery-first ordering.
4. Select a payment provider, implement sandbox requests and signed callbacks, and complete reconciliation/security reviews.
5. Only after formal approval, consider production payment credentials and any separately consented camera study.
