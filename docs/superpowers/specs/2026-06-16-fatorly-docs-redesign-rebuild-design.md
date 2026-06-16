---
title: Fatorly Documentation Redesign & Rebuild
date: 2026-06-16
status: Approved (pending user spec review)
repo: ~/repos/Fatorly-Docs (Mintlify, bilingual EN/AR)
---

# Fatorly Documentation Redesign & Rebuild — Design

## Context

The Fatorly web app (`app.fatorly.com`) has been **redesigned**: new visual
design for forms, a restructured left-hand navigation, new sections (Reports,
Files/Library), a consolidated tabbed **Company Profile** settings hub, and a
greatly expanded **invoice editor** that now exposes the full set of PINT-AE /
UBL fields (~40 fields across 9 sections).

The existing documentation (29 pages, EN+AR, in `~/repos/Fatorly-Docs`) was
authored against the *previous* app design and does not reflect any of this.
Every page's screenshots are now stale, the navigation no longer matches the
app, and the invoice guide covers only a fraction of the available fields.

This is a **full rebuild**: restructure the documentation information
architecture to mirror the redesigned app, rebuild every page with fresh
content and live screenshots in both languages, add an exhaustive **Invoice
Field Reference**, and replace the "launch link" API reference with a native,
interactive Mintlify API reference.

## Goals

- Documentation IA mirrors the redesigned app navigation 1:1.
- Every page rebuilt: updated prose + fresh light-mode screenshots, EN and AR.
- A dedicated **Invoice Field Reference** page documenting **every** invoice-form
  field in detail (grouped tables: Field · Required? · Type/Control · Allowed
  values · Notes), plus the narrative Create/Edit walkthrough.
- New sections documented: **Reports**, **Files/Library**, the **Company Profile
  hub** tabs (Configuration, Team & Auditors, Usage, Support Access,
  Linking/My Companies, Leave/Offboarding), and **Multi-Company** switching.
- **Developers** section rebuilt as a native Mintlify API reference
  (auto-generated interactive endpoint pages from `openapi.json`) plus
  hand-written guides.
- Both languages stay in sync; AR pages are RTL.

## Non-Goals (out of scope for this rebuild)

- **Platform Admin pages** (tenants, packages, add-ons, billing, offboarding
  *requests* queue, migrate-in) — operator/internal, not end-user.
- New app features or code changes. Docs only; we document what is LIVE in prod.
- A spec auto-sync pipeline for `openapi.json` (we vendor a snapshot now; an
  automated refresh script is a possible follow-up, not part of this rebuild).

## New Documentation Information Architecture

Both `en/` and `ar/` mirror this structure (AR group titles localized, RTL).
`docs.json` navigation groups, in order:

```
Getting Started      Introduction · Register · Login · Forgot Password ·
                     Reset Password · Verify Email · Onboarding (FTA linking) · MFA Setup
Dashboard            Dashboard · What's New
Sales                Sales Invoices · Sales Credit Notes
Purchases            Purchase Invoices · Purchase Credit Notes
Self-Billing         Self-Billing
The Invoice Editor   Create / Edit an Invoice  ·  Invoice Field Reference (NEW)  ·  View an Invoice
Reports              Reports Overview · Running a Report · Downloads · Scheduled Reports
Master Data          Items · Customers · Vendors · Payment Methods
Files                Files Library (+ attaching files to invoices)
Company Settings     Company Profile (General) · Branding & Documents ·
                     Company Configuration · Number Series · Team & Auditors ·
                     Usage & Statistics · Support Access · Linking / My Companies ·
                     Leave Fatorly (Offboarding) · Integrations · API Keys · Change Password
Multi-Company        Switching Companies (/select-company)
Auditor              Auditor Company Selector · Auditing a Company
Developers           Overview · Authentication · Quickstart · Idempotency · Errors ·
                     [Webhooks?] · API Reference (auto-generated)
```

### Changes from the current IA
- `Invoicing` (flat 7 pages) → split into **Sales**, **Purchases**,
  **Self-Billing**, and **The Invoice Editor**.
- New **Reports** group (Reports shipped 2026-06-11).
- New **Files** group (file library + attachment upload).
- `Settings` (6 flat pages) → **Company Settings** hub mirroring the app's
  tabbed Company Profile page, plus Integrations / API Keys / Change Password.
- New **Multi-Company** group (`/select-company`, replaces `/auditor/companies`).
- **Auditor** group retained (read-only views).
- **Developers** expanded from a single launch-link page to a full native API
  reference + guides.

### Route mapping notes (for screenshots / cross-links)
- Company selector moved: `/auditor/companies` → `/select-company`.
- Settings consolidated into `/settings/company-profile?tab=...`
  (General, Branding, Configuration, Number Series, Team, Usage, Support, Leave).
- Invoice create: `/invoices/new?category={TaxInvoice|CommercialInvoice}&direction={Outbound|Inbound}`.
- Invoice view: `/invoices/:id`; edit: `/invoices/:id/edit`.

