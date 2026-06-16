# Fatorly Documentation Redesign & Rebuild — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rebuild the bilingual (EN/AR) Fatorly Mintlify docs to match the redesigned app — restructured navigation, fresh screenshots on every page, an exhaustive Invoice Field Reference, and a native interactive API reference.

**Architecture:** Documentation-only changes in `~/repos/Fatorly-Docs`. Restructure `docs.json` to mirror the new app navigation, then rebuild each page via a Standard Page Rebuild Procedure (write EN MDX → write AR MDX → capture live screenshots → verify Mintlify build/links → update `TRACKER.md` → commit). The Developers section switches from a launch-link to native Mintlify OpenAPI pages generated from a vendored `openapi.json`.

**Tech Stack:** Mintlify (`mint` CLI), MDX, Playwright (screenshots via the prod test login), OpenAPI 3.

**Spec:** `docs/superpowers/specs/2026-06-16-fatorly-docs-redesign-rebuild-design.md`

---

## Conventions & Source Facts (read before starting)

- **Repo:** `~/repos/Fatorly-Docs`. Working directory for all `git`/`mint` commands.
- **App under test:** `https://app.fatorly.com`. Test login lives in E-Invoice.PEPPOL memory `project_test_accounts_seeding` (web login `abod.h95+fatorly01@gmail.com`; password in the referenced creds file). Read that memory file to get the password before screenshotting.
- **Brand:** white-label **Fatorly**. Never hardcode "HA Consultancy" (memory `project_brand`).
- **Field inventory source of truth:** the spec's "Field inventory" section (derived from `~/repos/E-Invoice.PEPPOL/EInvoice.Platform.Web/src/pages/InvoiceFormPage.tsx`, its child components, and `types/invoice.ts`).
- **Page frontmatter:** `title` + `description` only (see existing pages).
- **Image paths:** `/images/{en|ar}/{section}/{page}-{N}.png`. Wrap in `<Frame>`.
- **Components available:** `<Frame>`, `<Steps>/<Step>`, `<Note>`, `<Warning>`, `<Tip>`, `<Card>/<CardGroup>`, `<Tabs>/<Tab>`. Mintlify auto-handles AR RTL.
- **Tracker:** `TRACKER.md` — a row is *done* only when EN, AR, Shots EN, Shots AR all ✅.

### Standard Page Rebuild Procedure (SPRP)

Every page-rebuild task below means: **apply the SPRP** with that task's page-specific spec (route, sections, fields, screenshots, callouts). The SPRP steps are:

1. **Write EN MDX** at `en/<section>/<page>.mdx` covering the listed sections/fields, with `<Frame>` placeholders for each listed screenshot. Cross-link related pages with `/en/...` links.
2. **Write AR MDX** at `ar/<section>/<page>.mdx` — a faithful RTL translation of the EN page, same structure, `/ar/...` links, AR image paths.
3. **Capture screenshots** via the Screenshot Procedure (below) for each listed shot, into `images/en/<section>/` and `images/ar/<section>/`.
4. **Verify** (Build & Link Verification, below).
5. **Update `TRACKER.md`** row: set EN, AR, Shots EN, Shots AR to ✅.
6. **Commit** (one commit per page or per small group): `git add en/<section>/<page>.mdx ar/<section>/<page>.mdx images/en/<section>/* images/ar/<section>/* TRACKER.md && git commit -m "docs: rebuild <Section> — <Page> (EN/AR + shots)"`.

### Screenshot Procedure (Playwright via Playwright MCP)

Used by step 3 of the SPRP. Per screenshot:

1. If not logged in: `browser_navigate` to `https://app.fatorly.com/login`, `browser_fill_form` with the test creds, submit. Select the demo company if prompted.
2. `browser_resize` to `1440x900` (desktop, light mode).
3. `browser_navigate` to the page-specific route.
4. `browser_wait_for` the main content, dismiss any toasts/tours.
5. `browser_take_screenshot` saving to `images/en/<section>/<page>-<N>.png` (full-page or element as the task specifies).
6. For AR: switch the **app** UI language to Arabic (header language toggle), re-navigate, screenshot to `images/ar/<section>/<page>-<N>.png`. If the UI is identical except direction, still capture the AR shot so RTL is shown.
7. Redact secrets where noted (e.g. MFA QR/secret, API keys, TRNs if required).

### Build & Link Verification

Run from `~/repos/Fatorly-Docs`:

