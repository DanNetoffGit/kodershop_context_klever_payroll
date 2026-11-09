# kleverPayroll → Odoo Accounting (GL) sync — discussion notes

Status: analysis done, no code written. Date: 2026-11-04.
Scope: `ks_klever_payroll_client` (client instance, Odoo 19) in `repositories/klever_payroll_19`.

These notes summarise a working session over the vendor API docs and the sandbox. Statements marked
**(verified)** come from vendor docs or sandbox output; **(inferred)** are reasoning still to be
confirmed.

---

## 1. The task (as received)

Integrate KleverPayroll General Ledger with Odoo Accounting: after payroll processing, retrieve GL
data from kleverPayroll and create journal entries in Odoo.

Requirements:
1. Retrieve GL data via API.
2. Use the Accounting Information configured in kleverPayroll.
3. Map kleverPayroll GL accounts to `account.account`.
4. Create `account.move` with debit/credit lines.
5. Link journal entries to the relevant Payroll / Pay Run.
6. Prevent duplicates on repeated sync.
7. Handle sync errors and log results.

Condition: GL data can be extracted only if Accounting Information is properly configured.

Acceptance: GL imported; entries balanced; no duplicates on re-sync; users see sync status and errors.

Open questions in the task: GL API structure; sync trigger; account mapping rules; draft vs posted.

---

## 2. How payroll relates to the GL

A pay run is an accounting event:

- **Debit** — company expenses: gross wages, employer CPP/CPP2/EI, employer benefits.
- **Credit** — liabilities: employee withholdings owed to CRA/RQ, employer contributions to remit,
  net pay owed to employees.

Then payment clears the liabilities against the bank.

Illustrative accrual (numbers made up):

| Account | Dr | Cr |
|---|---:|---:|
| Wages Expense | 10 000 | |
| Employer CPP Expense | 550 | |
| Employer EI Expense | 230 | |
| Income Tax Payable | | 1 800 |
| CPP Payable (550 ee + 550 er) | | 1 100 |
| EI Payable (164 ee + 230 er) | | 394 |
| Net Pay Payable | | 7 486 |
| **Total** | **10 780** | **10 780** |

Odoo Enterprise `hr_payroll_account` does the same for native Odoo payroll; here payroll is external,
so Odoo only receives the finished accounting result.

---

## 3. Vendor API findings

Base: `https://sandbox.nmbr.co/services/payroll` (sandbox). Auth: `Bearer <company-scoped token>` —
the client already obtains it.

### 3.1 Accounting codes and rules (verified)

- **Accounting code** — one Chart-of-Accounts account, defined per Business Entity. Fields:
  `business_entity_id`, `title`, `type` ∈ `expense | liability | bank`, `code` (optional, e.g. `6000`),
  `description`. Can be imported from a connected accounting platform. Duplicates merged via
  `POST /accounting_codes/{id}/merge`.
- **Accounting code rules** — route each payroll line item to an `expense_accounting_code` and a
  `liability_accounting_code`. Match by `type` (+ `subtypes` set), `business_preset_id`, `tag_id`,
  `tag_group_id`. 12-level precedence, most specific first; expense and liability sides resolved
  independently. One catch-all rule (no matchers) per BE.
- Rules re-evaluate on every draft recalculation; **approved payrolls keep the codes resolved at
  approval**.
- **`accounting_settings`** on the Business Entity: `default_bank_accounting_code`,
  `default_payroll_payable_accounting_code`, `primary_tag_group`,
  `resolve_journal_entry_stat_withholdings`, `block_approval_on_incomplete_journal_entries`,
  `block_approval_on_missing_export_identifiers`.
- Without default bank / payroll payable codes, entries still generate but are incomplete and flagged.
- Deprecated: "GL Codes" entity and `fallback_mappings`. Do not use.

"Accounting Information properly configured" therefore means: default bank + payroll payable codes
set, rules (or a catch-all) cover every line item, and the resulting entry carries no validation flags.

### 3.2 Journal entries (verified)

Per payroll the vendor generates:

| `type` | Level | Meaning | Exported to accounting |
|---|---|---|---|
| `payroll_recording` | payroll | accrual: Dr expense / Cr liability | **yes** |
| `payroll_payment` | payroll | payment: Dr liability / Cr bank | **yes** |
| `pay_stub_recording` | one pay stub | accrual detail for one employee | no |

Only `payroll_recording` and `payroll_payment` are exported to accounting platforms (vendor docs).
Importing `pay_stub_recording` as well would double-count.

`GET /journal_entries?payroll_id=…` — `payroll_id` **required**; optional `type`,
`expand=journal_entry_rows,pay_stub,payroll`. Paginated (15/page, `links.next`, `meta.has_more`).
`GET /journal_entries/{id}` — same expand options.

Entry fields: `id`, `type`, `title` (+ `title_translations` en/fr), `payroll.id`, `pay_stub`,
`is_balanced`, `total`, `is_missing_line_item_accounting_codes`, `is_missing_default_account_payable`,
`is_missing_default_bank_account`, `posted_on`, `invalid_expense_types`, `invalid_liability_types`
(lists of `{type, subtype}`), `integrations.quickbooks.reference`, `integrations.xero.reference`,
`created_at`, `updated_at`.

Row fields (`expand=journal_entry_rows`): `id`, `type` ∈ `debit | credit`, `amount`, `code` (string),
`description`. **No accounting_code_id, no tags, no partner, no analytic dimension.**

Doc inconsistencies to tolerate: `is_missing_*` documented as integer but examples show booleans;
an example shows `total: 0` on a balanced entry.

Export readiness (vendor docs): entry must be balanced and have all accounting codes.

### 3.3 Payrolls (verified)

`GET /payrolls` — requires `business_entity_id` or `pay_schedule_id`. Filters: `status`
(comma-separated multi-value), `type`, `period_start`/`period_end`/`pay_date` (exact or
`[gt|gte|lt|lte]`), `external_ref`, `next_payrolls`, `sort` (`pay_date`, `period_start`, …).
Paginated. No `updated_at` filter.

- `status`: `draft`, `processing`, `approved`, `paid`, `failed`; `partially_paid` is deprecated and
  matches nothing.
- `type`: `regular`, `off_cycle`, `historical`, `correction`.
- Dates: `pay_date`, `period_start/end`, `debit_date`, `approved_at`.
- Totals: `employee_summary`, `contractor_summary`, `company_summary`, `liability`, `cash_requirement`.
- `external_ref` — free text for the integrator; not unique, not validated.

### 3.4 Payments (verified)

`destination` ∈ `payroll_float` (PAD debit from employer bank), `employee`, `cra`, `rq`, `bill_pay`,
`internal`, `no_op`, `on_mof`; `method` ∈ `pad`, `direct_deposit`, `wire`, `manual`.

Money flow: the employer's bank is debited **once** (`cash_requirement`) into the vendor's payroll
float; the vendor then pays employees and remits to CRA/RQ. Hence `payroll_payment` (Dr liabilities /
Cr bank) should match one bank-statement line in Odoo **(inferred, check in sandbox)**.

### 3.5 Journal Entry report and segment templates (verified)

- The Journal Entry report is async: `POST` create → `GET` retrieve → `GET` download
  (`text/csv` or `application/vnd.ms-excel`) → `DELETE`. It has a Summary and a Detailed variant in
  the dashboard.
- Segment templates build composite codes (e.g. `5000-ENG-TOR`) that appear **only in the report**
  (columns Generated Expense / Liability Code). They are **never exported** and never affect the
  accounting code synced to an accounting platform.
- Conclusion: segment templates are irrelevant to the GL sync; rows carry the plain accounting code.
  The report is a reconciliation oracle, not an import source.

### 3.6 Lookups

Line-item type lookups (Earning, Allowance, Deduction, Employee/Employer Benefit, Reimbursement,
Employee/Employer Statutory Withholding) list every `type/subtype` that rules route. Holidays is
irrelevant.

Which side needs a code **(inferred; statutory rows verified by the vendor example)**:

| Line item type | Expense | Liability |
|---|:-:|:-:|
| earning, allowance, reimbursement, employer benefit, employer statutory withholding | ✓ | ✓ |
| deduction, employee benefit, (employee) statutory withholding | — | ✓ |

Use: human-readable error labels for `invalid_*_types`; later, a pre-approval coverage check.
Exact `type` values must be read from the lookup pages.

### 3.7 Other reports

Liability report and CRA/RQ remittance reports — useful for reconciliation. Payroll register, YTD,
T4/RL1, year-end forms, EI/CPP balance, Forms, ROE, Vacation Pay — not GL.

Likely **not** in journal entries **(inferred)**: vacation pay accrual (earned, unpaid), EHT, WCB.

---

## 4. Sandbox state (2026-11-04)

Business Entity "BE One": Xero and QuickBooks not connected. Accounting Details tabs: Settings,
Recent Journals, Accounting Codes, Accounting Code Rules, Tracking Dimensions.

Recent Journals:
- Period 22 · Canadian Employee bi-weekly Hourly · pay date 2026-11-05 · Regular · **Processing** —
  the test candidate (post-approval, codes fixed).
- Period 23 · Canadian Employee bi-weekly · pay date 2026-11-06 · Regular · Draft — must be skipped.

Two pay schedules on one BE → separate payrolls, separate entries.

Still to check in the sandbox:
1. Settings: default bank and payroll payable codes set?
2. Accounting Codes: is `code` filled (number), or only `title`?
3. Rules: catch-all or rules covering `statutory_withholding` and `employer_statutory_withholding`?
4. Period 22 entries via API with `expand=journal_entry_rows`: balanced, no flags; what `code`
   actually contains; does `payroll_payment` clear all liabilities with one bank row?
5. Compare the Summary report of Period 22 with the API rows.

---

## 5. Existing client code

- `ks.klever.payroll` (`models/klever_payroll.py`) already mirrors payrolls: `ks_klever_ref_payroll_id`,
  `status`, `type`, `pay_date`, `liability`, `cash_requirement`, `warnings_json`, `hr_payslip_run_id`.
  → Select payrolls to sync from it; link `account.move` to it (Many2one) to satisfy requirement 5.
- `expense_accounting_code_id` / `liability_accounting_code_id` exist as raw `Char` on
  `klever_pay_rate.py` and `klever_overtime_rate.py` (passed through to the API).
- Embedded component settings `showGlCodes`, `hideJournalEntryDimensionSelection`
  (`wizard/klever_settings_wizard.py`).
- No accounting-code model, no `account.account` link, no journal entry import, no lookup usage.
- The Business Entity id is already stored on the client after registration.

---

## 6. Proposed design (draft)

Selection:

```
GET /payrolls?business_entity_id=<BE>&status=approved,processing,paid
             &pay_date[gte]=<sync_from>&sort=pay_date
(or read the same set from ks.klever.payroll)
filter type locally (skip historical unless the customer decides otherwise)
```

Per payroll:

```
GET /journal_entries?payroll_id=…&expand=journal_entry_rows   (follow pagination)
for entry with type ∈ {payroll_recording, payroll_payment}:
    already imported (klever journal entry id exists)      → skip
    not is_balanced or any is_missing_*                     → log error (+ invalid_*_types)
    any row.code not mapped to account.account              → log error "code X not mapped"
    else create account.move:
        journal   = configured Payroll journal
        date      = payroll.pay_date
        ref       = payroll label + entry title
        lines     = rows → debit/credit, account by code, name = description
        link      = ks.klever.payroll (Many2one), klever journal entry id (unique)
        state     = draft, or posted if the company enables auto-post
```

Account mapping:
- Primary: kleverPayroll `code` equals `account.account.code` → direct lookup per company.
- Fallback: a small mapping table "kleverPayroll code → account.account" per company.
- Optional later: push Odoo accounts into kleverPayroll as accounting codes so codes match by
  construction.