## The Invoice Field Reference (centerpiece)

A dedicated page `…/invoicing/invoice-field-reference` (EN + AR). One table per
form section, in form order. Every field documented. The narrative
**Create / Edit an Invoice** page links into each section here.

Conditional/validation rules are surfaced as Mintlify `<Warning>` / `<Note>`
callouts where they apply.

### Field inventory (authoritative — source: web app `InvoiceFormPage.tsx`, child components, `types/invoice.ts`)

**1. Document header**
| Field | Required | Control | Allowed / Default | Notes |
|---|---|---|---|---|
| Invoice # (`invoiceNumber`) | Yes | Text / auto | From number series | Read-only in edit mode |
| Currency (`documentCurrencyCode`) | Yes | Dropdown | Default `AED` | |
| Issue Date (`issueDate`) | Yes | Date | | |
| Due Date (`dueDate`) | Conditional | Date | | Expected for invoices; optional for credit notes |

**2. Counterparty & credit-note reference**
| Field | Required | Control | Allowed / Default | Notes |
|---|---|---|---|---|
| Customer/Vendor (`customerId`/`vendorId`) | Expected | Searchable dropdown | From master data | Tax Invoice requires VAT TRN; Commercial excludes it. Field depends on direction/self-billing |
| Referenced Invoice (`precedingInvoiceNumber`) | Yes (credit notes) | Text | | Credit notes only |
| Preceding Invoice Date (`precedingInvoiceIssueDate`) | No | Date | | Credit notes only |

**3. Line items**
| Field | Required | Control | Allowed / Default | Notes |
|---|---|---|---|---|
| Item Name (`itemName`) | Yes | Searchable dropdown / text | From item catalog | Selecting a catalog item prefills name, UoM, price, default tax & classification |
| Description (`itemDescription`) | No | Text | | |
| Quantity (`quantity`) | Yes | Number | Default 1 | |
| Unit of Measure (`unitOfMeasure`) | Yes | Dropdown | Default `EA` | UN/CEFACT unit |
| Unit Price (`unitPrice`) | Yes | Number | | |
| Line allowances/charges | No | Nested editor | | `isCharge`, `amount`, `reason`, `reasonCode` |
| VAT Group (`vatGroupId` → `taxCategoryCode`, `taxRate`) | Yes | Dropdown | S/Z/E/O/AE/L | New lines default to 5% standard group |
| Exemption Reason (`taxExemptionReasonId`) | Conditional | Dropdown | FTA reason codes | Required & shown only when category = `E` |
| Line Net (`lineNetAmount`) | — | Computed | | qty × price − allowances + charges |

**4. Line item identifiers** (per line)
| Field | Required | Control | Notes |
|---|---|---|---|
| Buyer's item ID (`buyersItemId`) | No | Text | BT-156 — item id in buyer's catalogue |
| Seller's item ID (`sellersItemId`) | No | Text | BT-155 — item id in seller's catalogue |
| Standard item ID (`standardItemId`) | No | Text | BT-157 — e.g. GTIN barcode |
| Standard scheme ID (`standardItemSchemeId`) | No | Text | BT-157-1 — e.g. `0160` for GTIN. Reverse-charge lines need this |

**5. Line item classifications** (per line, repeatable)
| Field | Required | Control | Allowed | Notes |
|---|---|---|---|---|
| Code (`classifications[].code`) | Per-row | Text | | Code within the chosen scheme (e.g. HS tariff code) |
| List / scheme (`classifications[].listId`) | Per-row | Dropdown | Valid UNTDID-7143 (ibr-cl-13) | HS, TSR(UNSPSC), SRV(GTIN), STJ, … |
| Version (`classifications[].listVersion`) | No | Text | | |

**6. Document Charges & Allowances** (collapsible, repeatable)
| Field | Required | Control | Allowed / Default | Notes |
|---|---|---|---|---|
| Allowance/Charge (`isCharge`) | Yes | Toggle | | |
| Amount (`amount`) | Yes | Number | | |
| Reason (`reason`) | No | Text | | |
| Code (`reasonCode`) | No | Text | | |
| Tax Category (`taxCategoryCode`) | Yes | Dropdown | S/E/O/AE/Z/L, default S | Document-level only |
| Tax Rate (`taxRate`) | Yes | Number | Default 5 | Document-level only |
| Exemption Reason (`taxExemptionReasonId`) | Conditional | Dropdown | | Required when category = E and amount > 0 |

**7. References & Delivery** (collapsible)
- Buyer reference (`buyerReference`) — text, optional.
- Document references (`references[]`, repeatable): Type
  (PurchaseOrder/SalesOrder/Contract/Project/Despatch/Receipt) — required;
  Value — required; Issue date — optional; Scheme id — optional.