- `mint broken-links` — Expected: "No broken links found" (or only known external ones). Fix any internal broken links.
- `mint dev` (start, confirm it compiles without MDX parse errors, then stop) — Expected: server starts, no MDX/compile errors printed for the changed pages.

If `mint` is not installed: `npm i -g mint` first.

---

## Phase 0 — Toolchain & IA Scaffold

### Task 0.1: Install/verify the Mintlify CLI

**Files:** none.

- [ ] **Step 1: Check for mint**

Run: `mint --version`
Expected: a version prints. If "command not found", continue to Step 2.

- [ ] **Step 2: Install mint if missing**

Run: `npm i -g mint && mint --version`
Expected: version prints.

- [ ] **Step 3: Baseline build of the current docs**

Run: `cd ~/repos/Fatorly-Docs && mint broken-links`
Expected: it runs (record current link state as the baseline).

### Task 0.2: Read the password and verify app access

**Files:** none (verification only).

- [ ] **Step 1: Read test creds**

Read the file referenced by memory `project_test_accounts_seeding` to get the test-login password.

- [ ] **Step 2: Verify login + capture the redesigned sidebar**

Using the Screenshot Procedure, log in and `browser_take_screenshot` the dashboard with the new left navigation visible to `images/en/home/dashboard-1.png` (also confirms the new IA against the live app).
Expected: logged in; the sidebar shows Sales / Purchases / Self-Billing / Reports / Master Data / Files / Settings as code-derived. **If the live grouping differs from the spec IA, note the difference and adjust Task 0.3 before committing it.**

### Task 0.3: Rewrite `docs.json` to the new IA

**Files:** Modify `docs.json`.

- [ ] **Step 1: Replace the EN `groups` array** with the new IA (order exactly):

```
Getting Started: en/index, en/getting-started/register, .../login, .../forgot-password,
  .../reset-password, .../verify-email, .../onboarding, .../mfa-setup
Dashboard: en/home/dashboard, en/home/whats-new
Sales: en/sales/sales-invoices, en/sales/sales-credit-notes
Purchases: en/purchases/purchase-invoices, en/purchases/purchase-credit-notes
Self-Billing: en/self-billing/self-billing
The Invoice Editor: en/invoicing/create-invoice, en/invoicing/invoice-field-reference, en/invoicing/view-invoice
Reports: en/reports/overview, en/reports/running-a-report, en/reports/downloads, en/reports/scheduled-reports
Master Data: en/master-data/items, .../customers, .../vendors, .../payment-methods
Files: en/files/files-library
Company Settings: en/settings/company-profile, en/settings/branding-documents,
  en/settings/company-configuration, en/settings/number-series, en/settings/team-auditors,
  en/settings/usage, en/settings/support-access, en/settings/linking-companies,
  en/settings/leave-fatorly, en/settings/integrations, en/settings/api-keys, en/settings/change-password
Multi-Company: en/multi-company/switching-companies
Auditor: en/auditor/company-selector, en/auditor/auditing-a-company
Developers: en/developers/overview, en/developers/authentication, en/developers/quickstart,
  en/developers/idempotency, en/developers/errors
```

(The Developers **API Reference** generated pages are wired in Phase 8 — leave a placeholder group entry to be replaced there.)

- [ ] **Step 2: Replace the AR `groups` array** with the same page list under `ar/...` paths, with localized group titles:

```
ابدأ من هنا · الرئيسية · المبيعات · المشتريات · الفوترة الذاتية · محرّر الفواتير ·
التقارير · البيانات الأساسية · الملفات · إعدادات الشركة · تعدّد الشركات · المدقق · المطوّرون
```

- [ ] **Step 3: Validate JSON**

Run: `cd ~/repos/Fatorly-Docs && python3 -c "import json;json.load(open('docs.json'));print('ok')"`
Expected: `ok`.

- [ ] **Step 4: Commit**

```bash
git add docs.json && git commit -m "docs: restructure navigation IA to match redesigned app"
```

### Task 0.4: Create the file/folder skeleton

**Files:** Create new section dirs and move/rename pages.

- [ ] **Step 1: Create directories**

Run:
```bash
cd ~/repos/Fatorly-Docs
for L in en ar; do mkdir -p $L/sales $L/purchases $L/self-billing $L/reports $L/files $L/multi-company $L/developers; \
  mkdir -p images/$L/sales images/$L/purchases images/$L/self-billing images/$L/reports images/$L/files images/$L/multi-company images/$L/developers; done
echo done
```
Expected: `done`.