Chart of Accounts push (the Odoo counterpart of the vendor's QuickBooks "Import GL Codes"):
- The QuickBooks integration menu offers Import GL Codes / Projects / Departments / Categories —
  pulling the accounting platform's accounts into accounting codes and its dimensions into tag
  groups/tags. There is no native Odoo integration, so the client pushes instead.
- Action "Send Chart of Accounts to kleverPayroll": selected `account.account` of the company →
  `POST /accounting_codes` (`business_entity_id`, `title` = name, `code` = account code, `type`).
  Type mapping (inferred): `expense*` → `expense`; `liability_current` / `liability_payable` →
  `liability`; `asset_cash` → `bank`.
- Store the kleverPayroll accounting code id per account (mapping model or field), so re-runs update
  instead of duplicating.
- Departments / projects (analytic accounts → tag groups/tags) are deferred: journal entry rows carry
  no tag data, so pushing dimensions does not affect the GL import.
- To read next: Accounting Codes API reference (create/update/list, and whether an external
  identifier for a non-Xero/QuickBooks integration can be set — relevant to
  `block_approval_on_missing_export_identifiers`).

Duplicate protection: unique kleverPayroll journal entry id on `account.move`. `external_ref` and
`integrations` are not used (the latter has no Odoo slot).

Sync log: one record per payroll/entry with status (created / skipped / error) and message; visible to
users. Trigger: cron plus a manual "Sync now" button.

Out of scope (proposed): `pay_stub_recording`, segment templates, analytic distribution (rows carry no
tags), EHT, WCB, vacation accrual.

Repository constraints: no `nmbr` in any identifier, string or doc inside `klever_payroll_19`; the
product is written **kleverPayroll** in user-visible text; no `_()` translations unless requested.

---

## 7. Questions for the customer

1. **Draft or posted?** Should imported journal entries stay in draft for accountant review, or be
   posted automatically? Proposal: draft by default, with a per-company "Auto-post" option.
2. **Trigger.** Automatic (scheduled, e.g. hourly/daily) after payroll approval, manual "Sync now",
   or both?
3. **Start date / history.** From which pay date should payrolls be imported? Are earlier periods
   already in the GL from a previous payroll provider?
4. **Historical payrolls.** Should payrolls of type `historical` ever be imported, or always skipped
   (they usually duplicate data already in the GL)?
5. **Off-cycle and correction payrolls.** Import them as separate journal entries like regular ones —
   confirm.
6. **Payment entry.** We plan to import both the accrual and the payment entry (Dr liabilities /
   Cr bank), as the vendor's Xero/QuickBooks export does. Is the payroll bank debit then reconciled
   in Odoo against this entry? Which Odoo bank journal/account represents the account the vendor
   debits?
7. **Account mapping.** Will the codes configured in kleverPayroll match the Odoo account codes
   exactly (e.g. `5000` = `5000`)? Or do you need a mapping screen in Odoo? Should Odoo push its Chart
   of Accounts into kleverPayroll so the codes always match?
8. **Who configures accounting in kleverPayroll** (codes, rules, default bank and payroll payable
   accounts) — the customer's accountant, or us during onboarding?
9. **Journal.** Which Odoo journal should hold payroll entries — a dedicated "Payroll" journal per
   company?
10. **Departments / analytics.** Do you need payroll costs split by department, location or project
    in Odoo (analytic accounts)? The vendor API rows carry no tag data, so this is not in the first
    version.
11. **Items outside payroll entries.** Vacation pay accrual (earned, not yet paid), Employer Health
    Tax (EHT) and WCB premiums are most likely not part of the payroll journal entries. Confirm they
    are out of scope and recorded by the accountant separately.
12. **Contractors.** Contractor pay appears in payroll totals. Should it go into the same entries
    (as the vendor generates them) — confirm no separate treatment is expected.
13. **Errors and visibility.** Who must see sync status and errors (accounting group only, payroll
    admins)? Should failures notify someone (activity / email), or is a status list enough?
14. **Multi-company.** One kleverPayroll Business Entity per Odoo company — confirm.
15. **After import changes.** If an imported entry is edited or deleted in Odoo, should the next sync
    recreate it, or leave it alone? Proposal: never recreate once imported; corrections go through
    kleverPayroll correction payrolls.