- Attachments (`additionalDocuments[]`, repeatable): Document ID; file via
  **Choose from library** (`tenantFileId`) or **Upload new**, or external URI
  (`externalUri`); read-only file metadata when selected. Note: a public link is
  generated per attached file once the document is created.
- Delivery: Actual delivery date (`delivery.actualDeliveryDate`); Incoterms
  (`delivery.incoterms`: EXW/FCA/CPT/CIP/DAP/DPU/DDP/FAS/FOB/CFR/CIF); Street;
  Additional address line; City (searchable); State/Region (Emirate dropdown);
  Country (`delivery.countryCode`, default AE, required if any address filled).

**8. Payment & notes** (collapsible)
| Field | Required | Control | Allowed | Notes |
|---|---|---|---|---|
| Payment Method (`paymentMethodId`) | No | Dropdown | From master data | |
| Payment Terms (`paymentTerms`) | No | Dropdown | Due on Receipt, COD, Net 7/15/30/45/60/90 | |
| Customer Notes / Remarks (`note`) | No | Textarea | | |

**9. Summary / Totals** (sticky, read-only, computed)
Line items net total · Document allowances total · Document charges total ·
Total net · Total VAT (per-category breakdown) · Total gross (incl. VAT).

### Validation rules to call out (callouts)
- Exemption reason **required** for tax category `E` (line and doc-level A/C).
- Reverse-charge (`AE`) lines need a Standard item ID with scheme `0160`.
- Classification scheme (listID) must be a valid UNTDID-7143 code (ibr-cl-13).
- Tax Invoice vs Commercial Invoice TRN requirement on the counterparty.

## Developers / API Reference

- **Native generated reference**: vendor a snapshot of the v1 OpenAPI spec into
  the repo (e.g. `api-reference/openapi.json`) and reference it from `docs.json`
  navigation so Mintlify auto-generates interactive per-endpoint pages with the
  API Playground. Generated endpoint pages are **EN-only** (schema/field names
  are English).
- **Guide pages** (bilingual EN+AR): Overview, Authentication (API keys),
  Quickstart (create your first invoice — mirrors `V1CreateInvoiceRequest`,
  including the new `references[]`, `delivery`, `allowancesCharges[]`, line
  `classifications[]`, item ids, `exemptionReasonCode`), Idempotency
  (`Idempotency-Key`), Errors. Confirm whether Webhooks/status-callbacks exist
  before adding that page.
- Implementation: obtain the spec (it is JWT-gated at
  `app.fatorly.com/developer/api-reference`; the schema auto-regenerates from
  code so it is current). Confirm the guide topic list against the actual v1 API
  surface before writing.

## Per-Page Rebuild Workflow

For each page, in nav order:
1. Write/update the **EN** MDX (prose reflecting the new design + fields).
2. Write/update the **AR** MDX (translated, RTL).
3. Capture fresh **light-mode** screenshots from `app.fatorly.com` using the
   test login (Playwright), for both EN and AR app locales where the UI differs;
   save under `images/en/<section>/...` and `images/ar/<section>/...` following
   existing naming.
4. Update `TRACKER.md` (EN, AR, Shots EN, Shots AR, Reviewed).
5. Commit per section, matching the existing repo commit style
   (`docs: …`).

Screenshot/access notes:
- Test login creds live in E-Invoice.PEPPOL memory `project_test_accounts_seeding`.
- Use the white-label **Fatorly** brand; do not hardcode "HA Consultancy"
  (memory `project_brand`).
- All prior screenshots are replaced by the redesign.

## Execution Order (batched)

1. **Scaffold**: new `docs.json` IA (EN+AR), create/rename page files & image
   folders, update `TRACKER.md` rows to the new page set.
2. **Invoice Editor** (highest value): Create/Edit walkthrough + **Invoice Field
   Reference** + View. EN+AR + screenshots.
3. **Sales / Purchases / Self-Billing** list pages.
4. **Reports** + **Files**.
5. **Master Data** refresh.
6. **Company Settings** hub (all tabs) + **Multi-Company** + **Auditor**.
7. **Getting Started** + **Dashboard** refresh.
8. **Developers**: vendor `openapi.json`, wire native API reference, write guides.

Each batch is independently reviewable and committable.

## Open Items / Risks
- **Attachments**: prior handover (HD-2026-06-15-02) said file-upload was not
  shipped; the current app exposes "Choose from library / Upload new" + a Files
  page, so it appears live. Verify on prod before documenting the upload flow;
  fall back to URL-reference form if not actually available.
- **OpenAPI spec retrieval**: spec is JWT-gated; need a way to export/vendor it.
- **AR API reference**: generated endpoint pages stay EN-only; only guide pages
  are translated. Confirm acceptable.
- **Webhooks guide**: include only if the v1 API actually has webhooks.
- Confirm the app sidebar grouping on live prod matches the code-derived IA
  before locking `docs.json` (verify during the screenshot pass).
