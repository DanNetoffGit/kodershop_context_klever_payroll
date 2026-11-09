---
status: open        # open | done | closed
created_by: Daniil Honcharenko
assigned_to: Daniil Honcharenko
done_by:
created: 2026-11-09
related_docs: [../klever_api/accounting-codes.md, ../klever_api/README.md, ../ACCOUNTING_kleverPayroll.md, ../specs/gl-journal-entries-sync.md]
---

# kleverPayroll: the endpoint that registers accounting codes

## Task

Find and document how accounting codes are created in kleverPayroll through the API, so Odoo can push
its chart of accounts there and the codes in journal entry rows match Odoo accounts by construction
(decision D9, phase 2, in [ACCOUNTING_kleverPayroll.md](../ACCOUNTING_kleverPayroll.md#3-decisions);
spec §18). Today [accounting-codes.md](../klever_api/accounting-codes.md) knows that
`/accounting_codes` exists, but its full reference is still to read.

1. **Record the vendor documentation.** Add the links to the kleverPayroll API reference pages already
   collected (accounting codes, accounting code rules, journal entries, business entity accounting
   settings) to [klever_api/README.md](../klever_api/README.md), one per endpoint, so every
   **(verified)** statement points at its source page.
2. **Read the `/accounting_codes` reference** — create, update, list, delete, merge — and write down:
   - the request body of `POST /accounting_codes`: required and optional fields, allowed `type`
     values, whether `code` must be unique per Business Entity;
   - whether a code can carry an external identifier for a platform other than QuickBooks or Xero
     (the "export identifier" behind `block_approval_on_missing_export_identifiers`), and how it is set;
   - how a code is updated or retired, and what happens to rules and approved payrolls that use it;
   - the endpoint that returns and updates the BE's `accounting_settings` (still to confirm).
3. **Call it in the sandbox** on "BE One": create one accounting code, list it back, update it, and
   paste the real request and response (ids may stay, no secrets) into the result.
4. **Look through the vendor web app's accounting menu** (accounting codes, rules, integrations,
   journal entry export): how codes are managed there, what an export file looks like (columns,
   format), and whether its layout could serve as the format Odoo parses directly.

## Result

<!-- Endpoint reference with links, sandbox request/response, notes on the accounting menu and export
files. Update klever_api/accounting-codes.md (marked verified) in the same commit. -->

## Follow-up

<!-- Optional -->