- [ ] **Step 2: Move existing invoicing list pages into Sales/Purchases/Self-Billing**

Run:
```bash
cd ~/repos/Fatorly-Docs
for L in en ar; do
  git mv $L/invoicing/sales-invoices.mdx $L/sales/sales-invoices.mdx
  git mv $L/invoicing/sales-credit-notes.mdx $L/sales/sales-credit-notes.mdx
  git mv $L/invoicing/purchase-invoices.mdx $L/purchases/purchase-invoices.mdx
  git mv $L/invoicing/purchase-credit-notes.mdx $L/purchases/purchase-credit-notes.mdx
  git mv $L/invoicing/self-billing.mdx $L/self-billing/self-billing.mdx
done
echo done
```
Expected: `done` (create-invoice.mdx and view-invoice.mdx stay under `invoicing/`).

- [ ] **Step 3: Fix internal links broken by the moves**

Run: `cd ~/repos/Fatorly-Docs && grep -rn "invoicing/sales-invoices\|invoicing/purchase\|invoicing/self-billing" en ar || echo "no stale links"`
For each hit, update the path to the new location (`/sales/...`, `/purchases/...`, `/self-billing/...`) with Edit.

- [ ] **Step 4: Reset `TRACKER.md`** to the new page set (one row per page in the new IA), all EN/AR/Shots columns ⬜ except rows you have not changed yet. Use the IA list from Task 0.3 as the row set.

- [ ] **Step 5: Verify + commit**

Run: `mint broken-links`
Expected: no *new* broken links from the moves (generated/not-yet-created pages may warn — acceptable until their phase).
```bash
git add -A && git commit -m "docs: scaffold new section folders + relocate list pages + reset tracker"
```

---

## Phase 1 — The Invoice Editor (highest value)

### Task 1.1: Invoice Field Reference page (EN)

**Files:** Create `en/invoicing/invoice-field-reference.mdx`.

This page is the exhaustive reference. Build it with one `##` section + table per group below, copying the field rows from the spec's Field Inventory verbatim. Lead with a short intro and a `<Note>` that the narrative walkthrough is on [Create / Edit an Invoice](/en/invoicing/create-invoice).

- [ ] **Step 1: Write the page** with these sections, each a Markdown table `| Field | Required | Control | Allowed / Default | Notes |`:
  1. Document header — Invoice #, Currency, Issue Date, Due Date.
  2. Counterparty & credit-note reference — Customer/Vendor, Referenced Invoice, Preceding Invoice Date.
  3. Line items — Item Name, Description, Quantity, Unit of Measure, Unit Price, Line allowances/charges, VAT Group, Exemption Reason, Line Net.
  4. Line item identifiers — Buyer's/Seller's/Standard item ID, Standard scheme ID.
  5. Line item classifications — Code, List/scheme (listID), Version.
  6. Document Charges & Allowances — Allowance/Charge, Amount, Reason, Code, Tax Category, Tax Rate, Exemption Reason.
  7. References & Delivery — Buyer reference; Document references (Type/Value/Issue date/Scheme id); Attachments (Document ID, library file / upload / external URI); Delivery (date, Incoterms, street, address line, city, state/emirate, country).
  8. Payment & notes — Payment Method, Payment Terms, Customer Notes.
  9. Summary / Totals — computed fields list.

- [ ] **Step 2: Add validation callouts** (`<Warning>`/`<Note>`):
  - Exemption reason required when tax category = `E` (line and document A/C).
  - Reverse-charge (`AE`) lines require a Standard item ID with scheme `0160`.
  - Classification scheme (listID) must be a valid UNTDID-7143 code (ibr-cl-13).
  - Tax Invoice requires a counterparty with a VAT TRN; Commercial Invoice does not.

- [ ] **Step 3: Verify**

Run: `mint broken-links` then start `mint dev` and confirm the page compiles.
Expected: no MDX errors; tables render.

- [ ] **Step 4: Commit**

```bash
git add en/invoicing/invoice-field-reference.mdx && git commit -m "docs: add Invoice Field Reference (EN)"
```

### Task 1.2: Invoice Field Reference page (AR)

**Files:** Create `ar/invoicing/invoice-field-reference.mdx`.

- [ ] **Step 1:** Translate Task 1.1's page to Arabic (RTL), same 9 sections/tables and the four callouts, `/ar/...` cross-link. Keep field *codes* (S, E, AE, `0160`, listID values, Incoterms) in Latin script; translate descriptive text.
- [ ] **Step 2: Verify** `mint dev` compiles the AR page.
- [ ] **Step 3: Commit**: `git add ar/invoicing/invoice-field-reference.mdx && git commit -m "docs: add Invoice Field Reference (AR)"`

