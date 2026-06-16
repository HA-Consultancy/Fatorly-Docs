# Docs Redesign & Rebuild — Progress / Handoff (2026-06-16)

Branch: `docs/redesign-rebuild` (9 commits, off `main`). Spec + plan in
`docs/superpowers/`. Source of truth for per-page status: `TRACKER.md`.

## Done (44/50 pages, EN + AR, committed)
- **Phase 0 scaffold:** `docs.json` restructured to the live-verified IA
  (Sales/Purchases/Self-Billing split, Reports incl. Submission Failures, Files
  under Master Data, Company Settings hub, Account, Multi-Company), list pages
  relocated, `TRACKER.md` reset to the 50-page set.
- **Invoice Editor:** Invoice **Field Reference** (every field, all 9 sections)
  + rewritten **Create/Edit** narrative.
- **Sales / Purchases / Self-Billing** (5 list pages, live-accurate columns/actions).
- **Master Data** (Items w/ default tax+classification, Customers, Vendors,
  Payment Methods) + **Files** library.
- **Getting Started** (index, register, login, forgot, reset, verify) +
  **Dashboard** + **Multi-Company** + **Account** (Profile, What's New).
- **Reports** (Overview, Running a Report, Downloads, Scheduled, Submission Failures).
- **Company Settings hub** (12 tab pages).
- **Developer guides** (Overview, Authentication, Quickstart, Idempotency, Errors).
- EN screenshots captured for all of the above except items noted below; AR pages
  currently reuse EN screenshots.

## Remaining (6 pages) — BLOCKED, need a working session or the API spec
These still hold pre-redesign content (nav not broken):
1. **#17 View an Invoice** — needs an authenticated screenshot of a submitted
   invoice + prose refresh.
2. **#7 Onboarding** and **#8 MFA Setup** — authenticated screenshots + refresh.
3. **#43 Auditor Company Selector** and **#44 Auditing a Company** — need an
   **auditor-role** login to inspect/screenshot.
4. **#50 Developers → API Reference (generated)** — vendor `api-reference/openapi.json`
   and add `{ "group": "API Reference", "openapi": "api-reference/openapi.json" }`
   to the Developers nav group. The spec is JWT-gated at
   `app.fatorly.com/developer/api-reference`.

## Blockers (need user help)
- **Authenticated browser access:** fresh automated login is **Turnstile-blocked**.
  The earlier screenshots worked because the session was logged in manually at the
  start; once it expired, re-login no longer passes Turnstile headlessly. Needed for
  the remaining authenticated screenshots: View Invoice, Onboarding, MFA, the
  Company-Settings sub-tabs (branding/configuration/number-series/team/usage/
  support/linking/leave/integrations/api-keys), and `running-a-report`.
- **OpenAPI spec** for the native generated API reference (gated).

## Deferred polish
- **AR screenshots:** AR pages reuse EN screenshots; capture Arabic-UI shots in a
  later pass (set the app language toggle to العربية).
- **Company-Settings sub-tab screenshots** and **running-a-report** screenshot:
  pages are written, images pending (no `<img>` left dangling).
- **Offboarding data-retention figure** in `settings/leave-fatorly.mdx` is marked to
  verify — fill the real retention period.
- **Cleanup:** remove the orphaned `*/developers/api-reference.mdx` once the generated
  reference is wired; remove stale pre-redesign screenshots no longer referenced.
- **Build check:** run `mint dev` and `mint broken-links` on an **LTS Node** (this
  environment had Node 25, unsupported) to validate rendering and links.

## How to resume the screenshot capture
Log in manually in the Playwright browser (Turnstile passes interactively), select
"Hamwi Computer Software", then run the route→path screenshot loop (see the two
`browser_run_code_unsafe` batches in the session) for the pending routes. AR shots:
toggle the app to العربية first.
