# BioPay pilot — Vercel + Supabase

Fresh rebuild of the BioPay pilot UI. The selected stack is **Vercel for the frontend/server-side functions** and **Supabase for managed data services**. See [`HANDOFF.md`](./HANDOFF.md) for the recovered project context and current decisions.

> **Current boundary:** the UI is static. It does not authenticate users, read/write Supabase data, create payments, capture camera measurements, or scan/enroll BLE devices. The Supabase project URL is configured in the source and in Vercel. No Supabase key has been added to this app or Vercel, and no API route or schema/migration has been installed. Empty states are intentional; no financial or biometric data is fabricated.

## Included

- Responsive dashboard: Overview, Payments, Merchants, BLE Devices, Activity, Security, and Settings
- Accessible navigation and a manually triggered API health-check control
- Empty states rather than sample accounts, balances, merchants, transactions, or biometrics
- Passkey guidance that keeps raw fingerprint/face data on the device authenticator
- Camera research and payment approval kept separate; camera access is currently disabled
- No third-party scripts, fonts, trackers, or runtime dependencies
- [`vercel.json`](./vercel.json) with baseline browser-security headers
- [`.env.example`](./.env.example) with the non-secret Supabase project URL only

## Run locally

```bash
python3 -m http.server 8080
```

Open `http://localhost:8080`. You can also preview `index.html` directly in the workspace viewer.

## Vercel

A Vercel project named `biopay-pilot` has been created. Its plain environment variable `SUPABASE_URL` is set to the configured Supabase project URL for **production, preview, and development**. The URL is public project metadata, not a credential.

No Git repository is linked and no Vercel deployment has been published yet. The GitHub connector is unsupported in this workspace, so I cannot create or push a repository directly. Create a private GitHub repository, upload these project files, then connect it to `biopay-pilot` in Vercel under **Project Settings → Git**. Vercel can then deploy the static site (no build step or package install is required). `SUPABASE_URL` is already configured for all Vercel environments. Keep the project private until an owner chooses otherwise and completes a review. Do not add payment credentials, database passwords, Supabase secret/service-role keys, participant records, or other secrets to the repository.

## Supabase project

The configured project is **Biopay**, ref `srtaysgfnopjcuosebbb`, region `eu-west-1`, status **ACTIVE_HEALTHY**. The source config and Vercel `SUPABASE_URL` point to `https://srtaysgfnopjcuosebbb.supabase.co`. The project's migration list is empty. This rebuild has not applied schema/RLS changes or performed database writes, and no API key has been added to the Vercel project or source code.

Before building any client-side Supabase features, review and enable Row Level Security for all exposed tables. Keep secret/service-role keys server-side only. No key is required for the current static preview, which makes no Supabase requests. The existing Vercel variable only establishes the server-side project endpoint for future trusted functions.

## API and integrations

`BIOPAY_CONFIG.apiBaseUrl` in `index.html` remains intentionally empty. The Check API control sends nothing until a trusted API route is implemented and configured. Authentication, passkey verification, transactions, audit events, BLE enrollment, and provider callbacks are not implemented.

Vercel server-side functions and Supabase can support those services after schema, RLS, and threat-model review. Payment-provider integration remains undecided. Use a provider's sandbox first, verify signed callbacks on the server, and do not enable real-money movement before security and operational approval. See [`BACKEND-PLAN.md`](./BACKEND-PLAN.md).

## Research and safety boundary

WebAuthn/passkeys allow the device's authenticator to use its local fingerprint, face recognition, or PIN without sending raw biometric templates to BioPay. Camera-based rPPG/eye research is separately opt-in, inactive in this build, and must never authorize or deny a payment. Do not simulate readings. BLE discovery remains off and does not auto-enroll devices.