### Task 1.3: Capture Invoice Field Reference screenshots (optional support shots)

**Files:** `images/{en,ar}/invoicing/field-reference-*.png` (only if the page benefits from a labelled-sections screenshot).

- [ ] **Step 1:** Using the Screenshot Procedure at route `/invoices/new?category=TaxInvoice&direction=Outbound`, capture the expanded editor showing each major section (Charges & Allowances, References & Delivery, a line's identifiers/classifications expanded). Save EN + AR.
- [ ] **Step 2:** Embed the shots in the reference page where they aid orientation. Re-verify, update tracker, commit.

### Task 1.4: Rebuild "Create / Edit an Invoice" (narrative)

**Page spec for SPRP:**
- Route(s): `/invoices/new?category=TaxInvoice&direction=Outbound` (and note credit-note/commercial variants).
- Sections to cover (narrative, with the new collapsible UI): start a document; header (invoice #, currency, dates); pick counterparty; add line items (catalog prefill of name/UoM/price/**default tax + classification**); set VAT group (defaults to 5%); **when to set exemption reason** (category E); **Charges & Allowances** (doc + line); **References & Delivery** (refs, attachments, delivery + Incoterms); **item identifiers & classification** per line; payment & notes; live Totals; Save as Draft → link to View.
- Each optional section is **collapsible with a filled-count badge** and auto-expands on a validation error — describe this.
- Cross-link the [Invoice Field Reference](/en/invoicing/invoice-field-reference) for full field detail.
- Screenshots (EN+AR): (1) empty editor with new layout, (2) a line with VAT group + exemption reason, (3) Charges & Allowances expanded, (4) References & Delivery expanded, (5) line identifiers/classification expanded, (6) Totals panel.

- [ ] **Step 1–6:** Apply the SPRP with the above spec (replace the current `en/` and `ar/invoicing/create-invoice.mdx`). Commit.

### Task 1.5: Rebuild "View an Invoice"

**Page spec for SPRP:**
- Route: `/invoices/:id` (use a representative submitted invoice).
- Cover: the redesigned read-only view; status/timeline; totals; how to submit/download; where attachments/references/delivery appear.
- Screenshots (EN+AR): the view page; the submission/status area.

- [ ] **Step 1–6:** Apply the SPRP. Commit.

---

## Phase 2 — Sales / Purchases / Self-Billing

Apply the SPRP to each. Each is a **list page**: cover the list/filters, the "New …" actions, row actions (view/edit/duplicate), and statuses. Screenshots: the list page (EN+AR), and the "New" menu where relevant.

- [ ] **Task 2.1:** `sales/sales-invoices` — route `/sales/invoices`. New Tax Invoice / New Commercial Invoice actions.
- [ ] **Task 2.2:** `sales/sales-credit-notes` — route `/sales/credit-notes`. New Credit Note; preceding-invoice reference.
- [ ] **Task 2.3:** `purchases/purchase-invoices` — route `/purchases/invoices`. Inbound documents.
- [ ] **Task 2.4:** `purchases/purchase-credit-notes` — route `/purchases/credit-notes`.
- [ ] **Task 2.5:** `self-billing/self-billing` — route `/self-billing`. Self-billed invoices & credit notes; how direction/role differs.

(Each task = full SPRP + commit.)

---

## Phase 3 — Reports & Files

- [ ] **Task 3.1:** `reports/overview` — route `/reports`. The reports hub: the catalog of curated reports, how to open one. Screenshots EN+AR.
- [ ] **Task 3.2:** `reports/running-a-report` — route `/reports/:reportKey`. Running/filtering a report; export to CSV/xlsx/PDF.
- [ ] **Task 3.3:** `reports/downloads` — route `/reports/downloads`. The generated-exports list.
- [ ] **Task 3.4:** `reports/scheduled-reports` — route `/reports/schedules`. Creating/managing scheduled reports.
- [ ] **Task 3.5:** `files/files-library` — route `/files`. The file library: upload, browse, the public-link behaviour, and **how files are attached to invoices** (cross-link the editor's References & Delivery → Attachments). Screenshots EN+AR.

(Each task = full SPRP + commit. Verify on the live app that Reports/Files exist for the test account; if a feature is gated/absent, note it and document from the spec, deferring screenshots.)

---

## Phase 4 — Master Data refresh

Apply the SPRP. Refresh content + screenshots to the new design; add the **new item-master fields** noted below.

- [ ] **Task 4.1:** `master-data/items` — route `/items` (+ `/items/new`). Document the **default tax category** and **default classification** fields and that they **prefill invoice lines**. Screenshots: list + form (EN+AR).
- [ ] **Task 4.2:** `master-data/customers` — route `/customers` (+ form). TRN, address dropdowns where present.
- [ ] **Task 4.3:** `master-data/vendors` — route `/vendors` (+ form).
- [ ] **Task 4.4:** `master-data/payment-methods` — route `/payment-methods`.

---

## Phase 5 — Company Settings hub + Multi-Company + Auditor

The app consolidated Settings into a tabbed **Company Profile** page at `/settings/company-profile?tab=...`. Each docs page below maps to one tab (screenshot that tab). Apply the SPRP.

- [ ] **Task 5.1:** `settings/company-profile` — tab `general`. Company identity, TRN, address.
- [ ] **Task 5.2:** `settings/branding-documents` — tab `branding`. Logo, document branding.
- [ ] **Task 5.3:** `settings/company-configuration` — tab `configuration`. Company-level config/preferences.
- [ ] **Task 5.4:** `settings/number-series` — tab `numberseries`. (Migrate existing content; refresh shots.)
- [ ] **Task 5.5:** `settings/team-auditors` — tab `team`. Team members & company auditors. (Merges legacy "Company Auditors".)
- [ ] **Task 5.6:** `settings/usage` — tab `usage`. Usage & statistics / quota.
- [ ] **Task 5.7:** `settings/support-access` — tab `support`. Approving temporary support-staff access.
- [ ] **Task 5.8:** `settings/linking-companies` — tab `companies` (`/settings/my-companies`). Linking/managing multiple companies.
- [ ] **Task 5.9:** `settings/leave-fatorly` — tab `offboarding`. The customer-initiated leave/offboarding flow. **Verify it is live for the test account; if not, document from the offboarding spec and defer screenshots.**
- [ ] **Task 5.10:** `settings/integrations` — route `/settings/integrations`. (Migrate; refresh QuickBooks shots.)
- [ ] **Task 5.11:** `settings/api-keys` — route `/settings/integration/api-keys`. (Migrate; refresh.)
- [ ] **Task 5.12:** `settings/change-password` — route `/settings/change-password`. (Migrate; refresh.)
- [ ] **Task 5.13:** `multi-company/switching-companies` — route `/select-company`. The company switcher (replaces `/auditor/companies`).
- [ ] **Task 5.14:** `auditor/company-selector` — auditor read-only company selection. (Migrate; refresh.)
- [ ] **Task 5.15:** `auditor/auditing-a-company` — auditor read-only views. (Migrate; refresh.)

---

## Phase 6 — Getting Started + Dashboard refresh

Apply the SPRP to refresh content + screenshots to the new design.

- [ ] **Task 6.1:** `index` (Introduction) — overview of the redesigned app; update the section map.
- [ ] **Task 6.2:** `getting-started/register`, `login`, `forgot-password`, `reset-password`, `verify-email` — refresh shots to the new auth screens.
- [ ] **Task 6.3:** `getting-started/onboarding` — FTA linking wizard; refresh.
- [ ] **Task 6.4:** `getting-started/mfa-setup` — refresh (redact QR/secret).
- [ ] **Task 6.5:** `home/dashboard` — new dashboard + sidebar; refresh (reuse the Task 0.2 shot).
- [ ] **Task 6.6:** `home/whats-new` — refresh.

(Group related small pages into single commits where sensible.)

---

## Phase 7 — Developers: native API reference + guides

### Task 7.1: Obtain & vendor the OpenAPI spec

**Files:** Create `api-reference/openapi.json`.

- [ ] **Step 1:** Obtain the v1 OpenAPI JSON. It is JWT-gated at `app.fatorly.com/developer/api-reference`. Options, try in order: (a) while logged in via Playwright, `browser_network_requests` to find the spec URL the Scalar viewer fetches, then fetch it with the session; (b) ask the user to export/paste `openapi.json` if it cannot be retrieved headlessly. **Blocker if neither works — surface to the user.**
- [ ] **Step 2:** Save the spec to `api-reference/openapi.json`. Validate: `python3 -c "import json;d=json.load(open('api-reference/openapi.json'));print(d['info']['title'], len(d['paths']),'paths')"`. Expected: title + path count print.
- [ ] **Step 3: Commit**: `git add api-reference/openapi.json && git commit -m "docs: vendor v1 OpenAPI spec for native API reference"`

### Task 7.2: Wire native API reference into `docs.json`

**Files:** Modify `docs.json`.

- [ ] **Step 1:** Replace the Developers placeholder with the guide pages **and** the generated reference. Per Mintlify, add the spec under the Developers group/tab so endpoints auto-generate:

```json
{ "group": "API Reference", "openapi": "api-reference/openapi.json" }
```

Keep the guide pages (`en/developers/overview`, `authentication`, `quickstart`, `idempotency`, `errors`) as explicit entries before it. (Generated endpoint pages are EN-only.)

- [ ] **Step 2:** Validate JSON (`python3 -c "import json;json.load(open('docs.json'));print('ok')"`) and run `mint dev`; confirm the interactive endpoint pages + playground generate without errors.
- [ ] **Step 3: Commit**: `git add docs.json && git commit -m "docs: generate native interactive API reference from openapi.json"`

### Task 7.3: Write the Developer guide pages (EN + AR)

**Files:** Create `en/developers/{overview,authentication,quickstart,idempotency,errors}.mdx` and AR mirrors.

- [ ] **Step 1:** Confirm guide scope against the live spec from Task 7.1 (endpoints, auth scheme, headers). If the spec exposes webhooks/status callbacks, add `developers/webhooks` to both `docs.json` and this task; otherwise omit.
- [ ] **Step 2:** Write each EN guide:
  - `overview` — what the API is, base URL, versioning (v1), how it relates to the app.
  - `authentication` — creating an API key (cross-link `/en/settings/api-keys`), how to send it.
  - `quickstart` — create your first invoice end-to-end, using `V1CreateInvoiceRequest` including the new `references[]`, `delivery`, `allowancesCharges[]`, line `classifications[]`, item ids, and `exemptionReasonCode`. Use a real request/response example consistent with the spec.
  - `idempotency` — the `Idempotency-Key` header behaviour.
  - `errors` — error shape and common codes.
- [ ] **Step 3:** Write the AR mirrors (RTL); keep field names/JSON in Latin script.
- [ ] **Step 4: Verify** `mint broken-links` + `mint dev` compile.
- [ ] **Step 5:** Update `TRACKER.md` (Developers rows) and **commit**: `git add en/developers ar/developers TRACKER.md && git commit -m "docs: add Developer guides (EN/AR)"`

### Task 7.4: Remove the legacy launch-link page

**Files:** Delete `en/developers/api-reference.mdx`, `ar/developers/api-reference.mdx` (if still present after Phase 0 didn't carry them).

- [ ] **Step 1:** `git rm` the old launch-link pages; ensure no nav entry references them.
- [ ] **Step 2:** `mint broken-links`; **commit**: `git commit -m "docs: drop legacy Scalar launch-link page"`

---

## Phase 8 — Final pass

### Task 8.1: Full link + build sweep

- [ ] **Step 1:** `mint broken-links` — Expected: clean. Fix any remaining.
- [ ] **Step 2:** Start `mint dev`, click through both EN and AR nav; confirm every group/page renders and AR is RTL.

### Task 8.2: Tracker reconciliation

- [ ] **Step 1:** Ensure every page in the new IA has a `TRACKER.md` row with EN/AR/Shots EN/Shots AR ✅. Set `Reviewed` per the user's review.
- [ ] **Step 2:** Update `README.md` "Structure" + the spec/plan pointers to the new IA. Commit.

### Task 8.3: Hand back for review/deploy

- [ ] **Step 1:** Summarize changed pages + any deferred items (gated features without screenshots, webhooks decision, attachment-upload verification) for the user.
- [ ] **Step 2:** Do not deploy; Mintlify deploy is the user's call (per repo conventions).

---

## Self-Review notes (gaps & deferrals carried from the spec)
- **Attachment upload** (Task 3.5 / 1.4): verify live before documenting the upload flow; fall back to URL-reference form if absent.
- **Leave/Offboarding** (Task 5.9): verify live; else document from offboarding spec, defer screenshots.
- **OpenAPI retrieval** (Task 7.1): possible blocker — JWT-gated; escalate to user if headless fetch fails.
- **Webhooks guide** (Task 7.3): include only if the v1 spec has webhooks.
- **Live IA verification** (Task 0.2): confirm the real sidebar grouping before locking `docs.json`.
