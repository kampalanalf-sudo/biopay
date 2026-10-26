# BioPay rebuild handoff

## Source and current stopping point

This rebuild continues the work described in the shared Claude conversation:

- Source chat: https://claude.ai/share/3937a96f-4198-49fa-878d-d28a35ae69a9
- The old BioPay server and application source were reported permanently unavailable, with no full backup.
- The latest platform choice is **Vercel + Supabase**, superseding the earlier GitHub Pages choice.

This is a fresh continuation, not a restoration of the lost source tree. No passwords, tokens, database dumps, or credentials from the shared chat are carried into this project.

## Product scope carried forward

- BioPay pilot in Uganda; keep the separate police case-management project out of this work.
- Payment/transaction interface, with a real provider to be selected and tested in sandbox before any live use.
- WebAuthn/passkeys for device-bound account/payment approval. A web app must not receive raw phone fingerprint images or templates; the device authenticator may use fingerprint, face, or PIN locally.
- Optional, separately consented rPPG/eye-measurement research. It is not an authentication or payment-decision signal. No simulated readings or silent fallbacks.
- BLE device registry, explicit scanning, newest-discovery-first ordering, and the requested two-tone discovery cue. Scanning is currently off; only the local cue test is active after a user action.
- Auditability, security, and clear pilot-readiness boundaries.

## Current artifact and cloud state

- `index.html` is a dependency-free responsive UI for Overview, Payments, Merchants, BLE Devices, Activity, Security, and Settings. It shows empty states and does not call Supabase.
- Vercel project `biopay-pilot` exists (ID `prj_NQeJIUnGaWmAdP0qyPb72XYm7TJp`). The plain environment variable `SUPABASE_URL` is configured for production, preview, and development. No source repository is linked and no deployment has been published. The GitHub connector returned `unsupported`, so no repository was created or pushed; the user needs to create a repo and link it in Vercel (Project Settings → Git), or provide a repo after creating it.
- The active Supabase project is **Biopay** (`eu-west-1`, ref `srtaysgfnopjcuosebbb`), status **ACTIVE_HEALTHY**, URL `https://srtaysgfnopjcuosebbb.supabase.co`. Its migration list is empty. This rebuild has not applied schema/RLS changes or performed database writes, and no API key has been added to the Vercel project or source code. The URL is present in `BIOPAY_CONFIG.supabaseUrl` and `.env.example`.
- The former inactive `My Project` (`hxfhayyznpvxdrwaftse`) was deleted after the user explicitly chose deletion following an irreversible-action warning. It was not inspected first; do not attempt to restore or recreate it.
- `BIOPAY_CONFIG.apiBaseUrl` remains empty. No provider is selected, no provider credentials have been added, and no real-money movement is enabled.

## Not implemented

- Database schema, Row Level Security policies, migrations, Supabase auth/client, or Vercel server-side API functions.
- User sessions, roles, server-side WebAuthn verification, provider integration, settlement/reconciliation, and durable audit events.
- Live Bluetooth scanning/device persistence and the event hook that would play the two-tone cue on a real discovery.
- Camera capture, rPPG/eye measurement, consent records, and retention enforcement.

These boundaries are intentional. Do not treat a static Vercel project as a production payment system.

## Credential hygiene

The shared chat contains an admin password and bearer token from the previous deployment. Treat any reused credential as compromised. Do not carry it into this rebuild; generate fresh secrets in their intended secure environment. The old server was reported lost, so the previous token should not be used. Never add provider credentials, database passwords, or Supabase secret/service-role keys to source control or chat.
