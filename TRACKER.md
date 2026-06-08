# Documentation Tracker

Legend: ⬜ todo · 🟡 in progress · ✅ done

A page is **done** only when EN, AR, both screenshot sets, and Reviewed are all ✅.

| #  | Section         | Page                  | Route                          | EN | AR | Shots EN | Shots AR | Reviewed | Notes |
|----|-----------------|-----------------------|--------------------------------|----|----|----------|----------|----------|-------|
| 1  | Getting Started | Introduction          | (overview)                     | ✅ | ✅ | n/a      | n/a      | 🟡       | authored |
| 2  | Getting Started | Register              | /register                      | ✅ | ✅ | ✅       | ✅       | 🟡       | light mode |
| 3  | Getting Started | Login                 | /login                         | ✅ | ✅ | ✅       | ✅       | 🟡       | light mode |
| 4  | Getting Started | Forgot Password       | /forgot-password               | ✅ | ✅ | ✅       | ✅       | 🟡       |       |
| 5  | Getting Started | Reset Password        | /reset-password                | ✅ | ✅ | ✅       | ✅       | 🟡       | form state (no token) |
| 6  | Getting Started | Verify Email          | /verify-email                  | ✅ | ✅ | ✅       | ✅       | 🟡       | error state (no token) |
| 7  | Getting Started | Onboarding Wizard     | /onboarding                    | ✅ | ✅ | ✅       | ✅       | 🟡       | step 1; launched from EmaraTax |
| 8  | Getting Started | MFA Setup             | /settings/mfa                  | ✅ | ✅ | ✅       | ✅       | 🟡       | QR+secret redacted |
| 9  | Home            | Dashboard             | /                              | ✅ | ✅ | ✅       | ✅       | 🟡       | light mode |
| 10 | Home            | What's New            | /whats-new                     | ✅ | ✅ | ✅       | ✅       | 🟡       |       |
| 11 | Master Data     | Items                 | /items                         | ✅ | ✅ | ✅       | ✅       | 🟡       | list + form |
| 12 | Master Data     | Customers             | /customers                     | ✅ | ✅ | ✅       | ✅       | 🟡       | list + form |
| 13 | Master Data     | Vendors               | /vendors                       | ✅ | ✅ | ✅       | ✅       | 🟡       | list + form |
| 14 | Master Data     | Payment Methods       | /payment-methods               | ✅ | ✅ | ✅       | ✅       | 🟡       | list |
| 15 | Invoicing       | Sales Invoices        | /sales/invoices                | ✅ | ✅ | ✅       | ✅       | 🟡       |       |
| 16 | Invoicing       | Sales Credit Notes    | /sales/credit-notes            | ✅ | ✅ | ✅       | ✅       | 🟡       |       |
| 17 | Invoicing       | Purchase Invoices     | /purchases/invoices            | ✅ | ✅ | ✅       | ✅       | 🟡       |       |
| 18 | Invoicing       | Purchase Credit Notes | /purchases/credit-notes        | ✅ | ✅ | ✅       | ✅       | 🟡       |       |
| 19 | Invoicing       | Self-Billing          | /self-billing                  | ✅ | ✅ | ✅       | ✅       | 🟡       |       |
| 20 | Invoicing       | Create / Edit Invoice | /invoices/new                  | ✅ | ✅ | ✅       | ✅       | 🟡       | empty form |
| 21 | Invoicing       | View Invoice          | /invoices/:id                  | ✅ | ✅ | ✅       | ✅       | 🟡       | invoice #1038 |
| 22 | Settings        | Company Profile       | /settings/company-profile      | ✅ | ✅ | ✅       | ✅       | 🟡       |       |
| 23 | Settings        | Number Series         | /settings/number-series        | ✅ | ✅ | ✅       | ✅       | 🟡       |       |
| 24 | Settings        | Company Auditors      | /settings/auditors             | ✅ | ✅ | ✅       | ✅       | 🟡       | empty state |
| 25 | Settings        | Integrations          | /settings/integrations         | ✅ | ✅ | ✅       | ✅       | 🟡       | QuickBooks connected |
| 26 | Settings        | API Keys              | /settings/integration/api-keys | ✅ | ✅ | ✅       | ✅       | 🟡       |       |
| 27 | Settings        | Change Password       | /settings/change-password      | ✅ | ✅ | ✅       | ✅       | 🟡       |       |
| 28 | Auditor         | Company Selector      | /auditor/companies             | ✅ | ✅ | ✅       | ✅       | 🟡       | EN-only UI |
| 29 | Auditor         | Auditing a Company    | (read-only views)              | ✅ | ✅ | ✅       | ✅       | 🟡       | bilingual app shell |
| 30 | Developers      | API Reference         | /developer/api-reference       | ✅ | ✅ | n/a      | n/a      | 🟡       | launch link; route blank on prod (not deployed?) — no screenshot |
